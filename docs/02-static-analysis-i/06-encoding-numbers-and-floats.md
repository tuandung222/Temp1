---
id: 06-encoding-numbers-and-floats
title: 3.6 Encoding numbers và floats
sidebar_position: 6
description: Cách BMC tool dịch int, unsigned, signed, char và float, double thành bitvector và floating-point theory để verify đúng semantics C/C++.
---

# 3.6 Encoding numbers và floating-point

> **Tóm tắt một dòng**: Để BMC verify chính xác semantics C, mọi kiểu số phải được encode đúng cách: số nguyên thành bitvector kích thước cố định, số thực thành IEEE 754 floating-point. Encoding sai cho kết quả sai (false negative hoặc false positive), vì thế bài này quan trọng cho việc dùng tool hiệu quả.

## Vì sao bài này quan trọng?

Ở [bài 2.2](../01-introduction/06-bmc-and-smt-basics), ta đã thấy một ví dụ kinh điển: hàm `abs(x)` được verify với theory `Int` thì pass, nhưng với theory `BitVec` 32-bit thì fail với input `INT_MIN`. Sự khác biệt này không phải lỗi của tool, mà phản ánh đúng sự khác biệt giữa **số nguyên toán học** và **số nguyên máy tính**.

Nếu bạn encode sai, hai kịch bản đáng sợ có thể xảy ra:

- **False negative** (miss bug): bạn dùng theory `Int` cho code C, tool trả "an toàn", nhưng thực ra có bug overflow. Code đẩy production, bug phát nổ.
- **False positive** (báo nhầm): bạn over-approximate, tool báo bug nhưng input đó không khả thi trong môi trường thực. Developer mất công xem mỗi PR.

Bài này dạy bạn encode đúng cho mọi kiểu số chính của C: `char`, `short`, `int`, `long`, `unsigned`, `float`, `double`. Sau bài này, bạn có thể đọc output của CBMC/ESBMC và hiểu vì sao counterexample đó là counterexample thật.

## Phần 1: Số nguyên với bitvector

### Bitvector là gì?

Bitvector kích thước $n$ là một dãy $n$ bit có thứ tự. Trong SMT-LIB:

```scheme
(declare-const x (_ BitVec 32))
```

Khai báo $x$ là bitvector 32-bit. Giá trị của $x$ là một số trong $\{0, 1, \ldots, 2^{32} - 1\}$ nếu xem là unsigned, hoặc $\{-2^{31}, \ldots, 2^{31} - 1\}$ nếu xem là signed (two's complement).

Điểm tinh tế: **bitvector tự nó không có dấu**. Nó chỉ là dãy bit. Việc "có dấu hay không" phụ thuộc vào **phép toán** bạn áp dụng. SMT-LIB cung cấp hai họ phép:

| Phép | Unsigned | Signed |
|---|---|---|
| So sánh nhỏ hơn | `bvult` | `bvslt` |
| So sánh nhỏ hơn hoặc bằng | `bvule` | `bvsle` |
| Chia | `bvudiv` | `bvsdiv` |
| Modulo | `bvurem` | `bvsrem` |

Còn cộng, trừ, nhân (`bvadd`, `bvsub`, `bvmul`), AND, OR, XOR (`bvand`, `bvor`, `bvxor`) không phân biệt dấu vì kết quả bit-level giống nhau.

### Two's complement: lý thuyết và thực hành

Số có dấu trong máy tính dùng **two's complement**. Bit cao nhất (MSB) là dấu: 0 nghĩa dương, 1 nghĩa âm. Để chuyển từ số dương sang số âm tương ứng: đảo mọi bit rồi cộng 1.

Ví dụ với 8-bit:
- $5_{10} = 00000101_2$
- $-5$: đảo bit thành $11111010$, cộng 1 thành $11111011 = -5_{10}$

Two's complement có một tính chất rất hữu ích: phép cộng và trừ hoạt động **giống hệt** signed và unsigned ở mức bit. Phần cứng CPU không cần biết bạn đang dùng signed hay unsigned, chỉ cần biết cách check overflow.

### Encode kiểu C sang bitvector

Bảng mapping (giả định x86-64):

| Kiểu C | Bitvector | Phép so sánh dùng |
|---|---|---|
| `char` (signed mặc định trên GCC) | `(_ BitVec 8)` | `bvslt`, `bvsle` |
| `unsigned char` | `(_ BitVec 8)` | `bvult`, `bvule` |
| `short` | `(_ BitVec 16)` | `bvslt` |
| `unsigned short` | `(_ BitVec 16)` | `bvult` |
| `int` | `(_ BitVec 32)` | `bvslt` |
| `unsigned int` | `(_ BitVec 32)` | `bvult` |
| `long` | `(_ BitVec 64)` | `bvslt` |
| `unsigned long`, `size_t` | `(_ BitVec 64)` | `bvult` |
| `bool` | `(_ BitVec 1)` | `bvult` |

### Ví dụ: phát hiện integer overflow

Code C:

```c
int add(int a, int b) {
    int c = a + b;
    assert(c >= a);   // tin rằng "cộng số dương cho kết quả lớn hơn"
    return c;
}
```

SSA encoding:

```
a₁ = nondet (BitVec 32)
b₁ = nondet (BitVec 32)
c₁ = bvadd(a₁, b₁)
assert(bvsge(c₁, a₁))
```

SMT-LIB với BV theory:

```scheme
(set-logic QF_BV)
(declare-const a (_ BitVec 32))
(declare-const b (_ BitVec 32))
(declare-const c (_ BitVec 32))
(assert (= c (bvadd a b)))
(assert (not (bvsge c a)))
(check-sat)
(get-model)
```

Solver trả `sat` với model như: $a = 1, b = $ INT_MAX. Khi $a + b = 1 + (2^{31} - 1) = 2^{31}$ wrap về $-2^{31}$, nhỏ hơn $a = 1$, assertion fail.

Bug này không thể bắt được nếu encode bằng theory `Int` (vì trong toán, $a + b > a$ khi $b > 0$).

### Phát hiện signed/unsigned confusion

Một dạng bug rất tinh tế trong C: dùng signed comparison với giá trị thực sự là unsigned (hoặc ngược lại).

```c
int receive(unsigned int len) {
    char buf[256];
    if (len > sizeof(buf)) return -1;   // check unsigned: OK
    memcpy(buf, src, len);              // copy len bytes
    return 0;
}
```

Hàm này có vẻ an toàn. Nhưng nếu caller gọi `receive(-1)`?

```c
receive(-1);   // -1 cast sang unsigned int = 0xFFFFFFFF = 4294967295
```

Trong hàm `receive`, `len = 4294967295`. `len > 256` đúng, hàm return -1, an toàn. Vậy không phải bug.

Nhưng nếu code check như sau (lỗi tinh tế):

```c
int receive_bad(int len) {              // KIỂU SIGNED!
    char buf[256];
    if (len > sizeof(buf)) return -1;   // signed > unsigned: tricky!
    memcpy(buf, src, len);
    return 0;
}
```

C standard nói gì? Khi so sánh `int len` với `sizeof(buf)` (kiểu `size_t`, unsigned), compiler **promote** `len` sang `size_t`. Nếu `len = -1`, promote thành `0xFFFFFFFF`. `0xFFFFFFFF > 256` đúng, return -1.

Nhưng dấu check không đủ. Nếu `len = -100`, promote thành `0xFFFFFF9C` (xấp xỉ $2^{32} - 100$), vẫn > 256, return -1.

Vậy bug ở đâu? Ở `memcpy(buf, src, len)` mà `len` có kiểu `int`. Khi truyền cho `memcpy` (yêu cầu `size_t`), `-1` cast thành `0xFFFFFFFF`, memcpy copy $2^{32} - 1$ byte. Buffer overflow khổng lồ.

BMC với BV theory phát hiện được bug này nếu ta thêm assertion `len >= 0`:

```scheme
(set-logic QF_BV)
(declare-const len (_ BitVec 32))   ; signed int
(declare-const buf_size (_ BitVec 64))   ; size_t
(assert (= buf_size #x0000000000000100))   ; 256
(declare-const len_promoted (_ BitVec 64))
(assert (= len_promoted ((_ sign_extend 32) len)))   ; sign-extend khi promote
(assert (bvugt len_promoted buf_size))   ; condition của if
(assert (bvslt len #x00000000))   ; len thực ra âm
(check-sat)
```

Solver trả sat với len = $-1$. Branching không trigger, bug ở memcpy không check signed.

### Bitvector encoding cho phép phức tạp

Một số phép C trông đơn giản nhưng encoding tinh tế:

**Division by zero**: `bvudiv` và `bvsdiv` không định nghĩa khi chia 0. SMT-LIB chuẩn 2.6 trả về `(_ bv0 N)` hoặc all-ones. Tool BMC thường thêm assertion `denominator != 0` ngay trước phép chia.

**Shift count overflow**: `bvshl x n` với `n >= 32` (cho BitVec 32) là Undefined Behavior trong C. BMC thường model bằng `(ite (bvugt n 31) (_ bv0 32) (bvshl x n))`.

**Sign extension và zero extension**:
- `((_ sign_extend N) x)`: mở rộng x từ kích thước nhỏ sang lớn, giữ dấu (copy MSB).
- `((_ zero_extend N) x)`: mở rộng, fill 0 ở các bit cao. Tương ứng cast `int → long` (sign) và `unsigned → unsigned long` (zero).

## Phần 2: Floating-point với IEEE 754

### IEEE 754: cấu trúc bit của float

Số float 32-bit (`single precision`) trong IEEE 754:

```
| 1 bit | 8 bits   | 23 bits          |
| sign  | exponent | mantissa (fraction) |
```

Công thức:

$$\text{value} = (-1)^{\text{sign}} \times 2^{\text{exponent} - 127} \times (1.\text{mantissa})_2$$

Ví dụ:
- $1.0 = 0$ $|$ $01111111$ $|$ $00000000000000000000000$ = $0x3F800000$.
- $-2.0 = 1$ $|$ $10000000$ $|$ $00000000000000000000000$ = $0xC0000000$.

Có các giá trị đặc biệt:

| Giá trị | Sign | Exponent | Mantissa |
|---|---|---|---|
| $+0$ | 0 | 0 | 0 |
| $-0$ | 1 | 0 | 0 |
| $+\infty$ | 0 | 255 | 0 |
| $-\infty$ | 1 | 255 | 0 |
| NaN | any | 255 | $\neq 0$ |
| Denormalized (rất nhỏ) | any | 0 | $\neq 0$ |

Double precision (`double`, 64-bit): 1 sign + 11 exponent + 52 mantissa.

### Vì sao float khó verify?

Float có nhiều property khó tin:
- **$0.1 + 0.2 \neq 0.3$**: Vì $0.1$ và $0.2$ không biểu diễn chính xác trong nhị phân (chu kỳ vô hạn). Kết quả là $0.30000000000000004$.
- **NaN $\neq$ NaN**: Phép so sánh `nan == nan` trả về false. Đây là rule của IEEE 754.
- **Rounding mode** ảnh hưởng kết quả: $(a + b) + c$ có thể khác $a + (b + c)$ vì rounding tích lũy.
- **Subnormal numbers** (gần 0) có precision giảm dần.

Mọi sự lạ này phải được encode chính xác trong SMT để verifier đúng.

### Theory FP trong SMT-LIB

```scheme
(declare-const x (_ FloatingPoint 8 24))   ; single precision
(declare-const y (_ FloatingPoint 11 53))   ; double precision

; Phép: cộng với rounding mode
(assert (= z (fp.add roundNearestTiesToEven x y)))

; Predicate
(assert (fp.isNaN x))
(assert (fp.isInfinite y))
(assert (fp.eq x y))   ; equality theo IEEE 754 (NaN != NaN!)
```

Các rounding mode:
- `roundNearestTiesToEven` (default, IEEE 754 "round half to even").
- `roundNearestTiesToAway`.
- `roundTowardPositive`.
- `roundTowardNegative`.
- `roundTowardZero` (truncate).

### Ví dụ verify: float associativity

Code C:

```c
float assoc(float a, float b, float c) {
    float r1 = (a + b) + c;
    float r2 = a + (b + c);
    assert(r1 == r2);   // tin rằng cộng có tính chất kết hợp
    return r1;
}
```

Trong toán, $(a + b) + c = a + (b + c)$ luôn đúng. Trong float, **không**.

Encode SMT:

```scheme
(set-logic QF_FP)
(declare-const a (_ FloatingPoint 8 24))
(declare-const b (_ FloatingPoint 8 24))
(declare-const c (_ FloatingPoint 8 24))
(declare-const r1 (_ FloatingPoint 8 24))
(declare-const r2 (_ FloatingPoint 8 24))
(assert (= r1 (fp.add roundNearestTiesToEven (fp.add roundNearestTiesToEven a b) c)))
(assert (= r2 (fp.add roundNearestTiesToEven a (fp.add roundNearestTiesToEven b c))))
(assert (not (fp.eq r1 r2)))
(check-sat)
(get-model)
```

Solver trả `sat` với counterexample như $a = 10^{20}, b = -10^{20}, c = 1$:
- $(a + b) + c = 0 + 1 = 1$.
- $a + (b + c) = 10^{20} + (-10^{20} + 1) = 10^{20} + (-10^{20}) = 0$ (vì 1 quá nhỏ để ảnh hưởng tới $10^{20}$).

Hai kết quả khác nhau, dù toán học nói chúng bằng nhau.

### Casts giữa float và int

Phép `float f = (float) i;` (int → float) và `int i = (int) f;` (float → int) cần encoding tinh tế:

```scheme
; int → float
(declare-const i (_ BitVec 32))
(declare-const f (_ FloatingPoint 8 24))
(assert (= f ((_ to_fp 8 24) roundNearestTiesToEven i)))

; float → int (truncate toward zero)
(declare-const g (_ FloatingPoint 8 24))
(declare-const j (_ BitVec 32))
(assert (= j ((_ fp.to_sbv 32) roundTowardZero g)))
```

Cast float → int có thể tạo Undefined Behavior nếu float quá lớn (vượt INT_MAX) hoặc là NaN. C standard không định nghĩa kết quả. BMC tool thường thêm assertion để báo lỗi.

## Tại sao tool BMC mặc định không dùng FP?

Float verification rất chậm vì:

1. FP theory phức tạp, decision procedure không đơn giản như LIA.
2. Eager bit-blasting cần ~40,000 gate cho một phép `fp.add` single precision.
3. Nhiều bug FP nằm ở subnormal hay rounding biên, cần khám phá kỹ.

Vì thế CBMC, ESBMC mặc định **tắt** FP support trừ khi user bật `--floatbv`. Khi tắt, tool model float như uninterpreted function: chỉ check khái niệm "không chia cho 0" mà không check chính xác giá trị.

Nếu bạn verify code numeric (engine physics, ML inference), bật FP support. Nếu chỉ verify control flow của code Web/CRUD, không cần.

## Tóm tắt

- **Bitvector** là dãy bit có kích thước cố định. Phép có hai họ signed/unsigned.
- Encode kiểu C: `int` → 32-bit BV, `long` → 64-bit BV, `unsigned X` → BV cùng size dùng phép unsigned.
- BMC + BV bắt được integer overflow, sign/unsigned confusion, shift overflow.
- **FP theory** model IEEE 754 chính xác, kể cả NaN, Inf, denormalized, rounding mode.
- FP verification chậm; chỉ bật khi code numeric.

## Mini-quiz

<details>
<summary>Q1. Vì sao theory Int (toán học) cho kết quả khác BitVec 32-bit cho cùng chương trình C?</summary>

Theory Int model số nguyên toán học vô hạn dải. Theory BitVec 32-bit model số trong dải $[-2^{31}, 2^{31}-1]$ (signed) hoặc $[0, 2^{32}-1]$ (unsigned), với wrap-around khi tràn.

Code C dùng số 32-bit, có wrap-around. Verify với Int **bỏ qua** mọi bug overflow. Verify với BV mới bắt được.

Ví dụ `int x = INT_MAX; x = x + 1; assert(x > 0);`:
- Int: $x = 2^{31}-1+1 = 2^{31}$, $> 0$, assert pass.
- BV: $x$ wrap về $-2^{31}$, $< 0$, assert fail.

BMC tool mặc định dùng BV cho C để khớp semantics thực.
</details>

<details>
<summary>Q2. Vì sao `nan == nan` trả về false trong IEEE 754?</summary>

Theo IEEE 754, NaN biểu diễn "kết quả không xác định" (ví dụ $0/0$, $\sqrt{-1}$ với real, $\log(-1)$). Hai NaN có thể đến từ hai phép khác nhau, mang ý nghĩa khác nhau. Tiêu chuẩn quyết định **mọi phép so sánh có NaN đều trả false**, bao gồm `nan == nan`.

Hệ quả thực dụng: để check `x` có phải NaN không, không dùng `x == nan`. Dùng `isnan(x)` hoặc tự check `x != x` (chỉ NaN cho true).

Trong SMT theory FP, `(fp.eq x y)` là IEEE 754 equality (NaN != NaN). Nếu muốn "bitwise equality" (xem hai bit pattern có giống không, kể cả NaN), dùng `(= x y)` direct.
</details>

<details>
<summary>Q3. Phân biệt `bvslt` và `bvult`. Cho ví dụ khi chúng cho kết quả khác nhau.</summary>

- `bvslt`: signed less than. Diễn giải bitvector là số two's complement.
- `bvult`: unsigned less than. Diễn giải bitvector là số nguyên không âm.

Ví dụ với BitVec 8-bit, $x = 0xFF$, $y = 0x01$:
- `bvslt x y`: signed, $x = -1, y = 1$. $-1 < 1$, **true**.
- `bvult x y`: unsigned, $x = 255, y = 1$. $255 < 1$, **false**.

Cùng bit pattern, kết quả ngược nhau. Đây là nguồn của nhiều bug "signed/unsigned confusion" trong C. Encoding đúng giúp BMC phát hiện ra.
</details>

---

**Tiếp theo**: [3.7 Encoding pointers và memory](./07-encoding-pointers-and-memory)

---
id: glossary
title: Thuật ngữ (Anh-Việt)
sidebar_position: 2
description: Bảng tra cứu thuật ngữ chuyên ngành Anh-Việt cho toàn bộ tài liệu.
---

# Thuật ngữ Anh-Việt

> **Hướng dẫn**: dùng `Ctrl+F` (Windows/Linux) hoặc `Cmd+F` (macOS) để tìm nhanh thuật ngữ. Mỗi mục có dạng *Tiếng Anh (tiếng Việt nếu có): giải nghĩa ngắn.*

## A

- **Abstract Interpretation** (diễn giải trừu tượng): kỹ thuật static analysis tính over-approximation của state space bằng abstract domain (interval, polyhedra, octagon).
- **Access Control** (kiểm soát truy cập): cơ chế quyết định ai được làm gì với resource nào.
- **AddressSanitizer (ASan)**: runtime tool của LLVM/GCC bắt memory bug (buffer overflow, UAF) bằng cách thêm shadow memory + redzone.
- **AES (Advanced Encryption Standard)**: thuật toán symmetric encryption tiêu chuẩn, block 128-bit, key 128/192/256-bit.
- **AFL (American Fuzzy Lop)**: fuzzer coverage-guided mutation-based, foundational tool của modern fuzzing.
- **AML (Anti-Money Laundering)**: chống rửa tiền, áp dụng cho fintech.
- **ASLR (Address Space Layout Randomization)**: OS feature randomize địa chỉ stack/heap/library để chống ROP.
- **Assertion** (khẳng định): câu kiểm tra runtime hoặc spec, ví dụ `assert(x > 0)`.
- **Atomic Operation** (thao tác nguyên tử): không thể bị chia cắt bởi thread switch.
- **ATT&CK (MITRE)**: knowledge base liệt kê attack tactic và technique đã quan sát thực tế.
- **Authenticity** (xác thực): tính chất danh tính người gửi đúng như tuyên bố.
- **Authentication**: xác minh "bạn là ai".
- **Authorization** (cấp quyền): quyết định "bạn được làm gì", sau khi đã authentication.
- **Availability** (sẵn sàng): tính chất hệ thống trả lời khi được yêu cầu.

## B

- **Backtracking** (quay lui): trong SAT solver, undo decision khi gặp conflict; trong regex engine có thể gây ReDoS.
- **bcrypt**: password hashing function dựa trên Blowfish, có work factor để slow brute force.
- **Bit-blasting**: kỹ thuật dịch bitvector formula thành SAT formula tương đương.
- **BMC (Bounded Model Checking)**: kiểm chứng có giới hạn depth k, encode chương trình + property thành công thức SMT.
- **Branch Coverage** (decision coverage): tiêu chí test mỗi nhánh if/else được chọn cả true và false.
- **Buffer Overflow** (tràn bộ đệm): ghi vượt biên mảng/buffer, có thể đè memory kế bên.
- **Büchi Automaton**: finite-state automaton trên infinite word, dùng để check LTL property.

## C

- **CBA (Context-Bounded Analysis)**: giới hạn số context switch trong concurrency verification để tránh state explosion.
- **CBMC**: Bounded model checker cho C/C++, sản phẩm của Diffblue/CPROVER.
- **CDCL (Conflict-Driven Clause Learning)**: cải tiến DPLL, học clause từ conflict và non-chronological backjumping.
- **Certificate (cert)**: tài liệu signed chứng nhận public key thuộc về một danh tính.
- **CFG (Control Flow Graph)** (đồ thị luồng điều khiển): biểu diễn các path khả dĩ qua chương trình.
- **CFI (Control Flow Integrity)**: kỹ thuật chỉ cho phép control flow đi tới đích đã định nghĩa lúc compile.
- **CIA Triad**: bộ ba Confidentiality, Integrity, Availability, ba thuộc tính gốc của security.
- **Code Coverage**: tỷ lệ code đã được test chạy qua.
- **Compile-time vs Run-time**: compile-time check thực hiện khi build, run-time check khi chạy.
- **Concurrency** (đồng thời): chương trình có nhiều thread/process chạy song song.
- **Concolic Execution**: viết tắt **conc**rete + symb**olic**, kết hợp chạy concrete với track symbolic constraint.
- **Confidentiality** (bí mật): thông tin chỉ lộ cho người được phép.
- **Counterexample** (phản ví dụ): trace cụ thể vi phạm property, output của BMC khi tìm bug.
- **Coverage-guided fuzzing**: fuzzer dùng coverage feedback để guide mutation, ví dụ AFL.
- **CSP (Content Security Policy)**: HTTP header browser dùng để chặn inline script và restrict origin.
- **CSRF (Cross-Site Request Forgery)**: tấn công lừa browser nạn nhân gửi request có authentication.
- **CVE (Common Vulnerabilities and Exposures)**: định danh chuẩn cho lỗ hổng đã được công bố.
- **CVSS (Common Vulnerability Scoring System)**: thang điểm 0-10 đánh giá độ nghiêm trọng CVE.
- **CWE (Common Weakness Enumeration)**: phân loại các lớp lỗ hổng phổ biến, parent của CVE.

## D

- **DAST (Dynamic Application Security Testing)**: scan ứng dụng đang chạy bằng fuzzing/pen testing kiểu black-box.
- **Data Race**: hai thread truy cập cùng biến chia sẻ, ít nhất một write, không sync.
- **Decision Procedure**: thuật toán quyết định cho một theory cụ thể trong SMT.
- **DEP / NX (Data Execution Prevention)**: CPU + OS feature đánh dấu vùng data không thực thi.
- **DPLL**: thuật toán Davis-Putnam-Logemann-Loveland cho SAT, dùng unit propagation + branching.
- **DPLL(T)**: kiến trúc Lazy SMT, SAT solver phối hợp với theory solver.
- **DPOR (Dynamic Partial Order Reduction)**: kỹ thuật optimal cho concurrency exploration.
- **DREAD**: thang đo risk (Damage, Reproducibility, Exploitability, Affected users, Discoverability).
- **DSE (Dynamic Symbolic Execution)**: tương đương concolic execution.
- **DSL (Domain-Specific Language)**: ngôn ngữ chuyên biệt cho một domain.

## E

- **eBPF**: technology Linux modern cho probe-based monitoring, JIT compile script.
- **ECDSA / Ed25519**: thuật toán chữ ký số dựa trên elliptic curve.
- **Encoding** (mã hoá): dịch chương trình thành công thức logic cho BMC.
- **ESBMC**: Bounded model checker cho C/C++, có support concurrency mạnh hơn CBMC.
- **EUF (Equality and Uninterpreted Functions)**: SMT theory đơn giản nhất.

## F

- **Forkserver**: kỹ thuật AFL: target start một lần, mỗi test case fork một child.
- **Format String Attack**: tấn công qua `printf(user_input)`, attacker dùng `%n` ghi memory.
- **FP Theory (Floating-Point)**: SMT theory cho IEEE 754 numbers.
- **Fuzzing**: kỹ thuật sinh input tự động để tìm crash.

## G

- **GDPR**: regulation EU về data protection, ảnh hưởng global.
- **Grammar-based Fuzzing**: fuzzer sinh input theo grammar formal (ví dụ JSON, SQL).

## H

- **Hash function**: ánh xạ input bất kỳ → output cố định, irreversible.
- **HMAC (Hash-based MAC)**: MAC dựa trên hash function.
- **HSM (Hardware Security Module)**: thiết bị phần cứng chuyên dùng để store key và thực hiện crypto operation an toàn.
- **HSTS (HTTP Strict Transport Security)**: header bắt browser luôn dùng HTTPS với domain này.
- **HttpOnly Cookie**: cookie không thể đọc từ JavaScript, chống XSS lấy session.

## I

- **IDOR (Insecure Direct Object Reference)**: lỗ hổng access control, user A access object của user B qua đổi ID.
- **Idempotency**: thực hiện cùng action nhiều lần cho cùng kết quả như 1 lần.
- **Implementation Vulnerability**: lỗi trong code, design đúng.
- **Information Disclosure**: rò rỉ thông tin (CIA's Confidentiality).
- **Insider Threat**: nhân viên hoặc người có access hợp pháp gây hại.
- **Integer Overflow** (tràn dải số nguyên): kết quả arithmetic vượt dải, wrap-around.
- **Integrity** (toàn vẹn): dữ liệu không bị sửa trái phép.
- **Invariant** (bất biến): điều kiện luôn đúng tại một điểm chương trình.
- **IOC (Indicator of Compromise)**: dấu hiệu cho biết đã bị tấn công.

## J

- **JWT (JSON Web Token)**: token format cho authentication, có claim + signature.

## K

- **k-induction**: kỹ thuật mở rộng BMC để chứng minh cho mọi depth, nếu property hold ở $k$ thì hold ở $k+1$.
- **KEK (Key Encryption Key)**: key dùng để encrypt key khác.
- **KISS (Keep It Sequential, Stupid)**: sequentialization scheme của Qadeer-Wu cho CBA.
- **KLEE**: symbolic execution tool nổi tiếng cho LLVM IR.
- **KYC (Know Your Customer)**: quy trình verify danh tính user khi onboard (fintech).

## L

- **Lazy Interleaving**: chỉ fork thread tại shared memory access, không tại local instruction.
- **LIA (Linear Integer Arithmetic)**: SMT theory cho số nguyên với phép +, -, *const.
- **libFuzzer**: in-process fuzzer của LLVM/Clang.
- **Liveness Property**: tính chất "điều tốt rồi sẽ xảy ra", counterexample vô hạn.
- **LR Sequentialization**: scheme của Lal-Reps, linh hoạt hơn KISS.
- **LRA (Linear Real Arithmetic)**: SMT theory cho số thực tuyến tính.
- **LTL (Linear Temporal Logic)**: logic thời gian dùng để diễn đạt property.

## M

- **MAC (Message Authentication Code)**: tag xác thực thông điệp, dùng symmetric key.
- **Malloc / Free**: cấp phát và giải phóng heap memory trong C.
- **Mazurkiewicz Trace**: equivalence class các schedule sau khi swap concurrent event.
- **MC/DC (Modified Condition/Decision Coverage)**: tiêu chí test mạnh, mỗi sub-condition độc lập ảnh hưởng outcome.
- **MD5**: hash function đã bị broken (collision attack từ 2004).
- **Memory Leak**: cấp phát memory mà không free.
- **MFA (Multi-Factor Authentication)**: xác thực dùng nhiều yếu tố (mật khẩu + OTP + sinh trắc).
- **Model Checking**: kiểm chứng property của finite-state system tự động.
- **mTLS (Mutual TLS)**: cả client và server authenticate nhau bằng cert.
- **Mutation-based Fuzzing**: fuzzer mutate seed corpus thành input mới.

## N

- **NaN (Not a Number)**: special float value, `nan != nan` theo IEEE 754.
- **Nelson-Oppen Combination**: thuật toán ghép decision procedure của nhiều theory.
- **NIST CSF (Cybersecurity Framework)**: khung work của NIST cho enterprise.
- **Non-repudiation** (chống chối bỏ): người gửi không thể chối là đã gửi.

## O

- **OAuth 2.0**: protocol authorization standard cho web.
- **OIDC (OpenID Connect)**: layer authentication on top of OAuth 2.0.
- **OTA (Over-the-Air) Update**: cơ chế update firmware IoT qua network.
- **OWASP (Open Web Application Security Project)**: cộng đồng phi lợi nhuận about web security.
- **OWASP Top 10**: danh sách 10 lỗ hổng web phổ biến nhất, update mỗi 3-4 năm.

## P

- **PAM (Privileged Access Management)**: tool quản lý quyền admin trong enterprise.
- **Parameterized Query**: cách dùng SQL với placeholder thay vì string concat, chống SQLi.
- **Partial-Order Reduction (POR)**: kỹ thuật giảm số schedule khám phá trong concurrency verification.
- **Path Coverage**: tiêu chí test mỗi path qua chương trình.
- **PBKDF2**: password-based key derivation function, có iteration count.
- **PCI-DSS**: standard cho mọi entity xử lý payment card.
- **Pen Test (Penetration Testing)**: manual security testing đóng vai attacker.
- **PKI (Public Key Infrastructure)**: infrastructure quản lý cert.
- **Pointer**: biến chứa địa chỉ memory trong C.
- **Property**: mệnh đề mà chương trình phải thoả, target của verification.

## R

- **Race Condition**: lỗi do thứ tự thực thi giữa thread.
- **Ransomware**: malware encrypt data rồi đòi tiền chuộc.
- **ReDoS (Regular Expression DoS)**: tấn công regex có catastrophic backtracking.
- **Reflected XSS**: XSS qua URL/form, server echo lại.
- **Round-trip Encoding**: encode rồi decode phải cho kết quả gốc.
- **RSA**: thuật toán mã hoá bất đối xứng, dựa trên integer factorization.

## S

- **Safety Property**: "điều xấu không xảy ra", counterexample hữu hạn.
- **SAGE**: white-box fuzzer của Microsoft Research.
- **SAMM (OWASP)**: maturity model cho secure SDLC.
- **SAML**: protocol federation identity, dùng nhiều trong enterprise.
- **SameSite Cookie**: thuộc tính cookie giới hạn cross-site request.
- **Sandbox**: isolation environment chạy untrusted code.
- **Sanitization**: làm sạch input không tin cậy.
- **Sanitizer** (compiler): runtime tool bắt UB (ASan, MSan, UBSan, TSan).
- **SAST (Static Application Security Testing)**: scan code tĩnh tìm bug.
- **SAT (Satisfiability)**: bài toán thoả được boolean formula, NP-complete.
- **Schedule Recording**: kỹ thuật lưu schedule đã thử để tránh khám phá lại.
- **Secure Boot**: chain of trust từ Boot ROM, verify firmware trước khi load.
- **Secure by Default**: thiết kế mặc định an toàn nhất.
- **Secrets Management**: hệ thống quản lý secret (API key, password, cert), ví dụ Vault, AWS Secrets Manager.
- **SHA-256**: hash function 256-bit từ SHA-2 family, hiện tại an toàn.
- **Sequentialization**: dịch multi-thread program thành sequential program tương đương.
- **Session Fixation**: attack ép user dùng session ID do attacker tạo.
- **Side-channel Attack**: trích key qua timing, power, EM emission.
- **SipHash**: keyed hash function chống hash collision DoS, dùng trong Python 3.4+.
- **SMT (Satisfiability Modulo Theories)**: mở rộng SAT với theory chuyên biệt.
- **SOAR (Security Orchestration, Automation and Response)**: tool tự động hoá incident response.
- **SOC 2**: standard compliance cho SaaS, audit của AICPA.
- **Soundness** (đáng tin): tool nói "OK" thì thực sự OK, không miss bug.
- **SQL Injection (SQLi)**: tấn công inject SQL qua input.
- **SSA (Static Single Assignment)**: dạng IR mỗi biến gán đúng 1 lần.
- **SSRF (Server-Side Request Forgery)**: lừa server gửi request tới target attacker chọn.
- **Stack Canary**: giá trị random chèn trước return address, check trước ret.
- **STRIDE**: framework threat modeling (Spoofing, Tampering, Repudiation, Info disclosure, DoS, Elevation).
- **Stored XSS**: XSS lưu trên server, tự kích hoạt mọi user.
- **Symbolic Execution**: chạy chương trình với symbolic input, track path constraint.

## T

- **TCP/TLS/HTTPS**: layer protocol stack cho secure communication.
- **Theorem Proving**: chứng minh property bằng prover (Coq, Isabelle, Lean).
- **ThreadSanitizer (TSan)**: runtime tool bắt data race.
- **Threat Model**: liệt kê threat tiềm năng và mitigation.
- **TLS 1.2/1.3**: Transport Layer Security, version 1.3 hiện tại.
- **TOCTOU (Time Of Check, Time Of Use)**: race condition giữa check và use.
- **Tokenization**: thay PAN bằng token có scope hẹp hơn (fintech).
- **TSO (Total Store Order)**: memory model của x86, cho phép store-load reordering.

## U

- **UAF (Use After Free)**: dereference pointer sau khi free.
- **UB (Undefined Behavior)**: hành vi không định nghĩa theo chuẩn C/C++.
- **UBSan (UndefinedBehaviorSanitizer)**: tool runtime bắt UB.
- **UEBA (User and Entity Behavior Analytics)**: ML-based monitoring detect anomaly.
- **Unit Propagation**: kỹ thuật cốt lõi của DPLL, ép giá trị từ unit clause.
- **UNSAT (Unsatisfiable)**: công thức không có model thoả.

## V

- **V-model**: SDLC model truyền thống với V&V đối xứng cho từng phase.
- **V&V (Verification and Validation)**: kiểm chứng và xác nhận, hai khái niệm khác nhau.
- **Verification**: "Có làm đúng spec không?", khác Validation.
- **VSIDS (Variable State Independent Decaying Sum)**: decision heuristic của CDCL SAT solver.
- **Vulnerability** (lỗ hổng): bug có thể bị khai thác để vi phạm security property.

## W

- **WAF (Web Application Firewall)**: firewall ở layer 7, chặn payload SQLi/XSS biết trước.
- **White-box Fuzzing**: fuzzer dùng symbolic execution để guide input generation.

## X

- **XSS (Cross-Site Scripting)**: tấn công inject script vào HTML.
- **XXE (XML External Entity)**: tấn công qua entity ngoại trong XML.

## Z

- **Zero Trust**: kiến trúc không tin tưởng dựa vào network location, verify mọi request.
- **ZKP (Zero-Knowledge Proof)**: proof system chứng minh biết secret mà không tiết lộ.

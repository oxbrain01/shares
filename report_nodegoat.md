# Báo Cáo Tổng Hợp NodeGoat
## Đối chiếu Tutorial, SAST và DAST

## 1) Kết luận nhanh

| Hạng mục | Kết quả |
|---|---:|
| Chủ đề tutorial | 12 |
| SAST findings (`report_sast.md`) | 96 |
| DAST findings (`report_dast.md`) | 6 |
| Tổng finding ghi nhận (chưa dedup) | 102 |

**Thông điệp chính**
- SAST cho độ bao phủ rộng trên mã nguồn và dependencies.
- DAST xác nhận các lỗ hổng runtime/business logic quan trọng.
- Tổng 102 là số finding cộng gộp, có thể có trùng lặp giữa SAST và DAST.

## 2) Mục tiêu và dữ liệu đầu vào

### Mục tiêu
- Đánh giá độ khớp giữa khung lỗ hổng trong tutorial và các lỗ hổng thực tế bị phát hiện.
- Tổng hợp kết quả SAST + DAST để phục vụ quyết định remediation.

### Nguồn dữ liệu
- `report_toturial.md`: 12 chủ đề huấn luyện (A1-A10, ReDoS, SSRF), 5 mục chi tiết.
- `report_sast.md`: 96 findings (27 Snyk Code + 69 Snyk SCA).
- `report_dast.md`: 6 findings đã xác minh trong test authenticated.

## 3) Độ bao phủ của SAST theo Tutorial

### 3.1 Theo 12 chủ đề

| Chỉ số | Giá trị |
|---|---:|
| Khớp trực tiếp | 9/12 (75.0%) |
| Khớp gián tiếp | 1/12 (8.3%) |
| Chưa thấy | 2/12 (16.7%) |
| Bao phủ tổng (trực tiếp + gián tiếp) | 10/12 (83.3%) |

### 3.2 Theo 5 mục chi tiết (panel A1/A2)

| Chỉ số | Giá trị |
|---|---:|
| Khớp trực tiếp | 1/5 (20%) |
| Khớp gián tiếp | 1/5 (20%) |
| Chưa thấy | 3/5 (60%) |
| Bao phủ tổng | 2/5 (40%) |

### 3.3 Các chủ đề SAST chưa bao phủ tốt

| Chủ đề | Trạng thái |
|---|---|
| A4 IDOR | Chưa thấy trực tiếp trong SAST |
| A7 Missing Function Level Access Control | Chưa thấy trực tiếp trong SAST |
| A2 brute-force/session behavior | Chỉ có bằng chứng gián tiếp |

## 4) Kết quả DAST (tóm tắt quan trọng)

### 4.1 Số lượng và phân loại

| Nhóm lỗ hổng DAST | Số lượng |
|---|---:|
| Access Control (BOLA/IDOR, Mass Assignment) | 2 |
| Injection (NoSQL Injection) | 1 |
| Stored XSS | 1 |
| Open Redirect | 1 |
| Security Misconfiguration (headers/cookie) | 1 |
| **Tổng** | **6** |

### 4.2 Finding tiêu biểu đã PoC
- Open Redirect: `/learn`
- BOLA/IDOR: `/benefits`
- Stored XSS: `/memos`
- Mass Assignment: `/profile`
- NoSQL Injection: `/allocations/:id?threshold=...`
- Misconfiguration: session cookie/security headers

## 5) Tổng hợp SAST + DAST

### 5.1 Bảng tổng hợp

| Nguồn | Số finding | Giá trị chính |
|---|---:|---|
| SAST | 96 | Mạnh về static code + dependency CVE |
| DAST | 6 | Mạnh về runtime/business logic xác minh thực tế |
| **Tổng cộng (chưa dedup)** | **102** | Có thể có overlap finding |

### 5.2 Lưu ý dedup
- Các lỗ hổng có khả năng trùng lặp giữa 2 nguồn: Open Redirect, XSS, NoSQL Injection, misconfiguration.
- Vì vậy, số finding unique cuối cùng sẽ **<= 102**.

## 6) Đánh giá chuyên môn

### Điểm mạnh hiện tại
- Bao phủ cao trên nhóm lỗ hổng kỹ thuật phổ biến (Injection, SSRF, XSS, CSRF, dependency risk).
- Có bằng chứng runtime rõ ràng từ DAST cho các flow quan trọng.

### Khoảng trống cần bổ sung
- Logic authz/authn (IDOR, function-level access control, brute-force scenario) cần test hành vi sâu hơn.
- Cần kết hợp test tự động và thủ công để giảm false negative.

## 7) Kế hoạch hành động đề xuất

### Ưu tiên 0-7 ngày
1. Khắc phục 5 finding DAST đã PoC (Open Redirect, BOLA/IDOR, Stored XSS, Mass Assignment, NoSQL Injection).
2. Hardening cookie + security headers (`Secure`, HSTS, CSP, X-Frame-Options, X-Content-Type-Options).

### Ưu tiên 1-2 tuần
1. Deduplicate SAST + DAST theo `endpoint + root cause`.
2. Tạo backlog remediation có owner, ETA và retest criteria.

### Ưu tiên 2-4 tuần
1. Duy trì pipeline SAST + DAST authenticated trong CI/CD.
2. Bổ sung test kịch bản cho A4/A7/A2 (authorization/authentication behavior).

## 8) Accuracy

### 8.0 Accuracy Matrix (style tham khảo)

| Category | SAST | DAST | Combined |
| --- | --- | --- | --- |
| Tutorial Topics (coverage) | 10/12 83.3% | 6/12 50.0% | 11/12 91.7% |
| Tutorial Topics (direct match) | 9/12 75.0% | 6/12 50.0% | 10/12 83.3% |
| Detailed Items (coverage) | 2/5 40.0% | 2/5 40.0% | 3/5 60.0% |
| Detailed Items (direct match) | 1/5 20.0% | 2/5 40.0% | 3/5 60.0% |
| **Total Findings (gross)** | **96/102 94.1%** | **6/102 5.9%** | **102/102 100%** |

> Cách đọc giống mẫu benchmark: mỗi ô theo định dạng `x/y %` để so sánh trực quan giữa các phương pháp.

### 8.1 Accuracy theo Tutorial Topics (12 chủ đề)

| Category | SAST | DAST | Combined |
| --- | --- | --- | --- |
| Tutorial topic coverage | 10/12 83.3% | 6/12 50.0% | 11/12 91.7% |
| Direct match only | 9/12 75.0% | 6/12 50.0% | 10/12 83.3% |
| Not covered | 2/12 16.7% | 6/12 50.0% | 1/12 8.3% |

### 8.2 Accuracy theo Detailed Tutorial Items (5 mục chi tiết)

| Category | SAST | DAST | Combined |
| --- | --- | --- | --- |
| Detailed item coverage | 2/5 40.0% | 2/5 40.0% | 3/5 60.0% |
| Direct match only | 1/5 20.0% | 2/5 40.0% | 3/5 60.0% |
| Not covered | 3/5 60.0% | 3/5 60.0% | 2/5 40.0% |

### 8.3 Accuracy theo quy trình phát hiện

| Category | Kết quả |
| --- | --- |
| SAST findings | 96 |
| DAST findings | 6 |
| Combined findings (gross, chưa dedup) | 102 |
| Combined unique findings | `<= 102` |

### 8.4 Overall contribution (SAST vs DAST)

| Chỉ số | Giá trị |
|---|---:|
| Tổng finding gross | 102 |
| Tỷ trọng từ SAST | 96/102 = 94.1% |
| Tỷ trọng từ DAST | 6/102 = 5.9% |

> Lưu ý: Đây là tỷ trọng theo tổng finding ghi nhận (gross), chưa loại trùng.
> Quy ước map detailed items: DAST được tính match trực tiếp cho các mục có bằng chứng PoC hoặc bằng chứng xác thực session/misconfig tương ứng.

### 8.5 Accuracy chi tiết theo từng loại lỗ hổng

| Vulnerability Category | Thuộc nhóm OWASP chính | SAST (số finding) | DAST (số finding) | Combined (gross) | Tỷ lệ SAST | Tỷ lệ DAST | Mức độ chính | Trạng thái |
| --- | --- | ---: | ---: | ---: | ---: | ---: | --- | --- |
| Code Injection (`eval`) | A03 Injection | 3 | 0 | 3 | 100% | 0% | High | Đã phát hiện qua SAST |
| NoSQL Injection | A03 Injection | 0 | 1 | 1 | 0% | 100% | High | Đã PoC qua DAST |
| SSRF | A10 SSRF | 1 | 0 | 1 | 100% | 0% | High | Đã phát hiện qua SAST |
| ReDoS | A03/A06 (input/resource handling) | 1 (code) + nhiều SCA | 0 | >= 2 | ~100% | ~0% | High/Medium | Đã phát hiện mạnh qua SAST |
| Open Redirect | A10 Redirects/SSRF-liên quan | 1 | 1 | 2 | 50% | 50% | Medium/High | Có overlap SAST-DAST |
| Stored XSS | A03 Injection (XSS) | 0 (stored XSS trực tiếp) | 1 | 1 | 0% | 100% | High | Đã PoC qua DAST |
| XSS (config/dependency) | A03 Injection | 1 (config) + nhiều SCA | 0 | >= 2 | ~100% | ~0% | Medium/High | Đã phát hiện qua SAST |
| BOLA / IDOR | A01 Broken Access Control | 0 | 1 | 1 | 0% | 100% | High | Đã PoC qua DAST |
| Missing Function Level Access Control | A01 Broken Access Control | 0 | 0 (chưa xác nhận riêng) | 0 | N/A | N/A | N/A | Chưa bao phủ đầy đủ |
| Mass Assignment | A01 Broken Access Control / API6 | 0 | 1 | 1 | 0% | 100% | High | Đã PoC qua DAST |
| CSRF Protection Disabled | A08 CSRF | 1 | 0 | 1 | 100% | 0% | Medium | Đã phát hiện qua SAST |
| Session/Cookie Security Misconfig | A05 Security Misconfiguration | 2 (cookie secure + csrf context) | 1 | 3 | 66.7% | 33.3% | Medium | Có bằng chứng từ cả SAST và DAST |
| Security Headers Missing | A05 Security Misconfiguration | 0 | 1 | 1 | 0% | 100% | Medium | Đã phát hiện qua DAST |
| Sensitive Data Exposure (hardcoded secrets/passwords) | A02 Cryptographic Failures / A07 Identification & Auth Failures | 6 | 0 | 6 | 100% | 0% | High/Medium/Low | Đã phát hiện qua SAST |
| Vulnerable Components (CVE dependencies) | A06 Vulnerable and Outdated Components | 69 | 0 | 69 | 100% | 0% | High/Medium/Low | Đã phát hiện rất mạnh qua SAST/SCA |
| Business Logic Validation flaws | A04 Insecure Design | 0 (không coi là flaw do test pass) | 0 (test âm tính ở `/contributions`) | 0 | N/A | N/A | N/A | Chưa thấy khai thác thành công |

> Công thức tính: `Tỷ lệ SAST = SAST / Combined`, `Tỷ lệ DAST = DAST / Combined` trên từng category; với dòng `>=` thì tỷ lệ để dạng `~` (ước tính).

### 8.6 Tổng hợp theo Category group

| Category Group | Số loại lỗ hổng đã có bằng chứng | Nguồn phát hiện chính |
| --- | ---: | --- |
| Injection family (Code/NoSQL/XSS/ReDoS) | 6 | SAST + DAST |
| Access Control family (BOLA/IDOR, Mass Assignment) | 2 | DAST |
| Security Misconfiguration family | 3 | SAST + DAST |
| Sensitive Data / Secret Management | 1 | SAST |
| Vulnerable Components | 1 | SAST/SCA |

## 9) Kết luận cuối

- **SAST phát hiện 96 lỗ hổng**
- **DAST phát hiện 6 lỗ hổng**
- **Tổng finding ghi nhận: 102 (chưa dedup)**

Kết hợp SAST + DAST là hướng tiếp cận phù hợp nhất cho NodeGoat:  
SAST để bao phủ rộng, DAST để xác minh khai thác thực tế, và dedup để chốt con số unique phục vụ quản trị rủi ro.

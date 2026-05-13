# TP benchmark vs TP Snyk

**Nguồn Snyk:** SARIF đã chấm — [`report_aggregate_snyk_non_rust_bash.md`](report_aggregate_snyk_non_rust_bash.md). **TP benchmark** = mẫu số [`report_benmark.md`](report_benmark.md) (bỏ Rust/Bash). **TP Snyk** = nhãn vuln bắt đúng sau map CSV (không phải số issue UI).

| Khu | TP benchmark | TP Snyk | Snyk / benchmark |
|-----|-------------:|--------:|------------------:|
| go | 675 | 192 | 28,4% |
| php | 625 | 67 | 10,7% |
| ruby | 675 | 20 | 3,0% |
| chains | 250 | 122 | 48,8% |
| adversarial | 64 | 7 | 10,9% |
| vulnerable_apps go | 254 | 33 | 13,0% |
| vulnerable_apps php | 59 | 12 | 20,3% |
| vulnerable_apps ruby | 31 | 2 | 6,5% |
| **Tổng** | **2633** | **455** | **17,3%** |

*Tỷ lệ cột cuối* = TP Snyk ÷ TP benchmark (từng khu). **Tổng 17,3%** ≠ recall trên tập chấm — xem aggregate nếu cần FN/Tests.

**Lưu ý ngắn:** `vulnerable_apps/go` / Snyk **33** chỉ từ app đã có SARIF (vd 1/5). **885** issue (UI) **không** dùng trong bảng này.

**Tham chiếu:** [`measurement_fair.md`](measurement_fair.md) · [`report_code_test.md`](report_code_test.md).

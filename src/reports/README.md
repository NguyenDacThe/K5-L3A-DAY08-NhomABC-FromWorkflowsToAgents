# src/reports/

Mọi lệnh dưới đây chạy trong thư mục `src/`, nên file JSON được ghi vào `src/reports/`.

Nhóm **commit** các file do công cụ của lab sinh ra. Đây là bằng chứng nộp bài:

| File | Lệnh tạo | Bắt buộc |
| --- | --- | --- |
| `benchmark.json` | `python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json` | Có |
| `crash.json` | `python -m lab8.crash_test --count 20 --out reports/crash.json` | Có |
| `ablation_workflow_draft.json` | `python -m tests.run_draft_ablation --out reports/ablation_workflow_draft.json` | Bằng chứng research card R3 của nhóm |
| `hidden_selfcheck.json` | benchmark trên `student_drop/` (xem `src/README.md`, mục Tự kiểm trên bộ hidden) | Không |
| `bonus_*.json` | script bonus của nhóm | Chỉ khi làm bonus |

Không sửa tay file JSON. Giảng viên chạy lại đúng các lệnh trên trên máy chấm. Số không khớp thì mục Reproducibility mất điểm. `hidden_grade.json` chỉ giảng viên tạo và bị gitignore.

## Đọc và tái lập bằng chứng của nhóm

- `benchmark.json`: mục `controlled_variables` ghi corpus/seed/giá; `by_shape` tổng hợp metric; `trials` có run_id và reason từng trial. Bản nhóm đủ 4 shape × 14 claim × 5 seed = 280 trial.
- `crash.json`: suite chuẩn 20/20 kiểm host và một kịch bản swarm. Kiểm crash riêng ba shape qua T11/T12 và test nhóm, không suy từ riêng file này.
- `ablation_workflow_draft.json`: hai nhánh workflow draft_on/draft_off, tổng 140 trial, do runner trong `tests/` sinh. Chỉ đổi cờ draft có sẵn; không sửa evaluator, Budget hoặc shape trên đĩa. `comparison` chứa đối chiếu theo cặp; SHA-256 xác định nguồn/config/data được đo.
- Run id của hai nhánh ablation giữ cùng shape/claim/seed. Phân biệt bằng nhãn draft_on/draft_off và thư mục `traces/ablation_workflow_draft/`; trace không commit.
- Report ablation lưu HEAD `f26fa94` tại lúc chạy trước commit, rồi được đưa vào commit `fd7275e`. Đây là provenance lịch sử; giữ nguyên JSON, đối chiếu nội dung bằng SHA-256 thay vì sửa hash theo commit tài liệu mới.

Chi phí là mô phỏng; `latency` là `unavailable`. Các bộ đếm hidden về leak/bypass không có trong benchmark public. Giới hạn instrumentation và quyết định shape tại [ADR](../../report/team/decision_adr.md); nghiên cứu tại [research_card.md](../../report/team/research_card.md).

# Báo cáo cá nhân — 2A202603562_PhanGiaBao

## 1. Thông tin thành viên

- Họ tên: Phan Gia Bảo
- MSSV: 2A202603562
- Email: pgb4work@gmail.com
- GitHub: https://github.com/pgb4work-ai-vinuni

## 2. Phần việc phụ trách

- Vai trò: **workflow + integration** (theo cách chia nhóm 4 người trong `TEAM.md`).
- Shape A `workflow`: `src/lab8/shapes/workflow.py` (TODO CP2 và CP3).
- Test cho workflow: `src/tests/test_workflow_bao.py`.
- Integration: ghép code các shape, giữ cổng host xanh, chạy crash suite và benchmark, kiểm `check_submission` trước khi nộp.

## 3. Đóng góp cụ thể

**Commit chính:** `926aea2ab8c82628a20c41a20c5bb8fb77380e52`: feat(workflow): implement shape A and add workflow tests

**Shape A workflow** là các bước Python cố định, không có vòng lặp model:

1. Đọc claim bằng `get_claim_view` (view allowlist, không `SELECT *`). Không có claim thì trả `failed`.
2. Thiếu `order_id` thì trả `needs_info`. Không đoán mã đơn từ chữ trong ticket.
3. `lookup_order` theo `principal_id`. Không thấy đơn hoặc sai chủ đơn thì trả `escalated`.
4. Tính tiền bằng `compute_compensation` từ order row và policy hiện hành. Quote = 0 thì trả `denied` và không gọi host.
5. Quote > 0 thì gọi `durability.issue_refund(conn, claim_id, quote, crash_at=crash_at)`. Map kết quả: `ok` → `resolved`; có `conflict` → `failed`; còn lại → `denied`, khi đó host đã ghi `policy_denied`.
6. MockLLM chỉ được gọi một lần (`purpose="draft"`) để soạn câu trả lời. Prompt chỉ chứa status.
7. Mọi nhánh return đều đi qua `lab8.shapes.common.finish()` và ghi trace.

**Quyết định kỹ thuật:**

- **Truyền quote đầy đủ vào `issue_refund`.** Host tự trừ phần đơn đã được hoàn bởi claim khác. Nếu shape trừ trước thì host trừ thêm lần nữa và T14 sai (đáng lẽ ledger 120.000 thì thành 90.000). `proposal_amount` luôn là quote đầy đủ.
- **Bỏ qua số ngày trễ ghi trong ticket.** Chỉ dùng cột `delay_days` của order. Ticket là dữ liệu người dùng, không phải lệnh. Vì vậy workflow không có lỗi của swarm ở seed 55 (T02).
- **Không bắt `InjectedCrash`.** Shape ghi trace event `crash` rồi `raise` lại. Việc giữ đúng một refund khi resume là nhờ ledger và idempotency key `refund:{claim_id}`, không phải nhờ checkpoint.
- **Trace chỉ ghi field đã qua allowlist:** các event `claim` (chỉ `found`, `has_order_id`), `lookup_order` (`ORDER_FIELDS`), `quote`, `issue_refund` (`ok`, `reason`, `conflict`), `decision`, `crash`. Cố ý **không** ghi ticket, `principal_id`, claim row thô (có `parent_secret`, `raw_headers`) và order row thô (SĐT, địa chỉ, tên hàng, ghi chú nội bộ).

**Test đã thêm:** `src/tests/test_workflow_bao.py`, 8 test, chạy trong cổng host:

- `test_t02_khong_tin_ticket` (5 seed): ticket ghi trễ 5 ngày, order ghi 2 ngày, phải ra `denied` và không có dòng ledger, kể cả seed 55.
- `test_t14_full_quote`: `proposal_amount` 150.000, ledger 120.000.
- `test_claim_khong_ton_tai`: claim không tồn tại ra `failed`, không ném exception, trace đúng `run_id`.
- `test_trace_khong_lo_secret`: cả 14 claim public, trace và `RunResult` không chứa `parent_secret`, SĐT, địa chỉ, ghi chú nội bộ, tên hàng.

## 4. Bằng chứng

- Commit: `926aea2ab8c82628a20c41a20c5bb8fb77380e52` (link: `https://github.com/NguyenDacThe/K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents/commit/926aea2ab8c82628a20c41a20c5bb8fb77380e52`)
- `python -m pytest -q -m student -o addopts= -k workflow` cho kết quả **62 passed** (T01–T14 × 5 seed, gồm T11 retry và T12 crash/resume).
- `python -m pytest -q tests/test_workflow_bao.py` cho kết quả **8 passed**. Cổng host `python -m pytest -q` xanh.
- `python -m lab8.check_submission` cho kết quả `[ OK ] shape workflow: format đúng, 12/12 claim public pass ở seed 11`.


## 5. Công cụ AI đã dùng

- **Claude** (Anthropic): tóm tắt đề và các tài liệu (README, CHECKPOINTS, business rules, contracts), giải thích host `durability.issue_refund`, gợi ý code cho `workflow.py` và viết test.
- **Cách tự kiểm tra:**
  - Đối chiếu từng nhánh status trong code với bảng output contract trong `docs/contracts.md` và luật trong `docs/business_rules.md`.
  - Chạy ma trận student (62/62), cổng host và `check_submission` trên máy mình.
  - Mở trace `traces/R-workflow-*.json` để xem trace không có ticket, secret hay PII.
  - Thử sửa tạm code (tự trừ phần đã hoàn trước khi gọi host) để thấy test T14 đỏ, rồi sửa lại.
- Không dùng AI để bịa số liệu benchmark, run id hay commit. Số liệu lấy từ output lệnh và file report.

## 6. Tự đánh giá

- Hoàn thành shape A đúng contract: qua đủ 62 test public, bao gồm idempotency, crash và trần hoàn tiền theo đơn. Không lộ secret trong trace.
- Hiểu ranh giới code / model: tiền, approval và ghi ledger do code và host quyết; model chỉ soạn chữ.
- Hạn chế: chỉ test trên bộ public với MockLLM. Chưa tự kiểm trên `student_drop/` (hidden). Phần integration phụ thuộc tiến độ của các shape còn lại.

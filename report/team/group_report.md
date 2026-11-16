# Báo cáo nhóm — Lab 8 LogiX-VN

Tối đa 5 trang ngoài phụ lục. Số liệu lấy từ file trong `src/reports/`, không chép tay từ terminal.

## 1. Thông tin nhóm

- Tên nhóm: NhomABC — lớp L3A, 4 thành viên.
- Thành viên: xem `TEAM.md`
- Commit chốt (trùng `report/team/decision_adr.md`): `fd7275e054c501b10f96bcc2272aa945633e6da0` — phiên bản code và bằng chứng; tài liệu được hoàn thiện ở commit sau.
- Shape chọn: **workflow**. Bài đã ghép vào nhánh nộp `main` qua PR #4, merge commit `3d3dbe7`; tài liệu có thể được cập nhật tiếp mà không đổi commit code đã đo.

## 2. Mục tiêu và cách tiếp cận

Nhóm xử lý claim hoàn phí vận chuyển LogiX-VN bằng ba shape tự triển khai và swarm tham chiếu, chọn kiến trúc ít tự chủ nhất vẫn đúng. Phan Gia Bảo phụ trách workflow/integration; Nguyễn Minh Đạt làm single; Trương Văn Thông làm orchestrator/handoff; Nguyễn Đắc Thể phụ trách kiểm thử tích hợp, evaluation, ablation và tổng hợp tài liệu. Các thành viên dùng Claude, ChatGPT/Codex theo khai báo cá nhân; bằng chứng là output của công cụ thực thi, không phải số liệu do AI dự đoán.

Nguồn sự thật là order có cấu trúc và policy hiện hành tại `as_of=2026-11-15`; ticket là dữ liệu không đáng tin. Tiền do code tính; approval, ownership, trần theo đơn và idempotency do host kiểm tra. Các shape chỉ yêu cầu host ghi qua `durability.issue_refund`, truyền quote đầy đủ. Ví dụ T14: quote 150.000 VND, đã hoàn 30.000 VND trước đó, host ghi phần chênh 120.000 VND.

## 3. Giải pháp

| Shape | Luồng thực thi và ranh giới code/model |
| --- | --- |
| Workflow | Python cố định: `get_claim_view` → `lookup_order` theo principal → `compute_compensation` → host. MockLLM chỉ soạn chữ từ status, không chọn tiền hoặc tool. Thiếu order trả needs_info; không thấy đơn trả escalated; quote 0 trả denied. |
| Single | `run_react(max_steps=8)`, `decide` chọn tool đọc theo observation bằng Python và ghi usage MockLLM vào Budget. Sau vòng lặp, code gọi host với quote đầy đủ. Chuỗi MockLLM trả về không được dùng làm quyết định tool; đây chưa phải bằng chứng về lập kế hoạch của model thật. |
| Orchestrator | `make_handoff` lọc allowlist; investigator chỉ dùng tool đọc để lấy order, policy, lịch sử refund và quote. Orchestrator mới yêu cầu host ghi tiền. Các tool investigator chạy tuần tự trong runtime hiện tại. |

Cả ba dùng `common.finish()` để lấy effect/audit từ ledger và ghi trace mới. Quote dương phải đi qua host kể cả khi approval không hợp lệ để host ghi `policy_denied`; quote 0 không gọi refund. Truyền `crash_at`, để `InjectedCrash` lan ra ngoài; resume trên cùng DB dựa vào ledger thay vì tin checkpoint đơn lẻ. Khóa `refund:{claim_id}` bảo đảm retry không ghi thêm một refund; transaction chứa refund và audit committed.

Workflow trace các event claim, lookup_order qua allowlist, quote, issue_refund, decision và crash khi có fault injection. Không dump ticket, parent context, secret, header, SĐT, địa chỉ, ghi chú nội bộ hoặc tên hàng. Orchestrator chỉ trace handoff đã lọc; investigator không nhận tool ghi. Trace ở `src/traces/`, không commit; evaluator kiểm file mới, đúng run_id và quét các chuỗi cấm theo contract.

## 4. Kết quả

Nguồn: [benchmark.json](../../src/reports/benchmark.json), `by_shape`; 14 claim × seed `11,22,33,44,55` × 4 shape = **280 trial**, mỗi trial một SQLite mới, cùng MockLLM, policy, giá và evaluator. Tiền dưới đây là VND mô phỏng.

| Shape | Pass rate | Pass^5 | Seed min–max | Variance | Cost/success | Mean / p95 steps |
| --- | ---: | ---: | --- | ---: | ---: | --- |
| workflow | 1,000000 | 1,000000 | 1–1 | 0 | 35,228571 | 4,428571 / 5 |
| single | 1,000000 | 1,000000 | 1–1 | 0 | 29,157143 | 4,571429 / 5 |
| orchestrator | 1,000000 | 1,000000 | 1–1 | 0 | 57,271429 | 6,000000 / 7 |
| swarm | 0,985714 | 0,928571 | 0,928571–1 | 0,000816327 | 49,566957 | 2,800000 / 3 |

Ba shape nhóm viết đều 70/70. Swarm 69/70: `R-swarm-C-002-s55` tin ticket trễ 5 ngày dù order chỉ trễ 2 ngày. Ma trận student coi đây là lỗi dự kiến của đối chứng, nên bộ test vẫn xanh trong khi benchmark ghi đúng trial fail.

**Chọn workflow.** Luật và trình tự task đã rõ; workflow có mức tự chủ theo cấu trúc lab thấp nhất và cùng độ đúng public với hai phương án còn lại. Single có số cost thấp nhất hiện tại, nhưng chưa đem lại lợi ích độ đúng; orchestrator có ranh giới worker hữu ích song chưa cần thiết cho task này. Chi tiết đánh đổi và điều kiện đổi quyết định ở [ADR](decision_adr.md).

**Giới hạn so cost:** workflow ghi tool cost cho yêu cầu `issue_refund`; single và orchestrator chưa ghi nhận bước này vào Budget. Số model call, prompt và tool đọc cũng khác. Vì vậy không suy rằng chênh lệch trong bảng do topology, hoặc single chắc chắn rẻ hơn sau chuẩn hóa instrumentation. Giữ nguyên số do harness sinh, không sửa thước đo hoặc JSON.

Kiểm chứng bổ sung: cổng host **78 passed, 248 deselected**; ma trận student **248 passed**; [crash.json](../../src/reports/crash.json) đạt **20/20**. Suite chuẩn chủ yếu kiểm host; riêng từng shape được kiểm retry/crash ở T11/T12, ba cửa sổ orchestrator và hai cửa sổ còn thiếu của workflow/single trong test nhóm. Không có `student_drop/` hoặc kết quả hidden; chưa kết luận đạt ngưỡng hidden ≥ 0,75 hay các bộ đếm must-pass chính thức.

Tại bản tài liệu đã commit `6b55066`, kiểm format bằng `lab8.check_submission` đạt **0 FAIL, 0 WARN**; bài sau đó đã merge vào `main`. Bảng đóng góp đủ 4 thành viên. Khi chỉnh sửa tài liệu tiếp, cảnh báo working tree chưa commit có thể xuất hiện; hash ADR vẫn trỏ tới code đã đo nếu chỉ đổi tài liệu.

Ablation R3 dùng cùng workflow, chỉ bật/tắt draft: **140 trial**, cả hai điều kiện 70/70, pass^5 = 1, variance = 0. Model call giảm 1 → 0/trial; cost/success 35,228571 → 34,285714, giảm **2,676399%**. Không lệch cặp nào ở status, kết quả chấm, reason, tool calls và steps. Nguồn: [ablation_workflow_draft.json](../../src/reports/ablation_workflow_draft.json). Điều kiện draft_off là nghiên cứu; bản chọn vẫn là workflow mặc định.

Quyết định kiến trúc và lý do: xem `report/team/decision_adr.md`. Phiếu nghiên cứu và ablation: xem `report/team/research_card.md`.

## 5. Khó khăn và cách xử lý

- Interpreter Python ngoài venv từng báo `No module named pytest` khi chạy `python -m pytest -q`. Dùng `src/.venv/Scripts/python.exe` đã có Python 3.12.7, pytest và PyYAML; không cần API key.
- Benchmark cũ còn ghi workflow/single pass rate 0 dù code mới đã qua ma trận student. Đã chạy lại đúng lệnh benchmark; không dùng kết quả cũ để viết ADR. `check_submission` kiểm cấu trúc report nên không tự chứng minh số đã cập nhật.
- Pytest có `PytestCacheWarning: Access is denied` tại `.pytest_cache`; các test vẫn pass và exit code 0. Test riêng ablation dùng `-p no:cacheprovider` để không phụ thuộc quyền ghi cache; không đổi assertions hoặc evaluator.
- Report ablation lưu HEAD `f26fa94` lúc chưa commit file mới. Sau đó code và report được commit ở `fd7275e`; SHA-256 trong report đã khớp nội dung nguồn được đo. Giữ nguyên metadata JSON, ghi rõ quan hệ này thay vì sửa hash bằng tay.

Các lệnh tái lập, chạy trong `src/` sau khi activate venv:

```powershell
python -m pytest -q
python -m pytest -q -m student -o addopts=
python -m lab8.crash_test --count 20 --out reports/crash.json
python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json
python -m tests.run_draft_ablation --out reports/ablation_workflow_draft.json
python -m lab8.check_submission
```

Run id dẫn chứng: `R-workflow-C-001-s11`, `R-workflow-C-002-s55`, `R-workflow-C-014-s11`; ablation phân biệt nhánh draft_on/draft_off bằng nhãn và thư mục trace riêng.

## 6. Tổng hợp đóng góp

Chi tiết đóng góp của từng thành viên nằm trong `TEAM.md` (bảng đóng góp) và báo cáo cá nhân `report/team/MSSV_HoTen.md`.

Commit kỹ thuật: Bảo `926aea2` (workflow và test), Đạt `eef682d` (single và test), Thông `6a023df` (orchestrator và test), Thể `fd7275e` (4 ca crash/resume, runner ablation, 5 test ablation và bằng chứng). Các hash có thật trong lịch sử Git; tài liệu bổ sung sau commit code. Mỗi thành viên có một báo cáo mang MSSV/họ tên riêng; file `MSSV_HoTen.md` là mẫu được giữ lại.

## 7. Hạn chế và hướng cải thiện

MockLLM không phải model sản xuất; latency là `unavailable`. Public chỉ 14 claim và evaluator không chấm chất lượng câu trả lời khách. Steps là số event trace, không phải thời gian chạy. Ablation chỉ kiểm draft trong workflow, không chứng minh quan hệ nhân quả giữa topology và hiệu năng. Chưa có hidden; không bịa số liệu hoặc nhận đã vượt must-pass hidden.

Ưu tiên tiếp theo: chuẩn hóa cách ghi cost cho cùng thao tác trước khi tối ưu chi phí; kiểm trên hidden khi được phát; đánh giá chất lượng lời trả lời nếu dùng cho khách thật. Xem xét single/orchestrator khi task cần nhiều nhánh tool hoặc điều tra độc lập và kết quả đo cho thấy lợi ích đủ bù cost/độ phức tạp.

## 8. Tài liệu tham khảo

- [Luật nghiệp vụ](../../docs/business_rules.md), [contract](../../docs/contracts.md), [metric](../../docs/metrics.md), [rubric](../../RUBRIC.md) và source code trong repo.
- Bogavelli, Subramani, Sharma (2026), [AgentArch: A Benchmark for Evaluating Agent Architectures in Enterprise Workflows](https://aclanthology.org/2026.aacl-long.44/), AACL-IJCNLP 2026. Nguồn nghiên cứu cho R3; suy luận ablation trong lab có phạm vi riêng, xem research card.
- Công cụ hỗ trợ: Codex; cách dùng và bằng chứng kiểm chứng ghi trong báo cáo cá nhân.

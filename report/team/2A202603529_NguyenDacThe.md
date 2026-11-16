# Báo cáo cá nhân — Nguyễn Đắc Thể — 2A202603529

## 1. Thông tin thành viên

- Họ tên: Nguyễn Đắc Thể
- MSSV: 2A202603529
- Email: dacthebn@gmail.com
- GitHub: https://github.com/NguyenDacThe

## 2. Phần việc phụ trách

- Vai trò người thứ 4: kiểm thử tích hợp, evaluation, nghiên cứu R3/ablation và tổng hợp hồ sơ nhóm.
- Làm trên nhánh `NguyenDacThe`, cập nhật code ba shape từ main trước khi kiểm thử chung.
- Phần việc đã merge vào nhánh nộp `main` qua PR #4 tại `3d3dbe7`; commit code/bằng chứng được giữ riêng với commit hoàn thiện tài liệu.
- Phối hợp với ba thành viên phụ trách workflow, single và orchestrator; không sửa host, thước đo, config hoặc dữ liệu để làm test xanh.

## 3. Đóng góp cụ thể

- Commit kỹ thuật: `fd7275e054c501b10f96bcc2272aa945633e6da0`, đã chứa test, runner, benchmark mới và research card.
- [test_eval_nguyendacthe.py](../../src/tests/test_eval_nguyendacthe.py): 4 trường hợp, kiểm crash trước effect và sau checkpoint cho workflow/single; crash phải thực sự xảy ra, trạng thái ledger/audit trước resume đúng và kết quả sau resume qua evaluator.
- [run_draft_ablation.py](../../src/tests/run_draft_ablation.py): giữ nguyên workflow và harness, chỉ tạm tắt cờ draft có sẵn, khôi phục sau thí nghiệm; mỗi trial một SQLite mới, trace tách thư mục, ghi SHA-256 để xác định nội dung nguồn.
- [test_draft_ablation.py](../../src/tests/test_draft_ablation.py): 5 trường hợp, kiểm kết quả nghiệp vụ/tiền không đổi khi bỏ draft, kể cả T14 và nhánh unknown claim; kiểm khôi phục binding khi experiment ném exception.
- Chạy lại benchmark bốn shape để thay report cũ; tổng hợp [research card](research_card.md), [ADR](decision_adr.md), [báo cáo nhóm](group_report.md) và bảng đóng góp trong `TEAM.md` với Codex hỗ trợ. Phần tài liệu hoàn thiện sau commit kỹ thuật.
- Đề xuất workflow vì đạt cùng độ đúng public với cấu trúc ít tự chủ nhất. Nêu rõ cost single thấp nhất theo số đã ghi nhưng cách tính tool cost cho yêu cầu refund khác nhau, nên chưa kết luận single rẻ hơn do topology.

## 4. Bằng chứng

- [Commit kỹ thuật fd7275e](https://github.com/NguyenDacThe/K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents/commit/fd7275e054c501b10f96bcc2272aa945633e6da0).
- Test riêng phần crash/resume: **4 passed**; test riêng ablation: **5 passed**. Cổng host sau bổ sung: **78 passed**; ma trận student: **248 passed**.
- [benchmark.json](../../src/reports/benchmark.json): 280 trial (4 shape × 14 claim × 5 seed). Workflow/single/orchestrator đều 70/70; swarm 69/70. Run id: `R-workflow-C-001-s11`, `R-workflow-C-014-s11`.
- [crash.json](../../src/reports/crash.json): suite chuẩn **20/20**; phân biệt suite host với kiểm thử crash trực tiếp từng shape.
- [ablation_workflow_draft.json](../../src/reports/ablation_workflow_draft.json): 140 trial, cả hai điều kiện 70/70; cost/success giảm 35,228571 → 34,285714 VND mô phỏng (2,676399%), model call 1 → 0/trial.
- Report ablation lưu HEAD trước commit; hash nguồn khớp nội dung tại commit kỹ thuật. Chưa có dữ liệu hidden; không tự khai điểm hidden hoặc các bộ đếm bảo mật chính thức.
- Bản tài liệu `6b55066` đã được kiểm `lab8.check_submission`: **0 FAIL, 0 WARN**. Đây là kiểm tra format, không thay thế điểm hidden hoặc xác nhận đã nộp LMS.

Lệnh tái lập chạy trong `src/`, sau khi activate venv:

```powershell
python -m pytest -q tests/test_eval_nguyendacthe.py tests/test_draft_ablation.py
python -m pytest -q
python -m pytest -q -m student -o addopts=
python -m lab8.crash_test --count 20 --out reports/crash.json
python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json
python -m tests.run_draft_ablation --out reports/ablation_workflow_draft.json
python -m lab8.check_submission
```

## 5. Công cụ AI đã dùng

Sử dụng **Codex** để đọc repo, cập nhật nhánh, nhận diện phần thiếu coverage, hỗ trợ viết test/runner, tìm nguồn R3 và soạn tài liệu. Codex đã trực tiếp thực thi các lệnh kiểm tra và tổng hợp số liệu từ report; không ghi rằng mọi dòng code hoặc kết quả phân tích đều được làm độc lập không có AI.

Cách kiểm chứng trong phiên làm việc: đối chiếu output pytest; dùng evaluator chính thức kiểm tiền/ledger/audit/trace; đọc JSON do harness sinh; kiểm hash nguồn và xác nhận nhánh draft_on khớp workflow trong benchmark chính. Không sửa JSON hoặc thước đo để đạt kết quả mong muốn. Khi trình bày, cần tự giải thích được code và chạy lại các lệnh nêu trên.

## 6. Tự đánh giá

Đánh giá theo sản phẩm đã có: phần kiểm thử và evaluation có bằng chứng chạy được, gồm 9 trường hợp test bổ sung, benchmark chuẩn và ablation một biến. Kết quả hỗ trợ lựa chọn workflow trong phạm vi public và làm rõ việc giảm model call không nhất thiết tăng độ đúng.

Phần cần tiếp tục củng cố là tự giải thích từng assertion, transaction/idempotency và ranh giới code/model; không đồng nhất việc test xanh với hiểu đầy đủ hệ thống. Cần phân biệt số đo công cụ ghi nhận với kết luận nhân quả về kiến trúc, và kiểm hidden khi được phát trước khi kết luận tổng quát. Đánh giá này dựa trên sản phẩm và giới hạn quan sát được, không tự nhận đã đo năng lực giải thích độc lập hoặc chất lượng triển khai production.

# Team

## Thông tin nhóm

- Tên nhóm: NhomABC
- Lớp: L3A
- Repo nhóm: `K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents`
- URL repo: https://github.com/NguyenDacThe/K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents
- Nhánh hoàn thiện: `NguyenDacThe`; nhánh nộp dự kiến: `main` sau khi merge PR.
- Commit code và bằng chứng dẫn chiếu: `fd7275e054c501b10f96bcc2272aa945633e6da0` (thống nhất với ADR và báo cáo nhóm; tài liệu được cập nhật bằng commit sau).
- Shape chọn: `workflow`, lý do và phạm vi bằng chứng tại [decision_adr.md](report/team/decision_adr.md).

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 | Phan Gia Bảo | 2A202603562 | pgb4work@gmail.com | pgb4work-ai-vinuni | workflow + integration |
|   2 | Nguyễn Minh Đạt | 2A202603582 | nguyenminhdat6401@gmail.com | https://github.com/nguyenminhdat6401 | Single agent |
|   3 | Trương Văn Thông | 2A202603560 | thongthong2k41306@gmail.com | https://github.com/thongthong2k41306-beep | Orchestrator + handoff |
|   4 | Nguyễn Đắc Thể | 2A202603529 | dacthebn@gmail.com | https://github.com/NguyenDacThe | Kiểm thử, evaluation, ablation và tổng hợp báo cáo |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Phan Gia Bảo | workflow + integration | `926aea2ab8c82628a20c41a20c5bb8fb77380e52` | `src/tests/test_workflow_bao.py` (8 test: T02 không tin ticket, T14 full quote, claim không tồn tại, trace không lộ secret/PII) |
| Trương Văn Thông (2A202603560) | Orchestrator, handoff, trace và kiểm tra retry/crash | `6a023df85a1eb31d322d9c22944fe0203086d9a2` | `src/tests/test_orchestrator.py`: `test_orchestrator_resumes_each_crash_window` (3 trường hợp), `test_orchestrator_unknown_claim_returns_fresh_trace` |
| Nguyễn Minh Đạt | Triển khai và kiểm thử single agent | eef682d | src/tests/test_student_unknown_claim.py — trường hợp single |
| Nguyễn Đắc Thể (2A202603529) | Kiểm thử, benchmark, ablation và báo cáo | `fd7275e054c501b10f96bcc2272aa945633e6da0` | `src/tests/test_eval_nguyendacthe.py` (4 trường hợp crash/resume workflow/single); `src/tests/test_draft_ablation.py` (5 trường hợp) |

## Cá nhân

Mỗi thành viên có báo cáo riêng dưới `report/team/`; file mẫu `MSSV_HoTen.md` được giữ để tham khảo.

- [Phan Gia Bảo](report/team/2A202603562_PhanGiaBao.md).
- [Nguyễn Minh Đạt](report/team/2A202603582_NguyenMinhDat.md).
- [Trương Văn Thông](report/team/2A202603560_TruongVanThong.md).
- [Nguyễn Đắc Thể](report/team/2A202603529_NguyenDacThe.md).

## 2A202603560_TruongVanThong

### Phần việc phụ trách

- Trương Văn Thông — MSSV 2A202603560: shape orchestrator, handoff allowlist, investigator chỉ đọc, trace và kiểm tra retry/crash.

### Đóng góp cụ thể

- Hoàn thiện `src/lab8/shapes/orchestrator.py` với sự hỗ trợ của Codex; giữ nguyên contract, dùng quote đầy đủ và để host kiểm approval, trần hoàn tiền, idempotency.
- Bổ sung `src/tests/test_orchestrator.py`: ba cửa sổ crash/resume và trường hợp claim không tồn tại.
- Commit kỹ thuật: `6a023df85a1eb31d322d9c22944fe0203086d9a2`.

### Bằng chứng

- `src/tests/test_orchestrator.py`: kiểm tra ba cửa sổ crash/resume và claim không tồn tại.
- Ma trận student orchestrator: 62 passed; benchmark public: 70/70 trial.
- Run id: `R-orchestrator-C-001-s11`, `R-orchestrator-C-012-s11`.
- Báo cáo: [2A202603560_TruongVanThong.md](report/team/2A202603560_TruongVanThong.md).

## 2A202603562_PhanGiaBao

### Phần việc phụ trách

- Shape A `workflow` (`src/lab8/shapes/workflow.py`) và integration: ghép code, giữ cổng host xanh, crash suite, benchmark, `check_submission`.

### Đóng góp cụ thể

- Cài đặt workflow cố định: `get_claim_view` → `lookup_order` → `compute_compensation` → `durability.issue_refund` với quote đầy đủ và `crash_at`. MockLLM chỉ soạn chữ.
- Trace chỉ ghi field allowlist, không ghi ticket, claim row hay order row thô.
- Thêm `src/tests/test_workflow_bao.py` (8 test).

### Bằng chứng

- Commit `926aea2ab8c82628a20c41a20c5bb8fb77380e52`; ma trận student workflow 62 passed; `check_submission` workflow 12/12; báo cáo chi tiết `report/team/2A202603562_PhanGiaBao.md`.
- `src/reports/benchmark.json`: workflow pass 70/70 trial public; `src/reports/crash.json`: suite chuẩn 20/20.

## 2A202603582_NguyenMinhDat

### Phần việc phụ trách

- Triển khai và kiểm thử single agent tại
  src/lab8/shapes/single.py.

### Đóng góp cụ thể

- Triển khai single agent trong src/lab8/shapes/single.py,
  giữ nguyên chữ ký run_claim.
- Sử dụng run_react với giới hạn tối đa 8 bước.
- Viết hàm decide chọn tool đọc dựa trên observation
  bằng logic Python.
- Sử dụng lookup_order để kiểm tra phạm vi sở hữu đơn.
- Dùng compute_compensation tính quote từ dữ liệu order,
  không tin số ngày trễ trong ticket.
- Gọi MockLLM và ghi nhận usage qua Budget.
- Tách thao tác ghi refund khỏi vòng ReAct; gọi host
  với quote đầy đủ và truyền crash_at.
- Xử lý các trạng thái needs_info, escalated, denied,
  resolved và failed.
- Dùng common.finish để tạo RunResult và ghi trace;
  đóng kết nối bằng finally.
- Bổ sung test claim không tồn tại cho single và chạy
  bộ test riêng đạt 62 passed.

### Bằng chứng 

- Commit: eef682d
- Test: src/tests/test_student_unknown_claim.py,
  trường hợp tham số single.
- Kiểm tra riêng single: 62 passed.
- Benchmark single: pass rate 1.000, pass^5 1.000.
- Báo cáo cá nhân: [2A202603582_NguyenMinhDat.md](report/team/2A202603582_NguyenMinhDat.md).

## 2A202603529_NguyenDacThe

### Phần việc phụ trách

- Kiểm thử tích hợp sau khi ghép ba shape, đo benchmark và hoàn thiện bằng chứng nghiên cứu R3.

### Đóng góp cụ thể

- Bổ sung hai cửa sổ crash còn thiếu cho workflow/single, tổng 4 trường hợp.
- Viết runner và 5 test cho ablation draft on/off; giữ nguyên shape, host, dữ liệu và evaluator.
- Chạy lại benchmark chuẩn 4 shape, tổng 280 trial; chạy ablation 140 trial và đối chiếu nguồn bằng SHA-256.
- Tổng hợp ADR, báo cáo nhóm và báo cáo cá nhân với Codex hỗ trợ; phân biệt bằng chứng public với hidden chưa có.

### Bằng chứng

- Commit kỹ thuật `fd7275e`; các test và report nêu trong bảng đóng góp.
- Cổng host 78 passed, ma trận student 248 passed, crash suite 20/20; chi tiết tại [group_report.md](report/team/group_report.md).
- Báo cáo: [2A202603529_NguyenDacThe.md](report/team/2A202603529_NguyenDacThe.md).

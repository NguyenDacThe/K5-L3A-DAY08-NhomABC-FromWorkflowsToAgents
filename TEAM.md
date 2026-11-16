# Team

## Thông tin nhóm

- Tên nhóm: NhomABC
- Lớp: L3A
- Repo nhóm: `K5-L3A-DAY08-TenNhom-FromWorkflowsToAgents` (xem [SUBMISSION.md](SUBMISSION.md))
- URL repo, nhánh nộp, commit chốt: https://github.com/NguyenDacThe/K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents, main,

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 |        |      |       |        |         |
|   2 | Nguyễn Minh Đạt | 2A202603582 | nguyenminhdat6401@gmail.com | https://github.com/nguyenminhdat6401 | Single agent |
|   3 |        |      |       |        |         |
|   4 |        |      |       |        |         |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Trương Văn Thông (2A202603560) | Orchestrator, handoff, trace và kiểm tra retry/crash | th | `src/tests/test_orchestrator.py`: `test_orchestrator_resumes_each_crash_window` (3 trường hợp), `test_orchestrator_unknown_claim_returns_fresh_trace` |
| Nguyễn Minh Đạt | Triển khai và kiểm thử single agent | eef682d | src/tests/test_student_unknown_claim.py — trường hợp single |

## Cá nhân

Mỗi thành viên đồng thời tạo một báo cáo riêng trong `report/` theo dạng:

```text
report/team/MSSV_HoTen.md
```

Ví dụ: `report/team/20241234_NguyenVanAn.md`.

Sao chép mục dưới đây cho từng thành viên.

## 2A202603560_TruongVanThong

### Phần việc phụ trách

- Trương Văn Thông — MSSV 2A202603560: shape orchestrator, handoff allowlist, investigator chỉ đọc, trace và kiểm tra retry/crash.

### Đóng góp cụ thể

- Hoàn thiện `src/lab8/shapes/orchestrator.py` với sự hỗ trợ của Codex; giữ nguyên contract, dùng quote đầy đủ và để host kiểm approval, trần hoàn tiền, idempotency.
- Bổ sung `src/tests/test_orchestrator.py`: ba cửa sổ crash/resume và trường hợp claim không tồn tại.
- Chưa có commit cá nhân cho các thay đổi này; bổ sung hash thực sau khi commit.

### Bằng chứng

- Báo cáo cá nhân: [2A202603560_TruongVanThong.md](report/team/2A202603560_TruongVanThong.md).
- Ma trận student orchestrator: 62 passed. Test host sau khi thêm test: 58 passed.
- `src/reports/benchmark.json`: orchestrator pass 70/70 trial public; `src/reports/crash.json`: suite chuẩn 20/20.
- Run id: `R-orchestrator-C-001-s11`, `R-orchestrator-C-012-s11`. Test crash riêng orchestrator: `test_orchestrator_resumes_each_crash_window`.

## 2A202603582_NguyenMinhDat

### Phần việc phụ trách

- Triển khai và kiểm thử single agent tại
  src/lab8/shapes/single.py.

### Đóng góp cụ thể

- Sử dụng run_react với giới hạn 8 bước.
- Điều phối tool đọc dựa trên observation bằng logic Python.
- Ghi nhận usage của MockLLM qua Budget.
- Host ghi refund sau vòng lặp; truyền quote đầy đủ và crash_at.
- Ghi trace và trả RunResult bằng common.finish.
- Bổ sung kiểm tra claim không tồn tại cho single.

### Bằng chứng

- Commit: eef682d
- Test: src/tests/test_student_unknown_claim.py,
  trường hợp tham số single.
- Kiểm tra riêng single: 62 passed.
- Benchmark single: pass rate 1.000, pass^5 1.000.
- Báo cáo cá nhân: report/team/2A202603582_NguyenMinhDat.md.

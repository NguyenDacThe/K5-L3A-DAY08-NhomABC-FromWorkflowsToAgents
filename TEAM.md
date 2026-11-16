# Team

## Thông tin nhóm

- Tên nhóm: NhomABC
- Lớp: L3A
- Repo nhóm: `K5-L3A-DAY08-TenNhom-FromWorkflowsToAgents` (xem [SUBMISSION.md](SUBMISSION.md))
- URL repo, nhánh nộp, commit chốt: https://github.com/NguyenDacThe/K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents, nhánh `main`, commit chốt `<HASH_CHOT>`

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 | Phan Gia Bảo | 2A202603562 | pgb4work@gmail.com | pgb4work-ai-vinuni | workflow + integration |
|   2 |        |      |       |        |         |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Phan Gia Bảo | workflow + integration | `926aea2ab8c82628a20c41a20c5bb8fb77380e52` | `src/tests/test_workflow_bao.py` (8 test: T02 không tin ticket, T14 full quote, claim không tồn tại, trace không lộ secret/PII) |

## Cá nhân

Mỗi thành viên đồng thời tạo một báo cáo riêng trong `report/` theo dạng:

```text
report/team/MSSV_HoTen.md
```

Ví dụ: `report/team/20241234_NguyenVanAn.md`.

Sao chép mục dưới đây cho từng thành viên.

## MSSV_HoTen

### Phần việc phụ trách

-

### Đóng góp cụ thể

-

### Bằng chứng

-

## 2A202603562_PhanGiaBao

### Phần việc phụ trách

- Shape A `workflow` (`src/lab8/shapes/workflow.py`) và integration: ghép code, giữ cổng host xanh, crash suite, benchmark, `check_submission`.

### Đóng góp cụ thể

- Cài đặt workflow cố định: `get_claim_view` → `lookup_order` → `compute_compensation` → `durability.issue_refund` với quote đầy đủ và `crash_at`. MockLLM chỉ soạn chữ.
- Trace chỉ ghi field allowlist, không ghi ticket, claim row hay order row thô.
- Thêm `src/tests/test_workflow_bao.py` (8 test).

### Bằng chứng

- Commit `926aea2ab8c82628a20c41a20c5bb8fb77380e52`; ma trận student workflow 62 passed; `check_submission` workflow 12/12; báo cáo chi tiết `report/team/2A202603562_PhanGiaBao.md`.

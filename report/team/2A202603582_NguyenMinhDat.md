# Báo cáo cá nhân — Nguyễn Minh Đạt — 2A202603582

## 1. Thông tin thành viên

- Họ tên: Nguyễn Minh Đạt
- MSSV: 2A202603582
- Email: nguyenminhdat6401@gmail.com
- GitHub: https://github.com/nguyenminhdat6401

## 2. Phần việc phụ trách

Triển khai và kiểm thử single agent tại [single.py](../../src/lab8/shapes/single.py).

## 3. Đóng góp cụ thể

- Giữ nguyên chữ ký run_claim và trả kết quả theo RunResult.
- Sử dụng run_react với giới hạn max_steps=8.
- Điều phối lookup_order và compute_compensation từ observation.
- Gọi MockLLM và ghi nhận usage qua Budget.
- Để host ghi refund sau vòng lặp; agent chỉ dùng tool đọc.
- Truyền quote đầy đủ và crash_at vào issue_refund.
- Dùng common.finish để tạo kết quả và trace.
- Kiểm tra trường hợp claim không tồn tại trên single
  trong tests/test_student_unknown_claim.py.

Commit chính: [`eef682d`](https://github.com/NguyenDacThe/K5-L3A-DAY08-NhomABC-FromWorkflowsToAgents/commit/eef682d), đã có trong lịch sử main sau khi tích hợp.

## 4. Bằng chứng

- Code: [single.py](../../src/lab8/shapes/single.py).
- Test bổ sung: [test_student_unknown_claim.py](../../src/tests/test_student_unknown_claim.py),
  trường hợp tham số single.
- Bộ test riêng single: 62 passed.
- Kiểm chứng tích hợp toàn nhóm tại phiên bản code `fd7275e`: 78 test host passed và 248 test student passed.
- Report: [benchmark.json](../../src/reports/benchmark.json), mục `by_shape.single`.
- Run ID dẫn chứng: R-single-C-001-s11.

Kết quả benchmark single trên 14 claim public và 5 seed:

- Pass rate: 1.000.
- Pass^5: 1.000.
- Seed variance: 0.000000.
- Cost/success: 29,157143 VND mô phỏng theo Budget hiện tại; lưu ý giới hạn ghi tool cost trong [ADR](decision_adr.md).
- Mean/p95 steps: 4,571429 / 5.

Các kiểm tra chạy trong `src/`: `python -m pytest -q -m student -o addopts= -k single`. Bộ benchmark 70 trial khác với ma trận student 62 test của shape.

## 5. Công cụ AI đã dùng

Sử dụng ChatGPT/Codex để giải thích yêu cầu, gợi ý code
single và test, hướng dẫn xử lý lỗi môi trường.

Kiểm chứng kết quả bằng test riêng single, bộ test tích hợp
và benchmark. Đối chiếu trạng thái, proposal_amount,
refund và audit thông qua bộ kiểm thử của repo.

## 6. Tự đánh giá

Single đạt các kiểm tra public đã chạy, bao gồm retry
và crash. Quyền ghi refund được tách khỏi vòng ReAct;
tiền do code tính từ order và policy hiện hành.

Trong triển khai này, action được chọn bằng logic Python.
MockLLM được gọi để ghi nhận usage nhưng chuỗi trả về
không quyết định action. Vì vậy chưa thể dùng kết quả
để kết luận về khả năng tự chủ của model thực.

Chưa có kết quả hidden. Chi phí là mô phỏng;
latency là unavailable.

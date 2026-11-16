# Báo cáo nhóm

Hồ sơ NhomABC đã có đủ 4 báo cáo cá nhân. Code/bằng chứng tại `fd7275e`, tài liệu tại `6b55066`, được ghép vào `main` qua PR #4 tại `3d3dbe7`. Các đợt sửa Markdown sau đó giữ nguyên hash code được đo trong ADR.

| File | Ai điền | Ghi chú |
|---|---|---|
| [group_report.md](group_report.md) | Cả nhóm | Báo cáo chính, tối đa 5 trang ngoài phụ lục |
| [decision_adr.md](decision_adr.md) | Cả nhóm | Chọn workflow; `check_submission` đọc bằng máy. Không đổi tên hoặc xóa nhãn |
| [research_card.md](research_card.md) | Cả nhóm | R3 và ablation draft on/off, đủ 7 trường và kết quả chạy thật |
| `bonus_report.md` | Cả nhóm, chỉ khi làm bonus | Tạo mới theo khung ở cuối [docs/bonus_topics.md](../../docs/bonus_topics.md) |
| [MSSV_HoTen.md](MSSV_HoTen.md) | Từng thành viên | File mẫu, không tính là một báo cáo cá nhân đã nộp |

## Báo cáo từng thành viên

- [Phan Gia Bảo — 2A202603562](2A202603562_PhanGiaBao.md).
- [Nguyễn Minh Đạt — 2A202603582](2A202603582_NguyenMinhDat.md).
- [Trương Văn Thông — 2A202603560](2A202603560_TruongVanThong.md).
- [Nguyễn Đắc Thể — 2A202603529](2A202603529_NguyenDacThe.md).

Đóng góp và commit/test của từng người được khai báo trong [TEAM.md](../../TEAM.md). Không khai trải nghiệm, số liệu hoặc việc tự chạy test nếu chưa có bằng chứng; phân biệt phần AI hỗ trợ với phần kiểm chứng thực tế.

Từ `src/`, chạy `python -m lab8.check_submission` để kiểm format. Kết quả này không kiểm việc nộp LMS, quyền truy cập của giảng viên hay điểm hidden. Yêu cầu nộp đầy đủ tại [SUBMISSION.md](../../SUBMISSION.md).

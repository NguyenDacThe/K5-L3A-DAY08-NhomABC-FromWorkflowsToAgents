# Hai bộ đánh giá

Đường dẫn trong file này tính từ thư mục `src/`.

Bộ học viên tự chạy nằm trong git: `data/public_claims.jsonl` và `tests/test_public_matrix.py` (marker `student`).

Bộ giảng viên nằm ở `src/eval/instructor/` và được `.gitignore` chặn. Thư mục đó có trên máy soạn bài, không đi theo `git clone` của học viên. Hidden được chấm bằng grader riêng của giảng viên (repo chấm riêng của giảng viên), không chạy từ repo này. Cách phát cuối buổi ghi trong `eval/instructor/README_GIANG_VIEN.md` trên máy giảng viên.

Nhóm hiện chưa có `student_drop/` hay kết quả hidden; việc thiếu `hidden_grade.json` trong repo nộp là đúng quy định. Khi được phát pack không nhãn, đặt ở `src/student_drop/`, rồi chạy từ `src/`:

```text
python -m lab8.benchmark --shapes workflow,single,orchestrator --cases student_drop/hidden_claims.jsonl --world student_drop/hidden_world_state.json --seeds 11,22,33,44,55 --out reports/hidden_selfcheck.json
```

Report tự kiểm không có nhãn nên không tính pass rate/pass^5. Xem status, trace contract và `leak_suspect`; không coi các trial chạy hết là đã vượt ngưỡng hidden ≥ 0,75. Không commit pack, label hay grader của giảng viên. Chi tiết [src/README.md](../README.md#tự-kiểm-trên-bộ-hidden-cp5).

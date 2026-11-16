# Research card

File này nằm cố định ở `report/team/research_card.md`; `python -m lab8.check_submission` đọc cột "Nhóm điền" bằng máy.

Phần này nối tiếp phần mini research ở buổi lý thuyết (slide 62–63). Chọn **một** câu R1–R4, điền đủ 7 dòng của phiếu, rồi biến nó thành **một ablation** chạy được trong lab: giữ nguyên model (MockLLM), tool, seed, corpus và giá, chỉ thay đổi một biến.

| Câu | Nguồn (thư viện tham khảo, slide 68–69) | Câu hỏi gợi ý cho lab |
| --- | --- | --- |
| R1 · Scaling | [Google Research 2026, *Towards a Science of Scaling Agent Systems*](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/) | Task claims chủ yếu tuần tự hay song song được? Dự đoán thêm investigator sẽ làm pass rate và cost/success thay đổi thế nào. |
| R2 · Failure taxonomy | [MAST, *Why Do Multi-Agent LLM Systems Fail?*](https://arxiv.org/abs/2503.13657) | Các trial fail của swarm (seed 55) thuộc nhóm nào: lỗi giao việc, lệch mục tiêu, hay lỗi kiểm chứng? |
| R3 · Architecture choices | [AgentArch, AACL 2026](https://aclanthology.org/2026.aacl-long.44/) | Nếu đổi topology mà giữ nguyên mọi thứ khác, biến nào phải cố định để kết luận còn đúng? |
| R4 · Evaluation confounders | [*Rethinking the Evaluation of Efficiency Methods for Multi-Agent Systems*, EMNLP 2026](https://aclanthology.org/2026.emnlp-main.798/) | Làm sao chứng minh chênh lệch giữa A/B/C/D là do topology, không phải do prompt, tool hay seed? |

## Phiếu của nhóm

Điền vào cột cuối. Không xóa hai cột đầu.

| Field | Hướng dẫn | Nhóm điền |
| --- | --- | --- |
| CÂU | R1 / R2 / R3 / R4 | R3 — Architecture choices. Đo riêng ảnh hưởng của bước soạn lời trả lời trong workflow; không quy chênh lệch cost giữa A/B/C/D hoàn toàn cho topology. |
| SOURCE | Paper/blog, năm, URL, loại bằng chứng (benchmark, production report, blog kinh nghiệm) | Tara Bogavelli, Hari Subramani, Roshnee Sharma (2026), [AgentArch: A Benchmark for Evaluating Agent Architectures in Enterprise Workflows](https://aclanthology.org/2026.aacl-long.44/), AACL-IJCNLP 2026, tr. 915–933, DOI: 10.18653/2026.aacl-long.44. Loại bằng chứng: paper benchmark thực nghiệm; xem Abstract, mục 3 và Figure 1. |
| CLAIM | Một kết luận gốc của nguồn. Không viết rộng hơn phạm vi paper | Trong benchmark AgentArch, cấu hình phù hợp thay đổi theo model và task; kết quả không ủng hộ một kiến trúc tối ưu cho mọi trường hợp. Đây là kết luận của nguồn. Việc kiểm tra riêng bước draft trên LogiX là suy luận/thí nghiệm của nhóm, không phải kết quả được paper khẳng định. |
| BOUNDARY | Model / dataset / setup giới hạn kết luận đó | Nguồn khảo sát 18 cấu hình, 6 LLM và hai workflow enterprise: Requesting Time Off, Customer Request Routing. Lab này chỉ dùng workflow có sẵn, MockLLM seeded, 14 claim public × 5 seed; không tái lập benchmark AgentArch và không dùng dữ liệu hidden. |
| HYPOTHESIS | Nếu đổi shape A→B (hoặc bật/tắt một thành phần), metric X sẽ đổi theo hướng nào, khoảng bao nhiêu | Đặt trước lần chạy trong `tests/run_draft_ablation.py`: tắt draft giữ nguyên pass rate và pass^5 (delta 0), giảm đúng 1 model call/trial và giảm cost/success khoảng 0,8–1,2 VND mô phỏng; status, số tool call và số event trace của từng cặp không đổi. Dự đoán dựa vào việc draft chỉ tạo câu chữ, không tính tiền hay ghi ledger. |
| METHOD | Biến giữ cố định: model, tools, seed, corpus, giá, evaluator. Biến duy nhất được thay đổi: … | Cố định shape workflow, code nghiệp vụ, MockLLM, tool, policy/as_of, corpus public, seed 11,22,33,44,55, bảng giá và evaluator chính thức. Chỉ đổi bước draft on/off: runner tạm đặt tham số có sẵn `workflow._done(draft=False)` cho nhánh off bằng context manager rồi khôi phục. Nhánh on giữ hành vi gốc. Mỗi trial có SQLite mới; trace hai nhánh tách thư mục; cả hai dùng `lab8.benchmark.run_benchmark` và `lab8.metrics.trial_pass` nguyên bản. |
| FAILURE / METRIC | Một lỗi dự kiến và cách đo nó: pass rate, pass^5, variance, cost/success, leak, bypass | Phản ví dụ cần phát hiện: tắt draft làm đổi quyết định/tiền/ledger/audit hoặc chỉ xóa cost mà vẫn gọi model. Đo pass rate, pass^5, seed min–max/variance, cost/success, model/tool calls, mean/p95 trace steps; ghép theo (claim_id, seed). Evaluator đối chiếu tiền, ledger, audit, trace mới và chuỗi cấm theo nhãn public. Không tự khai các bộ đếm leak/bypass hidden. |

## Kết quả ablation

- Lệnh đã chạy (copy nguyên văn, trong `src/`): `.\.venv\Scripts\python.exe -m tests.run_draft_ablation --out reports/ablation_workflow_draft.json`.
- Run id / file report: [src/reports/ablation_workflow_draft.json](../../src/reports/ablation_workflow_draft.json), experiment id `workflow_draft_ablation_v1`. Mỗi điều kiện có 70 trial, tổng 140 trial. Ví dụ: `draft_on` + `R-workflow-C-001-s11` và `draft_off` + `R-workflow-C-001-s11`; run id giữ đúng contract, phân biệt điều kiện bằng nhãn và thư mục trace.
- Kết quả so với hypothesis (đúng / sai / không kết luận được), kèm số liệu: **ủng hộ giả thuyết trên public**. Cả hai điều kiện đạt 70/70, pass rate = pass^5 = 1,0, seed min–max = 1,0–1,0 và variance = 0. Tắt draft giảm model call từ 1 xuống 0/trial, cost/success giảm 0,942857 VND mô phỏng (2,676399%), nằm trong khoảng dự đoán 0,8–1,2. Không có cặp nào lệch ở status, passed, reason, tool_calls hoặc steps (0/70).
- Giới hạn: MockLLM không phải model sản xuất, latency là `unavailable`.

| Metric | Draft on — workflow gốc | Draft off — bỏ lời gọi soạn chữ |
| --- | ---: | ---: |
| Trial pass | 70/70 | 70/70 |
| Pass rate | 1,000000 | 1,000000 |
| Pass^5 | 1,000000 | 1,000000 |
| Seed min–max | 1,0–1,0 | 1,0–1,0 |
| Seed variance | 0 | 0 |
| Tổng cost mô phỏng (VND) | 2.466 | 2.400 |
| Cost/success mô phỏng (VND) | 35,228571 | 34,285714 |
| Model calls trung bình/trial | 1 | 0 |
| Tool calls trung bình/trial | 3,428571 | 3,428571 |
| Mean / p95 steps | 4,428571 / 5 | 4,428571 / 5 |

Tất cả số trong bảng lấy từ `variants.draft_on.by_shape.workflow` và `variants.draft_off.by_shape.workflow` của report do runner sinh. Mức giảm và so sánh theo cặp lấy từ `comparison`. Tiền, ledger, audit và trace được evaluator chính thức chấm riêng cho từng trial; so sánh theo cặp không tự nhận là đối chiếu toàn bộ nội dung SQLite. Tổng cost giảm 66 VND mô phỏng trên 70 cặp.

Lệnh kiểm tra đã chạy trong `src/`:

```powershell
.\.venv\Scripts\python.exe -m pytest -q tests/test_draft_ablation.py -p no:cacheprovider
.\.venv\Scripts\python.exe -m pytest -q
```

Kết quả: test riêng ablation **5 passed**; cổng host sau khi bổ sung test **78 passed, 248 deselected**. Pytest có cảnh báo quyền ghi cache, không có test thất bại.

Có thể tái lập bằng `python -m tests.run_draft_ablation --out reports/ablation_workflow_draft.json` khi đã activate venv. Runner chạy offline, chỉ dùng thư viện chuẩn và dependencies có sẵn của lab. Trace nằm trong `src/traces/ablation_workflow_draft/draft_on/` và `draft_off/`, không commit theo `.gitignore`.

## Thiết kế và phạm vi suy luận

- Đây là ablation một thành phần trong cùng topology workflow. Không dùng so sánh bốn shape để kết luận nhân quả về topology vì prompt, số model/tool call và các bước phụ cũng khác nhau.
- Bước draft gọi MockLLM thật của lab khi bật; khi tắt, runner bỏ hẳn lời gọi, không giả số token/cost và không thay evaluator. Không sửa `workflow.py`, host, config, dữ liệu hoặc file JSON bằng tay.
- Runner nằm trong `src/tests/` để hỗ trợ thí nghiệm. Test `src/tests/test_draft_ablation.py` kiểm tra resolved, needs_info, T14 chỉ hoàn phần chênh và claim không tồn tại; kiểm tra kết quả nghiệp vụ giống nhau, budget đúng và các binding được khôi phục kể cả khi thí nghiệm ném exception.
- Hash Git trong report là HEAD tại thời điểm chạy, có thể chưa chứa các file mới. SHA-256 đi kèm định danh nội dung working tree thực sự được đo; không coi HEAD cũ là commit đã chứa runner.
- Code, runner và bằng chứng đã được đưa vào commit kỹ thuật `fd7275e054c501b10f96bcc2272aa945633e6da0`. SHA-256 trong report khớp nguồn tại commit này; HEAD `f26fa94` lưu trong report phản ánh thời điểm chạy trước khi commit, được giữ nguyên. ADR chọn workflow mặc định (`draft_on`), không dùng cost của `draft_off` thay cho baseline.
- Kết quả public không chứng minh hiệu năng hidden hoặc production. Evaluator không chấm chất lượng câu trả lời khách; trong workflow này, chuỗi draft không đi vào `RunResult`. Vì vậy thí nghiệm không đo được tác động khi khách cần một câu trả lời hữu ích.
- `steps` là số event trace theo contract, không phải số lời gọi model; giảm model call có thể không làm metric steps giảm. Hai điều kiện giữ nguyên quy tắc đếm của repo.

Muốn đi xa hơn một ablation để lấy điểm cộng thì xem [docs/bonus_topics.md](../../docs/bonus_topics.md).

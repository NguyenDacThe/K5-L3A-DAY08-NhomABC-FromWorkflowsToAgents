# Decision record

Các dòng nhãn dưới đây giữ đúng định dạng để `lab8.check_submission` và grader đọc được. Quyết định áp dụng cho phiên bản code đã đo; cập nhật tài liệu sau đó không thay đổi code shape.

- Shape chọn: workflow
- Commit hash: fd7275e054c501b10f96bcc2272aa945633e6da0
- Run id dẫn chứng: R-workflow-C-001-s11; R-workflow-C-002-s55; R-workflow-C-014-s11 (src/reports/benchmark.json)
- Pass rate / pass^5 / seed min-max / variance: 1,000000 / 1,000000 / 1,000000–1,000000 / 0,000000 trên 14 claim public × 5 seed = 70 trial workflow
- Cost per success (mô phỏng, VND): 35,22857142857143 theo by_shape.workflow.cost_per_success_vnd của src/reports/benchmark.json
- Crash safety: 20/20 theo src/reports/crash.json; workflow còn qua T11/T12 và before_effect/after_checkpoint trong src/tests/test_eval_nguyendacthe.py
- Secret leak / approval bypass: Không phát hiện vi phạm trong 70/70 trial public workflow qua evaluator và các test host về approval/trace; chưa có bộ đếm chính thức secret_leak_count hoặc approval_bypass_count của hidden

## Vì sao shape này

Chọn **workflow gốc đã benchmark**, giữ lời gọi draft mặc định. Nhiệm vụ LogiX-VN có luật nghiệp vụ và thứ tự kiểm tra rõ: đọc claim, kiểm phạm vi sở hữu đơn, tính quote bằng code, rồi yêu cầu host ghi refund. Cả ba shape nhóm viết đều đúng trên public, nên chưa có bằng chứng rằng tăng mức tự chủ hoặc thêm investigator giúp tăng độ đúng. Workflow biểu diễn trực tiếp chuỗi kiểm tra đó, dùng ít model call nhất và không có handoff liên agent. Đây là lựa chọn theo mục tiêu lab: kiến trúc ít tự chủ nhất vẫn làm được việc. Quyết định chưa chứng minh workflow tốt hơn trên hidden hoặc production.

Số liệu cùng 14 claim, seed `11,22,33,44,55`, MockLLM và bảng giá, trích từ [benchmark.json](../../src/reports/benchmark.json):

| Shape | Pass / trial | Pass^5 | Seed min–max / variance | Cost/success mô phỏng (VND) | Mean / p95 steps | Model calls trung bình |
| --- | ---: | ---: | --- | ---: | --- | ---: |
| workflow | 70/70 | 1,000000 | 1–1 / 0 | 35,228571 | 4,428571 / 5 | 1,000000 |
| single | 70/70 | 1,000000 | 1–1 / 0 | 29,157143 | 4,571429 / 5 | 2,714286 |
| orchestrator | 70/70 | 1,000000 | 1–1 / 0 | 57,271429 | 6,000000 / 7 | 1,928571 |

- **Workflow:** luồng Python cố định; model chỉ soạn chữ từ status. `compute_compensation` tính tiền; host kiểm approval, trần theo đơn và idempotency. Ít cơ chế điều phối cần bảo trì đối với task này.
- **Single:** vòng `run_react(max_steps=8)` cho cấu trúc phù hợp khi cần chọn tool theo observation. Trong bản hiện tại, `decide` vẫn chọn tool bằng Python và không dùng chuỗi MockLLM trả về để chọn hành động. Vì vậy kết quả chưa thể hiện khả năng lập kế hoạch của một model thật. Host vẫn là nơi ghi tiền.
- **Orchestrator:** có ranh giới handoff allowlist và investigator chỉ đọc, hữu ích khi cần chia trách nhiệm hoặc quyền truy cập. Trong task hiện tại, investigator chạy các tool theo thứ tự; chưa có bằng chứng lợi ích từ chạy song song hay tăng chất lượng. Cost và số event trace cao nhất trong ba shape.

**Giới hạn khi so cost:** single thấp hơn workflow 6,071429 VND/success trong số đã ghi. Tuy nhiên workflow gọi `budget.add_tool(tool_cost())` trước `issue_refund`, còn single và orchestrator không ghi nhận tool cost cho yêu cầu này. Số model call, prompt và các tool đọc cũng khác nhau. Do đó bảng là cost theo instrumentation hiện tại, chưa phải so sánh đã chuẩn hóa mọi thao tác. Không kết luận single rẻ hơn do topology; không sửa metric, bảng giá hay JSON để điều chỉnh số liệu. Nếu tối ưu cost là ưu tiên chính, cần thí nghiệm chuẩn hóa cách ghi nhận thao tác trước khi quyết định lại.

## Có đáng đổi lấy multi-agent không

Trên public, orchestrator không tăng pass rate hoặc pass^5 so với workflow, trong khi cost/success được ghi cao hơn. Vì vậy chưa có bằng chứng để đổi lấy thêm điều phối cho bài toán hiện tại. Single là phương án cần xem xét nếu task phát triển thành nhiều nhánh chọn tool, nhưng cấu trúc ReAct hiện tại chưa tạo lợi ích độ đúng đo được.

Swarm được giữ làm đối chứng: 69/70 trial pass, pass rate 0,985714, pass^5 0,928571, variance 0,000816327. Trial `R-swarm-C-002-s55` fail vì tin số ngày trong ticket thay vì order; ba shape nhóm viết đều tránh được lỗi này.

Ablation R3 trong [research_card.md](research_card.md) chỉ bỏ lời gọi draft của workflow: cả hai điều kiện đạt 70/70; cost/success giảm từ 35,228571 xuống 34,285714 VND mô phỏng (2,676399%). Điều này ủng hộ việc rà từng thành phần phụ, không chứng minh multi-agent luôn kém. `draft_off` chỉ là điều kiện nghiên cứu; shape chọn vẫn là workflow mặc định với cost 35,228571.

Đổi quyết định nếu có bằng chứng mới: workflow không đạt hidden ≥ 0,75 hoặc vi phạm must-pass; task cần truy xuất/điều tra nhiều nguồn độc lập; hoặc một shape khác tăng độ đúng/khả năng hoàn thành sau khi giữ công bằng model, tool, seed, corpus, cách ghi cost và evaluator. Khi đó phải đo lại, kể cả chất lượng câu trả lời nếu sản phẩm cần giao tiếp với khách.

## Trace và bằng chứng

Workflow ghi event `claim` (found/has_order_id), `lookup_order` đã qua allowlist, `quote`, `issue_refund`, `decision`; khi có fault injection thì ghi `crash` rồi ném lại `InjectedCrash`. Kết quả tạo qua `common.finish`, lấy effect/audit từ ledger và ghi trace mới ở mọi nhánh trả kết quả bình thường.

Không ghi nguyên ticket, claim/order row thô, parent context, `parent_secret`, header, phone, address, internal note hoặc item name. Prompt draft chỉ chứa status. Handoff của orchestrator chỉ chứa field allowlist và tool đọc; investigator không có registry ghi refund.

Trace ở `src/traces/<run_id>.json`; scanner của repo đọc trace và các file `<run_id>*` liên quan. Không có debug dump riêng do nhóm thêm ngoài vùng này. Trace không commit; người chấm tái tạo bằng các lệnh trong [báo cáo nhóm](group_report.md). Test `test_workflow_bao.py` và evaluator public quét chuỗi cấm; đây là bằng chứng có phạm vi, không phải chứng minh tuyệt đối rằng hệ thống không bao giờ rò dữ liệu.

Crash suite chuẩn 20/20 chủ yếu kiểm host và có một ca swarm. Bằng chứng riêng cho workflow là T11/T12 trong ma trận student và hai cửa sổ còn lại trong `test_eval_nguyendacthe.py`: crash có thật, resume dùng cùng DB, ledger/audit được đối chiếu.

## Giới hạn

- Chưa có `student_drop/` hoặc kết quả hidden; không tự nhận đã đạt ngưỡng 40 claim × 5 seed hay các must-pass chính thức của grader hidden.
- MockLLM seeded không mô phỏng đầy đủ suy luận, tool selection hoặc lỗi của model sản xuất. `latency` là `unavailable`; cost là VND mô phỏng.
- Chỉ 14 claim public. Pass^5 ở đây là đúng trên cả năm seed của lab; không phải bảo đảm xác suất cho dữ liệu mới.
- Steps là số event trace, chịu ảnh hưởng cách ghi log; không dùng số này để suy ra latency hoặc độ phức tạp tính toán.
- Evaluator chấm status, quote, ledger, audit và trace; không chấm chất lượng câu chữ trả lời khách. Ablation draft không trả lời được câu hỏi chất lượng giao tiếp.
- Hash `fd7275e` chứa code và bằng chứng. Report ablation được tạo trước commit đó nên lưu HEAD `f26fa94`; SHA-256 trong report đã đối chiếu khớp nội dung nguồn tại `fd7275e`. Không sửa metadata JSON bằng tay.

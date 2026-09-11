# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *Ở temperature = 0.0, model chọn một câu trả lời "an toàn" nhất (sự thật về cà phê Việt Nam) và có thể sẽ lặp lại y hệt nếu chạy lại nhiều lần. Khi tăng lên 0.5 và 1.0, model chuyển sang một chủ đề khác (hang Sơn Đoòng) và hai lần chạy ở mức này cho nội dung gần giống nhau nhưng cách diễn đạt có khác biệt nhẹ. Ở 1.5, model quay lại chủ đề cà phê nhưng diễn đạt theo hướng khác — cho thấy nhiệt độ càng cao, model càng dễ "trôi" giữa các chủ đề/khả năng khác nhau thay vì bám chặt vào lựa chọn xác suất cao nhất. Ngoài ra, latency cũng tăng rõ rệt khi temperature > 0 (từ 6.6s lên tới ~19–20s), có thể do độ dài/phức tạp câu trả lời thay đổi theo tính ngẫu nhiên của quá trình sinh token.*

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> *Với chatbot hỗ trợ khách hàng, tôi sẽ đặt temperature ở mức thấp, khoảng 0.0–0.3. Lý do: khách hàng cần câu trả lời chính xác, nhất quán và có thể lặp lại được (ví dụ: chính sách đổi trả, hướng dẫn sử dụng sản phẩm) — không cần model "sáng tạo" hay đưa ra nhiều phương án diễn đạt khác nhau cho cùng một câu hỏi. Nhiệt độ cao dễ khiến hai khách hàng hỏi cùng một câu nhận được câu trả lời khác nhau, hoặc tệ hơn là model "trôi" sang thông tin không chính xác — điều này gây mất uy tín và có thể dẫn tới khiếu nại.*

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> *Do các lệnh gọi thực tế trong bài tôi đang dùng model miễn phí Nex-N2.5 (Pro/Mini) qua OpenRouter nên chi phí thật là \$0; số liệu chi phí dưới đây được tính theo bảng giá chính thức GPT-4o/GPT-4o-mini của OpenAI để minh hoạ mức chênh lệch. Với workload 10.000 người dùng/ngày, GPT-4o tốn khoảng 16,7 lần chi phí so với GPT-4o-mini — tương đương \$105/ngày so với \$6.3/ngày, chênh lệch gần \$3.000/tháng. Nên dùng GPT-4o khi tác vụ đòi hỏi suy luận phức tạp, độ chính xác cao và ảnh hưởng trực tiếp tới quyết định quan trọng, ví dụ: phân tích hợp đồng pháp lý, chẩn đoán lỗi kỹ thuật phức tạp. Nên dùng GPT-4o-mini cho các tác vụ khối lượng lớn, đơn giản, lặp lại — ví dụ: trả lời FAQ, phân loại yêu cầu khách hàng, tóm tắt ngắn — vì chênh lệch chất lượng không đủ lớn để biện minh cho chi phí gấp 16,7 lần ở quy mô hàng ngày này.*
---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *Persona "giáo viên tiểu học" tạo ra câu trả lời dài hơn nhiều (150 từ, độ trễ 24.4s) và sử dụng cách diễn đạt gần gũi, hình tượng — ví dụ ẩn dụ cuốn sổ ghi chép, ví dụ trẻ con chia kẹo — cùng cấu trúc liệt kê đơn giản, dễ hình dung. Persona "chuyên gia tài chính" cho câu trả lời ngắn gọn hơn (108 từ, độ trễ 7.6s), dùng thuật ngữ kỹ thuật chuyên ngành như "distributed ledger technology (DLT)", "hash", "timestamp" và trình bày theo cấu trúc định nghĩa – thành phần dữ liệu, không cần ví dụ minh hoạ đời thường. Điều này cho thấy system prompt không chỉ đổi giọng văn mà còn thay đổi cả mức độ chi tiết, từ vựng chuyên môn, và cách tổ chức thông tin của model — nó định hình toàn bộ "vai diễn" model đảm nhận trong suốt câu trả lời, đúng như vai trò "đạo diễn" của system prompt.*

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *Với đoạn văn 124 từ, ước lượng theo công thức "số từ / 0.75" ra 165,3 token, trong khi đếm thật bằng tiktoken ra 166 token — chênh lệch chỉ khoảng 0,4%, khá sát nhau trong trường hợp này. Tuy nhiên đây là một sự trùng hợp may mắn của đoạn văn cụ thể; nhìn chung tiếng Việt thường tốn nhiều token hơn tiếng Anh ở cùng độ dài, vì bộ mã hoá của tiktoken được huấn luyện chủ yếu trên văn bản tiếng Anh — các từ tiếng Việt có dấu thanh (á, à, ả, ã, ạ...) thường không khớp với các cụm ký tự phổ biến trong từ điển token, nên bị tách thành nhiều token nhỏ hơn (đôi khi tách theo từng byte UTF-8) thay vì được gộp thành một token trọn vẹn như các từ tiếng Anh thông dụng. Đây là một cân nhắc chi phí thực tế: cùng một nội dung, phục vụ người dùng Việt Nam có thể tốn chi phí token cao hơn đáng kể so với người dùng nói tiếng Anh.*

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *Streaming quan trọng nhất trong các ứng dụng chatbot tương tác trực tiếp với người dùng, nơi cảm giác phản hồi nhanh ảnh hưởng lớn đến trải nghiệm — ví dụ chatbot hỗ trợ khách hàng, trợ lý ảo, hay các câu trả lời dài. Trong thử nghiệm thực tế, khi hỏi "Thủ đô của Việt Nam là gì?" rồi tiếp "Thành phố đó có bao nhiêu quận?", chữ xuất hiện dần trên màn hình ngay khi model sinh ra, giúp người dùng cảm thấy hệ thống đang "suy nghĩ" và phản hồi ngay, dù tổng thời gian xử lý không đổi. Ngược lại, non-streaming phù hợp hơn khi kết quả cần được xử lý tiếp trước khi hiển thị — ví dụ khi output phải parse thành JSON, validate cấu trúc, hoặc dùng làm input cho một bước xử lý khác trong pipeline — vì lúc đó hiển thị từng phần dở dang không có ý nghĩa và có thể gây lỗi nếu người dùng thao tác trên dữ liệu chưa hoàn chỉnh.*

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *So với delay cố định, exponential backoff giúp giãn cách các lần retry ra xa nhau dần theo thời gian, cho server có đủ khoảng nghỉ tăng dần để phục hồi thay vì bị dội liên tục ở cùng một nhịp. Minh hoạ thực nghiệm với retry_with_backoff: lần gọi 1 thất bại, chờ 0.5s rồi thử lại (lần 2) vẫn thất bại, chờ tiếp 1.0s rồi thử lại (lần 3) mới thành công — khoảng cách tăng đúng gấp đôi qua mỗi lần, tổng cộng 1.5s cho 3 lần gọi. Nếu dùng delay cố định (ví dụ luôn chờ 1 giây) với hàng nghìn client cùng lỗi một lúc, tất cả sẽ đồng loạt retry sau đúng 1 giây, tạo thành một đợt sóng request thứ hai dội thẳng vào server ngay khi nó vừa kịp hồi phục — có thể khiến server tiếp tục quá tải hoặc sập hẳn, gọi là hiệu ứng "thundering herd". Exponential backoff phá vỡ sự đồng bộ này bằng cách kéo giãn thời điểm retry của từng client ra xa nhau dần, giảm áp lực dồn cục tại cùng một thời điểm.*

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Persona tôi chọn cho trợ lý: "Bạn là trợ lý học tập AI, chuyên giải thích các khái niệm lập trình và AI bằng tiếng Việt cho người mới bắt đầu. Trả lời ngắn gọn, tối đa 3–4 câu, luôn kèm một ví dụ thực tế dễ hình dung. Nếu không chắc chắn về thông tin, hãy nói rõ thay vì đoán." Hai lựa chọn từ ngữ quan trọng: (1) "trả lời ngắn gọn, tối đa 3-4 câu" — vì đây là trợ lý CLI chạy qua terminal, câu trả lời dài dòng vừa khó đọc trên dòng lệnh vừa tốn thêm token đầu ra không cần thiết, ảnh hưởng trực tiếp đến total_cost mà hệ thống đang theo dõi. (2) "nếu không chắc chắn, hãy nói rõ thay vì đoán" — vì đây là trợ lý học tập, việc bịa thông tin sai (hallucination) có thể khiến người học hiểu nhầm kiến thức nền tảng, nên ưu tiên minh bạch về giới hạn hiểu biết hơn là tạo cảm giác "biết tuốt".*

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Hạn chế lớn nhất hiện tại là history chỉ giữ 3 lượt gần nhất (6 message) — nếu người dùng quay lại hỏi tiếp về một chủ đề đã nhắc tới cách đây hơn 3 lượt, trợ lý sẽ hoàn toàn "quên", dẫn đến trải nghiệm rời rạc trong các phiên trò chuyện dài. Ngoài ra, trợ lý cũng không có bộ nhớ giữa các phiên (session) — mỗi lần chạy lại run_assistant, mọi ngữ cảnh trước đó mất sạch, và không có lớp kiểm duyệt nội dung để chặn các câu hỏi độc hại hoặc yêu cầu vượt phạm vi persona. Đề xuất cải thiện cụ thể: thêm một bước tóm tắt định kỳ (rolling summary) — cứ mỗi khi history sắp bị cắt bớt (ví dụ đạt 6 message), gọi thêm một lần API riêng để tóm tắt các lượt sắp bị xoá thành 1-2 câu, rồi chèn đoạn tóm tắt đó vào ngay sau system prompt dưới dạng một message system phụ (ví dụ: {"role": "system", "content": "Tóm tắt hội thoại trước: ..."}). Cách này giữ được ngữ cảnh dài hạn ở dạng nén, mà không làm phình chi phí input theo cấp số nhân như việc giữ nguyên toàn bộ lịch sử gốc.*

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026

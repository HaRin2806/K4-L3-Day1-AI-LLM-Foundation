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
> Khi tăng temperature từ 0.0 lên 1,5, phản hồi trở nên đa dạng và ít ổn định hơn. Ở temperature thất, câu trả lời khá trực tiếp và nhất quán , còn ở mức cao, model có xu hướng thya đổi cách diễn đạt và đôi khi tạo ra phản hồi bấ thường hoặc không hoàn chỉnh.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Em sẽ đặt temperature khoảng 0.2–0.3 cho chatbot hỗ trợ khách hàng. Vì chatbot cần trả lời ổn định, chính xác và nhất quán, hạn chế việc trả lời quá sáng tạo hoặc khác nhau giữa các lần hỏi cùng một vấn đề. Temperature thấp phù hợp với các tác vụ như tra cứu thông tin, hướng dẫn sử dụng và hỗ trợ khách hàng.
### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Qua kết quả test, gemini-3.6-flash có latency khoảng **3,17 giây**, trong khi gemini-3.5-flash-lite khoảng **16,45 giây** ở lần chạy này. gemini-3.6-flash cho câu trả lời ngắn nhưng bị cắt, còn gemini-3.5-flash-lite trả lời đầy đủ hơn.

Kết quả cho thấy chất lượng và latency thực tế có thể thay đổi theo từng lần gọi API, vì vậy không nên kết luận model nào luôn nhanh hơn chỉ từ một lần test. Về chi phí, GPT-4o-mini vẫn có lợi thế đáng kể khi workload lớn và tác vụ không yêu cầu khả năng xử lý quá phức tạp.


---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Hai phản hồi có sự khác nhau về cách diễn đạt và mức độ chuyên môn: phản hồi dành cho trẻ 8 tuổi dùng cách nói đơn giản như “Lớp học và Sổ điểm”, trong khi phản hồi của chuyên gia sử dụng các từ như “Definition” và “Idea”. Phản hồi của chuyên gia cũng có latency cao hơn (7,77 giây so với 3,53 giây), nhưng nội dung thực tế đều khá ngắn nên chưa thể kết luận phản hồi nào dài hơn. System prompt ảnh hưởng đến cách model lựa chọn từ vựng, mức độ chuyên môn và phong cách trình bày. Điều này cho thấy việc thay đổi persona trong system prompt có thể làm thay đổi đáng kể hành vi phản hồi của model.
### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với một đoạn văn tiếng Việt khoảng 100 từ, giả sử `count_tokens()` cho kết quả khoảng **X token**, trong khi cách ước lượng `số từ / 0.75` cho khoảng **133 token**. Như vậy, hai kết quả chênh nhau khoảng **Y%**. Sự khác biệt là do tiktoken không đếm token theo từ mà chia văn bản thành các đơn vị token dựa trên cách mã hóa của model. Tiếng Việt thường tốn nhiều token hơn tiếng Anh cùng độ dài vì cách viết có nhiều dấu và các chuỗi ký tự tiếng Việt thường được tokenizer chia thành nhiều token hơn.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi ứng dụng cần phản hồi nhanh và nội dung được sinh ra tương đối dài, chẳng hạn như chatbot, viết văn bản hoặc hỗ trợ lập trình, vì người dùng có thể bắt đầu đọc kết quả ngay thay vì phải chờ toàn bộ câu trả lời hoàn thành. Ngược lại, non-streaming phù hợp hơn khi cần nhận toàn bộ kết quả trước khi xử lý tiếp, ví dụ như các tác vụ yêu cầu dữ liệu hoàn chỉnh, xử lý JSON có cấu trúc hoặc khi giao diện không cần hiển thị từng phần kết quả.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp giảm áp lực lên API khi hệ thống đang quá tải bằng cách tăng dần thời gian chờ giữa các lần retry, từ đó tránh việc client liên tục gửi request trong thời gian ngắn. Nếu sử dụng delay cố định, chẳng hạn tất cả client đều retry sau đúng 1 giây, hàng nghìn client có thể gửi request lại cùng một thời điểm, tạo ra hiện tượng “thundering herd”, khiến API càng quá tải và có thể tiếp tục thất bại. Với exponential backoff, các lần retry được giãn ra theo thời gian, giúp hệ thống có cơ hội phục hồi và giảm khả năng quá tải dây chuyền.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> **Persona:** Trợ lý học tập Python.

**System prompt:**

> Bạn là một trợ lý học tập Python. Hãy giải thích các khái niệm và đoạn code một cách rõ ràng, chính xác và dễ hiểu. Ưu tiên trả lời ngắn gọn nhưng đầy đủ, tập trung vào vấn đề người dùng đang hỏi. Khi đưa ra code, sử dụng Python và giải thích ngắn gọn những phần quan trọng. Nếu người dùng mắc lỗi trong code, hãy chỉ ra nguyên nhân và đề xuất cách sửa cụ thể.

**Giải thích lựa chọn từ ngữ:**

* **"trợ lý học tập Python"**: Giới hạn vai trò của chatbot vào một lĩnh vực cụ thể, giúp câu trả lời tập trung hơn thay vì trả lời quá chung chung.
* **"ngắn gọn nhưng đầy đủ"**: Tránh câu trả lời dài không cần thiết nhưng vẫn yêu cầu chatbot cung cấp đủ thông tin để người học hiểu và áp dụng được.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> **Hạn chế lớn nhất:** History hiện tại chỉ lưu tối đa 3 lượt hỏi–đáp, tương đương 6 message. Vì vậy, nếu cuộc hội thoại dài, trợ lý có thể quên những thông tin hoặc yêu cầu đã được đề cập ở các lượt trước.

**Cải thiện đề xuất:** Có thể xây dựng bộ nhớ dài hạn bằng cách lưu các thông tin quan trọng của cuộc hội thoại vào một cấu trúc dữ liệu riêng hoặc database. Trước mỗi lần gọi API, hệ thống sẽ lấy lại những thông tin liên quan và thêm chúng vào prompt. Có thể kết hợp với cơ chế tóm tắt conversation để giảm số token sử dụng mà vẫn giữ được ngữ cảnh quan trọng.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026

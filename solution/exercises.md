# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng placeholder in nghiêng bên dưới
mỗi câu hỏi bằng câu trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *Ở temperature=0.0 và 0.5, bốn phản hồi gần như giống nhau về nội dung và cách diễn đạt (đều nói về hang Sơn Đoòng với các số liệu giống nhau). Khi tăng lên 1.0 và đặc biệt 1.5, model thay đổi cách diễn đạt, ví dụ "đám mây tự nhiên", câu văn đa dạng hơn. Điều này cho thấy temperature thấp giúp output ổn định, lặp lại; temperature cao làm tăng tính sáng tạo/đa dạng nhưng cũng tăng rủi ro lạc đề hoặc thêm chi tiết không kiểm chứng được.*

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> *Với chatbot hỗ trợ khách hàng, tôi sẽ chọn temperature khoảng 0.2–0.3. Lý do: khách hàng cần câu trả lời nhất quán, chính xác về chính sách/sản phẩm; temperature thấp giảm rủi ro model "bịa" thông tin (hallucination) hoặc trả lời không đồng nhất giữa các lần hỏi giống nhau.*

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> * Output/ngày = 10.000 × 3 × 350 = 10.500.000 token = 10.500 (nghìn token).
Chi phí gpt-4o = 10.500 × 0.010 = 105 USD/ngày.
Chi phí gpt-4o-mini = 10.500 × 0.0006 = 6.3 USD/ngày.
→ gpt-4o đắt hơn khoảng 16.7 lần so với mini cho cùng khối lượng output.
gpt-4o xứng đáng khi: cần lập luận phức tạp, phân tích nhiều bước (ví dụ tư vấn kỹ thuật chuyên sâu). Nên dùng mini khi: trả lời FAQ đơn giản, phân loại yêu cầu, hoặc các tác vụ khối lượng lớn không cần độ chính xác cao nhất.*

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *Với persona "giáo viên tiểu học", câu trả lời ngắn hơn (569 ký tự), dùng ví dụ đời thường ("cuốn sổ tay"), từ vựng đơn giản, không dùng thuật ngữ tiếng Anh. Với persona "chuyên gia tài chính", câu trả lời dài hơn (961 ký tự), có cấu trúc đề mục rõ ràng, dùng thuật ngữ kỹ thuật kèm chú thích tiếng Anh (distributed ledger technology, immutability...). Điều này cho thấy system prompt định hình rõ rệt giọng văn, độ sâu và từ vựng của model, dù cùng một câu hỏi.*

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *Với đoạn văn ~127 từ tiếng Việt: tiktoken đếm được 154 token, trong khi ước lượng số từ / 0.75 cho ra 169.3 token — chênh nhau khoảng 10%. Công thức 1 từ ≈ 1.3 token vốn được hiệu chỉnh cho tiếng Anh; tiếng Việt có dấu (ký tự Unicode tổ hợp) thường bị bộ mã hóa BPE của tiktoken tách thành nhiều token hơn mỗi từ so với tiếng Anh thuần ASCII, nên công thức ước lượng nhanh này không hoàn toàn chính xác cho tiếng Việt.*

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *Streaming quan trọng nhất khi phản hồi dài và người dùng tương tác trực tiếp (chatbot, trợ lý viết văn) — nó giảm cảm giác chờ đợi vì người dùng thấy chữ xuất hiện ngay lập tức thay vì phải đợi toàn bộ response. Non-streaming phù hợp hơn khi hệ thống cần xử lý toàn bộ output trước khi dùng tiếp (ví dụ parse JSON, đưa vào bước xử lý tự động khác), hoặc khi gọi API từ backend không có giao diện hiển thị theo thời gian thực.*

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *Nếu dùng delay cố định (ví dụ luôn 1 giây), khi API quá tải, tất cả client bị lỗi sẽ retry gần như cùng lúc sau đúng 1 giây, tạo ra một đợt request dồn dập mới ("thundering herd") khiến server tiếp tục quá tải. Exponential backoff giãn thời gian chờ tăng dần (0.1s, 0.2s, 0.4s...), giúp giảm số request đồng thời theo thời gian, cho server cơ hội hồi phục. Trong thực tế thường kết hợp thêm jitter (random hóa nhẹ) để các client không retry đồng bộ với nhau.*

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Persona tôi dùng: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt." Từ "ngắn gọn" giúp giảm token/chi phí và tránh trả lời lan man khi dùng trong CLI; chỉ định rõ "bằng tiếng Việt" đảm bảo model không tự chuyển sang tiếng Anh khi câu hỏi mơ hồ, giữ trải nghiệm nhất quán cho người dùng Việt.*

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Hạn chế lớn nhất: history chỉ giữ 3 lượt gần nhất (history[-6:]), nên trợ lý "quên" ngữ cảnh của các lượt trước đó trong phiên dài; ngoài ra chưa có bước kiểm duyệt nội dung đầu vào/đầu ra. Cải thiện đề xuất: lưu trữ lịch sử đầy đủ vào một vector store (ví dụ embeddings + FAISS), khi cần ngữ cảnh cũ thì truy vấn lại đoạn liên quan thay vì giữ nguyên toàn bộ trong prompt — vừa tiết kiệm token vừa giữ được "trí nhớ" dài hạn.*

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README

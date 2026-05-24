**Status:** 🚧 - **Last Updated:** 24th May 2026

# Table of Contents

- [Table of Contents](#table-of-contents)
  - [MCP Server](#mcp-server)
  - [Skills](#skills)
  - [✅ **Tools**](#-tools)
  - [✅ **Agent Team**](#-agent-team)
  - [✅ **Prompt Engineering**](#-prompt-engineering)
    - [1. Cách hoạt động của LLM](#1-cách-hoạt-động-của-llm)
    - [2. Các nguyên tắc cơ bản khi viết prompt](#2-các-nguyên-tắc-cơ-bản-khi-viết-prompt)
    - [3. Cấu trúc của một prompt hiệu quả](#3-cấu-trúc-của-một-prompt-hiệu-quả)
    - [4. Kỹ thuật Prompt cơ bản (Text-based Prompting)](#4-kỹ-thuật-prompt-cơ-bản-text-based-prompting)
      - [System Prompting](#system-prompting)
      - [Zero Shot Prompting](#zero-shot-prompting)
      - [Role Prompting](#role-prompting)
      - [Few Shot Prompting](#few-shot-prompting)
      - [Instruction Prompting](#instruction-prompting)
    - [5. Kỹ thuật tạo suy luận (Thought Generation)](#5-kỹ-thuật-tạo-suy-luận-thought-generation)
      - [Chain of Thought (CoT) Prompting](#chain-of-thought-cot-prompting)
      - [Self-Consistency Prompting](#self-consistency-prompting)
      - [Reflection Prompting](#reflection-prompting)
      - [Zero-Shot-CoT Prompting](#zero-shot-cot-prompting)
      - [Tree of Thought (ToT) Prompting](#tree-of-thought-tot-prompting)

## MCP Server

- [Figma MCP Server Setup Guide](https://developers.figma.com/docs/figma-mcp-server/) - A step-by-step guide to setting up a MCP (Model Control Plane) server for Figma, enabling developers to manage and deploy AI models effectively within the Figma ecosystem.

[↑ Back to top](#table-of-contents)

## Skills

- [Skills.sh](https://skills.sh/) - A platform that provides a comprehensive list of skills across various domains, including AI, programming, data science, and more. It helps users identify and learn the necessary skills for their desired career paths.
- [Open Design AI Skills](https://open-design.ai/skills/) - A resource that offers a curated list of skills related to open design and AI, providing insights and guidance for individuals looking to develop expertise in these areas.

[↑ Back to top](#table-of-contents)

## ✅ **Tools**

- [ChatGPT](https://chat.openai.com/), [Claude](https://www.anthropic.com/), [Gemini](https://gemini.com/), [Grok](https://grok.com/), [deepseek](https://deepseek.com/) - AI-powered search engines that provide advanced search capabilities and insights, allowing users to find relevant information quickly and efficiently.
- [Google AI Studio](https://ai.google/studio/), , [openrouter](https://openrouter.ai/) - Platforms that offer tools and resources for building and deploying AI models, providing users with the necessary infrastructure and support to create AI solutions.
- [kiwi](https://kiwi.com/) - An AI-powered travel search engine that helps users find and book flights, hotels, and other travel services, providing personalized recommendations and a seamless booking experience.
- [Opencode](https://opencode.ai/) - A platform that provides tools and resources for building and deploying AI models, offering a user-friendly interface for developers to create and manage their AI projects.
- [Ollama](https://ollama.com/) - A platform for building and deploying LLM agents, providing tools for creating intelligent agents that can perform a wide range of tasks using large language models.
- [Stitch](https://stitch.withgoogle.com/) - A tool that allows users to create and manage AI agents, providing a user-friendly interface for building and deploying AI solutions.
- [n8n](https://n8n.io/) - A workflow automation tool that allows users to connect various applications and services, enabling the automation of repetitive tasks and processes.
- [Hailuo AI](https://hailuoai.video/zh-Intl), [Piclumen](https://www.piclumen.com/) - AI-powered tools for generating images and videos, providing users with creative capabilities to produce high-quality visual content.

[↑ Back to top](#table-of-contents)

## ✅ **Agent Team**

- [BMAD](https://docs.bmad-method.org/) - A framework for building and managing AI agents, providing tools and best practices for creating effective and efficient AI systems.
- [obra/superpowers](https://github.com/obra/superpowers) - An agentic skills framework & software development methodology that works. `npx skills add obra/superpowers`
- [paperclip](https://github.com/paperclipai/paperclip) - Open-source orchestration for zero-human companies

[↑ Back to top](#table-of-contents)

## ✅ **Prompt Engineering**

- [Prompt Engineering Guide](https://drive.google.com/drive/folders/13QLaJbQYWfZNRfIzFolyz9or7ziTlSfx?usp=drive_link) - A comprehensive guide to prompt engineering, covering best practices, techniques, and examples for creating effective prompts for AI models.


### 1. Cách hoạt động của LLM

- Các từ trong câu hỏi được chuyển đổi thành token -> Các token được chuyển đổi thành vector số -> Mô hình xử lý và tạo ra phản hồi dựa trên vector đầu vào.

  > "What is the capital of France?" -> ["What", "is", "the", "capital", "of", "France", "?"] -> [0.1, 0.5, 0.3, 0.7, 0.2, 0.9, 0.4] -> "The capital of France is Paris."

### 2. Các nguyên tắc cơ bản khi viết prompt

**1. Rõ ràng & cụ thể**
- Dùng **động từ hành động mạnh**: *"Liệt kê", "Phân tích", "So sánh", "Viết lại"* thay vì *"Bạn có thể... được không"*.
- Tránh từ mơ hồ (*"một vài", "khá là", "tốt"*); thay bằng số liệu/tiêu chí cụ thể.

  > ❌ "Gợi ý vài món ăn cho bữa tối nhé."
  >
  > ✅ "Gợi ý **3 món ăn tối** cho 2 người, nguyên liệu dễ mua ở chợ Việt Nam, nấu **dưới 30 phút**, mỗi món kèm danh sách nguyên liệu."

**2. Cung cấp đầy đủ ngữ cảnh**
- Nêu rõ **bối cảnh, mục tiêu, đối tượng đọc, ràng buộc** ngay trong prompt — AI không tự đoán được.

  > "Tôi là sinh viên năm 3 ngành Marketing, đang chuẩn bị **thuyết trình 10 phút** trước lớp về *xu hướng quảng cáo TikTok 2025*. Hãy giúp tôi soạn **dàn ý 5 phần**, văn phong trẻ trung, có số liệu thực tế."

**3. Tuỳ biến theo đối tượng**
- Điều chỉnh **độ phức tạp, thuật ngữ, giọng văn** theo trình độ người đọc.

  > Người mới: "Giải thích *lãi kép* cho học sinh lớp 9 bằng ví dụ tiền tiết kiệm heo đất."
  >
  > Chuyên gia: "Phân tích chiến lược DCA (Dollar-Cost Averaging) với danh mục ETF, so sánh với lump-sum investing."

**4. Chia nhỏ yêu cầu phức tạp**
- Tác vụ lớn → tách thành **các bước nhỏ**, hỏi lần lượt để dễ kiểm soát chất lượng.

  > Thay vì "Giúp tôi khởi nghiệp quán cà phê", hãy hỏi:
  > 1. "Liệt kê các bước mở một quán cà phê nhỏ tại Hà Nội."
  > 2. "Chi phí ước tính cho từng bước là bao nhiêu?"
  > 3. "Gợi ý 5 ý tưởng concept quán hợp với khách văn phòng."

**5. Dùng dấu phân cách & định dạng prompt**
- Tách phần **hướng dẫn / ngữ cảnh / dữ liệu / ví dụ** bằng `###`, `---`, `"""` hoặc tiêu đề rõ ràng → AI hiểu cấu trúc tốt hơn.

  > ```
  > ### Nhiệm vụ ###
  > Viết lại đoạn review dưới đây theo giọng chuyên nghiệp, dưới 80 từ.
  >
  > ### Review gốc ###
  > """ Quán này ngon vãi luôn, nhân viên nhiệt tình cực kỳ, giá hơi chát tí nhưng đáng đồng tiền! """
  > ```

**6. Nhấn mạnh yêu cầu bắt buộc & lặp – tinh chỉnh**
- Dùng từ **"phải", "bắt buộc", "không được"** cho các ràng buộc quan trọng.
- Không kỳ vọng prompt **đúng ngay lần đầu** — đọc kết quả, chỉnh prompt rồi thử lại nhiều lần (*iterate*).

  > "Viết caption Facebook quảng bá khoá học tiếng Anh online. **Bắt buộc**: dưới 60 từ, có 1 câu hỏi mở đầu, kết thúc bằng call-to-action. **Không được** dùng emoji và từ 'tuyệt vời'."

[↑ Back to top](#table-of-contents)

### 3. Cấu trúc của một prompt hiệu quả

Một prompt hiệu quả thường tuân theo công thức **PROMPT** — gồm 6 yếu tố:

| Chữ cái | Ý nghĩa | Mô tả chi tiết |
|---|---|---|
| **P – Purpose** | Mục đích chính | Bạn muốn AI làm gì? (viết, phân tích, dịch, tóm tắt, hỗ trợ lập trình, v.v.) |
| **R – Role** | Vai trò AI | Định nghĩa vai trò AI đang đóng (giáo viên, chuyên gia marketing, lập trình viên, bác sĩ, v.v.) |
| **O – Output** | Kết quả mong muốn | Bạn mong chờ định dạng gì? (bảng, đoạn văn, danh sách, mã code, slide, email, v.v.) |
| **M – Method** | Phương pháp xử lý | Có cần dùng phương pháp nào cụ thể không? (phân tích SWOT, so sánh, kể chuyện, v.v.) |
| **P – Parameters** | Thông số bổ sung | Giới hạn ký tự, thời gian, chủ đề cụ thể, đối tượng người đọc, v.v. |
| **T – Tone** | Giọng điệu | Trang trọng, thân mật, hài hước, học thuật, thuyết phục, v.v. |

**Ví dụ prompt hoàn chỉnh đáp ứng đầy đủ 6 yếu tố PROMPT:**

> **[P – Purpose]** Hãy giúp tôi **viết một bài blog** giới thiệu về lợi ích của việc thiền định mỗi sáng đối với dân văn phòng.
>
> **[R – Role]** Bạn đóng vai một **huấn luyện viên sức khỏe tinh thần (mental wellness coach)** với 8 năm kinh nghiệm hướng dẫn thiền cho người đi làm tại các đô thị lớn.
>
> **[O – Output]** Trả kết quả dưới dạng **bài blog Markdown** có cấu trúc: 1 tiêu đề hấp dẫn, 1 đoạn mở bài (~80 từ), 4 đoạn thân bài (mỗi đoạn có heading `##`), và 1 đoạn kết kèm lời kêu gọi hành động. Cuối bài thêm bảng tóm tắt "5 bước thiền 10 phút mỗi sáng".
>
> **[M – Method]** Sử dụng phương pháp **kể chuyện (storytelling)** — bắt đầu bằng một tình huống thực tế của một nhân viên văn phòng bị stress, sau đó dẫn dắt sang các lợi ích khoa học (trích dẫn ít nhất 2 nghiên cứu có thật).
>
> **[P – Parameters]** Độ dài **khoảng 800–1000 từ**. Đối tượng đọc: **nhân viên văn phòng 25–35 tuổi tại Việt Nam**, ít hoặc chưa từng thiền. Tránh thuật ngữ tôn giáo, ưu tiên góc nhìn khoa học – đời sống.
>
> **[T – Tone]** Giọng văn **thân mật, truyền cảm hứng, gần gũi**, xen lẫn vài câu hỏi tu từ để tạo cảm giác trò chuyện với người đọc.

[↑ Back to top](#table-of-contents)

### 4. Kỹ thuật Prompt cơ bản (Text-based Prompting)

#### System Prompting

**Khái niệm:** Đặt "chỉ thị nền" cho AI trước khi trò chuyện, quy định **vai trò, phong cách và giới hạn** mà AI tuân theo suốt phiên.

**Ưu điểm**
- Giữ vai trò & giọng điệu **nhất quán**.
- **Tiết kiệm token**, không cần lặp bối cảnh.
- Dễ **kiểm soát phạm vi** trả lời.

**Nhược điểm**
- Khó đổi vai trò giữa chừng (phải reset).
- Có thể bị **prompt injection / jailbreak**.
- Quá nghiêm ngặt → AI từ chối cả yêu cầu hợp lý.

**Ứng dụng thực tế**
- **Chatbot CSKH / trợ lý ảo** trong website, app (Shopee, Tiki, ngân hàng...).
- **Trợ lý nội bộ doanh nghiệp**: tra cứu chính sách HR, hỗ trợ IT helpdesk.
- **AI Agent chuyên biệt**: code reviewer, tutor, legal assistant, medical assistant.

**Ví dụ**

> **System:** "Bạn là trợ lý CSKH của shop **LUMI**. Chỉ trả lời về sản phẩm, đơn hàng, đổi trả, khuyến mãi. Xưng *LUMI*, gọi khách *Quý khách*, lịch sự & ngắn gọn."
>
> **User:** "Áo sơ mi trắng size M còn hàng không?"
>
> **AI:** "Dạ Quý khách, áo sơ mi trắng size M vẫn còn hàng tại LUMI ạ. Quý khách có muốn đặt ngay không?"

[↑ Back to top](#table-of-contents)

#### Zero Shot Prompting

**Khái niệm:** Đặt câu hỏi/yêu cầu trực tiếp cho AI **mà không cung cấp ví dụ mẫu**, dựa hoàn toàn vào kiến thức sẵn có của mô hình.

**Ưu điểm**
- **Nhanh & gọn**, viết prompt cực ngắn.
- **Tiết kiệm token**, không tốn chỗ cho ví dụ.
- Phù hợp các tác vụ **phổ biến, đơn giản** (dịch, tóm tắt, phân loại cơ bản).

**Nhược điểm**
- Kết quả **kém ổn định** với tác vụ phức tạp hoặc chuyên ngành.
- Khó kiểm soát **định dạng & phong cách** đầu ra.
- Dễ "ảo giác" (hallucinate) khi AI không rõ ngữ cảnh.

**Ứng dụng thực tế**
- **Dịch & tóm tắt nhanh** văn bản, email, bài báo.
- **Phân loại cảm xúc / chủ đề** (sentiment, topic tagging).
- **Q&A nhanh, brainstorm ý tưởng**, viết caption, gợi ý tiêu đề.
- Tích hợp trong các tác vụ **một lần (one-shot task)** trên ChatGPT, Gemini, Claude.

**Ví dụ**

> **Prompt:** "Phân loại cảm xúc của câu sau là *Tích cực*, *Tiêu cực* hay *Trung tính*: 'Hôm nay trời đẹp, tôi cảm thấy rất vui.'"
>
> **AI:** "Tích cực."

[↑ Back to top](#table-of-contents)

#### Role Prompting

**Khái niệm:** Gán cho AI một **vai trò cụ thể** (chuyên gia, giáo viên, luật sư...) ngay trong prompt để định hướng cách trả lời theo đúng chuyên môn và phong cách của vai trò đó.

**Ưu điểm**
- Câu trả lời **chuyên sâu, đúng ngữ cảnh** hơn.
- Dễ kiểm soát **văn phong & góc nhìn**.
- Tăng độ **thuyết phục và tin cậy** cho output.

**Nhược điểm**
- AI có thể "diễn vai" quá đà → **bịa thông tin** nghe có vẻ chuyên gia.
- Phụ thuộc vào **kiến thức nền** của mô hình; vai trò quá hẹp dễ sai.
- Không thay thế được chuyên gia thật trong lĩnh vực rủi ro cao (y tế, pháp lý).

**Ứng dụng thực tế**
- **Học tập**: AI đóng vai gia sư, giảng viên, người luyện phỏng vấn.
- **Công việc**: vai chuyên gia marketing, code reviewer, copywriter, HR.
- **Sáng tạo**: nhập vai nhân vật để viết truyện, kịch bản, role-play game.

**Ví dụ**

> **Prompt:** "Bạn là một **chuyên gia marketing 10 năm kinh nghiệm**. Hãy gợi ý 3 ý tưởng chiến dịch quảng bá sản phẩm trà sữa mới cho gen Z, kèm thông điệp chính."
>
> **AI:** "1. *'Trà sữa của tụi mình'* – chiến dịch UGC trên TikTok... 2. ... 3. ..."

[↑ Back to top](#table-of-contents)


#### Few Shot Prompting

**Khái niệm:** Cung cấp **một vài ví dụ mẫu (input → output)** trong prompt để AI học theo pattern và trả lời các trường hợp mới theo đúng định dạng/phong cách mong muốn.

**Ưu điểm**
- Kết quả **chính xác & nhất quán** hơn Zero-shot.
- Dễ ép AI theo **định dạng cố định** (JSON, bảng, template).
- Không cần fine-tune, chỉ cần vài ví dụ là dùng được.

**Nhược điểm**
- **Tốn token** hơn do phải kèm ví dụ.
- Chất lượng phụ thuộc nhiều vào **ví dụ được chọn**; ví dụ kém → kết quả lệch.
- Khó áp dụng nếu **không có sẵn ví dụ chuẩn**.

**Ứng dụng thực tế**
- **Dạy AI gắn nhãn** dữ liệu: ví dụ cho vài bình luận "tốt/xấu" rồi để AI tự phân loại các bình luận còn lại.
- **Bắt AI trả lời theo đúng khuôn mẫu**: ví dụ trích xuất tên, email, số điện thoại từ văn bản ra **dạng JSON**.
- **Viết hàng loạt nội dung cùng phong cách**: caption Facebook, mô tả sản phẩm, email chăm sóc khách hàng — chỉ cần đưa 2–3 mẫu, AI viết tiếp theo đúng "tông".

**Ví dụ**

> **Zero Shot Prompt:**
> Viết một slogan cho quán cafe
>
> **Few Shot Prompt:**
> Viết một slogan cho quán cafe. Ví dụ:
> - "Cà phê ABC – Nơi khơi nguồn cảm hứng."
> - "Cà phê XYZ – Hương vị đậm đà, tình thân gắn kết."

[↑ Back to top](#table-of-contents)

#### Instruction Prompting

**Khái niệm:** Ra **chỉ thị rõ ràng, chi tiết** cho AI biết **phải làm gì, làm như thế nào, kết quả ra sao** — thay vì hỏi mơ hồ. Prompt giống như "đơn đặt hàng" có yêu cầu cụ thể.

**Ưu điểm**
- Kết quả **đúng ý, ít phải chỉnh sửa**.
- Dễ kiểm soát **độ dài, định dạng, phong cách**.
- Phù hợp với hầu hết tác vụ, **không cần ví dụ mẫu**.

**Nhược điểm**
- Prompt **dài và mất công viết** hơn Zero-shot.
- Nếu chỉ thị **mâu thuẫn hoặc thiếu rõ ràng**, AI dễ hiểu sai.
- Quá nhiều ràng buộc → AI có thể bỏ sót yêu cầu.

**Ứng dụng thực tế**
- **Viết nội dung theo yêu cầu cụ thể**: "Viết email xin nghỉ phép 3 ngày, giọng lịch sự, dưới 100 từ".
- **Tóm tắt / dịch có ràng buộc**: "Tóm tắt bài báo thành 5 gạch đầu dòng, mỗi ý dưới 20 từ".
- **Hỗ trợ công việc hằng ngày**: tạo checklist, lên kế hoạch, viết báo cáo theo cấu trúc cho sẵn.

**Ví dụ**

> **Prompt:** "Hãy viết một đoạn giới thiệu bản thân cho CV. Yêu cầu: dưới 80 từ, giọng tự tin, nêu rõ 3 năm kinh nghiệm lập trình web, thành thạo React và Node.js, có khả năng làm việc nhóm."
>
> **AI:** "Tôi là lập trình viên web với 3 năm kinh nghiệm phát triển ứng dụng bằng React và Node.js. Tôi có khả năng làm việc nhóm tốt, chủ động học hỏi và luôn hướng đến sản phẩm chất lượng cao..."

[↑ Back to top](#table-of-contents)

### 5. Kỹ thuật tạo suy luận (Thought Generation)

#### Chain of Thought (CoT) Prompting

**Khái niệm:** Yêu cầu AI **suy nghĩ từng bước** (step-by-step) trước khi đưa ra đáp án cuối, thay vì trả lời ngay. Thường thêm câu *"Hãy suy luận từng bước"* vào prompt.

**Ưu điểm**
- **Tăng độ chính xác** với bài toán phức tạp (toán, logic, lập luận).
- **Minh bạch**: thấy rõ AI suy nghĩ thế nào → dễ kiểm tra, debug.
- Giúp AI **tự sửa sai** trong quá trình lập luận.

**Nhược điểm**
- Câu trả lời **dài, tốn token & thời gian** hơn.
- Bước suy luận có thể **sai logic** nhưng vẫn ra kết quả nghe hợp lý.
- Không cần thiết cho các tác vụ đơn giản.

**Ứng dụng thực tế**
- **Giải toán, bài tập logic**: AI trình bày từng bước như học sinh giải bài.
- **Ra quyết định / phân tích**: cân nhắc ưu-nhược điểm trước khi kết luận (chọn công nghệ, đầu tư, tuyển dụng).
- **Debug code, phân tích lỗi**: AI lần lượt kiểm tra từng phần để tìm nguyên nhân.

**Ví dụ**

> **Prompt:** "An có 5 quả táo. An cho Bình 2 quả, rồi mua thêm 3 quả. Hỏi An còn bao nhiêu quả? **Hãy suy luận từng bước.**"
>
> **AI:**
> "Bước 1: An có 5 quả.
> Bước 2: Cho Bình 2 quả → còn 5 − 2 = 3 quả.
> Bước 3: Mua thêm 3 quả → 3 + 3 = 6 quả.
> **Đáp án: 6 quả táo.**"

[↑ Back to top](#table-of-contents)

#### Self-Consistency Prompting

**Khái niệm:** Yêu cầu AI **trả lời cùng một câu hỏi nhiều lần** (theo nhiều hướng suy luận khác nhau), sau đó **chọn đáp án xuất hiện nhiều nhất / được số đông đồng thuận** làm kết quả cuối. Là phiên bản nâng cấp của Chain-of-Thought.

**Ưu điểm**
- **Tăng độ chính xác** rõ rệt với bài toán phức tạp, dễ sai lệch.
- **Giảm rủi ro AI "ảo giác"** — một lần sai vẫn còn nhiều lần khác để đối chiếu.
- Dễ áp dụng, **không cần fine-tune** mô hình.

**Nhược điểm**
- **Tốn nhiều token & chi phí** (chạy 5–10 lần thay vì 1 lần).
- **Chậm hơn** đáng kể so với prompting thông thường.
- Không hiệu quả nếu mô hình **liên tục sai theo cùng một kiểu**.

**Ứng dụng thực tế**
- **Giải toán, suy luận logic**: chạy 5 lần, lấy đáp án phổ biến nhất → kết quả ổn định hơn.
- **Ra quyết định quan trọng**: ví dụ chẩn đoán sơ bộ, đánh giá rủi ro — lấy ý kiến "đa số" từ AI.
- **Chấm điểm / phân loại nhạy cảm**: lọc nội dung độc hại, gắn nhãn dữ liệu cần độ tin cậy cao.

**Ví dụ**

> **Prompt (chạy 5 lần):** "Một cửa hàng giảm giá 20% một chiếc áo giá gốc 500.000đ, rồi giảm thêm 10% trên giá đã giảm. Giá cuối cùng là bao nhiêu? Hãy suy luận từng bước."
>
> **Lần 1 → 360.000đ** | **Lần 2 → 360.000đ** | **Lần 3 → 350.000đ** | **Lần 4 → 360.000đ** | **Lần 5 → 360.000đ**
>
> **Đáp án cuối (đa số):** **360.000đ** ✅

[↑ Back to top](#table-of-contents)

#### Reflection Prompting

**Khái niệm:** Sau khi AI trả lời, **yêu cầu nó tự nhìn lại, đánh giá và sửa chữa** câu trả lời của chính mình. AI đóng vai trò *người làm* lẫn *người chấm bài* trên cùng một output.

**Ưu điểm**
- **Tự phát hiện & sửa lỗi** logic, sai sót, thiếu sót trong câu trả lời ban đầu.
- **Nâng chất lượng output** mà không cần can thiệp thủ công.
- Hữu ích cho tác vụ đòi hỏi **độ chính xác & chiều sâu**.

**Nhược điểm**
- **Tốn token, chậm hơn** vì AI phải trả lời 2 lần (gốc + phản tư).
- AI có thể **"tự khen mình đúng"** thay vì thực sự sửa sai.
- Không hiệu quả nếu lỗi xuất phát từ **kiến thức nền sai** của mô hình.

**Ứng dụng thực tế**
- **Kiểm tra & chỉnh sửa code**: viết code trước, sau đó yêu cầu AI tự review tìm bug.
- **Viết & biên tập nội dung**: AI viết bài → tự đọc lại, chỉnh ngữ pháp, logic, văn phong.
- **Giải bài tập / suy luận**: AI giải xong → tự kiểm tra lại các bước trước khi chốt đáp án.

**Ví dụ**

> **Bước 1 — Prompt:** "Viết một đoạn 3 câu giới thiệu về Hà Nội."
>
> **AI:** "Hà Nội là thủ đô của Việt Nam, nằm ở miền Bắc. Thành phố có hơn 1000 năm lịch sử với nhiều di tích nổi tiếng. Hà Nội cũng có ẩm thực rất phong phú."
>
> **Bước 2 — Reflection:** "Hãy **tự đánh giá** đoạn trên về tính chính xác, mạch văn và sức hút. Sau đó **viết lại bản tốt hơn**."
>
> **AI:** "Đánh giá: thông tin đúng nhưng khô khan, thiếu cảm xúc. **Bản viết lại:** *Hà Nội — thủ đô nghìn năm văn hiến của Việt Nam — quyến rũ du khách bằng những con phố cổ kính, hồ Gươm thơ mộng và hương phở thơm nức mỗi sớm mai...*"-----------------------

[↑ Back to top](#table-of-contents)

#### Zero-Shot-CoT Prompting

**Khái niệm:** Kết hợp **Zero-Shot** (không cần ví dụ mẫu) với **Chain-of-Thought** (suy luận từng bước) — chỉ cần thêm câu *"Hãy suy luận từng bước"* (*"Let's think step by step"*) vào prompt là AI tự sinh ra chuỗi lập luận trước khi đưa đáp án.

**Ưu điểm**
- **Cực kỳ đơn giản**: chỉ thêm 1 câu, không cần ví dụ mẫu.
- **Tăng độ chính xác** đáng kể với bài toán logic, toán học, suy luận.
- **Tiết kiệm token** hơn Few-Shot-CoT (không cần kèm ví dụ).

**Nhược điểm**
- Vẫn có thể **suy luận sai logic** mà vẫn ra kết quả nghe hợp lý.
- Output **dài hơn**, chậm hơn so với Zero-Shot thuần.
- **Kém hiệu quả với mô hình nhỏ** — chỉ phát huy tốt trên LLM cỡ lớn.

**Ứng dụng thực tế**
- **Giải toán, câu đố logic nhanh**: chỉ cần thêm "suy luận từng bước" là AI làm như học sinh trình bày bài.
- **Phân tích tình huống công việc**: cân nhắc ưu-nhược trước khi đưa khuyến nghị.
- **Hỏi đáp suy luận đời thường**: tính tiền, lập kế hoạch, so sánh lựa chọn.

**Ví dụ**

> **Prompt:** "Một lớp có 30 học sinh, 60% là nữ. Nếu có thêm 5 bạn nam chuyển vào thì tỉ lệ nữ trong lớp là bao nhiêu? **Hãy suy luận từng bước.**"
>
> **AI:**
> "Bước 1: Số nữ ban đầu = 30 × 60% = 18 bạn.
> Bước 2: Số nam ban đầu = 30 − 18 = 12 bạn.
> Bước 3: Sau khi thêm 5 nam → tổng học sinh = 35, số nữ vẫn 18.
> Bước 4: Tỉ lệ nữ = 18 / 35 ≈ **51,4%**."

[↑ Back to top](#table-of-contents)

#### Tree of Thought (ToT) Prompting

**Khái niệm:** Yêu cầu AI **khám phá nhiều hướng suy luận song song** dưới dạng "cây" — mỗi nhánh là một cách tiếp cận, AI đánh giá từng nhánh và **chọn nhánh tốt nhất** để đi tiếp.

**Ưu điểm**
- **Khám phá nhiều phương án** trước khi quyết định → kết quả tối ưu hơn.
- **Tự loại bỏ hướng đi sai** sớm, tránh "đâm đầu vào ngõ cụt".
- Mạnh với bài toán **lập kế hoạch, sáng tạo, có nhiều cách giải**.

**Nhược điểm**
- **Rất tốn token & chi phí** vì phải sinh & đánh giá nhiều nhánh.
- **Chậm**, prompt phức tạp, khó viết đúng cách.
- Quá đà với các tác vụ đơn giản — dùng CoT là đủ.

**Ứng dụng thực tế**
- **Lập kế hoạch nhiều phương án**: ví dụ lên 3 lộ trình du lịch khác nhau, so sánh rồi chọn lộ trình tối ưu.
- **Giải bài toán có nhiều cách**: cờ vua, câu đố, tối ưu hoá.
- **Brainstorm sáng tạo**: nghĩ 3–4 concept khác nhau cho 1 chiến dịch marketing, đánh giá rồi chọn cái mạnh nhất.

**Ví dụ**

> **Prompt:** "Tôi có 5 triệu và 3 ngày nghỉ. Hãy đề xuất **3 phương án du lịch** trong nước khác nhau, đánh giá ưu-nhược của từng phương án, rồi chọn phương án tối ưu nhất."
>
> **AI:**
> "**Nhánh 1 — Đà Lạt:** Mát mẻ, chi phí ~4tr. Nhược: di chuyển xa từ HN.
> **Nhánh 2 — Ninh Bình:** Gần, đẹp, ~3tr. Nhược: ít hoạt động về đêm.
> **Nhánh 3 — Phú Quốc:** Biển đẹp nhưng vé bay ngốn ~3,5tr → ngân sách eo hẹp.
> → **Chọn Nhánh 2 (Ninh Bình):** cân bằng chi phí, thời gian và trải nghiệm tốt nhất."
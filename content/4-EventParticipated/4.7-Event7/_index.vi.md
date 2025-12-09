---
title: "Event 7"
date: 2025-09-18
weight: 7
chapter: false
pre: " <b> 4.7. </b> "
---

# AI-Driven Development Life Cycle: Tái tưởng tượng Kỹ thuật Phần mềm

## Mục Đích của Sự Kiện

- Giới thiệu AI-Driven Development Life Cycle (AI-DLC), một phương pháp tiếp cận chuyển đổi trong phát triển phần mềm, đặt AI làm cộng tác viên trung tâm trong toàn bộ quy trình phát triển phần mềm.
- Phương pháp này nhằm chuyển đổi phát triển phần mềm bằng cách tận dụng AI để xử lý các tác vụ thường ngày trong khi vẫn duy trì sự giám sát của con người, cuối cùng giải phóng các nhà phát triển để tập trung vào giải quyết vấn đề quan trọng và các giải pháp sáng tạo.
- Giới thiệu Kiro, một AI IDE giúp nhà phát triển chuyển từ ý tưởng đến sản xuất thông qua trải nghiệm nhà phát triển đơn giản hóa khi làm việc với các AI agent. Kiro rất giỏi trong 'vibe coding' nhưng vượt xa hơn thế—điểm mạnh của Kiro là đưa các nguyên mẫu đó vào hệ thống sản xuất với các tính năng như specs và hooks.

## Danh Sách Diễn Giả

- Toan Huynh
- My Nguyen

## Nội Dung Nổi Bật

### Phần 1: AI-Driven Development Life Cycle (AI-DLC)

#### Triết lý Cốt lõi

- **Thực thi do AI với Giám sát Con người:** AI-DLC đề xuất một điểm giữa giữa phát triển "AI-assisted" (cải thiện các tác vụ cụ thể) và phát triển "AI-autonomous" (cố gắng loại bỏ con người hoàn toàn).
- **Mô hình Tinh thần:** AI khởi tạo các quy trình làm việc bằng cách tạo kế hoạch, tìm kiếm làm rõ, và triển khai giải pháp. Con người tập trung vào cung cấp ngữ cảnh, xác thực kế hoạch, và đưa ra quyết định quan trọng.
- **Cộng tác Nhóm Động:** Các tác vụ thường ngày được chuyển giao cho AI, cho phép các nhóm con người tập trung vào giải quyết vấn đề sáng tạo và ra quyết định nhanh chóng trong không gian cộng tác.

#### Ba Giai đoạn của AI-DLC

Phương pháp này tái cấu trúc Software Development Life Cycle (SDLC) truyền thống thành ba giai đoạn tương tác cao:

1. **Inception (Mob Elaboration):**
   - AI chuyển đổi ý định kinh doanh thành các yêu cầu chi tiết và user stories.
   - Toàn bộ nhóm tích cực xác thực các đề xuất của AI và trả lời các câu hỏi làm rõ để đảm bảo phù hợp với mục tiêu kinh doanh.

2. **Construction (Mob Construction):**
   - AI đề xuất kiến trúc logic, mô hình domain, giải pháp code, và tests.
   - Nhóm cung cấp phản hồi thời gian thực về các quyết định kỹ thuật và lựa chọn kiến trúc.

3. **Operations:**
   - AI sử dụng ngữ cảnh tích lũy để quản lý infrastructure-as-code và deployments.
   - Con người cung cấp giám sát để đảm bảo bảo mật và ổn định.

#### Lợi ích Chính

- **Tốc độ:** Chu kỳ phát triển chuyển từ tuần sang "bolts" (giờ hoặc ngày).
- **Chất lượng:** Làm rõ liên tục đảm bảo sản phẩm phù hợp với ý định kinh doanh, trong khi AI thực thi các tiêu chuẩn tổ chức (bảo mật, design patterns).
- **Giữ lại Ngữ cảnh:** AI duy trì ngữ cảnh liên tục qua tất cả các giai đoạn, đảm bảo không có kiến thức nào bị mất giữa lập kế hoạch và coding.

### Phần 2: Kiro – IDE Agentic cho AI-DLC

Kiro là một Integrated Development Environment (IDE) AI-native mới được thiết kế để kết nối khoảng cách giữa "vibe coding" (prototyping) và xây dựng phần mềm sẵn sàng sản xuất. Nó tạo ra các công cụ cần thiết để triển khai các phương pháp như AI-DLC.

#### Khái niệm Cốt lõi: Phát triển dựa trên Spec

Kiro giải quyết vấn đề "black box" của AI coding nơi các mô hình đưa ra các giả định không được ghi chép. Nó buộc một cách tiếp cận có cấu trúc nơi AI xây dựng dựa trên các đặc tả rõ ràng ("specs") thay vì các prompt mơ hồ.

#### Tính năng Chính

**Specs (The "Brain"):**
- **Tạo Yêu cầu:** Kiro mở rộng một prompt đơn thành các user stories chi tiết với tiêu chí chấp nhận (sử dụng ký hiệu EARS).
- **Thiết kế Kỹ thuật:** Nó phân tích codebase để tạo sơ đồ luồng dữ liệu, TypeScript interfaces, và database schemas trước khi viết code.
- **Triển khai Tác vụ:** Nó chia nhỏ phát triển thành các tác vụ có trình tự (với dependencies, unit tests, và yêu cầu accessibility) để con người phê duyệt.

**Hooks (The "Guardian"):**
- Tự động hóa theo sự kiện hoạt động như một nhà phát triển cấp cao đang quan sát bạn.
- Ví dụ: Tự động cập nhật tests khi một component được lưu, làm mới READMEs khi APIs thay đổi, hoặc quét rò rỉ bảo mật trước khi commit.
- Hooks thực thi tính nhất quán và tiêu chuẩn coding của toàn nhóm một cách tự động.

**Đồng bộ hóa:** Kiro giữ specs và code đồng bộ. Nếu code thay đổi, Kiro có thể cập nhật specs, ngăn chặn documentation drift.

#### Tương thích & Hệ sinh thái

- **Tương thích VS Code:** Được xây dựng trên Code OSS, cho phép các nhà phát triển giữ các cài đặt và extensions hiện có của họ.
- **Model Context Protocol (MCP):** Hỗ trợ kết nối các công cụ chuyên biệt và nhà cung cấp ngữ cảnh.

## Những Gì Học Được / Giá trị rút ra

### Phương pháp AI-DLC

- **Cách tiếp cận Chuyển đổi:** Hiểu cách AI-DLC tái tưởng tượng kỹ thuật phần mềm bằng cách đặt AI ở trung tâm của quy trình phát triển thay vì chỉ coi nó như một trợ lý.
- **Cấu trúc Ba Giai đoạn:** Học cách phương pháp này tái cấu trúc SDLC thành các giai đoạn Inception, Construction, và Operations với sự cộng tác AI-con người ở mỗi giai đoạn.
- **Tốc độ và Chất lượng:** Nhận ra tiềm năng cho chu kỳ phát triển chuyển từ tuần sang giờ/ngày trong khi vẫn duy trì chất lượng thông qua làm rõ liên tục và các tiêu chuẩn được AI thực thi.
- **Giữ lại Ngữ cảnh:** Đánh giá cao cách AI duy trì ngữ cảnh liên tục qua tất cả các giai đoạn, đảm bảo không có kiến thức nào bị mất giữa lập kế hoạch và coding.

### Kiro IDE

- **Phát triển dựa trên Spec:** Hiểu cách Kiro giải quyết vấn đề "black box" bằng cách buộc AI xây dựng dựa trên các đặc tả rõ ràng thay vì các prompt mơ hồ.
- **Công cụ Sẵn sàng Sản xuất:** Học cách Kiro kết nối khoảng cách giữa prototyping ("vibe coding") và phần mềm sẵn sàng sản xuất thông qua các tính năng như specs và hooks.
- **Giám sát Tự động:** Nhận ra cách hooks hoạt động như các guardian tự động, thực thi tính nhất quán và tiêu chuẩn coding của toàn nhóm.
- **Tích hợp Hệ sinh thái:** Đánh giá cao khả năng tương thích của Kiro với VS Code và hỗ trợ Model Context Protocol (MCP).

### Kết nối giữa AI-DLC và Kiro

- **Phương pháp và Công cụ:** Hiểu rằng trong khi AI-DLC là phương pháp (cách thức và lý do tổ chức nhóm và quy trình làm việc xung quanh AI), Kiro là một công cụ (cái gì) cho phép sự thay đổi này.
- **Vận hành hóa:** Nhận ra cách tính năng "Specs" của Kiro vận hành hóa các giai đoạn "Inception" và "Construction" của AI-DLC bằng cách buộc AI lập kế hoạch và tìm kiếm xác thực trước khi thực thi, trong khi "Hooks" tự động hóa sự giám sát cần thiết trong giai đoạn "Operations".

## Trải nghiệm trong event

Sự kiện cung cấp một giới thiệu toàn diện về tương lai của kỹ thuật phần mềm thông qua AI-DLC và Kiro:

### Phương pháp Cách mạng

- **Thay đổi Mô hình:** Bài trình bày đã chứng minh rõ ràng cách AI-DLC đại diện cho một sự thay đổi cơ bản từ phát triển AI-assisted truyền thống sang một mô hình cộng tác nơi AI và con người làm việc như một nhóm thống nhất.
- **Cấu trúc Thực tế:** Cách tiếp cận ba giai đoạn (Inception, Construction, Operations) cung cấp một khung rõ ràng để hiểu cách AI có thể được tích hợp trong toàn bộ vòng đời phát triển.

### Công cụ Đổi mới

- **Tập trung Sản xuất:** Sự nhấn mạnh của Kiro về việc vượt ra ngoài prototyping đến các hệ thống sẵn sàng sản xuất đặc biệt có giá trị, giải quyết một khoảng trống phổ biến trong các công cụ AI coding.
- **Cách tiếp cận dựa trên Spec:** Khái niệm phát triển dựa trên spec cộng hưởng mạnh mẽ, vì nó giải quyết vấn đề quan trọng của AI đưa ra các giả định không được ghi chép.

### Ứng dụng Thực tế

- **Cộng tác Nhóm:** Sự nhấn mạnh về cộng tác nhóm động và chuyển giao các tác vụ thường ngày cho AI trong khi vẫn duy trì sự giám sát của con người cung cấp những hiểu biết thực tế để cải thiện quy trình làm việc phát triển.
- **Đảm bảo Chất lượng:** Sự tích hợp của hooks cho giám sát tự động và đồng bộ hóa giữa specs và code đã chứng minh cách AI có thể thực thi các tiêu chuẩn trong khi vẫn duy trì tính linh hoạt.

### Kết luận

Sự kiện đã thành công giới thiệu cả tầm nhìn chiến lược (AI-DLC) và công cụ thực tế (Kiro) cần thiết để chuyển đổi phát triển phần mềm. Sự kết hợp của phương pháp và triển khai cung cấp một con đường rõ ràng phía trước cho các nhóm muốn tận dụng AI trong khi vẫn duy trì kiểm soát và tiêu chuẩn chất lượng của con người.


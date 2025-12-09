---
title: "AWS cho các Ngành công nghiệp: 150 Mô hình và Đang tăng - Hướng dẫn về các Mô hình Generative AI cho Y tế và Khoa học Đời sống"
date: 2025-05-07
weight: 12
chapter: false
pre: " <b> 3.3. </b> "
---

Ngày đăng: 07-05-2025 – Tác giả: Malvika Viswanathan, Amrita Sarkar, và Stephanie Dattoli trong [Amazon Athena](https://aws.amazon.com/athena/), [Amazon Bedrock](https://aws.amazon.com/bedrock/), [Healthcare](https://aws.amazon.com/health/), [Industries](https://aws.amazon.com/industries/).

---

**Generative AI** đang định hình lại ngành Y tế và Khoa học Đời sống (HCLS). Từ AstraZeneca và Pfizer đến 3M và Allen Institute, các nhà đổi mới đang sử dụng AI để thúc đẩy quá trình khám phá thuốc, tăng năng suất của các nhà khoa học và hợp lý hóa các quy trình làm việc lâm sàng.

Khi việc áp dụng ngày càng tăng, nhu cầu về các mô hình được tùy chỉnh cho những thách thức riêng biệt của HCLS cũng vậy—từ tóm tắt văn bản y tế, phân loại hình ảnh DICOM, đến cải thiện hiệu quả của việc khám phá thuốc. Tuy nhiên, nhiều tổ chức vẫn dựa vào truyền miệng hoặc các sự kiện trong ngành để xác định các mô hình phù hợp, gây ra cả sự trở ngại và chậm trễ trong vòng đời phát triển AI.

Đó là lúc **Amazon Web Services (AWS) Marketplace** phát huy thế mạnh. Là danh mục được tuyển chọn lớn nhất về các mô hình AI cho y tế và khoa học đời sống, nó cung cấp quy trình mua sắm cấp doanh nghiệp đã được phê duyệt trước để hợp lý hóa việc triển khai ở quy mô lớn, tăng tốc độ khám phá các mô hình chuyên biệt của ngành và tích hợp nhanh chóng vào các quy trình lâm sàng, nghiên cứu hoặc vận hành. Dù bạn đang muốn cải thiện việc lựa chọn nhóm đối tượng, tăng tốc phát triển thuốc, hay mở rộng các quy trình làm việc về hình ảnh, AWS Marketplace giúp việc áp dụng mô hình generative AI phù hợp trở nên dễ dàng—và nhanh chóng.

Nhiều khách hàng đang chuyển sang áp dụng **AI Tác nhân (Agentic AI)** để cho phép các quy trình làm việc tự chủ trên chuỗi giá trị HCLS. Việc sử dụng đúng mô hình có thể giúp triển khai nhanh chóng các tác nhân để đưa ra các quyết định thông minh, phù hợp với ngữ cảnh mà không cần phải đào tạo mô hình.

Chúng tôi sẽ làm nổi bật một số mô hình generative AI phù hợp nhất hiện có trên AWS Marketplace, và khám phá các dịch vụ của AWS giúp vận hành AI ở quy mô lớn trong môi trường HCLS.

---

### **Tìm kiếm mô hình phù hợp**

Với việc nhiều tổ chức đã đặt nền móng cho việc áp dụng generative AI, chúng tôi đang thấy một sự thay đổi rõ rệt. Khách hàng ngày càng chuyển sang sử dụng các mô hình chuyên biệt cho từng ngành để giải quyết các thách thức phức tạp hơn. Một số khách hàng cũng đang tìm cách nâng cao các ứng dụng hiện có bằng các mô hình chuyên sâu, mang lại độ chính xác cao hơn, cải thiện tính an toàn và đạt được kết quả tốt hơn.

Sau đây là một số ví dụ nổi bật về cách các tổ chức hàng đầu đang tận dụng những mô hình chuyên biệt này (tất cả đều có sẵn trên AWS Marketplace):

* **Bio-FMs để nhận diện thuốc mới:** Các mô hình Generative AI có thể giúp dự đoán các đặc tính hấp thụ, phân phối, chuyển hóa và bài tiết (ADME) quan trọng của các ứng viên thuốc. Bằng cách phân tích cấu trúc phân tử và dữ liệu hóa lý, các mô hình này có thể dự báo một hợp chất sẽ hoạt động như thế nào trong cơ thể. **Evolutionary Scales’ ESMC models** là một ví dụ điển hình cho loại mô hình này.
* **Hoạt động y tế và an toàn bệnh nhân:** Generative AI đang đóng vai trò quan trọng trong việc giám sát an toàn thử nghiệm lâm sàng, đặc biệt là xác định các phản ứng thuốc có hại tiềm ẩn (ADR). Mô hình **John Snow Labs Adverse Drug Events (ADE)** được đào tạo trên hơn 400 loại thực thể y tế, cho phép phát hiện chính xác ADR trong các nguồn dữ liệu phi cấu trúc như mạng xã hội và ghi chú lâm sàng.
* **Thiết kế thử nghiệm lâm sàng:** Các mô hình của **QuantHealth** dự đoán các điểm cuối của thử nghiệm lâm sàng và việc đăng ký tham gia thử nghiệm. QuantHealth sở hữu thư viện mô hình phát triển lâm sàng được đào tạo trên dữ liệu của hàng triệu cuộc sống bệnh nhân, dự đoán độ an toàn và hiệu quả với độ chính xác 85% trong các lĩnh vực ung thư, tim mạch và bệnh tự miễn. Các mô hình này sẽ có mặt trên AWS Marketplace vào tháng 5 năm 2025.
* **Tối ưu hóa thử nghiệm lâm sàng:** Các tổ chức khoa học đời sống ngày càng tìm đến các mô hình ngôn ngữ lớn (LLM) trên **Amazon Bedrock**, như **Anthropic’s Claude**, để hợp lý hóa việc tạo tài liệu thử nghiệm lâm sàng (ví dụ: giao thức thử nghiệm đối chứng ngẫu nhiên). Việc tự động hóa này giúp các đội nghiên cứu tập trung vào thiết kế nghiên cứu và tuyển dụng bệnh nhân.
* **Các mô hình thị giác máy tính trong môi trường lâm sàng:** Được sử dụng cho một loạt các ứng dụng từ giám sát PPE đến hỗ trợ chẩn đoán. Ví dụ, mô hình **Diabetic Retinopathy Detector** của VITech Lab phân tích hình ảnh quét võng mạc để phát hiện và xếp hạng các bất thường liên quan đến bệnh tiểu đường, giúp ưu tiên các trường hợp khẩn cấp.
* **Phân tích hình ảnh bệnh lý để phát hiện ung thư sớm:** Mô hình **Bioptimus H-Optimus-O** là mô hình nền tảng lớn nhất thế giới về bệnh lý học, với 1,1 tỷ tham số. Nó cho phép phân loại cấp độ bản vá (patch-level) của các lát cắt bệnh lý, hỗ trợ phát hiện ung thư sớm và giảm sự chậm trễ trong chẩn đoán.
* **Tóm tắt y tế cho các quy trình làm việc lâm sàng:** Các mô hình như **John Snow Medical LLMs** được xây dựng có chủ đích để tóm tắt ghi chú xuất viện, báo cáo X-quang và kết quả bệnh lý. Chúng giúp các bác sĩ lâm sàng điều hướng qua các dữ liệu phi cấu trúc dày đặc.
* **Chẩn đoán và lập kế hoạch điều trị có hỗ trợ AI:** **Palmyra-Med 70B** của Writer là một LLM được thiết kế riêng cho y tế, đạt điểm chuẩn sinh học y tế 85,87%. Nó thực hiện nhận dạng thực thể nâng cao, trích xuất các khái niệm y tế từ văn bản phi cấu trúc, hỗ trợ ra quyết định lâm sàng.

---

### **Khám phá, tinh chỉnh và triển khai các mô hình generative AI trong ngành HCLS**

Khi bạn đã xác định được mô hình phù hợp, AWS cung cấp bộ công cụ đầy đủ để khám phá, tinh chỉnh và triển khai trong môi trường an toàn và tuân thủ.

#### **Amazon SageMaker Unified Studio – Phát triển và tinh chỉnh mô hình hoàn chỉnh**
Cung cấp môi trường tích hợp để xây dựng và tinh chỉnh các mô hình HCLS. Nó tích hợp liền mạch với Amazon EMR, AWS Glue, Amazon Athena, Amazon Redshift và các dịch vụ ML của SageMaker, cho phép:
* Tinh chỉnh mô hình bằng các bộ dữ liệu chuyên biệt của HCLS.
* Xử lý dữ liệu đa phương thức (có cấu trúc, văn bản, hình ảnh, omics).
* Phát triển và triển khai các ứng dụng generative AI hoàn chỉnh.

#### **Amazon Bedrock – Suy luận và đánh giá mô hình nhanh chóng**
Lý tưởng để triển khai nhanh các mô hình nền tảng (FMs) mà không cần quản lý cơ sở hạ tầng. Với hơn 160 mô hình FM, các tính năng chính bao gồm:
* **Amazon Bedrock Evaluations:** Đánh giá và so sánh các mô hình nhanh chóng.
* Truy cập dựa trên API vào các mô hình hàng đầu.
* Tích hợp liền mạch với SageMaker và các công cụ AWS khác.

#### **AWS HealthOmics – Tăng tốc Khám phá Khoa học**
Tăng tốc thông tin chuyên sâu sinh học bằng các quy trình làm việc Ready2Run (từ NVIDIA, Sentieon, Element Biosciences), hỗ trợ:
* Các Thực tiễn Tốt nhất của GATK từ Broad Institute.
* AlphaFold để dự đoán cấu trúc protein.
* Các đường ống khám phá thuốc độc quyền.

#### **Các Giải pháp Lưu trữ của AWS**
Cơ sở hạ tầng dữ liệu an toàn và có thể mở rộng:
* **Amazon S3:** Lưu trữ dữ liệu linh hoạt, đa năng.
* **AWS HealthImaging:** Chuyên dụng cho hình ảnh y tế.
* **AWS HealthOmics:** Tối ưu hóa cho dữ liệu omics.

---

### **Kết Luận**

Chúng tôi đã khám phá các trường hợp sử dụng chính trong ngành Y tế và Khoa học Đời sống (HCLS), nơi các mô hình generative AI chuyên biệt có thể mang lại những cải thiện đáng kể về hiệu suất kinh doanh và kết quả điều trị cho bệnh nhân.

Với các công cụ như **Amazon SageMaker** và **Amazon Bedrock**, AWS cung cấp một trong những lựa chọn mô hình phong phú nhất được tùy chỉnh cho các thách thức của HCLS.

**Các bước tiếp theo:**
1.  Khám phá danh mục các mô hình generative AI trên [AWS Marketplace](https://aws.amazon.com/marketplace).
2.  Làm việc với đội ngũ AWS để xác định mô hình phù hợp với mục tiêu lâm sàng.
3.  Hợp tác với Kiến trúc sư Giải pháp AWS hoặc Đối tác AWS HCLS để thiết kế kiến trúc mở rộng.

**Tìm hiểu thêm tại:**
* [Pre-training genomic language models using AWS HealthOmics and Amazon SageMaker](https://aws.amazon.com/blogs/industries/pre-training-genomic-language-models-using-aws-healthomics-and-amazon-sagemaker/)
* [John Snow Labs Medical LLMs are now available in Amazon SageMaker JumpStart](https://aws.amazon.com/blogs/machine-learning/john-snow-labs-medical-llms-are-now-available-in-amazon-sagemaker-jumpstart/)
* [Accelerate digital pathology slide annotation workflows on AWS using H-optimus-0](https://aws.amazon.com/blogs/industries/accelerate-digital-pathology-slide-annotation-workflows-on-aws-using-h-optimus-0/)

---

| ![Malvika Viswanathan](/images/3-BlogsTranslated/Blog_AWS_HCLS/malvika.jpg) | **Malvika Viswanathan** <br> Malvika Viswanathan là Trưởng nhóm Giải pháp GenAI cho ngành Y tế và Khoa học Đời sống tại AWS. Cô có 15 năm kinh nghiệm làm việc với các tổ chức cung cấp dịch vụ, công ty bảo hiểm và tổ chức khoa học đời sống. Malvika chuyên giúp khách hàng áp dụng công nghệ mới nhất để chuyển đổi doanh nghiệp và mang lại kết quả tối ưu cho bệnh nhân. |
| :--- | :--- |
| ![Amrita Sarkar](/images/3-BlogsTranslated/Blog_AWS_HCLS/amrita.jpg) | **Amrita Sarkar** <br> Amrita Sarkar, Tiến sĩ, là Principal trong đội ngũ Healthcare & Life Sciences Startups tại AWS. Cô làm việc với các nhà sáng lập và nhà đầu tư để giúp các startup HCLS xây dựng ở quy mô lớn. Trước đây, cô là nhà đầu tư mạo hiểm tại Paris và đã cố vấn cho hàng trăm startup. Cô có bằng Tiến sĩ Sinh học Tính toán và MBA từ Collège des Ingénieurs. |
| ![Stephanie Dattoli](/images/3-BlogsTranslated/Blog_AWS_HCLS/stephanie.jpg) | **Stephanie Dattoli** <br> Stephanie Dattoli là Giám đốc Toàn cầu phụ trách Marketing về Khoa học Đời sống và Genomics tại AWS. Cô chuyên sâu về giao điểm giữa khoa học đời sống và công nghệ đám mây, giúp các tổ chức đưa sản phẩm mới ra thị trường. Cô có bằng cao học về di truyền học từ Đại học Stanford. |
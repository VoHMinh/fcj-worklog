---
title: "Amazon SageMaker giới thiệu bộ lưu trữ chia sẻ dựa trên Amazon S3 để tăng cường cộng tác dự án"
date: 2025-09-16
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

Ngày đăng: 2025‑09‑16 – Tác giả: Hari Ramesh, Anagha Barve, Anchit Gupta, Saurabh Bhutyani và Zach Mitchell trong [Amazon SageMaker Unified Studio](https://aws.amazon.com/blogs/big-data/category/analytics/amazon-sagemaker-unified-studio/), [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/big-data/category/storage/amazon-simple-storage-services-s3/), [Announcements](https://aws.amazon.com/blogs/big-data/category/post-types/announcements/), [Intermediate (200)](https://aws.amazon.com/blogs/big-data/category/learning-levels/intermediate-200/),[Technical How-to](https://aws.amazon.com/blogs/big-data/category/post-types/technical-how-to/).

AWS gần đây đã công bố rằng [Amazon SageMaker](https://aws.amazon.com/sagemaker/) hiện cung cấp  [Amazon Simple Storage Service](https://aws.amazon.com/s3) (Amazon S3) làm lựa chọn lưu trữ file mặc định cho các dự án mới trong [Amazon SageMaker Unified Studio](https://aws.amazon.com/sagemaker/unified-studio/). Tính năng này giải quyết việc ngừng hỗ trợ [AWS CodeCommit](https://aws.amazon.com/codecommit/), đồng thời mang đến cho các nhóm một cách đơn giản và nhất quán để cộng tác trên các file dự án trong toàn bộ công cụ phát triển tích hợp của SageMaker.

Tùy chọn lưu trữ Amazon S3 mới này mang lại những lợi ích sau:

* **Hợp tác đơn giản** – Chia sẻ file trực tiếp giữa các thành viên dự án mà không cần thao tác Git.  
* **Truy cập đồng nhất** – File được truy cập nhất quán trên các công cụ SageMaker (JupyterLab, Query Editor, Visual ETL).  
* **Phân tách workspace rõ ràng** – Có sẵn phân tách lưu trữ cá nhân với [Amazon Elastic Block Store](https://aws.amazon.com/ebs) (Amazon EBS) volumes.  
* **Khả dụng toàn cầu** – Hỗ trợ ở tất cả các AWS Region mà SageMaker có mặt.

Mặc dù Amazon S3 là tùy chọn mặc định cho việc lưu trữ file, bạn vẫn có thể sử dụng Git version control nếu muốn có khả năng quản lý source code mạnh mẽ hơn.

Trong bài viết này, chúng ta sẽ thảo luận về tính năng mới và cách bắt đầu sử dụng **Amazon S3 shared storage** trong **SageMaker Unified Studio**.

---

**Tổng quan giải pháp**

Khi bạn tạo một **SageMaker Unified Studio domain** mới, dịch vụ sẽ tự động cấu hình **Amazon S3** làm tùy chọn lưu trữ mặc định cho dự án. Mỗi dự án sẽ nhận được một vùng lưu trữ chung chuyên biệt trong Amazon S3, khả dụng cho các thành viên dự án, với cấu trúc:

\[bucket\]/\[domain-id\]/\[project-id\]/shared/.

### Công cụ SageMaker (JupyterLab và Code Editor) cung cấp cho người dùng:

* Một **EBS volume cá nhân** để làm việc riêng trong JupyterLab và Code Editor.  
* Một thư mục chia sẻ được mount, chứa không gian lưu trữ dùng chung của dự án trên Amazon S3.  
* Phân tách rõ ràng giữa không gian cá nhân và không gian chia sẻ.

### Khả năng truy cập lưu trữ chia sẻ trong các công cụ phát triển tích hợp SageMaker:

* **JupyterLab** và **Code Editor** hiển thị file chia sẻ song song với file cá nhân.  
* **Query Editor** lọc các SQL notebook có liên quan.  
* **Visual ETL** cung cấp khả năng truy cập trực tiếp tới các workflow ETL (extract, transform, load) chia sẻ.

Các file được lưu vào thư mục dùng chung sẽ ngay lập tức khả dụng và hiển thị cho các thành viên dự án. Người dùng vẫn có thể tiếp tục làm việc với file cá nhân trong **EBS volumes** (ví dụ trong JupyterLab và Code Editor) và có thể chuyển file sang vùng lưu trữ chia sẻ khi sẵn sàng cộng tác. Nếu bạn muốn dùng Git cho việc cộng tác, bạn vẫn có thể làm điều đó bằng cách tích hợp dự án với GitHub, GitLab, hoặc các repository được quản lý trên Bitbucket.

---

## **Tùy chọn di chuyển và kiểm soát phiên bản**

Đối với các nhóm hiện đang sử dụng **Amazon CodeCommit**, các dự án hiện có vẫn sẽ hoạt động bình thường. Các dự án mới sẽ mặc định sử dụng lưu trữ **Amazon S3**. Nếu bạn muốn có chức năng **version control** cho các dự án dựa trên Amazon S3, bạn có thể bật tính năng **versioning trực tiếp** trong Amazon S3.

---

## **Các bước chuẩn bị**

Trước khi thực hiện các hướng dẫn trong phần tiếp theo, bạn cần hoàn thành các bước chuẩn bị sau:

1. [Đăng ký tài khoản AWS](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/adminguide/setting-up.html#sign-up-for-aws)**.**  
2. [Tạo người dùng có quyền quản trị (administrative access)](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/adminguide/setting-up.html#create-an-admin)**.**  
3. [Kích hoạt IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/enable-identity-center.html) trong cùng **AWS Region** mà bạn muốn tạo **SageMaker Unified Studio domain**. Xác nhận xem SageMaker Unified Studio hiện có sẵn ở Region nào. Thiết lập **Identity Provider (IdP)** và đồng bộ hóa danh tính (identities) và nhóm (groups) với **IAM Identity Center**. Để biết thêm chi tiết, tham khảo phần [IAM Identity Center Identity source tutorials](https://docs.aws.amazon.com/singlesignon/latest/userguide/tutorials.html).

---

## **Bắt đầu sử dụng Amazon S3 Shared Storage**

Để bắt đầu sử dụng **Amazon S3 shared storage**, hãy hoàn thành các bước sau:

1. Tạo một **SageMaker Unified Studio domain** mới.  

![](/images/3-BlogsTranslated/Blog2/img1.png)

2. Tạo một dự án mới (lưu trữ Amazon S3 sẽ được chọn mặc định làm tùy chọn lưu trữ file).  
![](/images/3-BlogsTranslated/Blog2/img2.png)

3. Mở dự án mới và chọn **JupyterLab** trong menu **Build**.  
![](/images/3-BlogsTranslated/Blog2/img3.png)

4. Lưu notebook mới mà bạn vừa tạo.  
![](/images/3-BlogsTranslated/Blog2/img4.png)

5. Đổi tên file theo ý bạn.  
   ![](/images/3-BlogsTranslated/Blog2/img5.png)


Sau khi dự án được lưu, người dùng trong dự án có thể xem notebook đã lưu trong phần **Project files** theo đường dẫn S3:

\[bucket\]/\[domain-id\]/\[project-id\]/shared/.

---
![](/images/3-BlogsTranslated/Blog2/img6.png)

### **Bật kiểm soát phiên bản với Git**

Để bật **version control** bằng Git, hãy thực hiện các bước sau:

1. Truy cập **SageMaker console** và tạo **project profile** mới.  
![](/images/3-BlogsTranslated/Blog2/img7.png)

2. Cung cấp đầy đủ thông tin cần thiết cho project profile của bạn.  
![](/images/3-BlogsTranslated/Blog2/img8.png)

3. Trong phần **Project files storage**, tùy chọn **Amazon S3** được chọn mặc định. Nếu muốn bật version control cho dự án, bạn có thể sử dụng các kết nối repository Git sẵn có bằng cách chọn **Git repository**.
![](/images/3-BlogsTranslated/Blog2/img9.png)


---

### **Sử dụng shared storage trong Query Editor**

Để sử dụng tính năng **shared storage** trong **Query Editor**, hãy thực hiện các bước sau:

1. Chọn **Query Editor** từ menu **Build**.  
![](/images/3-BlogsTranslated/Blog2/img10.png)

2. Soạn truy vấn của bạn, sau đó trong menu **Actions**, chọn **Save** để lưu truy vấn vào vùng lưu trữ chia sẻ.  
![](/images/3-BlogsTranslated/Blog2/img11.png)

3. Quay lại phần **Project files**, nơi bạn có thể xem các file query notebook nằm trong đường dẫn S3:  
   \[bucket\]/\[domain-id\]/\[project-id\]/shared/.
![](/images/3-BlogsTranslated/Blog2/img12.png)

---

## **Sử dụng shared storage trong Visual ETL flows**

Để sử dụng tính năng **shared storage** trong **Visual ETL flows**, hãy thực hiện các bước sau:

1. Chọn **Visual ETL flows** từ menu **Build**.  
![](/images/3-BlogsTranslated/Blog2/img13.png)

2. Phát triển workflow ETL của bạn và lưu mã vào dự án. 
![](/images/3-BlogsTranslated/Blog2/img14.png)

3. Quay lại phần **Project files**, nơi bạn có thể xem các file nằm trong đường dẫn S3: \[bucket\]/\[domain-id\]/\[project-id\]/shared/jobs/uploads/\<ETL name\>.
![](/images/3-BlogsTranslated/Blog2/img15.png)

---

## **Dọn dẹp tài nguyên**

Hãy đảm bảo bạn xóa các tài nguyên SageMaker Unified Studio sau khi hoàn tất để tránh phát sinh chi phí không mong muốn. Quy trình bao gồm các bước sau:

1. **Xóa các dự án (projects).**  
2. **Xóa domain.**  
3. **Xóa S3 bucket** có tên dạng:  
   amazon-datazone-AWSACCOUNTID-AWSREGION-DOMAINID

---

## **Kết luận**

Việc ra mắt tính năng Amazon S3 shared storage trong SageMaker là một bước tiến nữa trong việc đơn giản hóa trải nghiệm phát triển phân tích và học máy (ML) cho khách hàng. Bằng cách giảm độ phức tạp của các thao tác Git nhưng vẫn duy trì khả năng cộng tác mạnh mẽ, các nhóm có thể tập trung hơn vào việc xây dựng và triển khai các giải pháp phân tích và ML nhanh chóng hơn. Tính năng này hiện đã khả dụng tại các AWS Region có hỗ trợ SageMaker.

Để biết thông tin chi tiết về tính năng này, bao gồm hướng dẫn thiết lập và best practices, vui lòng tham khảo tài liệu [Unified storage in Amazon SageMaker Unified Studio](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/storage.html).

---

| ![](/images/3-BlogsTranslated/Blog2/author1.jpeg) | Hari Ramesh [Hari](https://www.linkedin.com/in/haryramesh/) là Senior Analytics Specialist Solutions Architect tại AWS. Ông tập trung vào việc xây dựng các nền tảng dữ liệu đám mây, cho phép trực tuyến theo thời gian thực, xử lý dữ liệu lớn và quản trị dữ liệu mạnh mẽ. |
| :---- | :---- |
| ![](/images/3-BlogsTranslated/Blog2/author2.jpg) | **Anagha Barve** [Anagha](https://www.linkedin.com/in/anagha-barve-9a86b8/) là Software Development Manager trong nhóm Amazon SageMaker Unified Studio. Nhóm của cô tập trung vào việc xây dựng các công cụ và trải nghiệm tích hợp cho các nhà phát triển bằng Amazon SageMaker Unified Studio. Trong thời gian rảnh rỗi, cô thích nấu ăn, làm vườn và du lịch. |
| 
| ![](/images/3-BlogsTranslated/Blog2/author3.jpg) | **Zach Mitchell** [Zach](https://www.linkedin.com/in/zachary-mitchell-ab882853/) là Sr. Big Data Architect. Anh ấy làm việc trong nhóm sản phẩm để tăng cường sự hiểu biết giữa các kỹ sư sản phẩm và khách hàng của họ đồng thời hướng dẫn khách hàng trong suốt hành trình phát triển hồ dữ liệu và các giải pháp dữ liệu khác trên các dịch vụ phân tích AWS. |
| 
| ![](/images/3-BlogsTranslated/Blog2/author4.jpg) | **Saurabh Bhutyani** [Saurabh](https://www.linkedin.com/in/s4saurabh/) là Principal Analytics Specialist Solutions Architect tại AWS. Anh đam mê công nghệ mới. Anh gia nhập AWS vào năm 2019 và làm việc với khách hàng để cung cấp hướng dẫn kiến ​​trúc cho việc vận hành các trường hợp sử dụng AI tạo sinh, các giải pháp phân tích có thể mở rộng và kiến ​​trúc lưới dữ liệu bằng các dịch vụ AWS như Amazon Bedrock, Amazon SageMaker, Amazon EMR, Amazon Athena, AWS Glue, AWS Lake Formation và Amazon DataZone. |
| 
| ![](/images/3-BlogsTranslated/Blog2/author5.jpg) | **Anchit Gupta** [Anchit](https://www.linkedin.com/in/anchitgupta92/) là Senior Product Manager cho Amazon SageMaker Studio. Cô tập trung vào việc tạo điều kiện cho các quy trình làm việc khoa học dữ liệu và kỹ thuật dữ liệu tương tác từ bên trong SageMaker Studio IDE. Trong thời gian rảnh rỗi, cô thích nấu ăn, chơi cờ bàn/bài và đọc sách. |
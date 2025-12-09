---
title: "Làm thế nào FINRA thiết lập khả năng quan sát vận hành thời gian thực cho các workload big data trên Amazon EMR chạy trên Amazon EC2 bằng Prometheus và Grafana"
date: 2024-11-15
weight: 10
chapter: false
pre: " <b> 3.1. </b> "
---

Ngày đăng: 2024-11-15 – Tác giả: Sumalatha Bachu, PremKiran Bejjam, và Akhil Chalamalasetty trong [AWS Big Data](https://aws.amazon.com/blogs/big-data/category/big-data/), [Customer Solutions](https://aws.amazon.com/blogs/big-data/category/post-types/customer-solutions/), [Monitoring and observability](https://aws.amazon.com/blogs/big-data/category/management-and-governance/monitoring-and-observability/).

Bài viết này là bài khách của FINRA (Financial Industry Regulatory Authority). FINRA cam kết bảo vệ nhà đầu tư và giữ vững tính liêm chính của thị trường theo cách thức thúc đẩy sự phát triển sôi động của các thị trường vốn.

[FINRA](https://www.finra.org/) thực hiện xử lý big data với khối lượng dữ liệu lớn và các workload với kích cỡ và loại instance khác nhau trên [Amazon EMR](http://aws.amazon.com/emr). Amazon EMR là một môi trường big data trên đám mây, được thiết kế để xử lý lượng dữ liệu lớn sử dụng các công cụ mã nguồn mở như Hadoop, Spark, HBase, Flink, Hudi và Presto.

Việc giám sát các cluster EMR là việc làm cần thiết để phát hiện các vấn đề nghiêm trọng với ứng dụng, cơ sở hạ tầng, hoặc dữ liệu theo thời gian thực. Một hệ thống giám sát được tinh chỉnh tốt sẽ giúp nhanh chóng xác định nguyên nhân gốc rễ, tự động sửa lỗi, giảm thiểu các tác vụ thủ công, và tăng năng suất. Ngoài ra, việc quan sát hiệu suất và mức độ sử dụng của cluster theo thời gian còn giúp các đội vận hành và kỹ thuật tìm ra những điểm nghẽn hiệu suất tiềm tàng và các cơ hội tối ưu hóa để mở rộng cluster của họ. Nhờ đó, việc này giúp giảm bớt các tác vụ thủ công và cải thiện việc tuân thủ các thỏa thuận cấp độ dịch vụ.

Trong bài viết này, chúng tôi sẽ thảo luận về những thách thức của mình và chỉ ra cách chúng tôi xây dựng một khung quan sát (observability framework) để cung cấp các thông tin chi tiết về số liệu vận hành cho các khối lượng công việc xử lý dữ liệu lớn trên các cluster Amazon EMR trên [Amazon Elastic Compute Cloud](http://aws.amazon.com/ec2) (Amazon EC2).

---

**Thách thức**

Trong thế giới lấy dữ liệu làm trọng tâm ngày nay, các tổ chức luôn nỗ lực để trích xuất những thông tin giá trị từ lượng lớn dữ liệu. Thách thức mà chúng tôi phải đối mặt là tìm ra một phương pháp hiệu quả để giám sát và quan sát các khối lượng công việc xử lý dữ liệu lớn trên Amazon EMR do tính phức tạp của nó. Việc giám sát và quan sát các giải pháp Amazon EMR đi kèm với nhiều thách thức khác nhau:

* **Độ phức tạp và quy mô** – Các cluster EMR thường xử lý lượng dữ liệu khổng lồ trên nhiều nút (node). Việc giám sát một hệ thống phân tán và phức tạp như vậy đòi hỏi phải xử lý thông lượng dữ liệu cao và giảm thiểu tác động đến hiệu suất. Quản lý và diễn giải lượng dữ liệu giám sát lớn do các cluster EMR tạo ra có thể gây quá tải, khiến việc xác định và khắc phục sự cố kịp thời trở nên khó khăn.
* **Môi trường động (Dynamic environments)** – Các cluster EMR thường có tính chất tạm thời (ephemeral), được tạo và tắt dựa trên yêu cầu của khối lượng công việc. Tính động này gây khó khăn cho việc giám sát, thu thập các chỉ số và duy trì khả năng quan sát một cách nhất quán theo thời gian.
* **Sự đa dạng của dữ liệu** – Việc giám sát tình trạng cluster và có cái nhìn tổng quan về chúng để phát hiện các điểm nghẽn, hành vi bất thường trong quá trình xử lý, độ lệch dữ liệu (data skew), hiệu suất công việc (job performance),... là rất quan trọng. Việc quan sát chi tiết các cluster chạy trong thời gian dài, các nút, các tác vụ, độ lệch dữ liệu tiềm ẩn, các tác vụ bị kẹt, các vấn đề về hiệu suất và các chỉ số cấp độ công việc (như Spark và JVM) là cực kỳ quan trọng để hiểu rõ. Đạt được khả năng quan sát toàn diện trên các loại dữ liệu đa dạng này là một điều khó khăn.
* **Tối ưu hóa tài nguyên** – Các cluster EMR bao gồm nhiều thành phần và dịch vụ hoạt động cùng nhau, khiến việc giám sát hiệu quả tất cả các khía cạnh của hệ thống trở nên thách thức. Giám sát việc sử dụng tài nguyên (CPU, bộ nhớ, I/O của đĩa) trên nhiều nút để ngăn chặn các điểm nghẽn và sự thiếu hiệu quả là điều cần thiết nhưng phức tạp, đặc biệt trong một môi trường phân tán.
* **Độ trễ và các chỉ số hiệu suất** – Việc thu thập và phân tích độ trễ cùng các chỉ số hiệu suất toàn diện theo thời gian thực để xác định và giải quyết vấn đề kịp thời là rất quan trọng, nhưng lại đầy thách thức do tính chất phân tán của Amazon EMR.
* **Bảng điều khiển quan sát tập trung** – Có một bảng điều khiển duy nhất (single pane of glass) cho tất cả các khía cạnh của chỉ số cluster EMR, bao gồm tình trạng cluster, việc sử dụng tài nguyên, quá trình thực thi công việc, nhật ký (logs) và bảo mật, nhằm cung cấp một bức tranh hoàn chỉnh về hiệu suất và tình trạng của hệ thống, là một thách thức.
* **Cảnh báo và quản lý sự cố** – Thiết lập các hệ thống cảnh báo và thông báo tập trung hiệu quả là một điều khó khăn. Việc cấu hình cảnh báo cho các sự kiện quan trọng hoặc ngưỡng hiệu suất đòi hỏi phải cân nhắc kỹ lưỡng để tránh tình trạng "mệt mỏi vì cảnh báo" (alert fatigue) trong khi vẫn đảm bảo các vấn đề quan trọng được giải quyết kịp thời. Ứng phó với các sự cố từ việc giảm tốc độ hoặc gián đoạn hiệu suất sẽ tốn thời gian và công sức để phát hiện và khắc phục nếu không có cơ chế cảnh báo phù hợp.
* **Quản lý chi phí** – Cuối cùng, tối ưu hóa chi phí trong khi vẫn duy trì việc giám sát hiệu quả là một thách thức liên tục. Cân bằng giữa nhu cầu giám sát toàn diện và các ràng buộc về chi phí đòi hỏi phải có kế hoạch cẩn thận và các chiến lược tối ưu hóa để tránh các chi phí không cần thiết mà vẫn đảm bảo khả năng giám sát đầy đủ.

Khả năng quan sát hiệu quả cho Amazon EMR đòi hỏi sự kết hợp của các công cụ, phương pháp và chiến lược phù hợp để giải quyết những thách thức này, từ đó cung cấp khả năng xử lý dữ liệu lớn một cách đáng tin cậy, hiệu quả và tiết kiệm chi phí.

Hệ thống [Ganglia](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-ganglia.html) trên Amazon EMR được thiết kế để giám sát tình trạng của toàn bộ cluster và tất cả các nút (node), hiển thị nhiều chỉ số (metrics) như Hadoop, Spark và JVM. Khi chúng ta xem giao diện web của Ganglia trong trình duyệt, chúng ta sẽ thấy một cái nhìn tổng quan về hiệu suất của cluster EMR, chi tiết về tải (load), mức sử dụng bộ nhớ, mức sử dụng CPU và lưu lượng mạng của cluster thông qua các biểu đồ khác nhau.

Tuy nhiên, với việc AWS đã thông báo ngừng hỗ trợ Ganglia cho các phiên bản [AWS for higher versions of Amazon EMR](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-ganglia.html), việc FINRA xây dựng giải pháp này trở nên quan trọng.

---

**Tổng quan về giải pháp**

Những thông tin chi tiết được rút ra từ bài viết [Monitor and Optimize Analytic Workloads on Amazon EMR with Prometheus and Grafana](https://aws.amazon.com/blogs/big-data/monitor-and-optimize-analytic-workloads-on-amazon-emr-with-prometheus-and-grafana/) đã truyền cảm hứng cho cách tiếp cận của chúng tôi. Bài viết này đã trình bày cách thiết lập một hệ thống giám sát bằng cách sử dụng [Amazon Managed Service for Prometheus](https://aws.amazon.com/prometheus/) và [Amazon Managed Grafana](https://aws.amazon.com/grafana/) để giám sát hiệu quả một cluster EMR, và sử dụng các dashboard của Grafana để xem các chỉ số nhằm khắc phục và tối ưu hóa các vấn đề về hiệu suất.

Dựa trên những thông tin này, chúng tôi đã hoàn thành một bằng chứng về khái niệm (proof of concept) thành công. Tiếp theo, chúng tôi đã xây dựng giải pháp giám sát tập trung cho doanh nghiệp của mình với Managed Prometheus và Managed Grafana để mô phỏng các chỉ số giống như Ganglia tại FINRA. Managed Prometheus cho phép thu thập dữ liệu khối lượng lớn theo thời gian thực, có thể mở rộng khả năng tiếp nhận, lưu trữ và truy vấn các chỉ số vận hành khi khối lượng công việc tăng hoặc giảm. Các chỉ số này được chuyển đến không gian làm việc của Managed Grafana để trực quan hóa.

Giải pháp của chúng tôi bao gồm một lớp tiếp nhận dữ liệu cho mọi cluster, với cấu hình để thu thập chỉ số thông qua một script tùy chỉnh được lưu trữ trong [Amazon Simple Storage Service](http://aws.amazon.com/s3). Chúng tôi cũng đã cài đặt Managed Prometheus khi khởi động cho các instance EC2 trên Amazon EMR thông qua một bootstrap script. Ngoài ra, các thẻ (tag) cụ thể cho ứng dụng được định nghĩa trong tệp cấu hình để tối ưu hóa việc đưa vào và thu thập các chỉ số cụ thể.

Sau khi Managed Prometheus (được cài đặt trên các cluster EMR) thu thập các chỉ số, chúng sẽ được gửi đến một không gian làm việc (workspace) Managed Prometheus từ xa. Các workspace Managed Prometheus là các môi trường logic và biệt lập dành riêng cho các máy chủ Managed Prometheus, quản lý các chỉ số cụ thể. Chúng cũng cung cấp kiểm soát truy cập để cấp quyền cho những ai hoặc những gì được gửi và nhận chỉ số từ workspace đó. Bạn có thể tạo một hoặc nhiều workspace theo tài khoản hoặc ứng dụng tùy thuộc vào nhu cầu, điều này giúp quản lý tốt hơn.

Sau khi các chỉ số được thu thập, chúng tôi đã xây dựng một cơ chế để hiển thị chúng trên các dashboard của Managed Grafana, sau đó được sử dụng để tiêu thụ thông qua một endpoint. Chúng tôi đã tùy chỉnh các dashboard cho các chỉ số cấp độ tác vụ, cấp độ nút và cấp độ cluster để chúng có thể được chuyển từ môi trường thấp hơn lên môi trường cao hơn. Chúng tôi cũng đã xây dựng một số dashboard mẫu hiển thị các chỉ số cấp độ nút như các chỉ số cấp độ hệ điều hành (CPU, bộ nhớ, mạng, I/O của đĩa), các chỉ số HDFS, các chỉ số YARN, các chỉ số Spark và các chỉ số cấp độ công việc (Spark và JVM), tối đa hóa tiềm năng cho mỗi môi trường thông qua việc tổng hợp chỉ số tự động trong mỗi tài khoản.

Chúng tôi đã chọn tùy chọn xác thực dựa trên SAML, cho phép chúng tôi tích hợp với các nhóm Active Directory (AD) hiện có, giúp giảm thiểu công việc cần thiết để quản lý quyền truy cập của người dùng và cấp quyền truy cập dashboard Grafana dựa trên vai trò người dùng. Chúng tôi đã sắp xếp ba nhóm chính—quản trị viên (admins), người chỉnh sửa (editors) và người xem (viewers)—để xác thực người dùng Grafana dựa trên vai trò của họ.

Thông qua việc tự động hóa giám sát phức tạp, những chỉ số mong muốn này được đẩy tới [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/). Chúng tôi sử dụng CloudWatch để cảnh báo khi cần thiết, khi các chỉ số vượt quá ngưỡng mong muốn.

![Sơ đồ kiến trúc giải pháp](/images/3-BlogsTranslated/Blog_FINRA/img1.jpg)

---

## **Các dashboard mẫu**

Các ảnh chụp màn hình sau đây giới thiệu các dashboard mẫu.

![Dashboard mẫu 1](/images/3-BlogsTranslated/Blog_FINRA/img2.jpg)

![Dashboard mẫu 2](/images/3-BlogsTranslated/Blog_FINRA/img3.jpg)

---

**Kết luận**

Trong bài viết này, chúng tôi đã chia sẻ cách FINRA nâng cao việc ra quyết định dựa trên dữ liệu bằng khả năng quan sát khối lượng công việc trên EMR một cách toàn diện, nhằm tối ưu hóa hiệu suất, duy trì độ tin cậy và thu được các thông tin chuyên sâu quan trọng về các hoạt động dữ liệu lớn, từ đó dẫn đến sự xuất sắc trong vận hành.

Giải pháp của FINRA đã cho phép các đội vận hành và kỹ thuật sử dụng một bảng điều khiển duy nhất (single pane of glass) để giám sát các khối lượng công việc dữ liệu lớn và nhanh chóng phát hiện bất kỳ vấn đề vận hành nào. Giải pháp có khả năng mở rộng này đã giúp giảm đáng kể thời gian xử lý sự cố và nâng cao tổng thể khả năng vận hành của chúng tôi. Giải pháp đã cung cấp cho các đội vận hành và kỹ thuật những thông tin chuyên sâu toàn diện về nhiều chỉ số Amazon EMR khác nhau như cấp độ hệ điều hành (OS levels), Spark, JMX, HDFS và Yarn, tất cả được tổng hợp tại một nơi duy nhất. Chúng tôi cũng đã mở rộng giải pháp này cho các trường hợp sử dụng như các cluster [Amazon Elastic Kubernetes Service](https://aws.amazon.com/eks/) (Amazon EKS), bao gồm cả các cluster EMR trên EKS và các ứng dụng khác, biến nó thành một hệ thống duy nhất để giám sát các chỉ số trên toàn bộ cơ sở hạ tầng và ứng dụng của chúng tôi.

---

| ![Sumalatha Bachu](/images/3-BlogsTranslated/Blog1/author.png) | **Sumalatha Bachu** <br> Sumalatha là Giám đốc cấp cao (Senior Director) của Bộ phận Công nghệ tại FINRA. Cô phụ trách mảng Vận hành Dữ liệu lớn (Big Data Operations), bao gồm việc quản lý dữ liệu quy mô petabyte và xử lý các khối lượng công việc phức tạp trên nền tảng đám mây. Ngoài ra, cô còn là một chuyên gia trong việc phát triển các giải pháp Giám sát và Quan sát Ứng dụng Doanh nghiệp, Phân tích Dữ liệu Vận hành, và các quy trình quản trị Mô hình Học máy. Ngoài công việc, cô thích tập yoga, luyện hát và dạy học trong thời gian rảnh rỗi. |
| :---- | :---- |
| ![PremKiran Bejjam](/images/3-BlogsTranslated/Blog_FINRA/author2.jpg) | **PremKiran Bejjam** <br> PremKiran là Kỹ sư Tư vấn chính (Lead Engineer Consultant) tại FINRA. Anh chuyên phát triển các hệ thống có khả năng phục hồi và mở rộng. Với sự tập trung cao độ vào việc thiết kế các giải pháp giám sát để tăng cường độ tin cậy của cơ sở hạ tầng, anh luôn tận tâm với việc tối ưu hóa hiệu suất hệ thống. Ngoài công việc, anh thích dành thời gian chất lượng bên gia đình và không ngừng tìm kiếm các cơ hội học hỏi mới. |
| ![Akhil Chalamalasetty](/images/3-BlogsTranslated/Blog_FINRA/author3.png) | **Akhil Chalamalasetty** <br> Akhil là Giám đốc (Director) của Bộ phận Công nghệ Giám sát Thị trường (Market Regulation Technology) tại FINRA. Anh là một chuyên gia về Dữ liệu lớn (Big Data), chuyên sâu trong việc xây dựng các giải pháp tiên tiến với quy mô lớn, cùng với việc tối ưu hóa khối lượng công việc, dữ liệu và khả năng xử lý của chúng. Akhil thích đua xe mô phỏng (sim racing) và xem đua xe Công thức 1 (Formula 1) trong thời gian rảnh. |
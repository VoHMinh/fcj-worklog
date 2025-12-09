---
title: "Multi-Region keys: Một cách tiếp cận mới cho nhân bản khóa trong AWS Payment Cryptography"
date: 2025-09-16
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Ngày đăng: 2025‑09‑16 – Tác giả: Ruy Cavalcanti và Mark Cline trong [Announcements](https://aws.amazon.com/blogs/security/category/post-types/announcements/), [Financial Services](https://aws.amazon.com/blogs/security/category/industries/financial-services/), [Industries](https://aws.amazon.com/blogs/security/category/industries/), [Intermediate (200)](https://aws.amazon.com/blogs/security/category/learning-levels/intermediate-200/), [Security, Identity, & Compliance](https://aws.amazon.com/blogs/security/category/security-identity-compliance/), [Technical How-to](https://aws.amazon.com/blogs/security/category/post-types/technical-how-to/)

Trong bài viết trước của chúng tôi (Phần 1 của loạt bài về nhân bản khóa), [Automatically replicate your card payment keys across AWS Regions](https://aws.amazon.com/blogs/security/automatically-replicate-your-card-payment-keys-across-aws-regions/), chúng tôi đã khám phá một kiến trúc serverless hướng sự kiện sử dụng [AWS PrivateLink](https://aws.amazon.com/privatelink) để nhân bản an toàn các khóa thanh toán thẻ giữa các Region AWS. Giải pháp đó minh họa cách xây dựng framework nhân bản khóa mã hóa thanh toán tùy chỉnh.

Dựa trên phản hồi từ khách hàng mong muốn cách thức tự động cao hơn, không cần code, chúng tôi rất vui mừng công bố một tùy chọn bổ sung với **Multi-Region keys** cho **AWS Payment Cryptography** trong Phần 2 của loạt bài.

Với tính năng mới này, bạn có thể tự động đồng bộ các khóa mã hóa thanh toán từ Region chính sang các Region khác mà bạn chọn, cải thiện khả năng chịu lỗi và tính sẵn sàng của ứng dụng thanh toán. Bạn cũng có thể lựa chọn giữa nhân bản ở cấp tài khoản (account-level) hoặc cấp khóa (key-level), mang lại linh hoạt hơn trong quản lý khóa thanh toán giữa các Region.

---

## **Multi-Region keys: Tổng quan và lợi ích**

Tính năng mới nhân bản khóa **Multi-Region** cho [AWS Payment Cryptography](https://aws.amazon.com/payment-cryptography/) cung cấp cho bạn quyền kiểm soát linh hoạt đối với chiến lược nhân bản khóa thông qua các khả năng chính sau:

* Quyết định xem các khóa có được nhân bản hay không  
* Lựa chọn các Region cụ thể để nhân bản khóa  
* Quản lý các thay đổi cấu hình nhân bản  
* Cấu hình nhân bản ở cấp tài khoản hoặc cấp khóa tùy theo nhu cầu kinh doanh

**Multi-Region keys** mang đến nhiều lợi ích cho hoạt động thanh toán toàn cầu, bao gồm:

* **Tăng tính sẵn sàng**: Truy cập khóa thanh toán ngay cả khi một Region không khả dụng.  
* **Khả năng phục hồi thảm họa**: Duy trì hoạt động kinh doanh khi có sự cố bằng cách nhân bản khóa giữa các Region.  
* **Vận hành toàn cầu**: Hỗ trợ xử lý thanh toán trên nhiều vùng địa lý.  
* **Quản lý đơn giản**: Kiểm soát tập trung với khả năng phân tán.  
* **ID khóa nhất quán**: ID khóa giống nhau giữa các Region giúp đơn giản hóa phát triển ứng dụng.

---

## **Tùy chọn cấu hình**

**Payment Cryptography** cung cấp hai phương thức riêng biệt để cấu hình nhân bản khóa Multi-Region, giúp bạn linh hoạt trong việc triển khai chiến lược phù hợp nhất với tổ chức: bạn có thể chọn giữa cách tiếp cận rộng (cấp tài khoản) hoặc cách tiếp cận chi tiết hơn (cấp khóa).

### **Cấp tài khoản (Account-level)**

Với cấu hình cấp tài khoản, AWS sẽ tự động sao chép các khóa đối xứng có thể xuất (exportable symmetric keys) được tạo trong tài khoản Payment Cryptography của bạn từ Region chính mà bạn chỉ định sang các Region khác mà bạn chỉ định. Điều này giúp đơn giản hóa việc quản lý khóa trong các triển khai đa vùng, cung cấp tính sẵn có nhất quán của khóa trong các Region bạn chọn và giảm bớt chi phí vận hành quản lý khóa.

Để cấu hình nhân bản cấp tài khoản bằng  [AWS Command Line Interface (AWS CLI)](https://aws.amazon.com/cli), sử dụng API mới enable-default-key-replication-regions để đặt các Region mà AWS sẽ nhân bản khóa của bạn. Để loại bỏ Region khỏi danh sách nhân bản mặc định, dùng API disable-default-key-replication-regions .

Lưu ý: Chỉ các khóa đối xứng được tạo sau khi nhân bản cấp tài khoản được bật mới được nhân bản.

## **Nhân bản cấp khóa (Key-level replication)**

Bằng cách sử dụng **key-level replication**, bạn có thể đạt được mức kiểm soát chi tiết hơn thông qua việc:

* Chỉ định các khóa cụ thể là **multi-Region keys**.  
* Xác định **mục tiêu nhân bản tùy chỉnh** cho từng khóa multi-Region.  
* Duy trì các khóa riêng biệt cho từng Region khi cần thiết.

**Lưu ý:** Trong mỗi Region, Payment Cryptography duy trì khả năng dự phòng của khóa trên nhiều Availability Zone để đảm bảo tính sẵn sàng cao. **Multi-Region key replication** mở rộng điều này ra phạm vi địa lý, giúp tăng cường khả năng chống chịu với sự cố mất kết nối giữa các Region trong khi vẫn giữ quyền kiểm soát vị trí lưu trữ khóa.

Bạn có thể chỉ định các Region nhân bản ngay khi tạo khóa bằng cách sử dụng tham số \--replication-regions trong **AWS CLI**, thông qua các API create-key hoặc import-key. Đối với các khóa đã tồn tại, bạn có thể sử dụng các API mới add-key-replication-regions và remove-key-replication-regions để quản lý những Region nào sẽ nhận bản nhân bản của khóa.

**Quan trọng:** Khi bạn chỉ định các Region nhân bản trong quá trình tạo khóa, các thiết lập này sẽ có độ ưu tiên cao hơn so với cấu hình nhân bản mặc định ở cấp tài khoản.

---

## **Cách hoạt động**

Hình 1 mô tả quy trình khi bạn nhân bản một khóa trong **Payment Cryptography**:

1. Khóa được tạo trong **Region chính** mà bạn chỉ định.  
2. **Payment Cryptography** tự động **nhân bản không đồng bộ (asynchronously)** phần **key material** đến các Region được chọn làm bản sao.  
3. Các khóa nhân bản duy trì **cùng một Key ID** trên tất cả các Region — chỉ phần **Region** trong **Amazon Resource Name (ARN)** thay đổi.  
4. Khóa trong Region chính được gắn nhãn MultiRegionKeyType: PRIMARY  
5. Các khóa trong các Region nhân bản được gắn nhãn MultiRegionKeyType: REPLICA và có tham chiếu tới Region chính.  
6. Khi xóa một khóa, việc xóa này sẽ **tự động lan truyền** (cascade) từ khóa chính sang các bản sao ở các Region khác.


| ![](/images/3-BlogsTranslated/Blog3/img1.png)

Hình 1: Minh họa quá trình nhân bản khóa từ us-east-1 sang us-west-2

### **Ví dụ: Tạo khóa multi-Region ở cấp khóa**

Ví dụ sau minh họa việc tạo **card verification key (CVK)** trong **Region chính (us-east-1)** với việc nhân bản sang **us-west-2**:

aws payment-cryptography create-key \\

\--exportable \\

\--key-attributes KeyAlgorithm=TDES\_2KEY,\\

KeyUsage=TR31\_C0\_CARD\_VERIFICATION\_KEY,\\

KeyClass=SYMMETRIC\_KEY,KeyModesOfUse='{Generate=true,Verify=true}' \\

\--region us-east-1 \\

\--replication-regions us-west-2

Kết quả phản hồi cho thấy khóa đang được tạo và quá trình nhân bản đang diễn ra:

{

  "Key": {

    "KeyArn": "arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk",

    "KeyAttributes": {

      "KeyUsage": "TR31\_C0\_CARD\_VERIFICATION\_KEY",

      "KeyClass": "SYMMETRIC\_KEY",

      "KeyAlgorithm": "TDES\_2KEY",

      "KeyModesOfUse": {

        "Encrypt": false,

        "Decrypt": false,

        "Wrap": false,

        "Unwrap": false,

        "Generate": true,

        "Sign": false,

        "Verify": true,

        "DeriveKey": false,

        "NoRestrictions": false

      }

    },

    "KeyCheckValue": "CC5EE2",

    "KeyCheckValueAlgorithm": "ANSI\_X9\_24",

    "Enabled": true,

    "Exportable": true,

    "KeyState": "CREATE\_COMPLETE",

    "KeyOrigin": "AWS\_PAYMENT\_CRYPTOGRAPHY",

    "CreateTimestamp": "2025-08-21T15:25:54.475000-03:00",

    "UsageStartTimestamp": "2025-08-21T15:25:54.287000-03:00",

    "MultiRegionKeyType": "PRIMARY",

    **"ReplicationStatus": {**

      **"us-west-2": {**

        **"Status": "IN\_PROGRESS"**

      **}**

    },

    "UsingDefaultReplicationRegions": false

  }

}

Khi quá trình nhân bản hoàn tất, trạng thái sẽ được cập nhật thành **SYNCHRONIZED**:

aws payment-cryptography get-key \\

\--key-identifier arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk \\

\--region us-east-1

Kết quả trả về cho thấy khóa chính đã đồng bộ hoàn tất:

"ReplicationStatus": {

      "us-west-2": {

        "Status": "SYNCHRONIZED"

      }

    }

Bạn có thể truy cập khóa ở **Region bản sao (us-west-2)** bằng cách sử dụng cùng **Key ID**, chỉ thay đổi tên Region:

aws payment-cryptography get-key \\

\--key-identifier arn:aws:payment-cryptography:us-west-2:111122223333:key/qs6643jl4ohibtqk \\

\--region us-west-2

Kết quả trả về hiển thị khóa bản sao với tham chiếu đến Region chính (**PrimaryRegion: us-east-1**).

---

## **Những điều cần lưu ý**

Khi sử dụng **multi-Region keys**, có một số điểm quan trọng cần cân nhắc:

* **Chỉ hỗ trợ symmetric keys có thuộc tính exportable**, không hỗ trợ asymmetric keys.  
* **Chi phí tính riêng cho từng Region** — ví dụ, nhân bản sang 3 Region sẽ phát sinh chi phí cho khóa chính cộng thêm chi phí của mỗi khóa bản sao.  
* **Key alias** và **tags** cần được quản lý riêng ở từng Region vì chúng **không được nhân bản tự động**.  
* **Khóa chính (primary key)** có thể được chỉnh sửa hoặc cập nhật, trong khi **khóa bản sao (replica)** chỉ là bản **read-only** hỗ trợ các thao tác mật mã (cryptographic operations). Mọi thay đổi phải được thực hiện ở khóa chính và **Payment Cryptography** sẽ tự động đồng bộ sang các Region bản sao.  
* **Theo dõi trạng thái nhân bản** để đảm bảo các thay đổi đã được đồng bộ thành công.

### **Hành vi khi xóa khóa**

Khi lên lịch xóa khóa chính:

* Tất cả các khóa bản sao sẽ bị xóa **ngay lập tức**.  
* Khóa chính sẽ chuyển sang trạng thái **pending deletion** trong tối thiểu 3 ngày, trong thời gian này có thể **hủy xóa (cancel deletion)**.  
* Nếu bạn khôi phục khóa chính, bạn cần bật lại nhân bản để tái tạo các bản sao ở các Region mong muốn.  
* Sau khi thời gian 3 ngày trôi qua, khóa chính sẽ bị xóa **vĩnh viễn** và không thể phục hồi.  
* Việc xóa một **replica key** chỉ ảnh hưởng đến Region đó, **không ảnh hưởng** đến khóa chính hoặc các bản sao khác.

### **Mô hình nhất quán cuối cùng (Eventual consistency)**

Nhân bản khóa đa vùng hoạt động theo mô hình **eventual consistency** — nghĩa là các thay đổi có thể không xuất hiện ngay lập tức trên tất cả các Region.  
 Do đó, **ứng dụng nên được thiết kế để xử lý mô hình này** và không giả định rằng khóa hoặc thay đổi sẽ có sẵn ngay lập tức ở các Region bản sao.

Nếu ứng dụng của bạn yêu cầu **tính nhất quán mạnh (strong consistency)**, hãy triển khai cơ chế **polling** bằng cách sử dụng API [GetKey](https://docs.aws.amazon.com/payment-cryptography/latest/APIReference/API_GetKey.html) để xác minh rằng các thay đổi đã được đồng bộ trước khi tiến hành các thao tác mã hóa.

---

## **Ghi nhật ký và giám sát (Logging and monitoring)**

**Payment Cryptography** ghi lại hoạt động API thông qua [AWS CloudTrail](https://aws.amazon.com/cloudtrail), hiện đã bao gồm các sự kiện (event) và thuộc tính (attribute) mới dành riêng cho tính năng Multi-Region key replication.

### **Sự kiện CloudTrail mới**

Dịch vụ này ghi nhận một loại sự kiện mới có tên SynchronizeMultiRegionKey, xuất hiện ở cả Region chính và Region bản sao.

#### **Sự kiện ở Region chính:**

Mỗi Region bản sao được định nghĩa sẽ tạo ra hai sự kiện **SynchronizeMultiRegionKey** trong Region chính:

1. Một sự kiện liên quan đến quá trình export khóa.  
   "serviceEventDetails": {  
           "keyArn": "arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk",  
           "replicationRegion": "us-west-2",  
           **"replicationType": "ExportKeyReplica"**  
       },

2. Một sự kiện liên quan đến quá trình import khóa.  
   "serviceEventDetails": {  
           "keyArn": "arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk",  
           "replicationRegion": "us-west-2",  
           **"replicationType": "ImportKeyReplica"**  
       },

#### **Sự kiện ở Region bản sao:**

Mỗi Region bản sao ghi lại một sự kiện **SynchronizeMultiRegionKey** tương ứng với quá trình import khóa:

"eventName": "SynchronizeMultiRegionKey",

"awsRegion": "us-west-2",

"serviceEventDetails": {

        "keyArn": "arn:aws:payment-cryptography:us-west-2:111122223333:key/qs6643jl4ohibtqk",

        "replicationRegion": "us-west-2",

        **"replicationType": "ImportKeyReplica"**

    },

---

### **Thuộc tính CloudTrail mới**

Các **API quản lý khóa (key management APIs)** giờ đây bao gồm những **thuộc tính mới** để phản ánh hoạt động của Multi-Region replication.

Ví dụ, sự kiện [CreateKey](https://docs.aws.amazon.com/payment-cryptography/latest/APIReference/API_CreateKey.html) trong **Region chính (us-east-1)** nay có thêm thuộc tính:

"requestParameters": {

    **"replicationRegions": \["us-west-2"\]**

 }

Sự kiện **CreateKey** trong **Region bản sao (us-west-2)** sẽ phản ánh quá trình khởi tạo khóa replica tương ứng, với các thông tin tương tự về KeyUsage, Algorithm và KeyCheckValue.

---

## **Bắt đầu sử dụng**

Để bắt đầu sử dụng **Multi-Region key replication** trong **Payment Cryptography**, hãy thực hiện các bước sau:

1. Xác định **Region chính (primary Region)**.

2. Xác định các **Region bản sao (replica Regions)** và quyết định xem bạn sẽ dùng cấu hình **account-level** hay **key-level**.

3. Tạo **symmetric key có thể export (exportable symmetric key)** mới hoặc cập nhật các khóa hiện có để bật tính năng **Multi-Region replication**.

4. Cập nhật ứng dụng của bạn để sử dụng **Key ID nhất quán** giữa các Region.

---

## **Kết luận**

Tính năng **Multi-Region key replication** mới trong **AWS Payment Cryptography** nâng cao khả năng nhân bản khóa tự động, giúp cải thiện **khả năng chịu lỗi**, **tính sẵn sàng**, và **đơn giản hóa việc quản lý khóa thanh toán toàn cầu**. Tính năng này đảm bảo rằng các **payment cryptography keys** của bạn luôn khả dụng **mọi lúc, mọi nơi**, đồng thời cung cấp sự linh hoạt trong việc lựa chọn chiến lược nhân bản giữa **account-level** và **key-level**, giúp tổ chức tối ưu hiệu năng, bảo mật, và chi phí trong môi trường đa vùng (multi-Region).

| ![](/images/3-BlogsTranslated/Blog3/author1.jpg) | Ruy Cavalcanti Ruy là Senior Security Architect là chuyên gia trong ngành tài chính Mỹ Latinh tại AWS. Ông đã làm việc trong lĩnh vực CNTT và Bảo mật hơn 19 năm, giúp khách hàng xây dựng kiến ​​trúc bảo mật và giải quyết các thách thức về bảo vệ dữ liệu và tuân thủ. Khi không phải thiết kế các giải pháp bảo mật, ông thích chơi guitar, nấu món thịt nướng kiểu Brazil và dành thời gian cho gia đình và bạn bè. |
| :---- | :---- |
| ![](/images/3-BlogsTranslated/Blog3/author2.jpg)| **Mark Cline** Mark là Trưởng phòng Quản lý Sản phẩm tại AWS Payments, nơi ông có hơn 15 năm kinh nghiệm trong lĩnh vực dịch vụ tài chính trên nhiều trường hợp sử dụng và lĩnh vực khác nhau. Ông hợp tác với các ngân hàng, tổ chức tài chính và nhà cung cấp công nghệ hàng đầu để giảm bớt gánh nặng cho hệ thống thanh toán, cho phép khách hàng tập trung vào đổi mới. Khi không bận rộn với việc đơn giản hóa thanh toán, bạn có thể thấy ông đang huấn luyện các đội bóng chày nhỏ hoặc chạy bộ. |
+++
title = "Tạo Bucket S3"
date = 2025
weight = 3
chapter = false
pre = "<b>3.1 </b>"
+++

#### Amazon S3 là gì?

**Amazon S3 (Simple Storage Service)** là dịch vụ lưu trữ đối tượng (object storage) của AWS, có khả năng mở rộng gần như vô hạn, độ bền cao và dễ dàng tích hợp với các dịch vụ khác như **Lambda**, **CloudFront**, và **API Gateway**.  
Trong dự án này, S3 sẽ đóng vai trò là nơi lưu trữ toàn bộ dữ liệu tĩnh như:

- 🗂️ **Frontend web** đã build bằng React.js  
- 🖼️ Hình ảnh và nội dung bài viết (media)  
- 📦 File tạm thời hoặc dữ liệu cần cho Lambda xử lý  

---

#### Vì sao cần tạo S3 Bucket?

- 📁 **Lưu trữ tĩnh website**: Upload toàn bộ frontend (HTML, CSS, JS) sau khi build để website có thể truy cập qua CloudFront.  
- 🔁 **Kết hợp với CloudFront**: S3 là nguồn dữ liệu gốc cho CDN giúp tối ưu tốc độ tải trang.  
- 🔐 **Tích hợp với Lambda**: Cho phép Lambda truy cập/ghi dữ liệu khi cần xử lý.  
- ⚙️ **Serverless kiến trúc**: Không cần máy chủ để lưu trữ nội dung, tất cả chạy hoàn toàn serverless.

---

#### 🪣 3.1. Tạo S3 Bucket

1. Truy cập [**AWS Management Console**](https://aws.amazon.com/console/)  
2. Mở dịch vụ **Amazon S3**.  
3. Trong menu bên trái, chọn **Buckets**, sau đó nhấn **Create bucket**.

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.1.png)

---

4. Trong giao diện **Create bucket**:

- **Bucket name**: đặt tên duy nhất toàn cầu, ví dụ: `my-serverless-blog-bucket`  
- **AWS Region**: chọn cùng region với Lambda và DynamoDB (ví dụ: `us-east-1`)  
- **Block Public Access**: bỏ chọn nếu bạn muốn bucket public để lưu frontend (sẽ chỉnh sau).  
- Các phần khác giữ mặc định.  
- Nhấn **Create bucket**

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.2.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.3.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.4.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.5.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.6.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.7.png)

{{% notice tip %}}
💡 **Gợi ý:** Bucket name phải **duy nhất toàn cầu** – nếu tên bạn nhập đã tồn tại, hãy thêm hậu tố như `-2025` hoặc tên dự án của bạn.
{{% /notice %}}

---

#### 📂 3.2. Kiểm tra và cấu hình Bucket

- Sau khi tạo, bạn sẽ thấy bucket của mình trong danh sách.  
- Nhấn vào tên bucket để mở chi tiết.

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.8.png)

- Vào tab **Properties** để kiểm tra cấu hình tổng quan.  
- Vào tab **Permissions** để cấu hình truy cập (quan trọng nếu bạn muốn lưu frontend công khai).
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.9.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.10.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.11.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.12.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.13.png)
{{% notice warning %}}
⚠️ Nếu bạn định host website tĩnh từ S3, cần **cho phép truy cập công khai**.  
Hãy chắc chắn bạn **hiểu rõ rủi ro bảo mật** khi bật public access.
{{% /notice %}}

---

#### ✅ Hoàn thành

🎉 Bạn đã tạo thành công một **S3 Bucket** – thành phần lưu trữ trung tâm trong kiến trúc serverless của dự án.  
Trong các bước tiếp theo, chúng ta sẽ:

- Upload frontend đã build vào bucket  
- Kết nối CloudFront để phân phối nội dung toàn cầu  
- Cấu hình Lambda để ghi/đọc dữ liệu nếu cần

{{% notice note %}}
📌 **Tóm tắt:** S3 là nền tảng lưu trữ dữ liệu tĩnh và nội dung cho ứng dụng web. Hãy đảm bảo bucket nằm cùng region với các dịch vụ khác để tránh lỗi quyền truy cập và tối ưu chi phí.
{{% /notice %}}

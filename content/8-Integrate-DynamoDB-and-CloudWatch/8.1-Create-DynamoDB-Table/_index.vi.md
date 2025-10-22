+++
title = "Tạo bảng DynamoDB để lưu metadata inference"
date = 2025
weight = 8
chapter = false
pre = "<b>8.1 </b>"
+++

**Amazon DynamoDB** là một dịch vụ cơ sở dữ liệu NoSQL hoàn toàn managed, hiệu năng cao và tự động mở rộng. Trong dự án này, DynamoDB sẽ được sử dụng để **lưu trữ metadata của các yêu cầu inference** từ mô hình, bao gồm đầu vào, đầu ra, thời gian xử lý và thông tin mô hình.  
Việc này giúp chúng ta dễ dàng **phân tích, giám sát và truy xuất lịch sử inference** khi cần thiết.

---

### DynamoDB trong dự án Machine Learning Pipeline

Trong phần này, chúng ta sẽ:
- Tạo một bảng DynamoDB để lưu trữ metadata mỗi lần inference.
- Xác định **Primary key** và các trường quan trọng.
- Cấu hình quyền truy cập để Lambda có thể ghi dữ liệu vào bảng.
- Kiểm tra bảng và xác minh dữ liệu được ghi thành công.

---

### 🎯 Lợi ích khi sử dụng DynamoDB:

- **Serverless & Tự động mở rộng**: Không cần quản lý máy chủ hay phân vùng dữ liệu.
- **Hiệu năng cao**: Đáp ứng hàng triệu yêu cầu mỗi giây.
- **Dễ dàng tích hợp**: Làm việc trực tiếp với Lambda và các dịch vụ khác của AWS.
- **Theo dõi lịch sử inference**: Lưu lại chi tiết đầu vào, đầu ra và thời gian inference.

---

#### 🛠️ Tạo bảng DynamoDB

1. **Truy cập AWS Management Console**
   - Trong thanh tìm kiếm, nhập **DynamoDB** và chọn dịch vụ này.

![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.1.png)

---

2. **Tạo bảng mới**
   - Chọn **Create table**.

   - **Table name**: Nhập tên bảng, ví dụ: `InferenceMetadata`.
   - **Partition key**: Nhập `requestId` (kiểu `String`).
   - Không cần thêm Sort key (tùy chọn).
   - Giữ các cài đặt còn lại mặc định.
   - Nhấn **Create table**.

![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.2.png)
![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.3.png)

---

3. **Cấu trúc dữ liệu gợi ý**

Mỗi bản ghi trong bảng có thể chứa các trường sau:

| Trường | Kiểu dữ liệu | Mô tả |
|--------|--------------|--------|
| `requestId` | String | ID duy nhất cho mỗi yêu cầu inference |
| `timestamp` | String | Thời gian thực hiện inference |
| `modelName` | String | Tên mô hình sử dụng |
| `inputData` | String | Dữ liệu đầu vào |
| `prediction` | String | Kết quả mô hình trả về |
| `latencyMs` | Number | Độ trễ (ms) của quá trình inference |

![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.4.png)

---

4. **Cấp quyền truy cập cho Lambda**
   - Mở **IAM Console**, chọn role của Lambda inference (ví dụ: `lambda-inference-role`).
   - Thêm quyền truy cập DynamoDB bằng cách attach policy sau:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:PutItem",
           "dynamodb:DescribeTable"
         ],
         "Resource": "arn:aws:dynamodb:<region>:<account-id>:table/InferenceMetadata"
       }
     ]
   }
   ```
![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.5.png)
![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.6.png)
![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.7.png)
   {{% notice warning %}}
Đảm bảo Partition key là duy nhất để tránh ghi đè dữ liệu.
Ghi lại tên bảng để sử dụng trong bước cấu hình Lambda (8.2).
Role Lambda phải có quyền PutItem để ghi dữ liệu vào bảng.
{{% /notice %}}

5. Kiểm tra bảng

   - Sau khi Lambda ghi dữ liệu inference, vào tab Explore table items của bảng InferenceMetadata.
   - Bạn sẽ thấy các bản ghi chứa đầy đủ thông tin mỗi lần inference.
![search-dynamodb.png](/images/8-Configure-CloudFront/8.1-Create-DynamoDB-Table/8.8.png)
#### ✅ Hoàn thành

- Bạn đã tạo bảng DynamoDB để lưu metadata inference.
- Đây là nền tảng để Lambda có thể ghi dữ liệu sau mỗi lần dự đoán, phục vụ mục tiêu phân tích và giám sát ở bước tiếp theo.
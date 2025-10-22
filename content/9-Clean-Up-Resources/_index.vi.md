+++
title = "Dọn dẹp tài nguyên"
date = 2025
weight = 9
chapter = false
pre = "<b>9. </b>"
+++

Sau khi hoàn thành toàn bộ workshop **"Building an End-to-End Machine Learning Pipeline on AWS"**, bước cuối cùng là **dọn dẹp tất cả tài nguyên AWS** mà bạn đã tạo trong quá trình triển khai.  
Điều này cực kỳ quan trọng để tránh phát sinh chi phí ngoài Free Tier, đảm bảo an toàn bảo mật và giữ cho tài khoản AWS luôn sạch sẽ cho các dự án tiếp theo.

---

### 🎯 Mục tiêu của bước này

- **💸 Tiết kiệm chi phí**: Ngăn chặn chi phí phát sinh từ các dịch vụ không còn sử dụng.  
- **🔐 Bảo mật**: Loại bỏ quyền IAM, API và tài nguyên không cần thiết để giảm thiểu rủi ro bảo mật.  
- **🧰 Gọn gàng và dễ quản lý**: Giúp tài khoản AWS sạch sẽ và sẵn sàng cho các workshop hoặc dự án mới.

---

### 🗑️ Các tài nguyên cần xóa

Dưới đây là danh sách tài nguyên đã được sử dụng xuyên suốt dự án mà bạn cần xóa:

- **Amazon CloudFront** – CDN phân phối ứng dụng và nội dung.  
- **Amazon S3** – Lưu trữ dữ liệu và model.  
- **Amazon API Gateway** – REST API kết nối Lambda và endpoint inference.  
- **AWS Lambda** – Hàm xử lý dữ liệu, tiền xử lý và suy luận.  
- **Amazon DynamoDB** – Bảng lưu metadata và kết quả inference.  
- **AWS IAM** – Vai trò và chính sách cấp quyền cho Lambda và SageMaker.

---

### 🧼 Hướng dẫn dọn dẹp chi tiết

#### 1. 🧭 Xóa phân phối CloudFront

1. Truy cập **CloudFront** từ **AWS Management Console**.  
2. Chọn phân phối bạn đã tạo (ví dụ: `d1234567890abcdef.cloudfront.net`).  
3. Nhấp **Disable (Vô hiệu hóa)** và đợi trạng thái chuyển sang **Disabled**.  
4. Nhấp **Delete (Xóa)** để xóa hoàn toàn phân phối.



---

#### 2. 📦 Xóa bucket Amazon S3

1. Vào **S3** từ bảng điều khiển AWS.  
2. Chọn bucket đã tạo (ví dụ: `ml-workshop-data-<account-id>`).  
3. Nhấp **Empty**, nhập `permanently delete` để xác nhận, rồi chọn **Empty**.  
4. Khi bucket trống, nhấp **Delete bucket**, nhập tên bucket để xác nhận xóa.

![Check Region](/images/9-Clean-Up-Resources/9.1.png)
![Check Region](/images/9-Clean-Up-Resources/9.2.png)
![Check Region](/images/9-Clean-Up-Resources/9.3.png)

---

#### 3. 🌐 Xóa API Gateway

1. Vào **API Gateway**.  
2. Chọn API bạn đã triển khai (ví dụ: `InferenceAPI`).  
3. Nhấp **Actions → Delete**, nhập tên API để xác nhận và hoàn tất xóa.

![Check Region](/images/9-Clean-Up-Resources/9.4.png)
![Check Region](/images/9-Clean-Up-Resources/9.5.png)
![Check Region](/images/9-Clean-Up-Resources/9.6.png)

---

#### 4. 🧠 Xóa các hàm AWS Lambda

1. Truy cập **Lambda** từ bảng điều khiển.  
2. Xóa tất cả các hàm bạn đã tạo, ví dụ:
   - `preprocessing-function`
   - `inference-function`
3. Nhấp **Actions → Delete**, xác nhận xóa từng hàm.

![Check Region](/images/9-Clean-Up-Resources/9.7.png)
![Check Region](/images/9-Clean-Up-Resources/9.8.png)
![Check Region](/images/9-Clean-Up-Resources/9.9.png)

---

#### 5. 📊 Xóa bảng DynamoDB

1. Vào **DynamoDB** → **Tables**.  
2. Chọn bảng mà bạn đã tạo (ví dụ: `InferenceMetadata`).  
3. Nhấp **Actions → Delete table**, nhập tên bảng để xác nhận.

![Check Region](/images/9-Clean-Up-Resources/9.10.png)
![Check Region](/images/9-Clean-Up-Resources/9.11.png)
![Check Region](/images/9-Clean-Up-Resources/9.12.png)

---

#### 6. 🔐 Xóa các tài nguyên IAM

1. Vào **IAM** trong bảng điều khiển.  
2. Trong **Policies**, chọn chính sách đã tạo (ví dụ: `lambda-inference-policy`) → **Delete**.  
3. Trong **Roles**, chọn vai trò liên quan (ví dụ: `lambda-inference-role`) → **Delete**.

![Check Region](/images/9-Clean-Up-Resources/9.13.png)
![Check Region](/images/9-Clean-Up-Resources/9.14.png)
![Check Region](/images/9-Clean-Up-Resources/9.15.png)
![Check Region](/images/9-Clean-Up-Resources/9.16.png)
![Check Region](/images/9-Clean-Up-Resources/9.17.png)

---

{{% notice warning %}}
⚠️ **Lưu ý quan trọng:**

- Đảm bảo rằng **bucket S3 đã trống** trước khi xóa.  
- Kiểm tra kỹ các tài nguyên trước khi xóa để tránh xóa nhầm tài nguyên từ dự án khác.  
- Nếu gặp lỗi khi xóa (ví dụ: tài nguyên vẫn đang được tham chiếu), hãy kiểm tra các **phụ thuộc** như quyền IAM, endpoint API Gateway, hoặc phân phối CloudFront.
{{% /notice %}}

---

#### ✅ Kết quả sau khi dọn dẹp

- Toàn bộ tài nguyên của dự án đã được xóa.  
- Tài khoản AWS của bạn không còn tài nguyên nào phát sinh chi phí.  
- Bạn có thể bắt đầu các workshop hoặc dự án mới mà không bị xung đột tài nguyên cũ.
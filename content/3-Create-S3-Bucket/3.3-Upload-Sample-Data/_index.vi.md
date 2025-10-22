+++
title = "Tải lên dữ liệu mẫu" 
date = 2025 
weight = 5 
chapter = false 
pre = "<b>3.3 </b>" 
+++

Trong phần này, chúng ta sẽ **tải lên dữ liệu mẫu (frontend build)** vào **Amazon S3 bucket** để kiểm tra khả năng truy cập website tĩnh trước khi kết nối với API Gateway và Lambda. 

---


#### 📦 Vì sao cần upload dữ liệu mẫu? 
{{% notice tip %}} 
Việc tải dữ liệu mẫu vào S3 giúp bạn: 
- ✅ Xác minh **bucket đã hoạt động chính xác** và website có thể được phân phối. 
- ✅ Kiểm tra khả năng truy cập của **frontend React/Vite** qua trình duyệt. 
- ✅ Đảm bảo cấu trúc dữ liệu đúng trước khi tích hợp với backend. 
{{% /notice %}} 


---


#### Bước 1 – Build ứng dụng frontend 

Nếu bạn dùng **React + Vite**, trong thư mục frontend chạy:

~~~
npm install
npm run build
~~~

Lệnh này sẽ tạo thư mục dist/ (hoặc build/) chứa file tĩnh sẵn sàng để upload lên S3. 
- Cấu trúc thư mục sau khi build có thể như sau:
~~~~ 
dist/ 
│ 
├─ index.html 
├─ favicon.ico 
├─ assets/ 
│ ├─ main.js 
│ └─ style.css 
└─ logo.png 
~~~~

#### Bước 2 - Upload frontend build lên S3 
- Truy cập Amazon S3 Console 
- Chọn bucket mà bạn đã tạo ở bước 3.1 
- Create S3 Bucket 
- Nhấn nút Upload 
- Chọn toàn bộ nội dung trong thư mục dist/ (hoặc build/) 
- Nhấn Upload để hoàn tất quá trình tải lên 
- Ví dụ minh họa quá trình upload:

#### Bước 3 – Kích hoạt Static Website Hosting (nếu chưa bật) 
- Trong tab Properties của bucket 
- Cuộn xuống phần Static website hosting 
- Chọn Enable 
- Chỉ định:
~~~
Index document: index.html
Error document: index.html (nếu dùng React Router)
~~~

{{% notice note %}} 
File index.html phải nằm ở root của bucket, không nằm trong thư mục con. Nếu website có routing (SPA), đặt cả Error document = index.html. 
{{% /notice %}}

#### Bước 4 – Kiểm tra truy cập website 
- Sao chép Static website endpoint URL từ phần cấu hình bucket. 
- Dán URL vào trình duyệt để kiểm tra kết quả. 
- Ví dụ:
~~~
http://my-blog-frontend.s3-website-ap-southeast-1.amazonaws.com
~~~
- Kết quả khi truy cập thành công:
![open-s3.png](/images/3-Create-DynamoDB-Table/3.3-Upload-Sample-Data/3.3.1.png)
#### Lưu ý quan trọng 
{{% notice warning %}} 
Kiểm tra lại permissions của bucket nếu bạn không truy cập được website. Đảm bảo file index.html đã được upload đúng vị trí. Nếu muốn dùng CloudFront sau này, không cần bật "Public access" cho bucket – CloudFront sẽ truy cập thay bạn. 
{{% /notice %}}

#### ✅ Hoàn thành 
Bạn đã tải thành công frontend build mẫu lên S3 bucket và xác minh khả năng truy cập website tĩnh.
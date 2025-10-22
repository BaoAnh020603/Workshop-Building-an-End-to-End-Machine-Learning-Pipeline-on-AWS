+++
title = "Tổ chức Cấu trúc Dữ liệu"
date = 2025
weight = 4
chapter = false
pre = "<b>3.2 </b>"
+++

Trong phần này, bạn sẽ học cách **tổ chức cấu trúc dữ liệu** (thư mục và tệp tin) bên trong **Amazon S3 bucket** đã tạo ở bước trước. Việc tổ chức dữ liệu hợp lý giúp frontend (React/Vite) dễ dàng được phân phối qua **CloudFront** và backend truy cập nội dung đúng cách.

---

#### 📁 Vì sao cần tổ chức cấu trúc dữ liệu?
{{% notice tip %}}
Một S3 bucket không có khái niệm thư mục thực sự – nó tổ chức dữ liệu bằng **key prefix** (chuỗi tên tệp). Việc sắp xếp dữ liệu hợp lý sẽ giúp:
- Dễ dàng quản lý và cập nhật nội dung frontend.
- Phân quyền truy cập chi tiết nếu sau này có nhiều nhóm làm việc.
- Tối ưu cache khi phân phối qua **CloudFront**.
{{% /notice %}}

---

#### 🧱 Cấu trúc thư mục khuyến nghị

Tạo cấu trúc như sau trong bucket S3:
~~~
my-blog-frontend/
│
├─ index.html
├─ favicon.ico
├─ /assets/
│ ├─ logo.png
│ └─ styles.css
├─ /js/
│ └─ main.js
└─ /posts/
└─ sample-post.json
~~~


- 📄 **index.html** – file gốc của ứng dụng React/Vite.  
- 📁 **/assets/** – chứa hình ảnh, CSS.  
- 📁 **/js/** – chứa các file JavaScript build ra từ ứng dụng frontend.  
- 📁 **/posts/** – (tuỳ chọn) chứa các bài viết mẫu hoặc JSON nội dung tĩnh.
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.1.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.2.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.3.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.4.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.5.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.6.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.7.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.8.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.9.png)

---

#### 🪄 Thêm dữ liệu mẫu để kiểm tra bucket

1. Truy cập vào **Amazon S3 Console**.  
2. Chọn bucket bạn đã tạo ở bước **3.1**.  
3. Nhấn **Upload**.  
4. Tải lên file `index.html` và các thư mục như trên.  
5. Sau khi tải lên, bạn sẽ thấy cấu trúc thư mục trong bucket tương tự như hình:

![open-s3.png](/images/3-Create-DynamoDB-Table/3.2-Organize-Data-Structure/3.2.10.png)

{{% notice note %}}
- Tên thư mục không bắt buộc nhưng **nên tuân theo cấu trúc trên** để dễ tích hợp với **CloudFront** và CI/CD sau này.
- Đảm bảo file `index.html` nằm ở **root của bucket**, vì đó là entry point của website.
- Nếu bạn dùng React Router, bật **Static website hosting** trong phần “Properties” để S3 có thể xử lý routing đúng cách.
{{% /notice %}}

---

#### ✅ Hoàn thành

Bạn đã tổ chức thành công cấu trúc dữ liệu trong **S3 bucket** và thêm dữ liệu mẫu để chuẩn bị cho bước triển khai frontend.  




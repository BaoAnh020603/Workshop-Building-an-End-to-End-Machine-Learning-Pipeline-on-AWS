+++
title = "Hội thảo: Xây dựng quy trình học máy toàn diện trên AWS với Lambda, API Gateway, S3, SageMaker và DynamoDB"
date = 2025
weight = 1
chapter = false
+++

# Hội thảo: Xây dựng quy trình học máy toàn diện trên AWS với Lambda, API Gateway, S3, SageMaker và DynamoDB

### Tổng quan

Trong hội thảo này, bạn sẽ học cách xây dựng và triển khai quy trình học máy **end-to-end** trên AWS.

Chúng ta sẽ sử dụng **AWS Lambda** để tiền xử lý và suy luận, **API Gateway** để hiển thị các điểm cuối RESTful, **S3** để lưu trữ dữ liệu, **Amazon SageMaker** để đào tạo và lưu trữ mô hình, **DynamoDB** để lưu trữ siêu dữ liệu và **CloudWatch** để giám sát và ghi nhật ký.

![Kiến trúc Hội thảo](/images/an%20automated%20Machine%20Learning%20(ML)%20pipeline%20system%20on%20AWS.drawio%20(1).svg)

---

### 🎯 Mục tiêu

- Hiểu cách thiết kế và triển khai một quy trình ML hoàn chỉnh trên AWS.
- Xây dựng và cấu hình các quy trình thu thập dữ liệu, tiền xử lý và đào tạo mô hình.
- Triển khai và quản lý các mô hình học máy với Amazon SageMaker.
- Hiển thị các điểm cuối suy luận thông qua API Gateway và Lambda.
- Tích hợp DynamoDB cho siêu dữ liệu mô hình và sử dụng CloudWatch để giám sát.
- Tìm hiểu các phương pháp hay nhất về quyền, bảo mật và tối ưu hóa chi phí.

---

### 🧰 Yêu cầu

- Có tài khoản AWS hiện tại (Mức miễn phí: [https://aws.amazon.com/free](https://aws.amazon.com/free))
- Có kiến ​​thức cơ bản về **Python** hoặc **Go** (cho các hàm Lambda và tập lệnh ML)
- Quen thuộc với **API REST** và **JSON**
- Công cụ: **AWS CLI**, **Git**, **Docker** (tùy chọn) và trình duyệt web
- *(Tùy chọn)* **Postman** để kiểm tra các điểm cuối suy luận

> 💡 Nếu bạn đã có tài khoản AWS với quyền truy cập đầy đủ, bạn có thể bỏ qua thiết lập IAM và tiếp tục xây dựng tài nguyên trực tiếp.

---

### 📚 Mục lục

1. [Giới thiệu](1-Introduction/)  
2. [Kiểm tra Tài khoản và Quyền AWS](2-Check-AWS-Account-and-Permissions/)  
3. [Tạo Bucket S3 để Lưu trữ Dữ liệu](3-Create-S3-Bucket/)  
4. [Triển khai Hàm Xử lý Trước Lambda](4-Implement-Lambda-Preprocessing/)  
5. [Đào tạo và Đăng ký Mô hình với Amazon SageMaker](5-Train-Model-with-SageMaker/)  
6. [Triển khai Endpoint của SageMaker cho Inference](6-Deploy-SageMaker-Endpoint/)  
7. [Xây dựng Lambda Function và REST API cho Inference](7-Build-Lambda-Inference-and-API/)  
8. [Tích hợp DynamoDB và CloudWatch](8-Integrate-DynamoDB-and-CloudWatch/)  
9. [Dọn dẹp tài nguyên](9-Clean-Up-Resources/)  
10. [Kết luận và những điểm chính](10-Conclusion/) 
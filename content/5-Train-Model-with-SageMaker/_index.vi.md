+++
title = "Đào tạo và Đăng ký Mô hình với Amazon SageMaker"
date = 2025
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

Trong phần này, chúng ta sẽ **huấn luyện mô hình machine learning** với Amazon SageMaker bằng dữ liệu đã được tiền xử lý từ Lambda và lưu trên S3. Đây là bước quan trọng để tạo ra mô hình sẵn sàng phục vụ inference thông qua API sau này.

---

## 🎯 Mục tiêu

- Tìm hiểu cách tạo **SageMaker Training Job** từ dữ liệu đã xử lý.
- Cấu hình các tham số huấn luyện và lựa chọn thuật toán.
- Đăng ký mô hình (Model Registry) để triển khai sau này.
- Quản lý version mô hình và theo dõi quá trình huấn luyện.

---

## 📚 Nội dung

- [**5.1 Chuẩn bị dữ liệu đầu vào cho SageMaker**](5.1-prepare-training-data/)  
- [**5.2 Tạo SageMaker Training Job**](5.2-create-training-job/)  
- [**5.3 Đăng ký mô hình và quản lý phiên bản**](5.3-register-and-manage-model/)  
- [**5.4 Kiểm tra kết quả huấn luyện**](5.4-validate-training-results/)

---

## 🧠 Tổng quan kiến trúc

![SageMaker-Training-Architecture](/images/5.0.png)

1. **Lambda** tạo dữ liệu đã xử lý và lưu vào thư mục `processed/` trên S3.  
2. **SageMaker** đọc dữ liệu từ S3, huấn luyện mô hình theo thuật toán bạn chọn.  
3. Kết quả mô hình được lưu trong `model/` và có thể đăng ký vào **Model Registry**.  
4. Mô hình đã đăng ký sẽ dùng để triển khai inference ở bước tiếp theo.

---

## 📦 Yêu cầu trước khi bắt đầu

- Đã hoàn thành **Lambda Preprocessing Function** (Chương 4).  
- Có dữ liệu đã xử lý nằm trong thư mục `processed/` của bucket S3.  
- IAM Role của SageMaker có quyền truy cập **S3**, **CloudWatch**, và **SageMaker**.

---

✅ Sau khi hoàn thành chương này, bạn sẽ có một mô hình ML được huấn luyện và đăng ký
+++
title = "Tích hợp DynamoDB và CloudWatch"
date = 2025
weight = 8
chapter = false
pre = "<b>8 </b>"
+++

#### DynamoDB & CloudWatch trong ML Pipeline

Trong bước cuối cùng của pipeline, chúng ta sẽ **tích hợp Amazon DynamoDB** để lưu trữ metadata của các yêu cầu inference và thông tin mô hình, đồng thời sử dụng **Amazon CloudWatch** để theo dõi logs, đo lường hiệu năng và tạo cảnh báo khi có sự cố.  
Đây là bước quan trọng để đảm bảo pipeline có thể vận hành bền vững trong môi trường thực tế.

---

#### 🗃️ DynamoDB – Lưu trữ metadata inference

**Amazon DynamoDB** là cơ sở dữ liệu NoSQL serverless, tự động mở rộng, hiệu năng cao. Trong dự án này, DynamoDB sẽ được dùng để:

- Lưu **kết quả inference** từ Lambda (đầu vào, đầu ra, thời gian).  
- Lưu **thông tin mô hình** như version, endpoint name.  
- Phục vụ việc **giám sát và phân tích hiệu suất** sau này.

---

#### 📊 CloudWatch – Giám sát và phân tích pipeline

**Amazon CloudWatch** là dịch vụ giám sát trung tâm trên AWS.  
Nó sẽ giúp bạn:

- Theo dõi **logs từ Lambda và SageMaker Endpoint**.  
- Tạo **metric filter** để phân tích số lượng inference, lỗi, độ trễ.  
- Cấu hình **alarm** khi pipeline có sự cố.

---

#### 📚 Nội dung

- [**8.1 Tạo bảng DynamoDB để lưu metadata inference**](8.1-Create-DynamoDB-Table/)  
- [**8.2 Cập nhật Lambda để ghi dữ liệu vào DynamoDB**](8.2-Update-Lambda-to-Write-DynamoDB/)  
- [**8.3 Giám sát và cảnh báo với CloudWatch**](8.3-Monitor-with-CloudWatch/)  

---

📌 **Tổng kết**

- ✅ Bạn sẽ học cách tạo và quản lý bảng DynamoDB để lưu kết quả inference.  
- ✅ Lambda sẽ được mở rộng để ghi dữ liệu mỗi lần dự đoán.  
- ✅ CloudWatch sẽ giúp bạn giám sát logs, phân tích hiệu suất và tạo cảnh báo.

---

🎯 **Kết quả sau chương này:**

- Một pipeline ML hoàn chỉnh có khả năng **lưu trữ lịch sử inference**, **giám sát tự động**, và **cảnh báo sớm** khi có sự cố.  
- Sẵn sàng vận hành ở môi trường **production** với khả năng mở rộng và bảo trì dễ dàng.

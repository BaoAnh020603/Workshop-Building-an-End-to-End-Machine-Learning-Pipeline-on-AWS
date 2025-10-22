+++
title = "Giới thiệu"
date = 2025-10-02T00:38:32+07:00
weight = 1
chapter = false
pre="<b>1. </b>"
+++

# Workshop: Xây dựng một quy trình học máy toàn diện trên AWS với Lambda, API Gateway, S3, SageMaker và DynamoDB

Trong Workshop này, bạn sẽ học cách xây dựng và triển khai một quy trình học máy **to-end** hoàn chỉnh trên AWS.

Chúng ta sẽ sử dụng **AWS Lambda** để tiền xử lý và suy luận, **Amazon API Gateway** để triển khai các API RESTful, **Amazon S3** để lưu trữ dữ liệu, **Amazon SageMaker** để đào tạo và triển khai mô hình, **Amazon DynamoDB** để lưu trữ siêu dữ liệu và **Amazon CloudWatch** để giám sát và ghi nhật ký.

![Kiến trúc Hội thảo](/images/an%20automated%20Machine%20Learning%20(ML)%20pipeline%20system%20on%20AWS.drawio%20(1).svg)

---

### Mục tiêu

- Hiểu cách thiết kế và triển khai quy trình ML trên AWS từ thu thập dữ liệu thô đến triển khai mô hình.
- Triển khai tiền xử lý dữ liệu và suy luận mô hình với **AWS Lambda**.
- Huấn luyện và đăng ký mô hình bằng **Amazon SageMaker**.
- Triển khai các điểm cuối RESTful để suy luận bằng **Amazon API Gateway**.
- Lưu trữ siêu dữ liệu và nhật ký suy luận trong **Amazon DynamoDB**.
- Giám sát hiệu suất hệ thống bằng **Amazon CloudWatch**.
- Tìm hiểu về quản lý chi phí và các chiến lược tối ưu hóa AWS Free Tier.

---

### Yêu cầu

- Tài khoản AWS (Gói miễn phí: https://aws.amazon.com/free)
- Kỹ năng Python cơ bản (cho các hàm Lambda và tập lệnh ML)
- Quen thuộc với REST API và JSON
- Công cụ: AWS CLI, Git, Docker (tùy chọn), trình duyệt web
- (Tùy chọn) Postman để kiểm tra API

---

### Tổng quan về Kiến trúc

Giải pháp bao gồm một số dịch vụ AWS được quản lý và không máy chủ hoạt động cùng nhau để tự động hóa quy trình làm việc ML:

**1. AWS Lambda**
**Công dụng:**
- Tiền xử lý dữ liệu trước khi đào tạo (ví dụ: làm sạch, trích xuất tính năng).
- Chạy suy luận trên dữ liệu mới đến.
- Tích hợp các giai đoạn khác nhau của quy trình.

**Chi phí:**
| Trạng thái | Yêu cầu | Chi phí/ngày | Chi phí/tháng |
|--------|--------|-----------|-----------|
| Gói miễn phí | 1 triệu yêu cầu | **$0.00** | **$0.00** |
| Sau gói miễn phí | 100 nghìn yêu cầu | ~$0.01 | ~$0.30 |

---

**2. Amazon API Gateway**
**Công dụng:**
- Hiển thị các điểm cuối RESTful để kích hoạt các hàm Lambda và suy luận mô hình.
- Hoạt động như giao diện giữa máy khách và các dịch vụ phụ trợ.

**Chi phí:**
| Trạng thái | Yêu cầu | Chi phí/ngày | Chi phí/tháng |
|--------|----------|----------|------------|
| Gói miễn phí | 1 triệu yêu cầu | **$0.00** | **$0.00** |
| Sau gói miễn phí | 3 triệu yêu cầu | ~$0.35 | ~$10.50 |

---

**3. Amazon S3**
**Công dụng:**
- Lưu trữ tập dữ liệu thô, kết quả tiền xử lý và các hiện vật mô hình.
- Kho dữ liệu tập trung cho quy trình làm việc ML.

**Chi phí:**
| Trạng thái | Lưu trữ | Chi phí/ngày | Chi phí/tháng |
|--------|----------|----------|----------|
| Gói miễn phí | Lưu trữ 5GB | **$0.00** | **$0.00** |
| Sau gói miễn phí | 5GB | ~$0.03 | ~$1.00 |

---

**4. Amazon SageMaker**
**Công dụng:**
- Đào tạo và đăng ký các mô hình học máy.
- Lưu trữ các mô hình dưới dạng điểm cuối được quản lý để suy luận theo thời gian thực.

**Chi phí:**
| Trạng thái | Loại | Chi phí/ngày | Chi phí/tháng |
|--------|------|-----------|------------|
| Dùng thử | 250 giờ/tháng | **$0.00** | **$0.00** |
| Sau khi hết hạn miễn phí | ml.t2.medium | ~$0.10 | ~$3.00 |

---

**5. Amazon DynamoDB**
**Công dụng:**
- Lưu trữ siêu dữ liệu như phiên bản mô hình, số liệu đào tạo và nhật ký suy luận.
- Cung cấp quyền truy cập độ trễ thấp cho các ứng dụng không có máy chủ.

**Chi phí:**
| Trạng thái | Công suất | Chi phí/ngày | Chi phí/tháng |
|--------|----------|----------|------------|
| Gói miễn phí | 25 đơn vị đọc/ghi | **$0.00** | **$0.00** |
| Sau gói miễn phí | 10K đọc/ghi | ~$0.02 | ~$0.60 |

---

**6. Amazon CloudWatch**
**Công dụng:**
- Giám sát nhật ký, theo dõi số liệu thực thi Lambda và cung cấp cảnh báo.

**Chi phí:**
- Thường **Miễn phí** cho các số liệu cơ bản.

---

### Tóm tắt chi phí

| Dịch vụ | Chi phí (Gói miễn phí) | Chi phí (Sau gói miễn phí) |
|--------|---------------------|------------------------|
| **AWS Lambda** | **$0.00** | ~$0.30 |
| **Amazon API Gateway** | **$0.00** | ~$10.50 |
| **Amazon S3** | **$0.00** | ~$1.00 |
| **Amazon SageMaker** | **$0.00** | ~$3.00 |
| **Amazon DynamoDB** | **$0.00** | ~$0.60 |
| **Amazon CloudWatch** | **$0.00** | ~$0.00 |
| **Tổng cộng** | **$0.00** | **~$15.40/tháng** |

---

### Quy trình làm việc

1. **Nhập dữ liệu** – Dữ liệu thô được tải lên S3.
2. **Tiền xử lý** – Hàm Lambda làm sạch và chuyển đổi dữ liệu.
3. **Đào tạo** – SageMaker đào tạo mô hình và đăng ký mô hình trong sổ đăng ký mô hình.
4. **Triển khai** – Mô hình được triển khai dưới dạng điểm cuối SageMaker.
5. **Suy luận** – Lambda + API Gateway cung cấp dự đoán theo thời gian thực cho các ứng dụng khách.
6. **Siêu dữ liệu & Giám sát** – DynamoDB lưu trữ siêu dữ liệu, CloudWatch giám sát nhật ký và số liệu.

---

### Kết luận

Buổi workshop này hướng dẫn cách xây dựng một quy trình ML **sẵn sàng cho sản xuất, có thể mở rộng** hoàn toàn trên AWS bằng cách sử dụng các dịch vụ không máy chủ và được quản lý.
Phương pháp này giảm thiểu chi phí vận hành, tự động mở rộng và vẫn tiết kiệm chi phí — thường là **$0 trong Gói miễn phí** và khoảng **$15/tháng** sau đó, tùy thuộc vào lưu lượng truy cập và mức sử dụng.


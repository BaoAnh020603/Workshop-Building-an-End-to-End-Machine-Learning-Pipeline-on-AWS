+++
title = "Tạo Bucket S3 để Lưu trữ Dữ liệu"
date = 2025
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

Trong phần này, chúng ta sẽ tạo một **Amazon S3 bucket** để làm nơi lưu trữ trung tâm cho toàn bộ dữ liệu của dự án Machine Learning Pipeline.

S3 sẽ đóng vai trò là **data lake** – nơi chứa dữ liệu đầu vào (raw data), dữ liệu sau tiền xử lý (processed data), kết quả huấn luyện mô hình (model artifacts) cũng như dữ liệu dự đoán (inference results). Đây là điểm khởi đầu quan trọng để pipeline có thể vận hành tự động và liên kết giữa các dịch vụ AWS.

{{% notice tip %}}
💡 **Gợi ý:** Luôn chọn cùng một region cho S3, Lambda và SageMaker để giảm độ trễ và tránh lỗi quyền truy cập.
{{% /notice %}}


### Nội dung
- [**3.1. Tạo S3 Bucket để lưu trữ dữ liệu**](3.1-Create-an-Bucket-to-store-data/)
- [**3.2. Cấu trúc tổ chức dữ liệu trong S3**](3.2-Organize-Data-Structure/)
- [**3.3. Tải dữ liệu mẫu lên S3 để test pipeline**](3.3-Upload-Sample-Data/)


{{% notice note %}}
📦 **Amazon S3 (Simple Storage Service)** là dịch vụ lưu trữ đối tượng giúp bạn dễ dàng quản lý dữ liệu lớn, tự động mở rộng quy mô và tích hợp liền mạch với các dịch vụ như Lambda, SageMaker, và API Gateway.
{{% /notice %}}

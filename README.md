# 🚀 End-to-End Machine Learning Pipeline on AWS

Dự án này trình bày cách **xây dựng, tự động hóa và triển khai toàn bộ quy trình Machine Learning (ML)** trên **AWS** bằng cách kết hợp các dịch vụ **serverless** và **AI managed** như **Amazon S3**, **AWS Lambda**, **Amazon SageMaker**, **API Gateway**, **DynamoDB** và **CloudWatch**.

Mục tiêu của dự án là tạo ra một hệ thống **ML pipeline tự động, mở rộng linh hoạt và tối ưu chi phí**, sẵn sàng cho môi trường **production (MLOps-ready)**.

---

## 🌟 Mục tiêu & Giá trị

- **Tự động hóa toàn bộ quy trình ML**: từ tải dữ liệu → tiền xử lý → huấn luyện → triển khai mô hình.  
- **Serverless & mở rộng linh hoạt**: loại bỏ nhu cầu quản lý hạ tầng.  
- **Giám sát & bảo mật toàn diện**: theo dõi logs, metrics, model drift bằng CloudWatch.  
- **Chi phí tối ưu**: trả tiền theo mức sử dụng (pay-per-use).  
- **Sẵn sàng production (MLOps-ready)**: có model registry, versioning, và hệ thống theo dõi tự động.

---

## ⚙️ Kiến trúc tổng quan

1. Người dùng tải dữ liệu thô lên **Amazon S3**  
2. **AWS Lambda** tự động kích hoạt tiến trình tiền xử lý dữ liệu và khởi chạy **SageMaker Training Job**  
3. Sau khi huấn luyện xong, mô hình được lưu và quản lý trong **SageMaker Model Registry**  
4. **API Gateway + Lambda** cung cấp API `/predict` để gọi tới **SageMaker Endpoint** phục vụ dự đoán theo thời gian thực  
5. **DynamoDB** lưu metadata của các lần inference, trong khi **CloudWatch** theo dõi logs, hiệu năng và cảnh báo  
![Workshop Architecture](/images/an%20automated%20Machine%20Learning%20(ML)%20pipeline%20system%20on%20AWS.drawio%20(1).svg)
---

## 🧩 Công nghệ chính

| Dịch vụ | Vai trò |
|----------|----------|
| **Amazon S3** | Lưu trữ dữ liệu đầu vào và đầu ra |
| **AWS Lambda** | Tiền xử lý dữ liệu & inference logic |
| **Amazon SageMaker** | Huấn luyện, quản lý và triển khai mô hình |
| **Amazon API Gateway** | Cung cấp REST API cho inference |
| **Amazon DynamoDB** | Lưu metadata kết quả inference |
| **Amazon CloudWatch** | Ghi log, theo dõi hiệu suất và cảnh báo |

---

## 🚀 Cách chạy nhanh

```bash
# Clone repository
git clone https://github.com/BaoAnh020603/Workshop-Building-an-End-to-End-Machine-Learning-Pipeline-on-AWS.git
cd Building-an-End-to-End-Machine-Learning-Pipeline-on-AWS

# Tạo S3 bucket lưu dữ liệu
aws s3 mb s3://ml-pipeline-demo-raw

# Build và deploy hạ tầng bằng AWS SAM
sam build && sam deploy --guided

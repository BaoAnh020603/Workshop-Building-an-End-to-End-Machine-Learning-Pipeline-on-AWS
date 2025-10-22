+++
title = "Tạo SageMaker Training Job"
date = 2025
weight = 2
chapter = false
pre = "<b>5.2 </b>"
+++


Trong phần này, chúng ta sẽ **khởi tạo và cấu hình một SageMaker Training Job** để huấn luyện mô hình machine learning bằng dữ liệu đã được tiền xử lý từ Lambda và lưu trên S3.

---

### 🎯 Mục tiêu
- Tạo **Training Job** trên SageMaker sử dụng dữ liệu trong S3.  
- Cấu hình các tham số huấn luyện như container, instance type, input/output S3.  
- Theo dõi tiến trình huấn luyện và xác minh kết quả.

---

#### ⚙️ 1. Truy cập Amazon SageMaker

1. Vào **AWS Management Console** → tìm và mở **Amazon SageMaker**.  
2. Trong thanh điều hướng bên trái, chọn **Training jobs** → **Create training job**.

![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.1.png)
![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.2.png)

---

#### 📁 2. Đặt tên và cấu hình cơ bản

- **Training job name**: `ml-pipeline-training-job`  
- **IAM Role**: Chọn role có quyền truy cập S3 và SageMaker (ví dụ: `SageMakerExecutionRole`).  
- **Algorithm source**:  
  - Chọn **Your own algorithm container** nếu bạn có custom script.  
  - Hoặc chọn **Built-in algorithm** (ví dụ: XGBoost) để thử nghiệm nhanh.

![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.3.png)

{{% notice tip %}}
💡 Nếu đây là lần đầu bạn thử nghiệm, nên chọn **XGBoost built-in container** để đơn giản hóa quá trình huấn luyện.
{{% /notice %}}

---

#### 📦 3. Cấu hình dữ liệu huấn luyện

Trong phần **Input data configuration**:

- **Channel name**: `train`  
- **Input mode**: File  
- **S3 location**:  s3://ml-pipeline-bucket/processed/train/


Thêm một channel mới cho validation:

- **Channel name**: `validation`  
- **S3 location**:  s3://ml-pipeline-bucket/processed/validation/

📁 Cấu trúc S3 nên như sau:
~~~
ml-pipeline-bucket/
└─ processed/
├─ train/
│ └─ train.csv
└─ validation/
└─ val.csv
~~~
![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.6.png)
![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.7.png)

---

#### ⚙️ 4. Cấu hình tài nguyên huấn luyện

- **Instance type**: `ml.m5.large` *(hoặc chọn instance GPU nếu mô hình yêu cầu)*  
- **Instance count**: `1`  
- **Volume size**: `10 GB`  
- **Max runtime**: `3600` (giới hạn 1 giờ huấn luyện)

![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.5.png)

{{% notice warning %}}
⚠️ Chọn kích thước instance phù hợp với ngân sách. Các instance như `ml.m5.large` nằm trong Free Tier và đủ mạnh cho demo.
{{% /notice %}}

---

#### 📤 5. Đặt vị trí lưu mô hình sau huấn luyện

Trong phần **Output data configuration**:

- **S3 output path**:  s3://ml-pipeline-bucket/model/

SageMaker sẽ lưu file mô hình đã huấn luyện (ví dụ: `model.tar.gz`) tại đây.
![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.4.png)

---

#### 🧪 6. Khởi tạo Training Job

- Kiểm tra lại toàn bộ cấu hình.
- Nhấn **Create training job** để bắt đầu quá trình huấn luyện.

📊 Giao diện khi job đang chạy:

![sagemaker-console.png](/images/5-Configure-API-Gateway/5.2-create-training-job/5.2.4.png)


#### 🔎 7. Theo dõi tiến trình và kiểm tra kết quả

- Trong danh sách **Training jobs**, chọn job vừa tạo.  
- Kiểm tra trạng thái: `InProgress` → `Completed`.  
- Xem log chi tiết trong **CloudWatch Logs** để theo dõi quá trình huấn luyện.

Sau khi hoàn tất, file mô hình sẽ được lưu ở: s3://ml-pipeline-bucket/model/model.tar.gz

{{% notice note %}}
- Nếu job thất bại, kiểm tra lại quyền IAM và đường dẫn S3.  
- Đảm bảo dữ liệu trong `train/` và `validation/` có cấu trúc và định dạng hợp lệ.
{{% /notice %}}


#### ✅ Hoàn thành

Bạn đã tạo thành công một **SageMaker Training Job** và huấn luyện mô hình sử dụng dữ liệu tiền xử lý từ Lambda.
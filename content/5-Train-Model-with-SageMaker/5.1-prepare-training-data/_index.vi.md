+++
title = "Chuẩn bị dữ liệu đầu vào cho SageMaker"
date = 2025
weight = 1
chapter = false
pre = "<b>5.1 </b>"
+++

Trước khi bắt đầu huấn luyện mô hình machine learning với **Amazon SageMaker**, chúng ta cần **kiểm tra và tổ chức dữ liệu đã được tiền xử lý** từ Lambda. Đây là bước quan trọng để đảm bảo quá trình huấn luyện diễn ra chính xác và hiệu quả.

---

### 🧠 Vai trò của dữ liệu trong pipeline

Dữ liệu là “nhiên liệu” của mô hình ML. Chất lượng và cấu trúc của dữ liệu đầu vào sẽ ảnh hưởng trực tiếp đến:

- 📊 **Hiệu suất huấn luyện** – mô hình học tốt hơn khi dữ liệu đã được làm sạch.  
- 🔎 **Độ chính xác dự đoán** – dữ liệu sạch và đúng định dạng giúp mô hình dự đoán chính xác hơn.  
- ⚙️ **Tự động hóa pipeline** – SageMaker yêu cầu cấu trúc dữ liệu nhất quán để có thể tạo training job.

---

#### 🪄 1. Kiểm tra dữ liệu đã xử lý trên S3

Truy cập **Amazon S3** trong AWS Management Console để xác minh dữ liệu đầu vào từ Lambda:

1. Chọn bucket bạn đã tạo ở phần **3 – Create S3 Bucket for Data Storage**.  
2. Mở thư mục `processed/` – đây là nơi Lambda đã lưu dữ liệu sau tiền xử lý.  
3. Kiểm tra xem file CSV hoặc Parquet đã tồn tại chưa (ví dụ: `data_processed.csv`).

📸 Ví dụ cấu trúc thư mục:
~~~
ml-pipeline-bucket/
├─ raw/
│ └─ data.csv
└─ processed/
└─ data_processed.csv
~~~
![create-lambda.png](/images/5-Configure-API-Gateway/5.1-prepare-training-data/5.1.png)
![create-lambda.png](/images/5-Configure-API-Gateway/5.1-prepare-training-data/5.2.png)
![create-lambda.png](/images/5-Configure-API-Gateway/5.1-prepare-training-data/5.3.png)

{{% notice tip %}}
💡 Dữ liệu trong thư mục `processed/` chính là input để SageMaker sử dụng trong bước huấn luyện mô hình tiếp theo.
{{% /notice %}}

---

#### 🧱 2. Tổ chức dữ liệu đúng cấu trúc SageMaker

SageMaker mong đợi dữ liệu đầu vào nằm trong một thư mục cụ thể trong S3, ví dụ:
~~~
s3://ml-pipeline-bucket/processed/train/
s3://ml-pipeline-bucket/processed/validation/
~~~


Bạn có thể tổ chức dữ liệu như sau:

- **train/** – chứa dữ liệu dùng để huấn luyện mô hình (~80%)  
- **validation/** – chứa dữ liệu dùng để đánh giá mô hình (~20%)

📌 Ví dụ:
~~~
processed/
├─ train/
│ └─ train.csv
└─ validation/
└─ val.csv
~~~
![create-lambda.png](/images/5-Configure-API-Gateway/5.1-prepare-training-data/5.4.png)

---

#### 🛠️ 3. Cập nhật quyền truy cập S3 cho SageMaker

Đảm bảo IAM Role của SageMaker có quyền truy cập vào bucket chứa dữ liệu:

- `s3:GetObject`
- `s3:ListBucket`
- `s3:PutObject` *(nếu cần ghi kết quả)*

{{% notice warning %}}
⚠️ Nếu SageMaker không có quyền đọc dữ liệu từ bucket S3, training job sẽ **thất bại ngay lập tức**.
{{% /notice %}}

---

#### 🔍 4. Kiểm tra định dạng dữ liệu

- SageMaker hỗ trợ định dạng **CSV**, **Parquet**, hoặc **RecordIO**.  
- Nếu dùng CSV, đảm bảo:
  - Có **header** mô tả các cột.
  - Không có giá trị null hoặc lỗi định dạng.
  - Các feature numeric đã được chuẩn hóa (nếu cần).

Ví dụ một file `train.csv` chuẩn:

```csv
feature1,feature2,feature3,label
0.21,0.75,0.11,1
0.56,0.22,0.65,0
0.34,0.12,0.88,1
```
![create-lambda.png](/images/5-Configure-API-Gateway/5.1-prepare-training-data/5.5.png)
![create-lambda.png](/images/5-Configure-API-Gateway/5.1-prepare-training-data/5.6.png)
#### ✅ Hoàn thành

Bạn đã hoàn tất việc chuẩn bị dữ liệu đầu vào cho SageMaker:

- 📁 Dữ liệu đã được tiền xử lý và lưu vào thư mục processed/ trên S3
- 🗂️ Dữ liệu đã được phân chia thành train/ và validation/
- 🔐 IAM Role đã có quyền truy cập cần thiết
- 🧹 Định dạng dữ liệu đã được xác minh



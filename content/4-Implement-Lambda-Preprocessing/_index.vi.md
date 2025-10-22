+++
title = "Triển khai Hàm Xử lý Trước Lambda"
date = 2025
weight = 4
chapter = false
pre = "<b>4. </b>"
+++

Trong phần này, chúng ta sẽ xây dựng **Lambda Preprocessing Function** để tự động xử lý dữ liệu thô được tải lên S3. Đây là bước **khởi đầu quan trọng** trong pipeline machine learning — đảm bảo dữ liệu sạch, chuẩn hóa và sẵn sàng cho quá trình huấn luyện mô hình trên Amazon SageMaker.

---

### 🎯 Mục tiêu

- Xây dựng Lambda function bằng **Python** để xử lý dữ liệu CSV từ S3.
- Tự động kích hoạt Lambda khi có file mới trong thư mục `raw/`.
- Ghi kết quả đã xử lý vào thư mục `processed/` trong cùng bucket.
- Đảm bảo dữ liệu sẵn sàng cho bước huấn luyện mô hình SageMaker.

---

### 🧠 Tổng quan kiến trúc

Dưới đây là cách Lambda Preprocessing Function hoạt động trong toàn bộ pipeline:

~~~
📁 S3 Bucket
├── raw/ ← CSV thô được upload lên
├── processed/ ← CSV đã xử lý, sẵn sàng train
~~~

- **Bước 1:** Người dùng hoặc hệ thống upload file CSV vào `raw/`.
- **Bước 2:** S3 kích hoạt Lambda qua trigger.
- **Bước 3:** Lambda đọc file, làm sạch dữ liệu (loại bỏ dòng lỗi, xử lý giá trị trống…).
- **Bước 4:** Lambda ghi file đã xử lý vào `processed/`.

---

#### 🛠️ 4.1 – Tạo Lambda Function

1. Truy cập [AWS Lambda Console](https://console.aws.amazon.com/lambda/home).
2. Nhấn **Create function**.
3. Cấu hình:
   - **Function name:** `preprocessData`
   - **Runtime:** Python 3.9
   - **Role:** Chọn IAM role đã tạo ở bước trước (có quyền S3).

![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.1.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.2.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.3.png)

---

#### 📜 4.2 – Viết mã xử lý dữ liệu

Trong tab **Code**, thay thế nội dung mặc định bằng đoạn mã sau:

```python
import boto3
import csv
import io
import os

s3 = boto3.client('s3')

def lambda_handler(event, context):
    # Lấy bucket và key của file mới upload
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    print(f"Processing file: s3://{bucket}/{key}")

    # Đọc file CSV từ S3
    try:
        response = s3.get_object(Bucket=bucket, Key=key)
        raw_bytes = response['Body'].read()
        try:
            content = raw_bytes.decode('utf-8')       # Thử decode UTF-8
        except UnicodeDecodeError:
            content = raw_bytes.decode('utf-16')      # Nếu lỗi, thử UTF-16
    except Exception as e:
        print(f"Error reading file from S3: {e}")
        raise e

    reader = csv.reader(io.StringIO(content))

    processed_rows = []
    for row in reader:
        # Bỏ qua dòng rỗng hoặc lỗi
        if all(row):
            processed_rows.append(row)

    # Viết file đã xử lý vào folder processed/
    output_prefix = os.getenv('OUTPUT_PREFIX', 'processed/')
    output_key = os.path.join(output_prefix, os.path.basename(key))

    output_content = io.StringIO()
    writer = csv.writer(output_content)
    writer.writerows(processed_rows)

    # Upload file đã xử lý lên S3
    try:
        s3.put_object(
            Bucket=bucket,
            Key=output_key,
            Body=output_content.getvalue().encode('utf-8'),
            ContentType='text/csv'
        )
        print(f"Processed file saved to s3://{bucket}/{output_key}")
    except Exception as e:
        print(f"Error writing file to S3: {e}")
        raise e

    return {
        'statusCode': 200,
        'body': f"Processed file saved to {output_key}"
    }
```
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.4.png)
#### ⚙️ 4.3 – Gán Environment Variables

Trong tab Configuration → Environment variables, nhấn Edit và thêm:

- Key: OUTPUT_PREFIX	
- Value: processed/

{{% notice tip %}}
Biến môi trường giúp bạn dễ dàng thay đổi đường dẫn lưu output mà không cần chỉnh sửa mã nguồn.
{{% /notice %}}
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.5.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.6.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.7.png)
#### 📦 4.4 – Đóng gói và Upload Lambda (tùy chọn cục bộ)

Nếu phát triển trên máy tính cục bộ:
~~~~
pip install -r requirements.txt -t .
zip -r preprocessData.zip .
~~~~

Sau đó quay lại Lambda Console → tab Code → Upload from → .zip file.

Tạo file requirements.txt với nội dung:
~~~
boto3
~~~ 

#### 🔔 4.5 – Kết nối Lambda với S3 (Trigger)

Trong Lambda Console, vào tab Configuration → Triggers.

Nhấn Add trigger.

Chọn:
~~~~
Trigger type: S3

Bucket: your-raw-data-bucket

Event type: PUT

Prefix: raw/
~~~~
📸 Ví dụ cấu hình trigger:

{{% notice warning %}}
Đảm bảo bucket và Lambda ở cùng region. Nếu không, trigger sẽ không hoạt động.
{{% /notice %}}
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.8.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.9.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.10.png)
#### 🧪 4.6 – Kiểm tra Preprocessing Function

Upload một file CSV mẫu vào thư mục raw/:
~~~
aws s3 cp data.csv s3://ml-pipeline-bucket/raw/data.csv
~~~

Lambda sẽ tự động chạy và tạo file đã xử lý trong processed/.
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.12.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.14.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.15.png)
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.11.png)
📊 Ví dụ kết quả:
~~~
Input: s3://ml-pipeline-bucket/raw/data.csv

Output: s3://ml-pipeline-bucket/processed/data.csv
~~~
![create-lambda.png](/images/4-Implement-Lambda-Preprocessing/4.13.png)
#### ✅ Hoàn thành

🎉 Bạn đã triển khai thành công Lambda Preprocessing Function – bước khởi đầu trong pipeline machine learning.
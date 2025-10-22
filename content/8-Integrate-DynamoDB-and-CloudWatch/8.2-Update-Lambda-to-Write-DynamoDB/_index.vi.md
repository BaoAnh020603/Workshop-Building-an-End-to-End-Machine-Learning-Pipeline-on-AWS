+++
title = "Cập nhật Lambda để ghi dữ liệu vào DynamoDB"
date = 2025
weight = 9
chapter = false
pre = "<b>8.2 </b>"
+++

![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.0.png)

Trong phần này, chúng ta sẽ:
- Cập nhật hàm Lambda inference để ghi metadata mỗi lần gọi mô hình vào DynamoDB.
- Kiểm tra dữ liệu được ghi thành công.
- Xác minh quá trình inference được theo dõi đầy đủ.

---

### 🎯 Mục tiêu của bước này:
- **Lưu trữ lịch sử inference**: Ghi lại dữ liệu đầu vào, kết quả, thời gian xử lý và thông tin mô hình.  
- **Phân tích & Debug dễ dàng**: Dữ liệu được lưu giúp theo dõi hành vi mô hình theo thời gian.  
- **Tích hợp với giám sát**: Chuẩn bị cho bước kết nối với CloudWatch để theo dõi pipeline.

---

#### 🛠️ Cập nhật Lambda để ghi dữ liệu vào DynamoDB

1. **Mở Lambda Function Inference**
   - Truy cập **AWS Lambda** trong Management Console.
   - Chọn hàm Lambda bạn đã tạo để gọi **SageMaker Endpoint** (ví dụ: `ml-inference-lambda`).

![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.1.png)
![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.3.png)

---

2. **Cập nhật mã nguồn Lambda**

Thay nội dung file `lambda_function.py` bằng mã dưới đây để ghi metadata vào bảng **InferenceMetadata** sau mỗi lần inference:

```python
import json
import boto3
import os
import time
import uuid
from datetime import datetime

sagemaker = boto3.client('sagemaker-runtime')
dynamodb = boto3.client('dynamodb')

ENDPOINT_NAME = os.environ['ENDPOINT_NAME']
TABLE_NAME = os.environ['DYNAMODB_TABLE']

def lambda_handler(event, context):
    # Parse input từ API Gateway
    body = json.loads(event['body'])
    input_data = body['input']

    # Gọi SageMaker endpoint
    start_time = time.time()
    response = sagemaker.invoke_endpoint(
        EndpointName=ENDPOINT_NAME,
        ContentType='application/json',
        Body=json.dumps(input_data)
    )
    prediction = json.loads(response['Body'].read())
    latency_ms = int((time.time() - start_time) * 1000)

    # Ghi metadata vào DynamoDB
    request_id = str(uuid.uuid4())
    timestamp = datetime.utcnow().isoformat()

    dynamodb.put_item(
        TableName=TABLE_NAME,
        Item={
            'requestId': {'S': request_id},
            'timestamp': {'S': timestamp},
            'modelName': {'S': ENDPOINT_NAME},
            'inputData': {'S': json.dumps(input_data)},
            'prediction': {'S': json.dumps(prediction)},
            'latencyMs': {'N': str(latency_ms)}
        }
    )

    # Trả về kết quả inference
    return {
        'statusCode': 200,
        'body': json.dumps({
            'requestId': request_id,
            'prediction': prediction,
            'latencyMs': latency_ms
        })
    }
```

#### ✅ Trong đó:

- ENDPOINT_NAME: tên SageMaker Endpoint đã triển khai.
- TABLE_NAME: tên bảng DynamoDB đã tạo ở 8.1.
- latencyMs: thời gian inference, tính bằng mili-giây.

3. Thêm biến môi trường cho Lambda

- Trong trang cấu hình của Lambda → Configuration → Environment variables → Edit.
- Thêm các biến:
  - ENDPOINT_NAME = tên SageMaker endpoint.
  - DYNAMODB_TABLE = InferenceMetadata.
![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.4.png)
![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.5.png)
4. Kiểm tra quyền truy cập IAM

- Đảm bảo role Lambda có quyền ghi dữ liệu vào DynamoDB như sau:
~~~
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:DescribeTable"
      ],
      "Resource": "arn:aws:dynamodb:<region>:<account-id>:table/InferenceMetadata"
    }
  ]
}
~~~

5. Triển khai và kiểm tra Lambda

- Nhấn Deploy để lưu thay đổi.
- Sử dụng Test trong Lambda Console hoặc gửi yêu cầu POST tới API Gateway như sau:
~~~
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"input": {"text": "Hello AI!"}}' \
  https://<api-id>.execute-api.<region>.amazonaws.com/prod/inference
~~~
- Kiểm tra phản hồi chứa requestId, prediction, và latencyMs.
![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.6.png)
6. Xác minh dữ liệu trong DynamoDB

- Mở bảng InferenceMetadata trong DynamoDB Console.
- Chọn Explore table items để xem dữ liệu được ghi.
![lambda-dynamodb.png](/images/8-Configure-CloudFront/8.2-Update-Lambda-to-Write-DynamoDB/8.2.7.png)
Bạn sẽ thấy các bản ghi như sau:

|requestId	|timestamp	|modelName	|inputData	|prediction	|latencyMs
|-----------|-----------|-----------|-----------|-----------|-----------|-----------------------
|6c1a4...	|2025-10-01T10:20:30Z	|ml-endpoint	|{"text": "Hello AI!"}	|{"result": "positive"}	|134

{{% notice warning %}}
Nếu dữ liệu không được ghi, kiểm tra quyền IAM của Lambda.
Xác minh tên bảng DynamoDB và biến môi trường chính xác.
Xem log chi tiết trong CloudWatch Logs để debug lỗi.
{{% /notice %}} 

#### ✅ Hoàn thành

- Bạn đã cập nhật Lambda để tự động ghi metadata mỗi lần inference vào DynamoDB.
- Đây là bước quan trọng để kết nối pipeline inference với hệ thống lưu trữ và giám sát.
+++
title = "Xây dựng Lambda Function và REST API cho Inference"
date = 2025
weight = 7
chapter = false
pre = "<b>7. </b>"
+++

![lambda-inference-overview.png](/images/7.0.png)

Trong phần này, chúng ta sẽ triển khai một **Lambda function** để gọi mô hình đã triển khai trên SageMaker Endpoint (bước 6) và tạo một **REST API Gateway** để client có thể gửi yêu cầu inference. Đây là mắt xích quan trọng giúp biến mô hình ML thành một dịch vụ dự đoán hoàn chỉnh.

---

### 🎯 Mục tiêu
- Tạo Lambda function gọi SageMaker Endpoint để xử lý inference.  
- Kết nối Lambda với API Gateway để tạo REST API.  
- Kiểm tra inference từ Postman hoặc trình duyệt.

---

#### 🧠 7.1 – Tạo Lambda Function để gọi SageMaker Endpoint

1. Truy cập **AWS Management Console** → **Lambda** → **Create function**  
2. Cấu hình:
   - **Function name**: `invoke-ml-endpoint`
   - **Runtime**: `Python 3.9`
   - **Execution role**: Chọn role có quyền gọi SageMaker (hoặc tạo role mới với quyền `AmazonSageMakerFullAccess` và `AWSLambdaBasicExecutionRole`)

3. Nhấn **Create function**
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.1.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.2.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.3.png)

---

#### ✏️ 7.2 – Viết mã Lambda để gọi Endpoint

Thay nội dung mặc định trong tab **Code** bằng đoạn mã sau:

```python
import json
import boto3
import os

runtime = boto3.client('sagemaker-runtime')

ENDPOINT_NAME = os.environ.get('ENDPOINT_NAME', 'ml-blog-endpoint')

def lambda_handler(event, context):
    try:
        body = json.loads(event['body'])
        features = body.get('features')

        if features is None:
            return {
                "statusCode": 400,
                "body": json.dumps({"error": "Missing 'features' in request body"})
            }

        response = runtime.invoke_endpoint(
            EndpointName=ENDPOINT_NAME,
            ContentType='application/json',
            Body=json.dumps({"features": features})
        )

        result = json.loads(response['Body'].read().decode())

        return {
            "statusCode": 200,
            "headers": {"Content-Type": "application/json"},
            "body": json.dumps({"prediction": result})
        }

    except Exception as e:
        return {
            "statusCode": 500,
            "body": json.dumps({"error": str(e)})
        }
```

📌 Giải thích:
- ENDPOINT_NAME: tên endpoint đã triển khai ở bước 6.
- Lambda nhận dữ liệu JSON từ client (features), gọi SageMaker endpoint, và trả kết quả dự đoán.

4. Trong phần Configuration → Environment variables, thêm biến:

    - Key: ENDPOINT_NAME
    - Value: ml-blog-endpoint

5. Nhấn Deploy để lưu function.

#### 🌐 7.3 – Tạo REST API Gateway kết nối Lambda

1. Truy cập API Gateway → Create API
    - Chọn REST API → Build
    - API name: InferenceAPI
    - Endpoint Type: Regional

2. Tạo resource /predict:
    - Trong Resources, chọn Actions → Create Resource
    - Resource name: predict
    - Resource path: /predict
    - Bật Enable API Gateway CORS → Create Resource

3. Thêm phương thức POST:
    - Chọn /predict → Actions → Create Method → POST
    - Integration type: Lambda Function
    - Lambda Function: invoke-ml-endpoint
    - Nhấn Save và xác nhận quyền.

#### 🔄 7.4 – Bật CORS và Triển khai API

1. Trong Resources, chọn Actions → Enable CORS
    - Giữ cấu hình mặc định và nhấn Enable CORS and replace existing CORS headers
2. Triển khai API:
    - Actions → Deploy API
    - Deployment stage: [New Stage] → đặt tên prod
    - Nhấn Deploy

📌 Lưu lại Invoke URL ví dụ: 
~~~~
https://abc123xyz.execute-api.ap-southeast-1.amazonaws.com/prod/predict
~~~~
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.1.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.2.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.3.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.4.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.5.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.6.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.7.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.8.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.9.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.10.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.11.png)
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.12.png)

#### 🧪 7.5 – Kiểm tra API Inference

Bạn có thể dùng Postman hoặc lệnh curl để kiểm tra:
~~~~
curl -X POST \
  https://abc123xyz.execute-api.ap-southeast-1.amazonaws.com/prod/predict \
  -H "Content-Type: application/json" \
  -d '{
    "features": [0.45, 0.12, 0.88, 0.33]
  }'
~~~~
####  ✅ Phản hồi mẫu:
~~~~
{
  "prediction": 1
}
~~~~
![lambda-inference-overview.png](/images/7-Build-Lambda-Inference-and-API/7.13.png)
#### 📊 7.6 – Ghi log và giám sát

- Kiểm tra log Lambda trong CloudWatch Logs → giúp debug nếu xảy ra lỗi.
- Theo dõi metric như Invocations, 4XXError, Latency để đảm bảo API hoạt động ổn định.

{{% notice tip %}}
💡 Bạn có thể thêm xác thực API bằng API Keys, Cognito User Pools hoặc IAM Auth nếu triển khai trong môi trường production.
{{% /notice %}}

#### ✅ Hoàn thành

🎉 Bạn đã xây dựng thành công một Lambda function để gọi SageMaker Endpoint, tạo REST API Gateway, và kiểm tra inference thành công.


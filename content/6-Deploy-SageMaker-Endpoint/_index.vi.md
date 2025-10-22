+++
title = "Triển khai Endpoint của SageMaker cho Inference"
date = 2025
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

![sagemaker-endpoint.png](/images/6.0.png)

Trong phần này, chúng ta sẽ **triển khai mô hình đã train từ bước 5 lên Amazon SageMaker Endpoint**, cho phép gọi inference thông qua API hoặc Lambda function. Đây là bước quan trọng để biến mô hình ML của bạn thành một dịch vụ có thể sử dụng trong thực tế.

---

### 🎯 Mục tiêu
- Tạo endpoint từ model đã train và register ở bước trước.  
- Kiểm tra endpoint bằng dữ liệu mẫu.  
- Chuẩn bị endpoint để tích hợp với Lambda ở bước tiếp theo.

---

#### 🧠 6.1 – Tạo SageMaker Model từ Artifact

Sau khi train và đăng ký model (bước 5), chúng ta sẽ tạo một **SageMaker Model** dựa trên output đó.

1. Truy cập **SageMaker Console** → **Inference → Models**  
2. Nhấn **Create model**

3. Cấu hình:
   - **Model name**: `ml-blog-model`
   - **Execution role**: Chọn IAM role đã tạo ở bước trước (`SageMakerExecutionRole`)
   - **Container definition**:  
     - Image: `382416733822.dkr.ecr.ap-southeast-1.amazonaws.com/xgboost:latest` *(hoặc image bạn đã dùng khi train)*
     - Model artifact location:  
       ```
       s3://ml-pipeline-bucket/model/xgboost-model.tar.gz
       ```

4. Nhấn **Create model** để hoàn tất.
![create-endpoint.png](/images/6-Deploy-SageMaker-Endpoint/6.1.png)
{{% notice tip %}}
📌 **Lưu ý:** Container image và đường dẫn artifact phải trùng khớp với job train đã tạo trước đó.
{{% /notice %}}

---

#### ⚙️ 6.2 – Tạo Endpoint Configuration

1. Điều hướng đến **Inference → Endpoint configurations**  
2. Chọn **Create endpoint configuration**  

3. Cấu hình:
   - **Name**: `ml-blog-endpoint-config`
   - **Model name**: `ml-blog-model`
   - **Instance type**: `ml.m5.large` *(hoặc `ml.t2.medium` nếu muốn tiết kiệm chi phí)*
   - **Initial instance count**: `1`

4. Nhấn **Create endpoint configuration**
![create-endpoint.png](/images/6-Deploy-SageMaker-Endpoint/6.2.png)

---

#### 🌐 6.3 – Triển khai SageMaker Endpoint

1. Điều hướng đến **Inference → Endpoints**  
2. Chọn **Create endpoint**  
3. Nhập:
   - **Endpoint name**: `ml-blog-endpoint`
   - **Endpoint configuration**: Chọn `ml-blog-endpoint-config`

4. Nhấn **Create endpoint**. Quá trình tạo sẽ mất vài phút ⏳.

📸 Ví dụ giao diện triển khai:

![create-endpoint.png](/images/6-Deploy-SageMaker-Endpoint/6.3.png)

{{% notice warning %}}
- Đảm bảo IAM Role có quyền truy cập S3 và SageMaker (`AmazonSageMakerFullAccess`, `AmazonS3ReadOnlyAccess`).
- Endpoint phải ở trạng thái **InService** trước khi sử dụng inference.
{{% /notice %}}

---

#### 🧪 6.4 – Kiểm tra Endpoint bằng boto3 (Python)

Sau khi endpoint ở trạng thái `InService`, hãy kiểm tra inference bằng đoạn mã Python sau:

```python
import boto3
import json

runtime = boto3.client('sagemaker-runtime')

payload = {
    "features": [0.56, 0.32, 0.78, 0.12]  # ví dụ dữ liệu input
}

response = runtime.invoke_endpoint(
    EndpointName='ml-blog-endpoint',
    ContentType='application/json',
    Body=json.dumps(payload)
)

result = json.loads(response['Body'].read().decode())
print("📊 Kết quả dự đoán:", result)
```
#### ✅ Kết quả sẽ trả về giá trị dự đoán (ví dụ: 1 hoặc 0 cho mô hình phân loại).

.

#### 📊 6.5 – Giám sát Endpoint với CloudWatch

Vào CloudWatch → Logs để xem log inference.
Theo dõi metric như:
- Invocations
- Invocation4XXErrors
- ModelLatency

Điều này giúp đánh giá hiệu năng mô hình trong môi trường production.
{{% notice tip %}}
📌 Bạn có thể bật Auto Scaling cho endpoint bằng cách dùng Application Auto Scaling để tự động tăng/giảm số instance theo lưu lượng inference.
{{% /notice %}}

#### ✅ Hoàn thành

Bạn đã triển khai thành công một SageMaker Endpoint từ mô hình đã train.
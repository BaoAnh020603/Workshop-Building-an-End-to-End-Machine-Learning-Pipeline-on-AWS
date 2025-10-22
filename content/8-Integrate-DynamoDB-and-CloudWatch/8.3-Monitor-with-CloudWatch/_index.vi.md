+++
title = "Giám sát với Amazon CloudWatch"
date = 2025
weight = 10
chapter = false
pre = "<b>8.3 </b>"
+++

![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.0.png)

**Amazon CloudWatch** là dịch vụ giám sát và quan sát hệ thống trên AWS. Trong dự án này, CloudWatch giúp theo dõi toàn bộ pipeline inference — từ **Lambda**, **SageMaker Endpoint**, đến **DynamoDB** — nhằm đảm bảo hiệu suất, phát hiện lỗi sớm và tối ưu chi phí.

---

### 🎯 Mục tiêu phần này
- Thu thập và giám sát log từ Lambda và SageMaker.  
- Tạo metric tùy chỉnh để theo dõi số lượng inference, độ trễ và lỗi.  
- Thiết lập cảnh báo (Alarm) khi hiệu suất mô hình hoặc API gặp vấn đề.  

---

#### 1. Kiểm tra log từ Lambda và SageMaker

#### 📜 Log Lambda
- Truy cập **Amazon CloudWatch → Logs → Log groups**.  
- Tìm log group tương ứng với Lambda (ví dụ: `/aws/lambda/ml-inference-lambda`).  
- Kiểm tra chi tiết từng lần gọi inference, bao gồm:
  - Thời gian gọi endpoint.
  - Dữ liệu đầu vào/đầu ra.
  - Thời gian inference (`latencyMs`).
  - Lỗi (nếu có).

![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.1.png)

---

#### 📜 Log SageMaker Endpoint
- Truy cập **Amazon CloudWatch → Logs → Log groups**.  
- Tìm log group có tiền tố `/aws/sagemaker/Endpoints/` và chọn endpoint tương ứng.  
- Xem log để biết thông tin:
  - Mô hình được gọi bao nhiêu lần.
  - Thời gian phản hồi của container mô hình.
  - Lỗi trong quá trình xử lý inference.
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.2.png)
{{% notice tip %}}
Kết hợp log của Lambda và SageMaker để chẩn đoán lỗi nhanh hơn khi inference thất bại.
{{% /notice %}}

---

#### 2. Tạo Custom Metrics từ Lambda

Để giám sát chi tiết hơn (ví dụ: số inference mỗi phút, độ trễ trung bình), bạn có thể gửi **Custom Metrics** từ Lambda lên CloudWatch.

Cập nhật hàm Lambda như sau:

```python
import boto3
import time
import os

cloudwatch = boto3.client('cloudwatch')

def publish_metrics(latency_ms, success=True):
    cloudwatch.put_metric_data(
        Namespace='InferencePipeline',
        MetricData=[
            {
                'MetricName': 'LatencyMs',
                'Value': latency_ms,
                'Unit': 'Milliseconds'
            },
            {
                'MetricName': 'SuccessCount' if success else 'ErrorCount',
                'Value': 1,
                'Unit': 'Count'
            }
        ]
    )

# Gọi hàm này sau mỗi lần inference thành công
publish_metrics(latency_ms, success=True)
```

- Namespace: tên nhóm metric (InferencePipeline).
- LatencyMs: thời gian xử lý inference.
- SuccessCount/ErrorCount: số lượt gọi thành công hoặc lỗi.

#### 3. Tạo Dashboard theo dõi pipeline

1. Truy cập CloudWatch → Dashboards → Create dashboard.
2. Đặt tên: Inference-Monitoring-Dashboard.
3. Thêm các widget:

    - 📊 Metric graph: biểu đồ LatencyMs theo thời gian.
    - 📈 Number: tổng số inference thành công (SuccessCount).
    - ❌ Number: tổng số inference lỗi (ErrorCount).
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.3.png)
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.4.png)
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.5.png)
{{% notice tip %}}
Dashboard giúp bạn theo dõi hiệu suất theo thời gian thực, hỗ trợ tối ưu mô hình và tài nguyên.
{{% /notice %}}

#### 4. Tạo Alarm cảnh báo tự động
Để nhận cảnh báo khi hệ thống gặp sự cố:
1. Truy cập CloudWatch → Alarms → Create alarm.
2. Chọn metric:
- LatencyMs > 2000 ms (2 giây).
- Hoặc ErrorCount > 0.
3. Cấu hình hành động:
- Gửi thông báo qua Amazon SNS (email, SMS).
4. Đặt tên: High-Latency-Alarm hoặc Inference-Error-Alarm.
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.6.png)
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.7.png)
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.8.png)
#### 5. Kiểm tra toàn bộ luồng inference
- Gửi vài yêu cầu đến API inference.
- Kiểm tra:
    - 📜 Log Lambda và SageMaker hiển thị đầy đủ.
    - 📊 Dashboard hiển thị số lượng inference và độ trễ.
    - 🚨 Alarm kích hoạt nếu vượt ngưỡng.
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.9.png)
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.10.png)
![cloudwatch-overview.png](/images/8-Configure-CloudFront/8.3-Monitor-with-CloudWatch/8.3.11.png)
{{% notice warning %}}
Nếu không thấy metric hiển thị, kiểm tra quyền IAM của Lambda (cloudwatch:PutMetricData).
Đảm bảo Lambda gửi metric sau mỗi lần inference.
Kiểm tra múi giờ khi đọc dữ liệu dashboard.
{{% /notice %}}

#### ✅ Hoàn thành
- Bạn đã tích hợp và giám sát toàn bộ pipeline inference với Amazon CloudWatch.
- Hệ thống giờ đây có thể ghi log, đo hiệu suất, phát hiện lỗi sớm và gửi cảnh báo tự động.
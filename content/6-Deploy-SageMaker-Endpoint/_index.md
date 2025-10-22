+++
title = "Deploy SageMaker Endpoint for Inference"
date = 2025
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

![sagemaker-endpoint.png](/images/6.0.png)

In this section, we will **deploy the trained model from step 5 to Amazon SageMaker Endpoint**, allowing inference calls via API or Lambda function. This is an important step to turn your ML model into a service that can be used in the real world.

---

### 🎯 Objectives
- Create an endpoint from the model trained and registered in the previous step.
- Test the endpoint with sample data.
- Prepare the endpoint for integration with Lambda in the next step.

---

#### 🧠 6.1 – Create a SageMaker Model from an Artifact

After training and registering the model (step 5), we will create a **SageMaker Model** based on that output.

1. Go to **SageMaker Console** → **Inference → Models**
2. Click **Create model**
3. Configure:
- **Model name**: `ml-blog-model`
- **Execution role**: Select the IAM role created in the previous step (`SageMakerExecutionRole`)
- **Container definition**:
- Image: `382416733822.dkr.ecr.ap-southeast-1.amazonaws.com/xgboost:latest` *(or the image you used when training)*
- Model artifact location:
```
s3://ml-pipeline-bucket/model/xgboost-model.tar.gz
```
4. Click **Create model** to complete.
![create-endpoint.png](/images/6-Deploy-SageMaker-Endpoint/6.1.png)

{{% notice tip %}}
📌 **Note:** The container image and artifact path must match the previously created train job.
{{% /notice %}}

---

#### ⚙️ 6.2 – Create Endpoint Configuration

1. Navigate to **Inference → Endpoint configurations**
2. Select **Create endpoint configuration**
3. Configuration:
- **Name**: `ml-blog-endpoint-config`
- **Model name**: `ml-blog-model`
- **Instance type**: `ml.m5.large` *(or `ml.t2.medium` if you want to save costs)*
- **Initial instance count**: `1`
4. Click **Create endpoint configuration**
![create-endpoint.png](/images/6-Deploy-SageMaker-Endpoint/6.2.png)

---

#### 🌐 6.3 – Deploy SageMaker Endpoint

1. Navigate to **Inference → Endpoints**
2. Select **Create endpoint**
3. Enter:
- **Endpoint name**: `ml-blog-endpoint`
- **Endpoint configuration**: Select `ml-blog-endpoint-config`
4. Click **Create endpoint**. The creation process will take a few minutes ⏳.

📸 Example deployment interface:

![create-endpoint.png](/images/6-Deploy-SageMaker-Endpoint/6.3.png)

{{% notice warning %}}
- Make sure the IAM Role has access to S3 and SageMaker (`AmazonSageMakerFullAccess`, `AmazonS3ReadOnlyAccess`).
- The endpoint must be in **InService** state before using inference.
{{% /notice %}}

---

#### 🧪 6.4 – Testing Endpoint with boto3 (Python)

Once the endpoint is in `InService` state, check the inference with the following Python code:

```python
import boto3
import json

runtime = boto3.client('sagemaker-runtime')

payload = {
"features": [0.56, 0.32, 0.78, 0.12] # example input data
}

response = runtime.invoke_endpoint(
EndpointName='ml-blog-endpoint',
ContentType='application/json',
Body=json.dumps(payload)
)

result = json.loads(response['Body'].read().decode())
print("📊 Predicted result:", result)
```
#### ✅ The result will return the predicted value (e.g., 1 or 0 for a classification model).

.

#### 📊 6.5 – Endpoint Monitoring with CloudWatch

Go to CloudWatch → Logs to view the inference log.
Monitor metrics such as:
- Invocations
- Invocation4XXErrors
- ModelLatency

This helps evaluate model performance in production environments.
{{% notice tip %}}
📌 You can enable Auto Scaling for the endpoint by using Application Auto Scaling to automatically scale up/down the number of instances based on inference traffic.
{{% /notice %}}

#### ✅ Done

You have successfully deployed a SageMaker Endpoint from the trained model.
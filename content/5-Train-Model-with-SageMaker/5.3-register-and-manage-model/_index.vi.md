+++
title = "Đăng ký và Quản lý Mô hình trong SageMaker Model Registry"
date = 2025
weight = 3
chapter = false
pre = "<b>5.3 </b>"
+++

Trong phần này, chúng ta sẽ **đăng ký mô hình đã huấn luyện** vào **SageMaker Model Registry** để quản lý phiên bản, theo dõi metadata và phục vụ triển khai sau này. Đây là bước quan trọng giúp pipeline ML của bạn trở nên **có thể lặp lại, kiểm soát phiên bản và dễ triển khai**.

---

### 🎯 Mục tiêu

- Đăng ký mô hình đã huấn luyện từ **S3 output** vào **SageMaker Model Registry**.  
- Gắn metadata (phiên bản, thông tin dữ liệu, siêu tham số, metrics).  
- Quản lý các phiên bản mô hình và trạng thái phê duyệt (Approved/Pending/Rejected).  

---

#### 🧠 1. Tổng quan về SageMaker Model Registry

**SageMaker Model Registry** là một dịch vụ quản lý vòng đời mô hình (ML model lifecycle), cho phép bạn:

- Lưu trữ và quản lý các phiên bản mô hình theo thời gian.  
- Gắn thông tin mô hình như metrics, siêu tham số, dữ liệu huấn luyện.  
- Kiểm soát quy trình phê duyệt mô hình trước khi triển khai.  
- Tích hợp trực tiếp với **SageMaker Endpoint** để triển khai mô hình production.

📊 Kiến trúc sau khi thêm Model Registry:

![model-registry-architecture.png](/images/5.3.png)

---

#### 📁 2. Truy cập và tạo Model Package Group

1. Vào **AWS Management Console** → chọn **Amazon SageMaker**.  
2. Trong menu bên trái, chọn **Model registry** → **Model package groups**.  
3. Nhấn **Create model package group**.

Điền thông tin:

- **Name**: `ml-pipeline-model-group`  
- **Description**: “Model group for ML pipeline workshop”  
- Nhấn **Create model package group**.
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.1.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.2.png)

---

#### 📤 3. Đăng ký mô hình đã huấn luyện

Bây giờ chúng ta sẽ tạo một **Model Package** từ mô hình đã lưu sau training (`model.tar.gz`) trong S3.

1. Trong **Model registry** → chọn group vừa tạo → **Create model package**.
2. Cấu hình như sau:

- **Model package name**: `ml-pipeline-model-v1`  
- **Model location (S3)**:  s3://ml-pipeline-bucket/model/model.tar.gz

- **Inference image URI**:  
- Nếu dùng built-in XGBoost: chọn container có sẵn từ SageMaker.  
- Nếu custom: nhập ECR container image của bạn.

- **IAM Role**: `SageMakerExecutionRole` (có quyền truy cập S3 và SageMaker).  
- **Approval status**: `Pending manual approval` *(hoặc Approved nếu sẵn sàng triển khai)*.

![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.4.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.5.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.6.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.7.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.8.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.9.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.10.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.11.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.12.png)
![register-model.png](/images/5-Configure-API-Gateway/5.3-register-and-manage-model/5.3.13.png)


---

#### 🧪 4. Gắn metadata & model metrics (tùy chọn)

Bạn có thể đính kèm thông tin quan trọng để giúp nhóm ML/DevOps hiểu rõ mô hình:

- **Training dataset version**: `v1.0`  
- **Algorithm**: `XGBoost`  
- **Accuracy**: `0.912`  
- **Hyperparameters**: learning_rate, max_depth, n_estimators  
- **Created by**: `Lambda Preprocessing Pipeline`

📌 Điều này rất hữu ích khi bạn có nhiều mô hình và cần chọn mô hình tốt nhất để deploy.

---

#### ✅ 5. Quản lý phiên bản mô hình

Mỗi lần bạn tạo một **Model Package** mới, nó sẽ trở thành một **version** trong group:

| Version | Model Name              | Accuracy | Status      | Approval         |
|--------:|-------------------------|----------|-------------|------------------|
| 1       | ml-pipeline-model-v1    | 0.912    | Completed   | Approved ✅       |
| 2       | ml-pipeline-model-v2    | 0.927    | Completed   | Pending 🕐       |

📈 Bạn có thể cập nhật trạng thái mô hình từ `Pending` → `Approved` khi đã đánh giá xong.

---

#### 🔎 6. Kiểm tra mô hình đã đăng ký bằng AWS CLI (tùy chọn)

Nếu muốn tự động hóa, bạn có thể đăng ký mô hình bằng CLI:

```bash
aws sagemaker create-model-package \
--model-package-group-name ml-pipeline-model-group \
--model-package-description "ML pipeline v1 model" \
--model-approval-status Approved \
--inference-specification '{"Containers": [{"Image": "<IMAGE_URI>", "ModelDataUrl": "s3://ml-pipeline-bucket/model/model.tar.gz"}], "SupportedContentTypes": ["text/csv"], "SupportedResponseMIMETypes": ["text/csv"]}'
```
#### 🧹 7. Cập nhật và kiểm soát vòng đời mô hình

- Khi huấn luyện mô hình mới, bạn chỉ cần tạo một Model Package mới trong cùng một Model Package Group.
- Điều này giúp theo dõi lịch sử mô hình và dễ dàng rollback nếu mô hình mới không đạt yêu cầu.

#### 🎯 Hoàn thành

- Bạn đã đăng ký thành công mô hình đã huấn luyện vào SageMaker Model Registry, đồng thời quản lý phiên bản và trạng thái phê duyệt.
- Đây là bước quan trọng giúp quy trình ML của bạn có tính tổ chức và dễ dàng triển khai mô hình vào production.
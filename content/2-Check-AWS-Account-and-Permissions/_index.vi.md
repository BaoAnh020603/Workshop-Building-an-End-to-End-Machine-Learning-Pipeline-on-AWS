+++
title = "Kiểm tra Tài khoản và Quyền AWS"
date = 2025
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

> 💡 **Best practice:** Không dùng **Root User** để thao tác hằng ngày. Thay vào đó, hãy tạo **IAM Role** và **IAM Policy** với **quyền tối thiểu cần thiết** để Lambda, SageMaker và các dịch vụ khác truy cập vào **S3**, **DynamoDB**, **CloudWatch** trong pipeline của bạn.

Trong phần này, bạn sẽ:

- Kiểm tra tài khoản AWS và vùng hoạt động (Region).
- Tạo IAM Policy cho phép Lambda và SageMaker truy cập DynamoDB, S3, CloudWatch.
- Tạo IAM Role và gán cho Lambda.
- (Tùy chọn) tạo IAM Role cho SageMaker.
- Kiểm tra quyền hoạt động bằng Lambda test.

---

#### 1. Kiểm tra tài khoản AWS và Region

- Đăng nhập vào [AWS Management Console](https://console.aws.amazon.com/)
- Kiểm tra **Region** ở góc trên bên phải (nên sử dụng `us-east-1` hoặc `ap-southeast-1` để đồng nhất pipeline).
- Nếu chưa kích hoạt IAM, vào [IAM Console](https://console.aws.amazon.com/iam/) để bật dịch vụ.

![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.1.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.2.png)

{{% notice tip %}}
📍 **Lưu ý:** Hầu hết các dịch vụ AWS hoạt động theo vùng. Nếu bạn tạo tài nguyên ở một vùng và gọi ở vùng khác, pipeline có thể **không hoạt động hoặc lỗi `ResourceNotFound`**.
{{% /notice %}}

---

#### 2. Tạo Custom IAM Policy

Chúng ta sẽ tạo một policy để cấp quyền truy cập DynamoDB, S3, SageMaker và CloudWatch cho Lambda.
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.3.png)
- Điều hướng đến: **IAM → Policies → Create policy**
- Chọn tab **JSON** và dán nội dung sau:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject","s3:PutObject","s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::your-bucket-name",
        "arn:aws:s3:::your-bucket-name/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": ["dynamodb:PutItem","dynamodb:UpdateItem","dynamodb:GetItem","dynamodb:Scan","dynamodb:Query"],
      "Resource": "arn:aws:dynamodb:us-east-1:*:table/ModelMetadata"
    },
    {
      "Effect": "Allow",
      "Action": [
        "sagemaker:CreateTrainingJob",
        "sagemaker:DescribeTrainingJob",
        "sagemaker:CreateModel",
        "sagemaker:CreateEndpoint",
        "sagemaker:InvokeEndpoint"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": ["logs:CreateLogGroup","logs:CreateLogStream","logs:PutLogEvents"],
      "Resource": "*"
    }
  ]
}
```
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.4.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.5.png)
{{% notice note %}}
🔐 Giải thích:
Phân quyền DynamoDB chỉ cho phép Lambda thao tác trên bảng ModelMetadata.
Phân quyền S3 giới hạn chỉ trong bucket bạn chỉ định.
logs:* cần thiết để Lambda ghi log ra CloudWatch.
{{% /notice %}}
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.6.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.7.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.8.png)
{{% notice warning %}}
⚠️ Cảnh báo: Đừng để "Resource": "*" cho DynamoDB hoặc S3 trong môi trường production. Luôn giới hạn ARN cụ thể để tăng bảo mật.
{{% /notice %}}

#### 3. Tạo IAM Role cho Lambda

- Điều hướng đến: IAM → Roles → Create role
- Chọn AWS Service → Lambda → Next
- Gán policy ml-pipeline-access-policy vừa tạo.
- Gán thêm AWSLambdaBasicExecutionRole để cho phép ghi log.
- Đặt tên: lambda-ml-pipeline-role → Create role
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.9.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.10.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.11.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.12.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.13.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.14.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.15.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.16.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.17.png)
{{% notice info %}}
📘 Thông tin: Role này sẽ được gán cho tất cả các Lambda functions trong pipeline, ví dụ: PreprocessLambda, InferenceLambda...
{{% /notice %}}

#### 4. Gán IAM Role cho Lambda Functions

- Vào Lambda Console → Functions → [Tên function] → Configuration → Permissions
- Chọn Edit và thay đổi role thành lambda-ml-pipeline-role

{{% notice tip %}}
📍 Lưu ý: Gán nhầm role là nguyên nhân phổ biến nhất gây lỗi AccessDenied khi Lambda truy cập DynamoDB hoặc S3.
{{% /notice %}}

#### 5. (Tùy chọn) Tạo IAM Role cho SageMaker

- Nếu bạn dùng SageMaker để train và deploy model:
- Vào IAM → Roles → Create role
- Chọn SageMaker
- Gán ml-pipeline-access-policy
- Đặt tên: sagemaker-ml-pipeline-role

#### 6. Kiểm tra lại quyền và Test Lambda

- Vào IAM → Roles → lambda-ml-pipeline-role
- Đảm bảo policy đã gán đầy đủ và ARN đúng tài nguyên.
- Tạo Test event trong Lambda và chạy thử:

```
{
  "statusCode": 200,
  "body": "Preprocessing complete and metadata updated",
  "headers": { "Content-Type": "application/json" }
}
```

{{% notice warning %}}
⚠️ Nếu nhận lỗi AccessDenied:
Kiểm tra log trong CloudWatch.
Xác minh region (us-east-1).
Đảm bảo tên bảng DynamoDB và bucket S3 khớp với ARN trong policy.
{{% /notice %}}
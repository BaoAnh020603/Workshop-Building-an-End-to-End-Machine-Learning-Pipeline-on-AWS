+++
title = "Check AWS Account and Permissions"
date = 2025
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

> 💡 **Best practice:** Do not use **Root User** for daily operations. Instead, create **IAM Role** and **IAM Policy** with **minimum required permissions** for Lambda, SageMaker and other services to access **S3**, **DynamoDB**, **CloudWatch** in your pipeline.

In this section, you will:

- Check AWS account and region.
- Create IAM Policy to allow Lambda and SageMaker to access DynamoDB, S3, CloudWatch.
- Create IAM Role and assign it to Lambda.
- (Optional) create IAM Role for SageMaker.
- Test permissions using Lambda test.

---

#### 1. Check AWS Account and Region

- Log in to [AWS Management Console](https://console.aws.amazon.com/)
- Check **Region** in the upper right corner (use `us-east-1` or `ap-southeast-1` to align the pipeline).

- If IAM is not enabled, go to [IAM Console](https://console.aws.amazon.com/iam/) to enable the service.

![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.1.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.2.png)

{{% notice tip %}}
📍 **Note:** Most AWS services operate by region. If you create a resource in one region and call it in another, the pipeline may **not work or give a `ResourceNotFound` error**.

{{% /notice %}}

---

#### 2. Create Custom IAM Policy

We will create a policy to grant access to DynamoDB, S3, SageMaker, and CloudWatch to Lambda.

- Navigate to: **IAM → Policies → Create policy**
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.3.png)
- Select the **JSON** tab and paste the following content:

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
🔐 Explanation:
DynamoDB permissions only allow Lambda to operate on the ModelMetadata table.
S3 permissions are limited to the bucket you specify.
logs:* is required for Lambda to log to CloudWatch.
{{% /notice %}}
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.6.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.7.png)
![Check Region](/images/2-Check-AWS-Account-and-Permissions/2.8.png)
{{% notice warning %}}
⚠️ Warning: Do not leave "Resource": "*" for DynamoDB or S3 in production. Always limit to a specific ARN for added security.
{{% /notice %}}

#### 3. Create an IAM Role for Lambda

- Navigate to: IAM → Roles → Create role
- Select AWS Service → Lambda → Next
- Assign the newly created ml-pipeline-access-policy policy.
- Assign AWSLambdaBasicExecutionRole to enable logging.
- Name: lambda-ml-pipeline-role → Create role
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
📘 Information: This role will be assigned to all Lambda functions in the pipeline, for example: PreprocessLambda, InferenceLambda...
{{% /notice %}}

#### 4. Assign IAM Role to Lambda Functions

- Go to Lambda Console → Functions → [Function name] → Configuration → Permissions
- Select Edit and change the role to lambda-ml-pipeline-role

{{% notice tip %}}
📍 Note: Assigning the wrong role is the most common cause of AccessDenied errors when Lambda accesses DynamoDB or S3.
{{% /notice %}}

#### 5. (Optional) Create IAM Role for SageMaker

- If you use SageMaker to train and deploy the model:
- Go to IAM → Roles → Create role
- Select SageMaker
- Assign ml-pipeline-access-policy
- Name: sagemaker-ml-pipeline-role

#### 6. Check permissions and Test Lambda

- Go to IAM → Roles → lambda-ml-pipeline-role
- Make sure the policy is fully assigned and ARN the correct resource.
- Create a Test event in Lambda and run it:

```
{
"statusCode": 200,
"body": "Preprocessing complete and metadata updated",
"headers": { "Content-Type": "application/json" }
}
```

{{% notice warning %}}
⚠️ If you get an AccessDenied error:
Check the log in CloudWatch.
Verify the region (us-east-1).
Make sure the DynamoDB table name and S3 bucket match the ARN in the policy.
{{% /notice %}}
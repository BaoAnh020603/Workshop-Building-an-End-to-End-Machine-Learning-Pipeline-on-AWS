+++
title = "Clean Up Resources"
date = 2025
weight = 9
chapter = false
pre = "<b>9. </b>"
+++

After completing the entire **"Building an End-to-End Machine Learning Pipeline on AWS"** workshop, the final step is to **clean up all AWS resources** that you created during the deployment.

This is extremely important to avoid incurring costs outside of the Free Tier, ensuring security and keeping your AWS account clean for future projects.

---

### 🎯 Goal of this step

- **💸 Cost Savings**: Prevent costs from services that are no longer in use.
- **🔐 Security**: Remove unnecessary IAM, API and resource permissions to reduce security risks.
- **🧰 Clean and easy to manage**: Keep your AWS account clean and ready for new workshops or projects.

---

### 🗑️ Resources to delete

Here is a list of resources that have been used throughout the project that you need to delete:

- **Amazon CloudFront** – CDN for delivering applications and content.
- **Amazon S3** – Stores data and models.
- **Amazon API Gateway** – REST API connecting Lambda and endpoint inference.
- **AWS Lambda** – Functions for data processing, preprocessing, and inference.
- **Amazon DynamoDB** – Table for storing metadata and inference results.
- **AWS IAM** – Roles and permissions for Lambda and SageMaker.

---

### 🧼 Detailed Cleanup Guide

#### 1. 🧭 Delete a CloudFront Distribution

1. Go to **CloudFront** from the **AWS Management Console**.
2. Select the distribution you created (e.g., `d1234567890abcdef.cloudfront.net`).
3. Click **Disable** and wait for the status to change to **Disabled**.
4. Click **Delete** to completely delete the distribution.

---

#### 2. 📦 Delete an Amazon S3 bucket

1. Go to **S3** from the AWS console.
2. Select the bucket you created (e.g., `ml-workshop-data-<account-id>`).
3. Click **Empty**, enter `permanently delete` to confirm, then select **Empty**.
4. When the bucket is empty, click **Delete bucket**, enter the bucket name to confirm the deletion.

![Check Region](/images/9-Clean-Up-Resources/9.1.png)
![Check Region](/images/9-Clean-Up-Resources/9.2.png)
![Check Region](/images/9-Clean-Up-Resources/9.3.png)

---

#### 3. 🌐 Delete API Gateway

1. Go to **API Gateway**.
2. Select the API you deployed (e.g., `InferenceAPI`).
3. Click **Actions → Delete**, enter the API name to confirm and complete the deletion.

![Check Region](/images/9-Clean-Up-Resources/9.4.png)
![Check Region](/images/9-Clean-Up-Resources/9.5.png)
![Check Region](/images/9-Clean-Up-Resources/9.6.png)

---

#### 4. 🧠 Delete AWS Lambda functions

1. Go to **Lambda** from the console.
2. Delete all the functions you created, e.g.,
- `preprocessing-function`
- `inference-function`
3. Click **Actions → Delete**, confirm the deletion of each function.

![Check Region](/images/9-Clean-Up-Resources/9.7.png)
![Check Region](/images/9-Clean-Up-Resources/9.8.png)
![Check Region](/images/9-Clean-Up-Resources/9.9.png)

---

#### 5. 📊 Delete a DynamoDB table

1. Go to **DynamoDB** → **Tables**.
2. Select the table you created (e.g., `InferenceMetadata`).
3. Click **Actions → Delete table**, enter the table name to confirm.

![Check Region](/images/9-Clean-Up-Resources/9.10.png)
![Check Region](/images/9-Clean-Up-Resources/9.11.png)
![Check Region](/images/9-Clean-Up-Resources/9.12.png)

---

#### 6. 🔐 Delete IAM resources

1. Go to **IAM** in the console.
2. In **Policies**, select the created policy (e.g., `lambda-inference-policy`) → **Delete**.
3. In **Roles**, select the related role (e.g., `lambda-inference-role`) → **Delete**.

![Check Region](/images/9-Clean-Up-Resources/9.13.png)
![Check Region](/images/9-Clean-Up-Resources/9.14.png)
![Check Region](/images/9-Clean-Up-Resources/9.15.png)
![Check Region](/images/9-Clean-Up-Resources/9.16.png)
![Check Region](/images/9-Clean-Up-Resources/9.17.png)

---

{{% notice warning %}}
⚠️ **Important Notes:**
- Make sure your **S3 bucket is empty** before deleting.
- Double-check your resources before deleting to avoid accidentally deleting resources from other projects.
- If you encounter errors when deleting (e.g., a resource is still referenced), check any **dependencies** such as IAM permissions, API Gateway endpoints, or CloudFront distributions.
{{% /notice %}}

---

#### ✅ Cleanup Results

- All project resources have been deleted.
- Your AWS account no longer has any resources that incur charges.
- You can start new workshops or projects without conflicting with old resources.
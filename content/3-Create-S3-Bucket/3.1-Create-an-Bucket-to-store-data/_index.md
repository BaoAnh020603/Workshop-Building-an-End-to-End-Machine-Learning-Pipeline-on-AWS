+++
title = "Create an S3 Bucket to store data"
date = 2025
weight = 3
chapter = false
pre = "<b>3.1 </b>"
+++

#### What is Amazon S3?

**Amazon S3 (Simple Storage Service)** is an object storage service from AWS that is virtually infinitely scalable, highly durable, and easily integrated with other services such as **Lambda**, **CloudFront**, and **API Gateway**.

In this project, S3 will serve as a place to store all static data such as:

- 🗂️ **Web Frontend** built with React.js
- 🖼️ Images and article content (media)
- 📦 Temporary files or data needed for Lambda to process

---

#### Why create an S3 Bucket?

- 📁 **Static website hosting**: Upload the entire frontend (HTML, CSS, JS) after building so that the website can be accessed via CloudFront.

- 🔁 **Integrated with CloudFront**: S3 is the original data source for CDN to help optimize page loading speed.

- 🔐 **Integrated with Lambda**: Allow Lambda to access/write data when processing is needed.

- ⚙️ **Serverless architecture**: No need for a server to store content, everything runs completely serverless.

---

#### 🪣 3.1. Create an S3 Bucket

1. Access [**AWS Management Console**](https://aws.amazon.com/console/)
2. Open the **Amazon S3** service.

3. In the left menu, select **Buckets**, then click **Create bucket**.

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.1.png)

---

4. In the **Create bucket** interface:

- **Bucket name**: set a globally unique name, for example: `my-serverless-blog-bucket`
- **AWS Region**: select the same region as Lambda and DynamoDB (for example: `us-east-1`)
- **Block Public Access**: uncheck if you want the bucket to be public for frontend storage (will be adjusted later).
- Keep the other parts default.
- Click **Create bucket**

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.2.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.3.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.4.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.5.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.6.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.7.png)

{{% notice tip %}}
💡 **Hint:** The bucket name must be **globally unique** – if the name you enter already exists, add a suffix like `-2025` or your project name.

{{% /notice %}}

---

#### 📂 3.2. Check and configure the Bucket

- Once created, you will see your bucket in the list.

- Click on the bucket name to open the details.

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.8.png)

- Go to the **Properties** tab to check the overall configuration.

- Go to the **Permissions** tab to configure access (important if you want to save the frontend publicly).

![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.9.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.10.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.11.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.12.png)
![open-s3.png](/images/3-Create-DynamoDB-Table/3.1-Create-an-S3-Bucket-to-store-data/3.13.png)

{{% notice warning %}}
⚠️ If you plan to host a static website from S3, you need to **allow public access**.
Make sure you **understand the security risks** of enabling public access.
{{% /notice %}}

---

#### ✅ Done

🎉 You have successfully created an **S3 Bucket** – the central storage component in your project's serverless architecture.

In the next steps, we will:

- Upload the built frontend to the bucket
- ​​Connect CloudFront to distribute content globally
- Configure Lambda to write/read data if needed

{{% notice note %}}
📌 **Summary:** S3 is a platform for storing static data and content for web applications. Make sure the bucket is in the same region as other services to avoid access errors and optimize costs.
{{% /notice %}}
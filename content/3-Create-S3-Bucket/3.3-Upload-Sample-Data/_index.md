+++
title = "Upload sample data"
date = 2025
weight = 5
chapter = false
pre = "<b>3.3 </b>"
+++

In this section, we will **upload sample data (frontend build)** to **Amazon S3 bucket** to test the accessibility of the static website before connecting to API Gateway and Lambda.

---

#### 📦 Why upload sample data?
{{% notice tip %}}
Uploading sample data to S3 helps you:
- ✅ Verify that the **bucket is working correctly** and the website can be distributed.
- ✅ Test the accessibility of the **React/Vite frontend** via the browser.
- ✅ Ensure the data structure is correct before integrating with the backend.
{{% /notice %}}

---

#### Step 1 – Build the frontend application

If you use **React + Vite**, in the frontend folder run:

~~~
npm install
npm run build
~~~

This command will create a dist/ (or build/) folder containing static files ready to upload to S3.

- The folder structure after build can be as follows:
~~~~
dist/
│
├─ index.html
├─ favicon.ico
├─ assets/
│ ├─ main.js
│ └─ style.css
└─ logo.png
~~~~

#### Step 2 - Upload frontend build to S3
- Access Amazon S3 Console
- Select the bucket you created in step 3.1
- Create S3 Bucket
- ​​Click Upload
- Select all contents in dist/ (or build/) folder
- Click Upload to complete the upload process
- Example to illustrate the upload process:

#### Step 3 - Enable Static Website Hosting (if not enabled)
- In the Properties tab of the bucket
- ​​Scroll down to the Static website section hosting
- Select Enable
- Specify:
~~~
Index document: index.html
Error document: index.html (if using React Router)
~~~

{{% notice note %}}
The index.html file must be located at the root of the bucket, not in a subfolder. If the website has routing (SPA), set Error document = index.html.
{{% /notice %}}

#### Step 4 – Test website access
- Copy the Static website endpoint URL from the bucket configuration.
- Paste the URL into the browser to check the result.
- Example:
~~~
http://my-blog-frontend.s3-website-ap-southeast-1.amazonaws.com
~~~
- Result when accessing successfully:
![open-s3.png](/images/3-Create-DynamoDB-Table/3.3-Upload-Sample-Data/3.3.1.png)

#### Important note
{{% notice warning %}}
Check the permissions of the bucket again if you cannot access the website. Make sure the index.html file has been uploaded to the correct location. If you want to use CloudFront in the future, you don't need to enable "Public access" for the bucket – CloudFront will access it for you.
{{% /notice %}}

#### ✅ Done
You have successfully uploaded the sample frontend build to the S3 bucket and verified the accessibility of the static website.
+++
title = "Create an S3 Bucket to Store Data"
date = 2025
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

In this section, we will create an **Amazon S3 bucket** to serve as a central storage for all data for the Machine Learning Pipeline project.

S3 will act as a **data lake** – a place to store input data (raw data), pre-processed data (processed data), model artifacts as well as prediction data (inference results). This is an important starting point for the pipeline to operate automatically and connect between AWS services.

{{% notice tip %}}
💡 **Hint:** Always choose the same region for S3, Lambda and SageMaker to reduce latency and avoid access errors.
{{% /notice %}}

### Contents
- [**3.1. Create an S3 Bucket to store data**](3.1-Create-an-Bucket-to-store-data/)
- [**3.2. S3 Data Organization Structure**](3.2-Organize-Data-Structure/)
- [**3.3. Upload sample data to S3 to test pipeline**](3.3-Upload-Sample-Data/)

{{% notice note %}}
📦 **Amazon S3 (Simple Storage Service)** is an object storage service that makes it easy to manage big data, automatically scales, and seamlessly integrates with services like Lambda, SageMaker, and API Gateway.
{{% /notice %}}
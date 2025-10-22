+++
title = "Introduction"
date = 2025-10-02T00:38:32+07:00
weight = 1
chapter = false
pre="<b>1. </b>"
+++

# Workshop: Building an End-to-End Machine Learning Pipeline on AWS with Lambda, API Gateway, S3, SageMaker & DynamoDB

In this workshop, you will learn how to build and deploy a complete **end-to-end machine learning pipeline** on AWS.  
We will use **AWS Lambda** for preprocessing and inference, **Amazon API Gateway** to expose RESTful APIs, **Amazon S3** for data storage, **Amazon SageMaker** for model training and deployment, **Amazon DynamoDB** for metadata storage, and **Amazon CloudWatch** for monitoring and logging.

![Workshop Architecture](/images/an%20automated%20Machine%20Learning%20(ML)%20pipeline%20system%20on%20AWS.drawio%20(1).svg)

---

### Objectives

- Understand how to design and deploy an ML pipeline on AWS from raw data ingestion to model deployment.
- Implement data preprocessing and model inference with **AWS Lambda**.
- Train and register models using **Amazon SageMaker**.
- Expose RESTful endpoints for inference using **Amazon API Gateway**.
- Store metadata and inference logs in **Amazon DynamoDB**.
- Monitor system performance with **Amazon CloudWatch**.
- Learn cost management and AWS Free Tier optimization strategies.

---

### Requirements

- An AWS account (Free Tier: https://aws.amazon.com/free)  
- Basic Python skills (for Lambda functions and ML scripts)  
- Familiarity with REST APIs and JSON  
- Tools: AWS CLI, Git, Docker (optional), web browser  
- (Optional) Postman for testing APIs

---

### Architecture Overview

The solution consists of several serverless and managed AWS services working together to automate the ML workflow:

**1. AWS Lambda**  
**Uses:**  
- Data preprocessing before training (e.g., cleaning, feature extraction).  
- Running inference on new incoming data.  
- Integrating different stages of the pipeline.  

**Cost:**  
| Status | Requests | Cost/day | Cost/month |
|--------|----------|----------|------------|
| Free Tier | 1M requests | **$0.00** | **$0.00** |
| After Free Tier | 100K requests | ~$0.01 | ~$0.30 |

---

**2. Amazon API Gateway**  
**Uses:**  
- Exposes RESTful endpoints for triggering Lambda functions and model inference.  
- Acts as the interface between clients and backend services.

**Cost:**  
| Status | Requests | Cost/day | Cost/month |
|--------|----------|----------|------------|
| Free Tier | 1M requests | **$0.00** | **$0.00** |
| After Free Tier | 3M requests | ~$0.35 | ~$10.50 |

---

**3. Amazon S3**  
**Uses:**  
- Stores raw datasets, preprocessing outputs, and model artifacts.  
- Centralized data lake for the ML workflow.

**Cost:**  
| Status | Storage | Cost/day | Cost/month |
|--------|----------|----------|------------|
| Free Tier | 5GB storage | **$0.00** | **$0.00** |
| After Free Tier | 5GB | ~$0.03 | ~$1.00 |

---

**4. Amazon SageMaker**  
**Uses:**  
- Trains and registers machine learning models.  
- Hosts models as managed endpoints for real-time inference.

**Cost:**  
| Status | Type | Cost/day | Cost/month |
|--------|------|----------|------------|
| Trial | 250 hours/month | **$0.00** | **$0.00** |
| After Free Tier | ml.t2.medium | ~$0.10 | ~$3.00 |

---

**5. Amazon DynamoDB**  
**Uses:**  
- Stores metadata such as model version, training metrics, and inference logs.  
- Provides low-latency access for serverless apps.

**Cost:**  
| Status | Capacity | Cost/day | Cost/month |
|--------|----------|----------|------------|
| Free Tier | 25 read/write units | **$0.00** | **$0.00** |
| After Free Tier | 10K read/write | ~$0.02 | ~$0.60 |

---

**6. Amazon CloudWatch**  
**Uses:**  
- Monitors logs, tracks Lambda execution metrics, and provides alerts.  

**Cost:**  
- Typically **Free** for basic metrics.

---

### Cost Summary

| Service | Cost (Free Tier) | Cost (After Free Tier) |
|--------|------------------|------------------------|
| **AWS Lambda** | **$0.00** | ~$0.30 |
| **Amazon API Gateway** | **$0.00** | ~$10.50 |
| **Amazon S3** | **$0.00** | ~$1.00 |
| **Amazon SageMaker** | **$0.00** | ~$3.00 |
| **Amazon DynamoDB** | **$0.00** | ~$0.60 |
| **Amazon CloudWatch** | **$0.00** | ~$0.00 |
| **Total** | **$0.00** | **~$15.40/month** |

---

### Workflow

1. **Data Ingestion** – Raw data is uploaded to S3.  
2. **Preprocessing** – A Lambda function cleans and transforms the data.  
3. **Training** – SageMaker trains the model and registers it in the model registry.  
4. **Deployment** – The model is deployed as a SageMaker endpoint.  
5. **Inference** – Lambda + API Gateway provide real-time predictions to client applications.  
6. **Metadata & Monitoring** – DynamoDB stores metadata, CloudWatch monitors logs and metrics.

---

### Conclusion

This workshop shows how to build a **production-ready, scalable ML pipeline** entirely on AWS using serverless and managed services.  
It minimizes operational overhead, scales automatically, and remains cost-effective — often **$0 during the Free Tier** and around **$15/month** after that, depending on traffic and usage.


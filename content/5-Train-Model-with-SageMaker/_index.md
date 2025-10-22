+++
title = "Train and Register Models with Amazon SageMaker"
date = 2025
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

In this section, we will **train a machine learning model** with Amazon SageMaker using preprocessed data from Lambda and stored on S3. This is an important step to create a model that is ready to serve inference via API later.

---

## 🎯 Objectives

- Learn how to create a **SageMaker Training Job** from processed data.

- Configure training parameters and choose algorithms.

- Register the model (Model Registry) for later deployment.

- Manage model versions and track training progress.

---

## 📚 Contents

- [**5.1 Prepare input data for SageMaker**](5.1-prepare-training-data/)
- [**5.2 Create SageMaker Training Job**](5.2-create-training-job/)
- [**5.3 Register model and manage version**](5.3-register-and-manage-model/)
- [**5.4 Check training results**](5.4-validate-training-results/)

---

## 🧠 Architecture overview

![SageMaker-Training-Architecture](/images/5.0.png)

1. **Lambda** creates processed data and saves it to the `processed/` folder on S3.

2. **SageMaker** reads data from S3, trains the model using the algorithm of your choice.

3. The resulting model is stored in `model/` and can be registered in the **Model Registry**.

4. The registered model will be used to deploy inference in the next step.

---

## 📦 Prerequisites

- Completed **Lambda Preprocessing Function** (Chapter 4).

- Has processed data in the `processed/` folder of the S3 bucket.

- SageMaker IAM Role with access to **S3**, **CloudWatch**, and **SageMaker**.

---

✅ After completing this chapter, you will have a trained and registered ML model
+++
title = "Xác thực Kết quả Huấn luyện Mô hình"
date = 2025
weight = 4
chapter = false
pre = "<b>5.4 </b>"
+++

Trong phần này, chúng ta sẽ học cách **xác thực kết quả huấn luyện** để đảm bảo mô hình đã được huấn luyện thành công và đạt chất lượng trước khi triển khai vào production. Đây là một bước **rất quan trọng** trong quy trình ML pipeline vì nó giúp bạn đánh giá chất lượng mô hình, kiểm tra output và quyết định có nên triển khai hay cần điều chỉnh lại dữ liệu/siêu tham số.

---

### 🎯 Mục tiêu

- Kiểm tra trạng thái job huấn luyện trong SageMaker.  
- Phân tích log và output của quá trình training.  
- Đánh giá metrics (độ chính xác, F1, AUC…) của mô hình.  
- Xác thực file mô hình đầu ra trước khi đưa vào Model Registry hoặc deploy.

---

#### 📊 1. Kiểm tra trạng thái Training Job

Truy cập vào **AWS Management Console** → **Amazon SageMaker** → **Training jobs** để xem danh sách job huấn luyện đã tạo ở bước [5.2 – Tạo Training Job](../5.2-create-training-job/).

- Cột **Status** sẽ hiển thị trạng thái:
  - ✅ `Completed` – Hoàn tất thành công.  
  - ❌ `Failed` – Huấn luyện thất bại (kiểm tra log).  
  - 🕐 `InProgress` – Đang huấn luyện.

![register-model.png](/images/5-Configure-API-Gateway/5.4-validate-training-results/5.4.1.png)

{{% notice warning %}}
Nếu trạng thái là **Failed**, hãy kiểm tra log CloudWatch để xác định nguyên nhân (ví dụ: lỗi quyền S3, lỗi code training script, hoặc thiếu dữ liệu).
{{% /notice %}}

---

#### 📁 2. Kiểm tra Output Model trong S3

Sau khi huấn luyện thành công, SageMaker sẽ lưu mô hình vào S3 theo đường dẫn bạn đã chỉ định: s3://ml-pipeline-bucket/model/model.tar.gz

- `model.tar.gz` chứa mô hình đã được serialize (ví dụ: pickle, joblib hoặc định dạng framework như TensorFlow SavedModel).
- Bạn có thể tải file này về máy và kiểm tra cấu trúc để đảm bảo nội dung hợp lệ.
![register-model.png](/images/5-Configure-API-Gateway/5.4-validate-training-results/5.4.2.png)
![register-model.png](/images/5-Configure-API-Gateway/5.4-validate-training-results/5.4.3.png)
![register-model.png](/images/5-Configure-API-Gateway/5.4-validate-training-results/5.4.4.png)

---

#### 📜 3. Phân tích log trong CloudWatch

1. Vào **CloudWatch Logs** → **Log groups** → tìm nhóm log tương ứng với training job.  
2. Xem log output từ script huấn luyện – bạn sẽ thấy thông tin như:

~~~
[INFO] Training accuracy: 0.912
[INFO] Validation accuracy: 0.905
[INFO] Model saved to /opt/ml/model/
~~~

📌 Nếu bạn ghi metrics ra stdout hoặc lưu vào `/opt/ml/output/metrics.json`, SageMaker sẽ tự động thu thập để hiển thị trong giao diện.

---

#### 📈 4. Đánh giá metrics huấn luyện

Metrics là thước đo chính để quyết định mô hình có thể đưa vào production hay không.  
Một số metrics phổ biến:

| Metric                | Ý nghĩa                                      |
|-----------------------|----------------------------------------------|
| Accuracy             | Tỷ lệ dự đoán đúng trên tổng số mẫu.        |
| Precision            | Độ chính xác khi mô hình dự đoán Positive. |
| Recall               | Khả năng phát hiện toàn bộ Positive cases. |
| F1-Score            | Trung bình điều hòa giữa Precision và Recall.|
| AUC-ROC             | Khả năng phân biệt giữa các lớp.             |

{{% notice tip %}}
Bạn nên đặt **ngưỡng tối thiểu** cho các metrics này (ví dụ: `Accuracy > 0.90`) để tự động phê duyệt hoặc từ chối mô hình trước khi đăng ký.
{{% /notice %}}
![register-model.png](/images/5-Configure-API-Gateway/5.4-validate-training-results/5.4.5.png)

---

#### 🔬 5. Xác thực tính đúng đắn của mô hình

- Kiểm tra mô hình có thể **load và inference** cục bộ không:

```python
import joblib
model = joblib.load("model.tar.gz")
print(model.predict([[5.1, 3.5, 1.4, 0.2]]))
```

- Nếu dùng TensorFlow/PyTorch, kiểm tra bằng hàm load_model() hoặc torch.load().
- Nếu mô hình không thể load, có thể training script đã không lưu mô hình đúng định dạng.

#### ✅ 6. Đánh giá và Quyết định

Dựa vào kết quả validation, bạn có thể đưa ra các quyết định sau:

- ✅ Nếu mô hình đạt yêu cầu → Tiếp tục đăng ký vào Model Registry hoặc triển khai.
- ⚠️ Nếu mô hình chưa đủ tốt → Điều chỉnh dữ liệu, siêu tham số hoặc mô hình → huấn luyện lại.
- 📊 Ví dụ về kết quả đánh giá:

|Metric	|Giá trị	|Ngưỡng yêu cầu	|Kết luận
|-----------------------|-----------------------|-----------------------|-----------------------
|Accuracy	|0.912	|>0.90	|✅ Passed
|Precision	|0.908	|>0.85	|✅ Passed
|Recall	|0.901	|>0.85	|✅ Passed
|F1-Score	|0.905	|>0.85	|✅ Passed
![register-model.png](/images/5-Configure-API-Gateway/5.4-validate-training-results/5.4.6.png)
#### 🎉 Hoàn thành
- Bạn đã xác thực thành công kết quả huấn luyện mô hình trong SageMaker 
- Đây là bước quan trọng đảm bảo chất lượng trước khi mô hình được đăng ký và triển khai.

# Báo cáo Thực hành Lab 16: Cloud AI Environment Setup

**Người thực hiện:** Phạm Thành Duy - 2A202600267 
**Ngày thực hiện:** 23/04/2026

---

## 1. Bối cảnh và Lựa chọn Hạ tầng (Infrastructure Context)

Trong quá trình thực hiện Lab 16, do tài khoản AWS mới bị giới hạn Quota nghiêm ngặt (0 vCPU) đối với các dòng máy GPU (`g4dn.xlarge`), em đã chủ động chuyển đổi sang phương án dự phòng theo hướng dẫn của bài học.

Thay vì sử dụng GPU, em đã lựa chọn máy chủ CPU hiệu năng cao thế hệ mới **m7i-flex.large** (2 vCPU, 8GB RAM). Tuy thay đổi về loại Instance, hạ tầng Infrastructure-as-Code (IaC) thông qua Terraform vẫn được đảm bảo tuân thủ đầy đủ các tiêu chuẩn bảo mật của bài Lab:
* Triển khai trong mạng nội bộ (**Private VPC**).
* Truy cập an toàn qua **Bastion Host**.
* Kết nối Internet một chiều qua **NAT Gateway** để tải thư viện và dữ liệu.

## 2. Kết quả thực nghiệm (Benchmark Results)

Dưới đây là các thông số thu được sau khi huấn luyện mô hình LightGBM với bộ dữ liệu *Credit Card Fraud Detection* (284,807 dòng dữ liệu):

| Chỉ số (Metric) | Kết quả thực tế |
| :--- | :--- |
| **Thời gian tải dữ liệu (Load Time)** | 1.01 giây |
| **Thời gian huấn luyện (Train Time)** | 4.62 giây |
| **Độ chính xác (AUC-ROC)** | 0.947 |
| **Độ trễ suy luận (Inference Latency - 1 dòng)** | 0.58 ms (0.00057s) |

## 3. Phân tích và Đánh giá

Kết quả benchmark cho thấy mô hình LightGBM tối ưu hóa phần cứng cực tốt trên môi trường CPU:
1. **Tính hiệu quả:** Thời gian huấn luyện siêu tốc (~4.6 giây) cho thấy với các thuật toán Gradient Boosting, việc sử dụng CPU đời mới như dòng `m7i-flex` mang lại hiệu năng rất ấn tượng, không thua kém nhiều so với các cấu hình GPU phổ thông cho các bài toán dữ liệu bảng (tabular data).
2. **Độ chính xác:** Chỉ số AUC đạt **0.947** chứng minh mô hình đã học được các đặc trưng quan trọng của tập dữ liệu lừa đảo tín dụng một cách hiệu quả.
3. **Khả năng ứng dụng:** Tốc độ suy luận chỉ **0.58ms/dòng** cho thấy hệ thống hoàn toàn đủ khả năng triển khai cho các bài toán yêu cầu phản hồi thời gian thực (Real-time detection).

## 4. Kết luận

Việc sử dụng phương án CPU thay thế không chỉ giúp hoàn thành bài Lab đúng hạn trong bối cảnh vướng mắc về Quota GPU, mà còn chứng minh được tính tối ưu về chi phí hạ tầng (chỉ ~$0.095/giờ) trong khi vẫn đảm bảo đầy đủ các yêu cầu về kiến trúc hệ thống và độ chính xác của mô hình AI.

---
*Ghi chú: Toàn bộ tài nguyên đã được xóa bỏ ngay sau khi benchmark bằng lệnh `terraform destroy` để tối ưu chi phí.*
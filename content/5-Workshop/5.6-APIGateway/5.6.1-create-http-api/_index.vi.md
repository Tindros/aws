---
title : "Tạo HTTP API & Cấu hình CORS"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.6.1 </b> "
---

Trong phần này, bạn sẽ khởi tạo dịch vụ **Amazon API Gateway HTTP API** và thiết lập chính sách **Cross-Origin Resource Sharing (CORS)** toàn cục, cho phép trình duyệt web tại máy phát triển cục bộ và trên môi trường AWS Amplify gọi API an toàn.

---

#### Bước 1: Khởi tạo HTTP API

1. Truy cập [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
2. Nhấn nút **Create API**.
3. Tại ô **HTTP API**, nhấn chọn **Build**.
4. Trong giao diện tạo API:
   + **API name:** Nhập `ExpenseTrackerApi`.
   + Tạm thời để trống phần **Integrations** (chúng ta sẽ gán tích hợp cho từng tuyến đường cụ thể ở các bước sau).
5. Nhấn **Next**.
6. Tại trang **Configure routes**, nhấn **Next** (bỏ qua việc tạo tuyến đường mặc định).
7. Tại trang **Define stages**:
   + Stage name: Giữ nguyên `$default`.
   + Đảm bảo tùy chọn **Auto-deploy** được bật (tự động cập nhật các thay đổi cấu hình mà không cần triển khai thủ công).
8. Nhấn **Next**, kiểm tra lại các thông số và nhấn **Create**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-01-create-http-api.png`  
*(Cần chụp khi triển khai: Màn hình tạo HTTP API mang tên ExpenseTrackerApi với stage $default)*
{{% /notice %}}
<!-- ![Khởi tạo HTTP API](/images/5-Workshop/5.6-APIGateway/apigw-01-create-http-api.png) -->

---

#### Bước 2: Cấu hình CORS (Cross-Origin Resource Sharing)

Các trình duyệt web hiện đại tuân thủ chính sách Same-Origin Policy. Khi ứng dụng frontend chạy tại địa chỉ `http://localhost:5173` hoặc trên tên miền phát hành của AWS Amplify (`https://*.amplifyapp.com`) gửi yêu cầu đến tên miền của API Gateway, trình duyệt sẽ tự động phát một yêu cầu kiểm tra trước HTTP `OPTIONS` (preflight request). Nếu thiếu cấu hình CORS phù hợp, trình duyệt sẽ chặn phản hồi trả về.

1. Tại menu bên trái của API Gateway, chọn mục **CORS** dưới phần **Develop**.
2. Nhấn nút **Configure**.
3. Điền các tham số cấu hình:
   + **Access-Control-Allow-Origin:** Nhập `*` (hoặc nhập cụ thể các nguồn gốc cho phép như `http://localhost:5173` và tên miền Amplify).
   + **Access-Control-Allow-Headers:** Nhập `Content-Type`, sau đó nhấn Enter. Nhập tiếp `Authorization`, sau đó nhấn Enter.
   + **Access-Control-Allow-Methods:** Chọn các phương thức `GET`, `POST`, `PUT`, `DELETE` và `OPTIONS`.
   + **Access-Control-Max-Age:** Nhập `300` (lưu đệm kết quả kiểm tra trước trong 5 phút để giảm thiểu số lượng cuộc gọi OPTIONS lặp lại).
4. Nhấn **Save**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-02-cors-configuration.png`  
*(Cần chụp khi triển khai: Giao diện cấu hình CORS trong API Gateway với danh sách origins, headers và methods)*
{{% /notice %}}
<!-- ![Cấu hình CORS](/images/5-Workshop/5.6-APIGateway/apigw-02-cors-configuration.png) -->

---
title: "Bản đề xuất"
date: 2026-09-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---



# Serverless House Price Prediction API

## Giải pháp AWS Serverless cho dự đoán giá nhà

### 1. Tóm tắt điều hành

**Serverless House Price Prediction API** là một hệ thống được xây dựng nhằm triển khai mô hình Machine Learning dưới dạng dịch vụ dự đoán Serverless trên nền tảng AWS.

Mô hình dự đoán được huấn luyện bằng **Python và Scikit-learn trên Google Colab** sử dụng dữ liệu nhà ở. Sau quá trình huấn luyện, mô hình được export dưới dạng file `.pkl` hoặc `.joblib` và upload lên **Amazon S3** để lưu trữ.

Khi người dùng truy cập web application thông qua **AWS Amplify**, các thông tin về căn nhà được gửi dưới dạng HTTP POST request tới **Amazon API Gateway**. API Gateway invoke **AWS Lambda** để thực hiện prediction. Lambda tải trained model từ S3 và sử dụng model để dự đoán giá nhà.

Kết quả dự đoán sau đó được trả về thông qua API Gateway tới frontend và hiển thị cho người dùng.

Kiến trúc Serverless giúp giảm nhu cầu quản lý server truyền thống, đồng thời cung cấp một giải pháp có khả năng mở rộng và tiết kiệm chi phí cho một ứng dụng Machine Learning quy mô nhỏ.

---

# 2. Tuyên bố vấn đề

### *Vấn đề hiện tại*

Các mô hình dự đoán giá nhà thường được phát triển và kiểm thử trong các môi trường như Google Colab hoặc máy tính cá nhân. Tuy nhiên, sau khi được huấn luyện, mô hình chưa cung cấp một phương thức thuận tiện để người dùng nhập thông tin căn nhà và nhận kết quả dự đoán thông qua một web application.

Việc chạy model trực tiếp trên máy tính cá nhân cũng khiến việc cung cấp prediction service cho nhiều người dùng hoặc tích hợp model vào web application trở nên khó khăn hơn.

Ngoài ra, việc triển khai một Machine Learning model dưới dạng API cần một kiến trúc backend phù hợp. Việc sử dụng một server truyền thống như EC2 có thể tạo ra chi phí và yêu cầu quản lý cơ sở hạ tầng không cần thiết đối với một prediction service quy mô nhỏ.

### *Giải pháp*

Project đề xuất xây dựng **Serverless House Price Prediction API** sử dụng các dịch vụ AWS.

Machine Learning model được training bên ngoài AWS bằng **Google Colab, Python và Scikit-learn**. Sau khi training và đánh giá, model được export dưới dạng file `.pkl` hoặc `.joblib` và upload lên **Amazon S3**.

Khi người dùng truy cập web application được triển khai bằng **AWS Amplify**, frontend gửi thông tin về căn nhà thông qua HTTP POST request tới **Amazon API Gateway**. API Gateway invoke **AWS Lambda**, sau đó Lambda tải trained model từ S3 và thực hiện prediction.

Predicted house price được trả về thông qua API Gateway tới frontend và hiển thị cho người dùng.

**AWS IAM Execution Role** cung cấp cho Lambda các quyền cần thiết để truy cập model trong S3 theo nguyên tắc Least Privilege. **Amazon CloudWatch** được sử dụng để lưu logs và monitoring quá trình thực thi Lambda.

### *Lợi ích và hoàn vốn đầu tư (ROI)*

Giải pháp chuyển một Machine Learning model từ môi trường phát triển thành một **web-based prediction service**, cho phép người dùng sử dụng model mà không cần trực tiếp chạy Python hoặc Google Colab.

Việc sử dụng kiến trúc Serverless giúp giảm yêu cầu quản lý cơ sở hạ tầng vì không cần duy trì một server liên tục hoạt động. AWS Lambda chỉ thực hiện prediction function khi có request.

Project cũng tạo nền tảng cho việc mở rộng trong tương lai, chẳng hạn như sử dụng dataset lớn hơn, cải thiện độ chính xác của model, bổ sung thêm các đặc trưng của căn nhà hoặc phát triển thêm các Machine Learning API khác.

Chi phí vận hành dự kiến tương đối thấp đối với workload quy mô nhỏ do các dịch vụ AWS chính được tính phí dựa trên mức độ sử dụng. Chi phí hàng tháng và hàng năm cuối cùng sẽ được ước tính bằng **AWS Pricing Calculator** dựa trên số lượng prediction request và mức sử dụng tài nguyên thực tế.

---

# 3. Kiến trúc giải pháp

Hệ thống sử dụng kiến trúc **AWS Serverless** để triển khai mô hình dự đoán giá nhà.

Machine Learning model được training bên ngoài AWS bằng Google Colab. Housing Dataset được sử dụng trong quá trình training. Sau khi training, model được export dưới dạng file `.pkl` hoặc `.joblib` và upload lên Amazon S3.

Trong môi trường AWS, người dùng truy cập web application được hosting bằng AWS Amplify. Frontend gửi thông tin căn nhà tới Amazon API Gateway thông qua HTTP POST request. API Gateway invoke AWS Lambda để xử lý prediction request.

Lambda tải trained model từ Amazon S3, thực hiện prediction và trả kết quả thông qua API Gateway về frontend.

Lambda Execution Role cung cấp các quyền S3 cần thiết, trong khi Amazon CloudWatch thu thập logs và metrics của Lambda để monitoring.

### *Kiến trúc hệ thống*

**[Chèn hình kiến trúc hệ thống tại đây]**

*Kiến trúc Serverless House Price Prediction API*

### *Các dịch vụ AWS sử dụng*

- **Amazon S3**: Lưu trữ trained Machine Learning model (`.pkl` / `.joblib`).
- **AWS Lambda**: Thực hiện prediction function bằng trained model.
- **Amazon API Gateway**: Cung cấp REST API và nhận HTTP requests từ frontend.
- **AWS Amplify**: Hosting và triển khai web frontend.
- **AWS IAM**: Cung cấp Lambda Execution Role với các quyền cần thiết để truy cập model trong S3.
- **Amazon CloudWatch**: Cung cấp logging và monitoring cho Lambda execution.

### *Thiết kế thành phần*

- **Model Training**: Google Colab, Python và Scikit-learn được sử dụng để preprocessing housing dataset và training regression model.
- **Model Storage**: Trained model được export dưới dạng `.pkl` hoặc `.joblib` và upload lên Amazon S3.
- **Web Frontend**: AWS Amplify hosting web application cho phép người dùng nhập các đặc trưng của căn nhà.
- **API Layer**: Amazon API Gateway cung cấp REST API để nhận prediction requests.
- **Prediction Function**: AWS Lambda load trained model từ S3 và thực hiện dự đoán giá nhà.
- **Security**: IAM Lambda Execution Role cung cấp các quyền S3 cần thiết theo nguyên tắc Least Privilege.
- **Monitoring**: Amazon CloudWatch thu thập Lambda logs và metrics để troubleshooting và monitoring.

---

# 4. Triển khai kỹ thuật

### *Các giai đoạn triển khai*

Project bao gồm hai phần chính: **phát triển Machine Learning model** và **triển khai AWS Serverless**. Quá trình triển khai được chia thành bốn giai đoạn:

1. **Nghiên cứu và phát triển model**: Tìm hiểu bài toán dự đoán giá nhà, chuẩn bị housing dataset và xây dựng regression model bằng Python và Scikit-learn trên Google Colab.

2. **Đánh giá model và chuẩn bị triển khai**: Đánh giá model bằng các metrics phù hợp, lựa chọn model cuối cùng và export dưới dạng `.pkl` hoặc `.joblib`.

3. **Phát triển Serverless API**: Upload trained model lên Amazon S3, xây dựng AWS Lambda prediction function và cấu hình Amazon API Gateway làm REST API endpoint.

4. **Phát triển frontend, kiểm thử và triển khai**: Xây dựng web interface, kết nối frontend với API Gateway, triển khai frontend bằng AWS Amplify và thực hiện end-to-end testing.

### *Yêu cầu kỹ thuật*

- **Machine Learning**: Python, Pandas, Scikit-learn và các thư viện cần thiết cho data preprocessing, training và evaluation.
- **Model**: Regression model được export dưới dạng `.pkl` hoặc `.joblib`.
- **Storage**: Amazon S3 dùng để lưu trữ trained model.
- **Backend**: AWS Lambda xử lý prediction requests.
- **API**: Amazon API Gateway cung cấp REST API.
- **Frontend**: Web application cho phép người dùng nhập các đặc trưng của căn nhà và xem predicted house price.
- **Deployment**: AWS Amplify dùng để hosting frontend application.
- **Security**: IAM Lambda Execution Role với các quyền tối thiểu cần thiết để truy cập S3.
- **Monitoring**: Amazon CloudWatch Logs và Metrics.

---

# 5. Lộ trình & Mốc triển khai

Project sẽ được thực hiện trong **12 tuần thực tập**, kết hợp quá trình học AWS, phát triển Machine Learning model, triển khai Serverless, kiểm thử và hoàn thiện tài liệu.

### *Tuần 1 – AWS Fundamentals*

- Tạo và cấu hình AWS account.
- Tìm hiểu AWS cost management và AWS Support.
- Tìm hiểu AWS IAM và access management.
- Tìm hiểu networking fundamentals với Amazon VPC.
- Tìm hiểu các kiến thức cơ bản về Amazon EC2.
- Nắm được các khái niệm cơ bản về AWS infrastructure và Cloud Services.

**Mốc hoàn thành:** Hoàn thành các bài học AWS nền tảng và hiểu được môi trường AWS cơ bản.

### *Tuần 2 – AWS Compute, Storage & Database Services*

- Tìm hiểu IAM Roles cho EC2.
- Tìm hiểu AWS Cloud9.
- Tìm hiểu Amazon S3 và Static Website Hosting.
- Tìm hiểu Amazon RDS.
- Tìm hiểu AWS Lambda và Serverless Architecture.
- Tìm hiểu cách các dịch vụ Storage, Database và Serverless của AWS có thể được sử dụng trong project.

**Mốc hoàn thành:** Xác định được các dịch vụ AWS cần thiết cho project House Price Prediction.

### *Tuần 3 – Lập kế hoạch Project & Chuẩn bị Machine Learning*

- Hoàn thiện yêu cầu của project và system architecture.
- Chuẩn bị Housing Dataset.
- Thực hiện data cleaning và preprocessing.
- Khám phá dataset và xác định các house features phù hợp.
- Xây dựng baseline regression model.
- Tiếp tục học các AWS services liên quan đến project.

**Mốc hoàn thành:** Hoàn thành Machine Learning pipeline ban đầu và hoàn thiện architecture của project.

### *Tuần 4 – Phát triển Machine Learning Model*

- Training regression models cho bài toán House Price Prediction.
- So sánh các phương pháp và model khác nhau.
- Đánh giá model performance bằng các metrics phù hợp.
- Cải thiện feature selection và preprocessing.
- Lựa chọn model ban đầu để deployment.

**Mốc hoàn thành:** Có một House Price Prediction model hoạt động và đạt performance phù hợp.

### *Tuần 5 – Đóng gói Model & Amazon S3*

- Export trained model dưới dạng `.pkl` hoặc `.joblib`.
- Tạo và cấu hình Amazon S3 bucket.
- Upload trained model lên S3.
- Kiểm tra việc download và load model từ S3.
- Tìm hiểu S3 permissions và access control.

**Mốc hoàn thành:** Lưu trữ và truy xuất thành công trained model từ Amazon S3.

### *Tuần 6 – AWS Lambda Prediction Function*

- Xây dựng Lambda prediction function.
- Load trained model từ S3.
- Xử lý house features đầu vào.
- Thực hiện prediction bằng trained model.
- Trả về predicted house price.
- Kiểm thử Lambda với sample input data.

**Mốc hoàn thành:** Hoàn thành serverless prediction function.

### *Tuần 7 – Tích hợp API Gateway*

- Tạo Amazon API Gateway REST API.
- Cấu hình HTTP POST endpoint.
- Kết nối API Gateway với AWS Lambda.
- Kiểm thử requests và responses.
- Xử lý invalid hoặc missing input data.

**Mốc hoàn thành:** Hoàn thành backend prediction API.

### *Tuần 8 – Phát triển Frontend*

- Thiết kế web interface cho House Price Prediction.
- Tạo input fields cho các house features.
- Implement frontend validation.
- Kết nối frontend với API Gateway.
- Hiển thị predicted house price.

**Mốc hoàn thành:** Hoàn thành functional frontend kết nối với prediction API.

### *Tuần 9 – Triển khai bằng AWS Amplify*

- Cấu hình AWS Amplify cho frontend hosting.
- Deploy web application.
- Cấu hình frontend giao tiếp với production API.
- Kiểm thử application thông qua web application đã được deploy.

**Mốc hoàn thành:** Deploy phiên bản đầu tiên của House Price Prediction web application.

### *Tuần 10 – Bảo mật, Monitoring & Tối ưu hóa*

- Cấu hình Lambda Execution Role bằng IAM.
- Áp dụng nguyên tắc Least Privilege.
- Kiểm tra Lambda access tới S3 model.
- Cấu hình và kiểm tra Amazon CloudWatch Logs.
- Monitoring Lambda execution và errors.
- Tối ưu Lambda execution và model loading nếu cần.

**Mốc hoàn thành:** Hoàn thành security và monitoring configuration.

### *Tuần 11 – Kiểm thử & Đánh giá hệ thống*

- Thực hiện end-to-end testing.
- Kiểm thử với nhiều house feature combinations.
- Kiểm thử invalid và incomplete input.
- Đánh giá prediction accuracy.
- Xác định và sửa các lỗi liên quan đến API, Lambda, S3 hoặc frontend.
- Kiểm tra AWS resource usage và estimated costs.

**Mốc hoàn thành:** Hoàn thành system testing và xử lý các lỗi chính.

### *Tuần 12 – Hoàn thiện & Documentation*

- Hoàn thiện Machine Learning model và AWS architecture.
- Review toàn bộ system.
- Đánh giá kết quả project so với objectives ban đầu.
- Hoàn thiện tài liệu triển khai.
- Hoàn thành Internship Worklog và project documentation.
- Chuẩn bị final project presentation và demonstration.

**Mốc hoàn thành:** Hoàn thành và trình bày **Serverless House Price Prediction API**.

# 6. Ước tính ngân sách

Chi phí của hệ thống phụ thuộc vào số lượng prediction requests, thời gian thực thi Lambda, dung lượng trained model trên S3, số lượng API Gateway requests, frontend hosting và data transfer.

**AWS Pricing Calculator** sẽ được sử dụng để ước tính chi phí hàng tháng và hàng năm dựa trên workload dự kiến của project.

### *Chi phí hạ tầng*

- **AWS Lambda**: Chi phí phụ thuộc vào số lượng prediction requests và thời gian thực thi Lambda.
- **Amazon S3**: Chi phí phụ thuộc vào dung lượng trained model và số lượng requests truy cập model.
- **Amazon API Gateway**: Chi phí phụ thuộc vào số lượng API requests.
- **AWS Amplify**: Chi phí phụ thuộc vào frontend hosting, storage và data transfer.
- **Amazon CloudWatch**: Chi phí phụ thuộc vào lượng logs và metrics được tạo ra và lưu trữ.
- **AWS IAM**: IAM Roles không phát sinh chi phí riêng.

### *Tối ưu chi phí*

Do project được thiết kế cho workload prediction quy mô nhỏ, số lượng requests dự kiến tương đối thấp. Kiến trúc Serverless giúp tránh chi phí duy trì một EC2 instance liên tục hoạt động khi hệ thống không có requests.

Có thể tiếp tục tối ưu chi phí bằng cách:

- Giữ trained model ở kích thước phù hợp.
- Tối ưu thời gian thực thi Lambda.
- Thiết lập thời gian lưu trữ CloudWatch Logs phù hợp.
- Theo dõi S3 storage và API request usage.
- Sử dụng AWS Budgets để theo dõi và kiểm soát chi phí.

Chi phí hàng tháng và hàng năm cuối cùng sẽ được tính toán sau khi xác định workload dự kiến bằng AWS Pricing Calculator.

---

# 7. Đánh giá rủi ro

### *Ma trận rủi ro*

- **Prediction model có độ chính xác thấp**: Ảnh hưởng cao, xác suất trung bình.
- **Lambda không thể truy cập trained model trong S3**: Ảnh hưởng cao, xác suất thấp.
- **API hoặc frontend gặp lỗi**: Ảnh hưởng trung bình, xác suất thấp.
- **Chi phí AWS tăng ngoài dự kiến**: Ảnh hưởng trung bình, xác suất thấp.
- **Model hoặc data bị thay đổi ngoài dự kiến**: Ảnh hưởng cao, xác suất thấp.

### *Chiến lược giảm thiểu*

- **Model accuracy**: Đánh giá model bằng các performance metrics phù hợp và kiểm thử trên dữ liệu không được sử dụng trong quá trình training.
- **S3/Lambda access**: Kiểm tra IAM permissions và monitoring Lambda logs để xác định các lỗi truy cập model.
- **API reliability**: Kiểm thử API Gateway với cả valid và invalid requests trước khi deployment.
- **Cost control**: Sử dụng AWS Budgets và monitoring Lambda, S3, API Gateway và Amplify usage.
- **Model management**: Lưu giữ các phiên bản model trước đó trong quá trình phát triển và kiểm tra model mới trước khi deployment.

### *Kế hoạch dự phòng*

Nếu AWS prediction API không hoạt động, trained model vẫn có thể được chạy trực tiếp trong môi trường Python/Google Colab để thực hiện prediction.

Nếu model mới được deployment tạo ra kết quả không mong muốn, có thể khôi phục phiên bản model trước đó và sử dụng phiên bản này cho đến khi model mới được sửa lỗi.

---

# 8. Kết quả kỳ vọng

### *Cải tiến kỹ thuật*

Project dự kiến chuyển House Price Prediction model từ môi trường Machine Learning development thành một **Serverless Web API** có thể được truy cập thông qua trình duyệt.

Người dùng có thể:

- Truy cập web application.
- Nhập các house features.
- Gửi prediction request.
- Nhận predicted house price trực tiếp trên web interface.

Người dùng không cần cài đặt Python hoặc trực tiếp chạy Google Colab để sử dụng prediction service.

### *Giá trị dài hạn*

Architecture được thiết kế theo hướng modular, cho phép trained model được thay thế hoặc cập nhật mà không yêu cầu thay đổi lớn đối với frontend hoặc toàn bộ system.

Các hướng phát triển trong tương lai có thể bao gồm:

- Xây dựng Machine Learning model có độ chính xác cao hơn.
- Hỗ trợ nhiều prediction models.
- Bổ sung thêm các house features.
- Implement model versioning.
- Bổ sung user authentication.
- Cải thiện monitoring và analytics.
- Mở rộng system thành các Machine Learning prediction APIs khác.

Project cũng cung cấp kinh nghiệm thực tế trong việc kết hợp **Data Science, Machine Learning, AWS Cloud và Serverless Architecture**, phù hợp với định hướng chuyên ngành Khoa học Dữ liệu.
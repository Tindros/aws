---
title: "Bản đề xuất"
date: 2026-09-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---



# Serverless Personal Expense Tracker
## Giải Pháp Serverless Trên AWS Cho Quản Lý Chi Tiêu Cá Nhân

### 1. Tóm tắt điều hành

Serverless Personal Expense Tracker là một ứng dụng web được thiết kế để giúp người dùng ghi nhận, quản lý, xem lại và tổng hợp các khoản chi tiêu cá nhân thông qua kiến trúc serverless trên AWS.

Ứng dụng cung cấp các chức năng quản lý chi tiêu cốt lõi bao gồm đăng ký và đăng nhập người dùng, thêm khoản chi tiêu, xem danh sách chi tiêu, chỉnh sửa và xóa khoản chi tiêu, lọc các khoản chi tiêu và xem bảng tổng hợp chi tiêu.

Frontend của ứng dụng được lưu trữ bằng AWS Amplify. Sau khi các tệp frontend được cung cấp cho trình duyệt của người dùng, trình duyệt sẽ giao tiếp trực tiếp với Amazon Cognito để xác thực và trực tiếp gọi Amazon API Gateway để thực hiện các yêu cầu API của ứng dụng. Amazon Cognito cung cấp chức năng xác thực và phát hành JWT token được sử dụng trong các API request.

Amazon API Gateway được cấu hình dưới dạng HTTP API với JWT authorizer sử dụng Amazon Cognito User Pool. Các request đã được xác thực sẽ được chuyển đến AWS Lambda, nơi chứa business logic của ứng dụng và thực hiện các thao tác đọc và ghi dữ liệu trên Amazon DynamoDB.

Amazon DynamoDB lưu trữ các bản ghi chi tiêu, trong khi Amazon CloudWatch cung cấp logging và monitoring cho quá trình thực thi Lambda. Lambda Execution Role của AWS IAM cung cấp cho Lambda các quyền cần thiết để truy cập DynamoDB và CloudWatch theo nguyên tắc quyền hạn tối thiểu.

Kiến trúc serverless giúp giảm nhu cầu quản lý các máy chủ truyền thống và cung cấp một giải pháp có khả năng mở rộng và tối ưu chi phí cho một ứng dụng quản lý chi tiêu cá nhân quy mô nhỏ.





### 2. Phát biểu vấn đề

### Vấn đề là gì?

Thông tin về chi tiêu cá nhân thường được ghi chép thủ công trong sổ tay, bảng tính hoặc các ứng dụng ghi chú đơn giản. Những phương pháp này có thể khiến việc tổ chức các khoản chi tiêu một cách nhất quán và nhanh chóng hiểu được thói quen chi tiêu trở nên khó khăn.

Người dùng cũng có thể cần tính tổng chi tiêu theo tháng, so sánh chi tiêu theo danh mục hoặc tìm kiếm các giao dịch trong một khoảng thời gian cụ thể. Việc thực hiện các tác vụ này thủ công có thể tốn thời gian và dẫn đến các bản ghi không nhất quán hoặc sai sót trong tính toán.

Ngoài ra, một ứng dụng web quản lý chi tiêu cần một kiến trúc backend phù hợp cho việc xác thực, lưu trữ dữ liệu và xử lý API. Việc sử dụng một máy chủ truyền thống chạy liên tục như EC2 có thể làm phát sinh các yêu cầu quản lý hạ tầng và chi phí không cần thiết đối với một ứng dụng cá nhân quy mô nhỏ.

### Giải pháp

Dự án đề xuất xây dựng một Serverless Personal Expense Tracker sử dụng các dịch vụ AWS.

Người dùng truy cập ứng dụng web thông qua trình duyệt. AWS Amplify lưu trữ và triển khai frontend, trong khi trình duyệt giao tiếp trực tiếp với Amazon Cognito để đăng ký và đăng nhập người dùng. Sau khi xác thực thành công, Cognito trả về JWT token cho trình duyệt.

Trình duyệt gửi các request quản lý chi tiêu đã được xác thực đến Amazon API Gateway bằng JWT token trong Authorization header. API Gateway xác thực JWT bằng JWT authorizer dựa trên Cognito và gọi AWS Lambda đối với các request hợp lệ.

AWS Lambda xử lý logic quản lý chi tiêu và thực hiện các thao tác tạo, đọc, cập nhật và xóa trên Amazon DynamoDB. DynamoDB lưu trữ các bản ghi chi tiêu được liên kết với người dùng đã xác thực.

Ứng dụng cũng có thể cung cấp các chức năng lọc và tổng hợp, cho phép người dùng xem tổng chi tiêu và phân tích chi tiêu theo ngày hoặc danh mục.

AWS IAM Lambda Execution Role cung cấp cho Lambda các quyền cần thiết để truy cập DynamoDB và ghi log vào CloudWatch theo nguyên tắc quyền hạn tối thiểu. Amazon CloudWatch được sử dụng để logging và monitoring quá trình thực thi Lambda.





### Lợi ích và giá trị mang lại

Giải pháp chuyển đổi việc theo dõi chi tiêu cá nhân từ quy trình thủ công thành một ứng dụng web, cho phép người dùng đã xác thực quản lý các bản ghi chi tiêu của họ trực tiếp từ trình duyệt.

Việc sử dụng kiến trúc serverless giúp giảm yêu cầu quản lý hạ tầng vì không cần duy trì một application server chạy liên tục. AWS Lambda chỉ thực thi logic ứng dụng khi có API request được gửi đến.

Amazon Cognito cung cấp chức năng xác thực người dùng, trong khi API Gateway và JWT authorizer giúp bảo vệ các API route của ứng dụng để dữ liệu chi tiêu chỉ có thể được truy cập thông qua các request đã được xác thực.

Dự án cũng tạo nền tảng cho các cải tiến trong tương lai như quản lý ngân sách, cảnh báo ngân sách, phân tích nâng cao, trực quan hóa chi tiêu, chi tiêu định kỳ hoặc bổ sung các dịch vụ thông báo.

Chi phí vận hành dự kiến tương đối thấp đối với workload quy mô nhỏ vì các dịch vụ AWS chính sử dụng cơ chế tính phí theo mức sử dụng. Chi phí hàng tháng và hàng năm cuối cùng sẽ được ước tính bằng AWS Pricing Calculator dựa trên số lượng người dùng dự kiến, số lượng API request, hoạt động cơ sở dữ liệu, mức sử dụng frontend và hoạt động monitoring.





### 3. Kiến trúc giải pháp

Hệ thống sử dụng kiến trúc AWS Serverless để cung cấp một ứng dụng quản lý chi tiêu cá nhân có xác thực.

AWS Amplify lưu trữ và triển khai frontend của ứng dụng. Người dùng / trình duyệt web truy cập frontend thông qua Amplify và chạy mã frontend trực tiếp trên trình duyệt.

Trình duyệt giao tiếp trực tiếp với Amazon Cognito để đăng ký và đăng nhập. Sau khi xác thực thành công, Cognito trả về JWT token cho trình duyệt.

Trình duyệt gửi API request cùng JWT token đến Amazon API Gateway thông qua HTTP API. API Gateway sử dụng JWT authorizer được cấu hình với Cognito User Pool để xác thực token trước khi gọi AWS Lambda.

AWS Lambda chứa expense service và business logic của ứng dụng. Lambda thực hiện các thao tác tạo, đọc, cập nhật và xóa trên Amazon DynamoDB và có thể tạo các thông tin tổng hợp chi tiêu cho người dùng đã xác thực.

Amazon DynamoDB lưu trữ dữ liệu chi tiêu. Amazon CloudWatch thu thập log và metric của Lambda để monitoring, troubleshooting và theo dõi hoạt động của hệ thống.

Lambda Execution Role cung cấp cho Lambda các quyền cần thiết để truy cập DynamoDB và CloudWatch.

Kiến trúc hệ thống

![Serverless Personal Expense Tracker Architecture](/aws/images/2-Proposal/architecture.jpeg)

### Các dịch vụ AWS được sử dụng

- **AWS Amplify**: Lưu trữ và triển khai web frontend, đồng thời cung cấp các tệp frontend tĩnh.

- **Amazon Cognito**: Cung cấp chức năng đăng ký, đăng nhập và xác thực người dùng thông qua User Pool và phát hành JWT token.

- **Amazon API Gateway**: Cung cấp HTTP API, nhận API request từ trình duyệt của người dùng và sử dụng JWT authorizer để xác thực các request.

- **AWS Lambda**: Thực thi business logic quản lý chi tiêu và xử lý các API request.

- **Amazon DynamoDB**: Lưu trữ các bản ghi chi tiêu và hỗ trợ các thao tác tạo, đọc, cập nhật và xóa.

- **AWS IAM**: Cung cấp Lambda Execution Role với các quyền cần thiết để truy cập DynamoDB và CloudWatch.

- **Amazon CloudWatch**: Cung cấp logging, metrics và monitoring cho quá trình thực thi Lambda.

### Thiết kế thành phần

- **Web Frontend**: AWS Amplify lưu trữ ứng dụng web. Trình duyệt của người dùng chạy mã frontend sau khi nhận được các tệp tĩnh.

- **Xác thực người dùng**: Amazon Cognito User Pool xử lý đăng ký và đăng nhập người dùng và trả về JWT token cho trình duyệt.

- **API Layer**: Amazon API Gateway cung cấp HTTP API được frontend sử dụng. JWT authorizer dựa trên Cognito xác thực JWT được gửi trong Authorization header.

- **Expense Service**: AWS Lambda chứa business logic của ứng dụng cho việc tạo, đọc, cập nhật, xóa, lọc và tổng hợp các bản ghi chi tiêu.

- **Data Storage**: Amazon DynamoDB lưu trữ các bản ghi chi tiêu. Mỗi bản ghi được liên kết với người dùng đã xác thực để người dùng có thể quản lý dữ liệu chi tiêu của chính mình.

- **Security**: Amazon Cognito xác thực người dùng và API Gateway xác thực JWT token. IAM Lambda Execution Role cung cấp cho Lambda các quyền tối thiểu cần thiết để truy cập DynamoDB và CloudWatch.

- **Monitoring**: Amazon CloudWatch thu thập log và metric của Lambda để troubleshooting, monitoring và theo dõi hoạt động.

### Dữ liệu chi tiêu cốt lõi

Ứng dụng có thể lưu trữ các bản ghi chi tiêu với các trường như:

- **userId**: Xác định người dùng đã xác thực sở hữu bản ghi chi tiêu.

- **expenseId**: Mã định danh duy nhất của bản ghi chi tiêu.

- **amount**: Số tiền chi tiêu.

- **category**: Danh mục chi tiêu như Food, Transportation, Shopping, Bills hoặc Other.

- **description**: Mô tả tùy chọn của khoản chi tiêu.

- **date**: Ngày phát sinh chi tiêu.

- **createdAt**: Timestamp dùng để ghi nhận thời điểm bản ghi chi tiêu được tạo.

### Các API endpoint cốt lõi

HTTP API có thể cung cấp các endpoint như:

- **GET /expenses**: Lấy các bản ghi chi tiêu của người dùng đã xác thực.

- **POST /expenses**: Tạo một bản ghi chi tiêu mới.

- **PUT /expenses/{id}**: Cập nhật một bản ghi chi tiêu hiện có.

- **DELETE /expenses/{id}**: Xóa một bản ghi chi tiêu.

- **GET /summary**: Trả về thông tin tổng hợp chi tiêu như tổng số tiền và tổng theo từng danh mục.





### 4. Triển khai kỹ thuật

**Các giai đoạn triển khai**

Dự án tập trung xây dựng một ứng dụng quản lý chi tiêu serverless hoàn chỉnh. Quá trình triển khai có thể được chia thành bốn giai đoạn:

- Thiết kế yêu cầu và kiến trúc: Xác định các yêu cầu quản lý chi tiêu, xác định các dịch vụ AWS cần sử dụng, hoàn thiện kiến trúc và thiết kế cấu trúc dữ liệu DynamoDB cũng như các API endpoint.

- Phát triển xác thực và backend: Cấu hình Amazon Cognito, tạo API Gateway HTTP API với JWT authorizer, triển khai business logic trên AWS Lambda và cấu hình các thao tác với DynamoDB.

- Phát triển frontend và tích hợp: Xây dựng giao diện web, triển khai các luồng xác thực, tạo các màn hình quản lý chi tiêu, kết nối frontend trực tiếp với Cognito và API Gateway, đồng thời hiển thị dữ liệu chi tiêu và thông tin tổng hợp.

- Testing, monitoring, optimization và deployment: Thực hiện end-to-end testing, cấu hình quyền IAM và CloudWatch monitoring, xử lý lỗi, tối ưu ứng dụng, triển khai frontend cuối cùng và hoàn thiện tài liệu hệ thống.

**Yêu cầu kỹ thuật**

- **Machine Learning:** Không cần thiết đối với MVP hiện tại.

- **Database:** Amazon DynamoDB để lưu trữ các bản ghi chi tiêu.

- **Backend:** AWS Lambda để xử lý các request quản lý chi tiêu.

- **API:** Amazon API Gateway HTTP API với JWT authorizer.

- **Authentication:** Amazon Cognito User Pool cho đăng ký, đăng nhập và xác thực dựa trên JWT.

- **Frontend:** Một ứng dụng web cho phép người dùng đã xác thực thêm, xem, chỉnh sửa, xóa, lọc và tổng hợp các khoản chi tiêu.

- **Deployment:** AWS Amplify để hosting và deployment frontend.

- **Security:** Xác thực dựa trên Cognito, JWT authorization trên API Gateway và IAM Lambda Execution Role với các quyền tối thiểu cần thiết.

- **Monitoring:** Amazon CloudWatch Logs và Metrics.

### 5. Tiến độ & các mốc quan trọng
**Tiến độ dự án**
Dự án được triển khai trong **12 tuần thực tập**, kết hợp quá trình học AWS, thiết kế project, xây dựng hệ thống serverless, tích hợp frontend, kiểm thử, hoàn thiện tài liệu và chuẩn bị đánh giá cuối kỳ.

### Tuần 1 – Kiến thức nền tảng AWS
- Tạo và cấu hình AWS Account.
- Tìm hiểu AWS Cost Management và AWS Support.
- Tìm hiểu IAM và quản lý quyền truy cập.
- Tìm hiểu kiến thức networking với Amazon VPC.
- Tìm hiểu các khái niệm cơ bản về hạ tầng và dịch vụ AWS.

**Milestone:** Hoàn thành các nội dung AWS nền tảng và hiểu môi trường AWS.

### Tuần 2 – Compute, Storage và Database trên AWS
- Tìm hiểu IAM Role cho EC2.
- Tìm hiểu AWS Cloud9.
- Tìm hiểu Amazon S3 và static website hosting.
- Tìm hiểu Amazon RDS.
- Tìm hiểu AWS Lambda và serverless computing.
- Hiểu cách storage, database và serverless services có thể hỗ trợ phát triển ứng dụng.

**Milestone:** Xây dựng kiến thức nền tảng về các AWS services liên quan đến project.

### Tuần 3 – Serverless Learning và định hướng Project
- Tiếp tục tìm hiểu các dịch vụ AWS serverless và API.
- Ôn tập AWS Lambda và API Gateway.
- Rà soát các hướng phát triển project.
- Xác định requirements và các chức năng chính của Personal Expense Tracker.
- Xác nhận hướng triển khai serverless.

**Milestone:** Xác định Serverless Personal Expense Tracker và các AWS services chính.

### Tuần 4 – Requirements và thiết kế Architecture
- Hoàn thiện requirements của project.
- Hoàn thiện AWS architecture.
- Xác định authentication flow bằng Amazon Cognito.
- Thiết kế cấu trúc dữ liệu expense trên DynamoDB.
- Xác định các API endpoint cho quản lý expense.
- Tài liệu hóa flow giữa browser, authentication, API, backend, database, IAM và monitoring.

**Milestone:** Hoàn thành architecture và technical design ban đầu.

### Tuần 5 – Xây dựng Frontend cơ bản
- Chuyển hướng project từ ý tưởng House Price Prediction ban đầu sang Serverless Personal Expense Tracker.
- Khởi tạo frontend bằng React, Vite và TypeScript.
- Xây dựng các trang Login, Register và Dashboard.
- Xây dựng các component chính để quản lý expense.
- Tạo mock authentication service và expense service để phát triển local.
- Kiểm thử luồng thêm, xem, sửa và xóa expense trên local.

**Milestone:** Hoàn thiện cấu trúc frontend cơ bản và luồng quản lý expense trên local.

### Tuần 6 – Cognito, DynamoDB và IAM
- Cấu hình Amazon Cognito User Pool và App Client.
- Tìm hiểu authentication flow và JWT token của Cognito.
- Tạo DynamoDB ExpenseTable.
- Thiết kế partition key và sort key với userId và expenseId.
- Tạo Lambda Execution Role.
- Cấu hình các quyền cần thiết cho DynamoDB và CloudWatch Logs.

**Milestone:** Hoàn thiện nền tảng authentication, lưu trữ dữ liệu và IAM cho backend.

### Tuần 7 – Lambda và API Gateway Backend
- Tạo Lambda function expenseTrackerFunction.
- Xây dựng các API GET, POST, GET by ID, PUT, DELETE và summary.
- Kết nối Lambda với DynamoDB.
- Tạo API Gateway HTTP API và Lambda integration.
- Cấu hình các expense route và CORS.
- Cấu hình Cognito JWT Authorizer và bảo vệ các API route.

**Milestone:** Hoàn thiện backend serverless có authentication.

### Tuần 8 – Tích hợp Frontend với Backend
- Tích hợp Amazon Cognito vào React frontend.
- Xử lý đăng nhập và token.
- Thay thế mock expense service bằng API Gateway thực tế.
- Gửi Cognito JWT Access Token trong Authorization header.
- Kết nối Dashboard với dữ liệu expense và summary thực tế.
- Kiểm thử đầy đủ luồng thêm, xem, sửa và xóa expense.

**Milestone:** Hoàn thiện tích hợp frontend với AWS backend thực tế.

### Tuần 9 – Kiểm thử hệ thống và User Isolation
- Kiểm thử Lambda với các API GET, POST, PUT, DELETE và summary.
- Kiểm thử JWT authentication và authorization của API Gateway.
- Kiểm tra request có và không có authentication hợp lệ.
- Kiểm tra data isolation giữa các user thông qua Cognito sub.
- Kiểm tra unauthorized access và DELETE đối với dữ liệu không thuộc quyền sở hữu.
- Rà soát nội dung Workshop từ 5.1 đến 5.9.

**Milestone:** Xác minh security, authorization và data isolation, đồng thời ổn định hệ thống.

### Tuần 10 – Rà soát Workshop và Documentation
- Rà soát các trang Workshop từ 5.1 đến 5.9.
- Kiểm tra cấu hình AWS trong tài liệu có khớp với project thực tế hay không.
- Đồng bộ các trang Workshop tiếng Việt và tiếng Anh.
- Cập nhật các nội dung liên quan đến API configuration của frontend.
- Rà soát hướng dẫn Amplify và cấu trúc repository frontend.
- Kiểm tra image paths và chuẩn bị screenshot/deployment checklist.

**Milestone:** Ổn định tài liệu Workshop và đồng bộ với project thực tế.

### Tuần 11 – Deployment và kiểm tra Screenshot
- Deploy frontend theo hosting workflow đã cấu hình.
- Kiểm tra authentication và các chức năng quản lý expense sau deployment.
- Chụp các screenshot cần thiết cho Cognito, DynamoDB, Lambda, API Gateway, CloudWatch và Amplify.
- Lưu screenshot vào thư mục hình ảnh của Workshop.
- Kiểm tra image paths và hiển thị trên GitHub Pages.
- Rà soát Proposal, Workshop và cấu hình project lần cuối.

**Milestone:** Kiểm tra ứng dụng sau deployment và hoàn thiện các bằng chứng cần thiết cho project.

### Tuần 12 – Hoàn thiện và chuẩn bị đánh giá
- Kiểm thử lần cuối Cognito authentication, CRUD và user data isolation.
- Hoàn thiện screenshot và tài liệu Workshop song ngữ.
- Rà soát Proposal, Worklog, Workshop và cấu trúc repository.
- Thực hiện cleanup các AWS resources sau khi hoàn tất kiểm tra nếu phù hợp.
- Chuẩn bị project và tài liệu cho việc đánh giá và nộp báo cáo.

**Milestone:** Hoàn thiện Serverless Personal Expense Tracker và chuẩn bị repository cho đánh giá cuối kỳ.

## 6. Ước tính ngân sách

Có thể xem chi phí trên [AWS Pricing Calculator]\([https://calculator.aws/#/estimate?id=d3feb73b825d0a1424aee2bcbb91b80078f5523d](https://calculator.aws/#/estimate?id=d3feb73b825d0a1424aee2bcbb91b80078f5523d)).

### Chi phí hạ tầng

- **Amazon Cognito:** 0,00 USD/tháng.
- **Amazon DynamoDB:** 0,00 USD/tháng.
- **AWS Lambda:** 0,00 USD/tháng.
- **Amazon API Gateway:** 0,00 USD/tháng.
- **Amazon CloudWatch:** 0,00 USD/tháng.
- **AWS Amplify:** 0,25 USD/tháng.

**Tổng:** **0,28 USD/tháng**, **3,36 USD/12 tháng**.

> Chi phí trên là mức ước tính dựa trên cấu hình và mức sử dụng dự kiến của project trong AWS Pricing Calculator. Chi phí thực tế có thể thay đổi tùy theo mức sử dụng tài nguyên, khu vực triển khai, chính sách giá và các khoản phí phát sinh của AWS.

### Tối ưu chi phí

Để giảm chi phí vận hành, project áp dụng các biện pháp sau:

- **Sử dụng AWS Free Tier:** Cognito, DynamoDB, Lambda, API Gateway và CloudWatch được cấu hình ở mức sử dụng nhỏ, phù hợp với project học tập và có thể nằm trong phạm vi Free Tier tùy theo mức sử dụng thực tế.
- **AWS Lambda:** Sử dụng mô hình serverless, chỉ phát sinh chi phí dựa trên số lần request và thời gian thực thi thay vì duy trì máy chủ chạy liên tục.
- **Amazon DynamoDB:** Sử dụng DynamoDB cho dữ liệu expense với mô hình serverless, phù hợp với lượng dữ liệu và lưu lượng request nhỏ của ứng dụng.
- **Amazon API Gateway:** Sử dụng HTTP API thay vì REST API để giảm chi phí cho các request của ứng dụng.
- **AWS Amplify:** Chỉ sử dụng để hosting frontend React và cấu hình ở mức tài nguyên cần thiết cho project, tránh sử dụng các tính năng hoặc tài nguyên phát sinh thêm chi phí không cần thiết.
- **AWS CloudWatch:** Chỉ sử dụng các log và monitoring cần thiết để theo dõi Lambda và hệ thống, tránh tạo thêm các metric hoặc cấu hình giám sát không cần thiết.
- **Dọn dẹp tài nguyên sau khi hoàn thành:** Sau khi hoàn tất quá trình thử nghiệm và đánh giá project, các tài nguyên AWS không còn sử dụng sẽ được xóa để tránh phát sinh chi phí ngoài dự kiến.

Với cấu hình hiện tại, AWS Pricing Calculator ước tính chi phí khoảng **0,28 USD/tháng**, tương đương **3,36 USD trong 12 tháng**. Chi phí thực tế phụ thuộc vào mức sử dụng tài nguyên và chính sách giá AWS tại thời điểm triển khai.



### 7. Đánh giá rủi ro

#### Ma trận rủi ro

- Truy cập trái phép vào dữ liệu chi tiêu: Tác động cao, xác suất thấp.

- Lambda không thể truy cập DynamoDB do IAM permissions không chính xác: Tác động cao, xác suất thấp.

- API hoặc frontend gặp lỗi: Tác động trung bình, xác suất thấp.

- Chi phí AWS tăng ngoài dự kiến: Tác động trung bình, xác suất thấp.

- Mất dữ liệu hoặc bản ghi chi tiêu không chính xác: Tác động cao, xác suất thấp.

- Lỗi cấu hình authentication hoặc JWT: Tác động cao, xác suất thấp.

#### Chiến lược giảm thiểu

- Authentication và authorization: Sử dụng Amazon Cognito để xác thực người dùng và cấu hình JWT authorization trên API Gateway cho các API route được bảo vệ.

- DynamoDB/Lambda access: Kiểm tra IAM permissions và đảm bảo Lambda Execution Role tuân theo nguyên tắc quyền hạn tối thiểu.

- API reliability: Kiểm tra API Gateway với các request hợp lệ, không hợp lệ, thiếu dữ liệu và không được phép trước khi deployment.

- Data integrity: Validation dữ liệu chi tiêu và kiểm tra cẩn thận các thao tác tạo, cập nhật và xóa.

- Cost control: Sử dụng AWS Budgets và theo dõi mức sử dụng Lambda, DynamoDB, API Gateway, Cognito, CloudWatch và Amplify.

- Monitoring: Xem xét CloudWatch logs và metrics để xác định các lỗi ứng dụng và vấn đề vận hành.

#### Kế hoạch dự phòng

Nếu web application đã triển khai không khả dụng, nhóm dự án có thể sử dụng development environment để tiếp tục testing và debugging trong khi khôi phục phiên bản đã triển khai.

Nếu một bản cập nhật ứng dụng tạo ra hành vi không mong muốn, frontend hoặc Lambda version hoạt động trước đó có thể được khôi phục trong khi vấn đề được sửa chữa.

Nếu cấu hình database hoặc authentication gây ra vấn đề trong quá trình development, component bị ảnh hưởng có thể được cô lập và kiểm tra độc lập trước khi tích hợp lại vào hệ thống hoàn chỉnh.





### 8. Kết quả mong đợi

#### Cải thiện kỹ thuật:

Dự án được kỳ vọng sẽ chuyển đổi việc theo dõi chi tiêu cá nhân từ quy trình thủ công thành một Serverless Web Application có thể được truy cập thông qua trình duyệt web.

Người dùng sẽ có thể:

- Truy cập ứng dụng web.

- Đăng ký và đăng nhập an toàn.

- Thêm các bản ghi chi tiêu mới.

- Xem lịch sử chi tiêu.

- Chỉnh sửa và xóa các bản ghi chi tiêu.

- Lọc chi tiêu theo ngày hoặc danh mục.

- Xem tổng chi tiêu và các thông tin tổng hợp theo danh mục.

Người dùng có thể quản lý chi tiêu mà không cần cài đặt một desktop application riêng hoặc tự duy trì một backend server cá nhân.

#### Giá trị lâu dài

Kiến trúc được thiết kế theo hướng module, cho phép bổ sung các chức năng mới mà không cần thay đổi lớn đối với toàn bộ hệ thống.

Các cải tiến trong tương lai có thể bao gồm:

- Quản lý ngân sách và cảnh báo ngân sách.

- Hỗ trợ chi tiêu định kỳ.

- Phân tích và trực quan hóa nâng cao.

- Bổ sung các danh mục chi tiêu và danh mục tùy chỉnh.

- Notifications sử dụng các dịch vụ AWS bổ sung.

- Export dữ liệu chi tiêu.

- Monitoring và reporting nâng cao.

Dự án cũng mang lại kinh nghiệm thực tế trong việc kết hợp web development, AWS Cloud, authentication, database design, serverless computing và monitoring, phù hợp với chuyên ngành Khoa học dữ liệu.
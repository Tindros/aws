---
title: "Blog 1"
date: 2026-11-21
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

###  Nếu website đột nhiên có 10.000 người truy cập thì chuyện gì xảy ra?

Giả sử hôm nay mình vừa deploy một website.

Ban đầu mọi thứ khá yên bình.

Có khoảng **10 người truy cập cùng lúc**.

Website phản hồi nhanh.

Server chỉ chạy khoảng 20–30% CPU.

Mọi thứ có vẻ ổn.

Rồi một ngày...

Một bài đăng trên mạng xã hội bất ngờ viral.

Từ 10 người...

**10 → 100 → 1.000 → 10.000 người**

cùng truy cập website.

Và lúc này mình bắt đầu có một câu hỏi khá đáng sợ:

> **Server của mình có chịu nổi không?**

---

### Nếu chỉ có một EC2 thì sao?

Giả sử website đang chạy trên một EC2 duy nhất.

Khi chỉ có vài người truy cập, mọi thứ bình thường.

Nhưng khi lượng request tăng lên, CPU và memory cũng bắt đầu tăng.

Ví dụ:

**10 users → CPU 20%**

**100 users → CPU 45%**

**1.000 users → CPU 85%**

**10.000 users → 💥 ?**

Đến đây mình có hai lựa chọn.

**Một là:** mua một server mạnh hơn.

**Hai là:** có nhiều server cùng xử lý.

Và đây là lúc mình bắt đầu gặp hai khái niệm rất quan trọng trong Cloud.

---

###  Scale Up và Scale Out

Nếu mình đổi:

**EC2 nhỏ → EC2 lớn hơn**

thì đây gọi là **Scale Up**.

Ví dụ từ một instance có ít CPU và RAM lên một instance mạnh hơn.

Cách này khá đơn giản.

Nhưng nó có một vấn đề:

> **Server lớn đến đâu thì cũng có giới hạn.**

Nếu traffic tiếp tục tăng thì sao?

Mình lại phải nâng cấp.

Và trong quá trình nâng cấp, có thể còn phải đối mặt với downtime.

---

Một cách khác là:

**EC2 #1 + EC2 #2 + EC2 #3**

Thay vì làm **một server thật mạnh**, mình dùng **nhiều server cùng làm việc**.

Đây là **Scale Out**.

Nhưng ngay lập tức xuất hiện một vấn đề mới:

> **User sẽ được gửi đến EC2 nào?**

---

###  Có 3 EC2 nhưng ai chia traffic?

Giả sử mình có:

**EC2 #1 — EC2 #2 — EC2 #3**

Nếu có 3.000 request đến cùng lúc, mình không muốn tất cả đều đổ vào EC2 #1.

Mình cần một thành phần đứng phía trước để phân phối traffic.

Đó là **Load Balancer**.

Luồng lúc này có thể hình dung đơn giản như:

**Users → Load Balancer → EC2 #1 / EC2 #2 / EC2 #3**

Load Balancer giúp phân phối request đến các server phía sau thay vì để một server phải chịu toàn bộ traffic.

Lúc này mình giải quyết được một vấn đề:

> **Một server không cần phải chịu toàn bộ traffic.**

Nhưng vẫn còn một câu hỏi.

---

### Vậy tại sao không tạo sẵn 100 EC2?

Nếu hôm nay website chỉ có 10 người truy cập:

**10 Users → 100 EC2**

thì phần lớn server gần như không làm gì.

Nhưng mình vẫn phải trả tiền cho những resource đó.

Vậy mình cần một thứ thông minh hơn:

> **Traffic tăng thì tạo thêm server.**
>
> **Traffic giảm thì bớt server.**

Đây là lúc **Auto Scaling** xuất hiện.

Ví dụ ban đầu mình có:

**2 EC2**

Traffic tăng:

**2 EC2 → 4 EC2 → 6 EC2 → 8 EC2**

Khi traffic giảm:

**8 EC2 → 6 EC2 → 4 EC2 → 2 EC2**

Mình không cần ngồi trước AWS Console để tạo và xóa EC2 bằng tay.

Hệ thống có thể tự điều chỉnh số lượng instance dựa trên những điều kiện mình cấu hình.

Đây là một trong những điều mình thấy thú vị nhất khi học Cloud:

> **Không phải chỉ “có nhiều server”.**
>
> Mà là hệ thống có thể **tự thay đổi số lượng server theo nhu cầu.**

---

### Nhưng nếu một EC2 bị chết thì sao?

Đây là tình huống mình chưa nghĩ tới lúc đầu.

Giả sử mình đang có:

**Load Balancer → EC2 #1 / EC2 #2 / EC2 #3**

Nhưng EC2 #2 đột nhiên gặp lỗi.

Nếu Load Balancer vẫn gửi request vào EC2 #2 thì user có thể nhận lỗi.

Vì vậy hệ thống cần biết:

> **Instance nào còn khỏe?**

Load Balancer có thể sử dụng **Health Checks** để kiểm tra tình trạng của các instance.

Nếu EC2 #2 không còn healthy, traffic có thể được chuyển sang các instance khác.

Sau đó Auto Scaling có thể tạo instance mới để duy trì capacity mong muốn.

Luồng lúc này có thể hiểu đơn giản là:

**EC2 #2 Unhealthy → Health Check phát hiện → Traffic chuyển sang instance khỏe → Auto Scaling tạo instance thay thế**

Từ đây mình bắt đầu nhận ra:

> **Có nhiều server chưa đủ. Hệ thống còn phải biết server nào đang hoạt động tốt.**

---

### Nhưng làm sao hệ thống biết lúc nào cần Scale?

Đến đây lại xuất hiện một câu hỏi khác.

> **Traffic tăng thì lúc nào mới cần tạo thêm EC2?**

Ví dụ mình muốn theo dõi:

- CPU Utilization
- Network traffic
- Request count
- Error rate
- Response time

Nếu CPU liên tục tăng:

**20% → 30% → 45% → 60% → 75% → 85%**

thì mình có thể cấu hình scaling policy để hệ thống phản ứng khi đạt điều kiện phù hợp.

AWS có **Amazon CloudWatch** để thu thập metrics và hỗ trợ monitoring.

Lúc này mình mới nhận ra:

> **Auto Scaling không tự nhiên biết lúc nào cần tạo EC2.**

Nó cần dữ liệu để quan sát tình trạng hệ thống.

Monitoring và scaling vì vậy có mối quan hệ khá chặt chẽ.

---

### Nhưng còn một vấn đề nữa...

Giả sử website của mình có người dùng ở:

🇻🇳 Việt Nam  
🇯🇵 Nhật Bản  
🇺🇸 Mỹ  
🇬🇧 Anh

Tất cả đều phải gửi request về backend.

Nếu website có nhiều nội dung tĩnh như:

- hình ảnh
- CSS
- JavaScript
- video
- file

thì có nhất thiết request nào cũng phải đi thẳng về server không?

**Không nhất thiết.**

Đây là lúc **CDN** trở nên hữu ích.

Với AWS, **Amazon CloudFront** có thể giúp phân phối nội dung từ các edge location gần người dùng hơn.

Những nội dung có thể cache sẽ không nhất thiết phải quay về origin mỗi lần.

Và một lần nữa mình nhận ra:

> Một service xuất hiện không phải vì “AWS có service này nên mình thêm vào”.
>
> Nó xuất hiện vì hệ thống đang gặp một vấn đề cụ thể.

**User ở xa server → Latency**

→ CDN có thể giúp giải quyết vấn đề này.

---

### Còn Security thì sao?

Đến đây hệ thống của mình đã có:

**User → CloudFront → Load Balancer → EC2**

Nhưng nếu bất kỳ ai cũng có thể gửi hàng triệu request độc hại vào application thì sao?

Hệ thống vẫn có thể gặp vấn đề dù mình đã có Auto Scaling.

Vì vậy:

> **Scalability không đồng nghĩa với Security.**

Có thể cần thêm những lớp bảo vệ như:

- Security Groups
- Network controls
- AWS WAF
- IAM

Đây là một điều mình thấy khá quan trọng:

> **Scale được không có nghĩa là an toàn.**
>
> **Server mạnh không có nghĩa là application được bảo vệ.**

Performance, scalability và security là những bài toán khác nhau.

---

### Vậy ban đầu chỉ có một EC2, tại sao architecture lại trở nên phức tạp như vậy?

Ban đầu:

**User → EC2**

Sau đó mình lần lượt đặt những câu hỏi:

> **Nếu traffic tăng thì sao?**

→ Scale Up / Scale Out

> **Có nhiều EC2 thì ai chia traffic?**

→ Load Balancer

> **Traffic tăng giảm liên tục thì sao?**

→ Auto Scaling

> **Một EC2 chết thì sao?**

→ Health Check

> **Làm sao biết hệ thống đang gặp vấn đề?**

→ CloudWatch

> **User ở rất xa server thì sao?**

→ CloudFront

> **Nếu traffic là malicious traffic thì sao?**

→ Security layer / WAF

Từ một server đơn giản, architecture dần trở thành một hệ thống có khả năng:

**Xử lý traffic → phân phối traffic → tự mở rộng → phát hiện lỗi → monitoring → cải thiện performance → tăng security**

---

### Điều mình học được

Điều thú vị nhất sau khi tìm hiểu vấn đề này không phải là:

> “AWS có Load Balancer.”

hay:

> “AWS có Auto Scaling.”

Mà là cách mình bắt đầu suy nghĩ về một hệ thống.

Ban đầu mình chỉ có một câu hỏi:

> **“Nếu có quá nhiều người truy cập thì sao?”**

Sau đó câu hỏi lại kéo theo những câu hỏi khác:

**Một server chịu không nổi thì sao?**

→ Scale Out.

**Có nhiều server thì gửi request vào đâu?**

→ Load Balancer.

**Traffic tăng giảm liên tục thì sao?**

→ Auto Scaling.

**Một server chết thì sao?**

→ Health Check.

**Làm sao biết hệ thống đang gặp vấn đề?**

→ Monitoring.

**User ở xa server thì sao?**

→ CDN.

**Nếu traffic là malicious traffic thì sao?**

→ Security layer.

Và mình nhận ra một điều:

> **Cloud Architecture không bắt đầu bằng việc chọn AWS Service.**
>
> Nó bắt đầu bằng việc đặt câu hỏi:
>
> **“Nếu chuyện này xảy ra thì hệ thống của mình sẽ thế nào?”**

Khi mình bắt đầu suy nghĩ theo cách đó, những cái tên như **EC2, Load Balancer, Auto Scaling, CloudWatch hay CloudFront** không còn là những service phải học thuộc nữa.

Chúng trở thành **những công cụ dùng để giải quyết từng vấn đề cụ thể**.

Có lẽ đây cũng là một trong những điều mình thích nhất khi học Cloud:

> **Không phải xây một hệ thống thật to.**
>
> Mà là hiểu tại sao hệ thống cần từng thành phần, và chuyện gì sẽ xảy ra khi một trong số chúng không còn đáp ứng được nhu cầu.

#AWS #CloudComputing #AWSArchitecture#EC2 #AutoScaling #LoadBalancer #CloudWatch #CloudFront #CloudLearning #AWSBeginner
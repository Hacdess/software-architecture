# 4. Kiến trúc Microservices

> src: `Slides/Kien-truc-microservices.pdf`

## 4.1. Bản chất của microservices

Microservices là cách chia hệ thống thành một tập hợp các dịch vụ nhỏ, tự trị, ghép nối lỏng lẻo. Mỗi dịch vụ:

- Là một thành phần riêng biệt.
- Có thể được xây dựng, chạy và triển khai độc lập.
- Tự chứa logic và dữ liệu cần thiết.
- Chịu trách nhiệm cho một năng lực kinh doanh cụ thể trong một bối cảnh giới hạn.

Theo cách hiểu thực tế:

$$\text{Microservices} = \text{dịch vụ nhỏ} + \text{tự trị} + \text{ghép nối lỏng} + \text{triển khai độc lập}$$

## 4.2. Microservices so với monolith

### 4.2.1. Mô hình nguyên khối

- Gộp toàn bộ chức năng vào một mã nguồn lớn.
- Thường dùng một cơ sở dữ liệu chung.
- Khi hệ thống phình to thì khó hiểu, khó kiểm thử và khó mở rộng.
- Mỗi lần thay đổi nhỏ cũng có thể phải build và triển khai lại toàn bộ ứng dụng.

### 4.2.2. Mô hình microservices

- Chia ứng dụng thành nhiều dịch vụ nhỏ, độc lập.
- Mỗi dịch vụ có thể có cơ sở dữ liệu riêng.
- Có thể hiểu, kiểm thử và mở rộng từng phần.
- Có thể triển khai hoặc rollback từng dịch vụ độc lập.
- Thay đổi nhỏ được đưa ra nhanh hơn và ít ảnh hưởng hơn tới toàn hệ thống.

## 4.3. Mục đích của microservices

### 4.3.1. Tăng tốc độ ra mắt

Các tính năng mới có thể được triển khai nhanh mà không cần phát hành lại toàn bộ hệ thống.

### 4.3.2. Cải thiện khả năng mở rộng

Thay vì scale toàn bộ ứng dụng, ta có thể scale riêng những dịch vụ đang chịu tải cao.

### 4.3.3. Tự chủ cho các nhóm

Mỗi nhóm nhỏ có thể chịu trách nhiệm một dịch vụ, tự chủ về công nghệ, quy trình và vòng đời phát hành.

### 4.3.4. Cách ly lỗi

Nếu một dịch vụ gặp sự cố, lỗi không nhất thiết làm sập toàn bộ hệ thống. Các kỹ thuật như circuit breaker và bulkhead giúp giới hạn phạm vi lan truyền lỗi.

## 4.4. Loose coupling

Loose coupling nghĩa là các dịch vụ không chia sẻ chi tiết nội bộ với nhau. Chúng giao tiếp qua API hoặc message được định nghĩa rõ ràng.

- Mỗi dịch vụ sở hữu dữ liệu của mình.
- Dữ liệu chỉ được cung cấp cho dịch vụ khác thông qua API hoặc event.
- Áp dụng mẫu `Database per Service`: dữ liệu bền vững của mỗi dịch vụ là riêng tư.
- Giảm phụ thuộc giữa các nhóm phát triển.
- Cho phép thay đổi schema, công nghệ hoặc logic nội bộ mà ít ảnh hưởng tới dịch vụ khác.

## 4.5. Triển khai độc lập

Một kiến trúc microservices đúng nghĩa cần có từ hai thành phần trở lên có thể triển khai độc lập và ghép nối lỏng lẻo.

Các dấu hiệu thường gặp:

- **Kho mã riêng:** mỗi dịch vụ có mã nguồn riêng hoặc ít nhất có ranh giới mã nguồn rõ ràng.
- **Pipeline CI/CD riêng:** mỗi dịch vụ có quy trình build, test và deploy riêng.
- **Deploy và rollback riêng:** có thể sửa lỗi hoặc thêm tính năng cho một dịch vụ mà không phải build lại toàn bộ hệ thống.

## 4.6. Chiến lược phân rã hệ thống

Có ba hướng phân rã chính:

- Phân rã theo năng lực kinh doanh.
- Phân rã theo bối cảnh giới hạn hoặc subdomain trong Domain-Driven Design.
- Phân rã theo quyền sở hữu dữ liệu.

## 4.7. Phân rã theo năng lực kinh doanh

Business capability là những gì doanh nghiệp làm để tạo ra giá trị, ví dụ quản lý đơn hàng, xử lý thanh toán, quản lý kho hoặc giao hàng.

Mẫu `Decompose by business capability` khuyến nghị mỗi dịch vụ đại diện cho một năng lực kinh doanh và do một nhóm nhỏ chịu trách nhiệm.

Phù hợp khi:

- Mô hình tổ chức rõ theo chức năng hoặc năng lực.
- Các capability tương đối ổn định.
- Muốn các nhóm làm việc độc lập theo dòng giá trị.

Ví dụ với cửa hàng trực tuyến:

- Dịch vụ danh mục sản phẩm.
- Dịch vụ tồn kho.
- Dịch vụ đơn hàng.
- Dịch vụ giao hàng.
- Dịch vụ thanh toán.

## 4.8. Phân rã theo bối cảnh giới hạn

Trong Domain-Driven Design, `bounded context` là phạm vi mà một mô hình miền được định nghĩa và áp dụng nhất quán.

Một domain lớn thường có nhiều subdomain. Microservices nên được thiết kế theo các subdomain này để dịch vụ có cohesion cao và ranh giới ổn định.

DDD thường chia subdomain thành ba loại:

### 4.8.1. Core subdomain

Core subdomain là phần tạo lợi thế cạnh tranh chính cho doanh nghiệp.

- Cần đầu tư kỹ về mô hình nghiệp vụ.
- Nên có đội mạnh và hiểu domain sâu.
- Thay đổi ở đây ảnh hưởng trực tiếp tới giá trị công ty.

Ví dụ:

- Amazon: dynamic pricing, recommendation.
- Uber: dispatch và matching giữa tài xế với khách.
- Ngân hàng: ledger, quy tắc ghi nợ/có, đảm bảo tính bất biến.

### 4.8.2. Supporting subdomain

Supporting subdomain hỗ trợ core hoạt động. Nó quan trọng, nhưng thường không tạo khác biệt cạnh tranh trực tiếp.

Ví dụ:

- E-commerce: quản lý tồn kho, quản lý shipping.
- Uber: hồ sơ tài xế, tích hợp bản đồ.
- Ngân hàng: KYC.

### 4.8.3. Generic subdomain

Generic subdomain là các chức năng phổ biến, nhiều hệ thống đều cần và không tạo khác biệt riêng.

Ví dụ:

- Auth và identity.
- Notification qua email/SMS.
- Reporting, logging, monitoring.

Với generic subdomain, thường nên dùng giải pháp có sẵn hoặc tích hợp hệ thống ngoài thay vì tự xây lại từ đầu.

### 4.8.4. Lợi ích của cách chia này

- Tập trung nguồn lực vào phần tạo khác biệt.
- Tổ chức team theo giá trị kinh doanh.
- Không làm phức tạp hóa các phần generic bằng mô hình DDD quá nặng.
- Giúp ranh giới microservices ổn định lâu dài.

Tóm gọn:

$$\text{Core} = \text{tạo khác biệt}$$

$$\text{Supporting} = \text{giúp core hoạt động}$$

$$\text{Generic} = \text{chức năng chung, nên tận dụng giải pháp sẵn có}$$

## 4.9. Phân rã theo quyền sở hữu dữ liệu

Microservices nên sở hữu dữ liệu của mình và không chia sẻ trực tiếp database với dịch vụ khác.

Nguyên tắc:

- Mỗi dịch vụ có dữ liệu bền vững riêng.
- Dịch vụ khác không được truy cập trực tiếp database của dịch vụ đó.
- Mọi truy cập dữ liệu đi qua API hoặc event.
- Mỗi dịch vụ có thể chọn loại database phù hợp với nhu cầu của mình.

Hệ quả:

- Giảm coupling.
- Tăng tự chủ công nghệ.
- Khó thực hiện transaction xuyên nhiều dịch vụ.
- Khó join dữ liệu trực tiếp giữa nhiều service.

Giải pháp thường dùng:

- Saga cho giao dịch phân tán.
- API composition cho truy vấn cần dữ liệu từ nhiều dịch vụ.
- CQRS để tách command và query.

## 4.10. Kích thước và số lượng dịch vụ

Dịch vụ nên đủ nhỏ để một nhóm nhỏ có thể phát triển và vận hành. Một cách nói phổ biến là "two-pizza team", khoảng 6-10 người.

Tiêu chí xác định kích thước:

- Mỗi dịch vụ quản lý một năng lực kinh doanh rõ ràng.
- Ranh giới nên dựa trên bounded context.
- Không đo bằng số dòng code, mà bằng độ kết dính nghiệp vụ.

Cần cân bằng:

- Dịch vụ quá nhỏ làm tăng chi phí quản lý, deploy, monitoring và giao tiếp.
- Dịch vụ quá lớn làm mất lợi ích của microservices.
- Kích thước hợp lý phụ thuộc vào chi phí vận hành và năng lực đội ngũ.

## 4.11. Thiết kế và ranh giới dịch vụ

### 4.11.1. Nguyên tắc trách nhiệm đơn

Mỗi dịch vụ chỉ nên có một lý do chính để thay đổi. Tránh gộp nhiều chức năng khác bản chất vào cùng một service.

### 4.11.2. API và hợp đồng rõ ràng

Các dịch vụ giao tiếp qua API rõ ràng như REST hoặc gRPC. Cần thống nhất versioning và backward compatibility.

### 4.11.3. Tự trị dữ liệu

Dịch vụ quản lý database riêng. Có thể dùng RDBMS hoặc NoSQL tùy nhu cầu, nhưng không truy cập trực tiếp dữ liệu của dịch vụ khác.

### 4.11.4. Phương thức giao tiếp

- Đồng bộ: HTTP, REST, gRPC, dùng khi cần phản hồi tức thì.
- Bất đồng bộ: message bus như Kafka hoặc Service Bus, dùng khi muốn giảm coupling và tăng khả năng chịu lỗi.

### 4.11.5. Phân tách front-end

Có thể dùng `Backend for Frontend` hoặc meta-endpoint để hỗ trợ nhiều loại client như web và mobile. Cách này tránh việc client phải gọi trực tiếp quá nhiều dịch vụ nhỏ.

## 4.12. Ưu điểm của microservices

- **Mở rộng linh hoạt:** mỗi dịch vụ scale độc lập theo nhu cầu.
- **Triển khai nhanh:** có thể deploy, rollback hoặc cập nhật một dịch vụ mà ít ảnh hưởng tới phần còn lại.
- **Đa dạng công nghệ:** từng dịch vụ có thể dùng ngôn ngữ, framework hoặc database phù hợp.
- **Cách ly lỗi:** lỗi ở một dịch vụ không nhất thiết làm sập toàn bộ hệ thống.
- **Đội ngũ tự trị:** dịch vụ nhỏ giúp nhóm nhỏ sở hữu trọn vòng đời phát triển và vận hành.
- **Tương thích cloud-native:** phù hợp với container, Kubernetes, orchestration và tự động hóa.

## 4.13. Nhược điểm của microservices

- **Độ phức tạp hệ thống tăng:** hệ thống phân tán cần giải quyết giao tiếp, service discovery, cân bằng tải, timeout và quản lý lỗi.
- **Chi phí vận hành cao:** nhiều service kéo theo nhiều pipeline, container, log, metric và hạ tầng.
- **Nhất quán dữ liệu khó hơn:** giao dịch xuyên dịch vụ phức tạp; thường tránh 2PC và dùng Saga.
- **Truy vấn liên dịch vụ khó:** không thể join trực tiếp nhiều database; cần API composition hoặc CQRS.
- **Kiểm thử và debug khó hơn:** test một service có thể phải mô phỏng hoặc tích hợp với nhiều service khác.
- **Thiếu quản trị dễ gây hỗn loạn:** tự do công nghệ nếu không có chuẩn chung sẽ làm hệ thống khó vận hành.

## 4.14. Biện pháp giảm thiểu rủi ro

- **Saga và event-driven:** chia giao dịch phân tán thành các transaction cục bộ, có transaction bồi thường khi thất bại.
- **Quan sát tập trung:** dùng log aggregation, metrics và distributed tracing để phát hiện lỗi.
- **Orchestration và service mesh:** dùng Kubernetes, service mesh để tự động hóa triển khai, quản lý traffic và bảo mật.
- **Circuit breaker và bulkhead:** ngăn lỗi lan rộng và phân bổ tài nguyên riêng cho từng dịch vụ.
- **Tiêu chuẩn nội bộ:** thống nhất cách đặt tên, logging, monitoring, bảo mật, API và versioning.

## 4.15. Trường hợp sử dụng thực tế

### 4.15.1. Amazon

Amazon chuyển từ mô hình nguyên khối sang các web service đơn giản. Mỗi chức năng được giao cho một đội riêng và giao tiếp qua API.

### 4.15.2. Netflix

Netflix chuyển đổi mạnh sang microservices sau sự cố cơ sở dữ liệu, đồng thời di chuyển lên AWS. Hệ thống xử lý lượng yêu cầu rất lớn mỗi ngày nhờ nhiều dịch vụ độc lập.

### 4.15.3. Uber

Uber tách từng chức năng thành microservices, kết nối qua API gateway. Điều này giúp triển khai nhanh hơn và quản lý hệ thống dễ hơn khi quy mô tăng.

### 4.15.4. Etsy

Etsy gặp vấn đề hiệu năng khi truy cập tăng cao và đặt mục tiêu mỗi giao dịch dưới 1 giây. Họ áp dụng hệ thống API hai tầng, trong đó các meta-endpoint tổng hợp nhiều endpoint nhỏ để client chỉ cần gọi một điểm vào phù hợp.

### 4.15.5. CIBC

CIBC, một ngân hàng Canada, chịu áp lực từ kênh di động, fintech và yêu cầu mở dữ liệu. Họ xem microservices là cách thích ứng với tương lai biến động, đồng thời xây framework riêng dựa trên mã nguồn mở để giảm phụ thuộc vendor.

## 4.16. Khi nào nên dùng microservices?

Phù hợp khi:

- Hệ thống lớn, phức tạp và cần mở rộng linh hoạt.
- Tổ chức có nhiều năng lực kinh doanh độc lập.
- Nhóm phát triển đa chức năng, có khả năng sở hữu service trọn vòng đời.
- Cần phát hành nhanh, liên tục.
- Đang chuyển đổi lên cloud.
- Cần resilience cao.
- Muốn thử nghiệm công nghệ mới ở từng phần.
- Phải đáp ứng yêu cầu pháp lý thay đổi nhanh.

Không phù hợp khi:

- Ứng dụng nhỏ, đơn giản.
- Nhóm chưa có kinh nghiệm vận hành hệ thống phân tán.
- Thiếu hạ tầng DevOps, monitoring và tự động hóa.
- Không có khả năng quản trị và thiết lập tiêu chuẩn.
- Chỉ chọn microservices vì xu hướng.

Microservices không phải là "liều thuốc thần". Cần đánh giá phạm vi dự án, năng lực đội ngũ, chi phí vận hành và lợi ích thật sự trước khi áp dụng.

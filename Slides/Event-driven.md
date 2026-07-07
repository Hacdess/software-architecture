# 5. Kiến trúc hướng sự kiện

> src: `Slides/Kien-Truc-Huong-Su-Kien-Event-Driven-Architecture.pdf`

## 5.1. Kiến trúc hướng sự kiện là gì?

Kiến trúc hướng sự kiện, hay `Event-Driven Architecture` (EDA), là mô hình kiến trúc phần mềm trong đó các thành phần hoặc dịch vụ giao tiếp với nhau chủ yếu thông qua việc sản xuất và tiêu thụ sự kiện.

Sự kiện có thể là:

- Hành động của người dùng.
- Cập nhật dữ liệu.
- Thông báo từ hệ thống khác.
- Một thay đổi trạng thái trong nghiệp vụ.

Ví dụ:

- `OrderPlaced`: khách đặt hàng.
- `StockReserved`: hệ thống đã giữ hàng.
- `PaymentCaptured`: thanh toán đã được ghi nhận.
- `RideRequested`: khách yêu cầu chuyến xe.

## 5.2. Lợi ích của EDA

### 5.2.1. Loose coupling

Các thành phần hoạt động độc lập và không cần biết chi tiết nội bộ của nhau. Thành phần phát event chỉ cần công bố sự kiện; thành phần quan tâm tự subscribe và xử lý.

### 5.2.2. Xử lý thời gian thực

Hệ thống có thể phản ứng nhanh khi sự kiện xảy ra, phù hợp với các nghiệp vụ cần cập nhật tức thời.

### 5.2.3. Khả năng phục hồi

Nếu một số thành phần bị lỗi, phần còn lại của hệ thống vẫn có thể tiếp tục hoạt động. Event broker hoặc queue có thể giữ sự kiện để xử lý lại sau.

## 5.3. Phân loại kiến trúc hướng sự kiện

EDA thường được phân loại theo topology, tức cách các component kết nối và phối hợp với nhau.

Hai mô hình phổ biến:

- `Broker Topology`: không có trung tâm điều phối workflow; event được phân phối qua message broker.
- `Mediator Topology`: có một mediator điều phối và quản lý luồng sự kiện giữa các service.

## 5.4. Broker topology

Trong broker topology, luồng tin nhắn được phân phối tới các event processor thông qua message broker. Không có một trung tâm điều phối toàn bộ workflow.

### 5.4.1. Thành phần chính

1. **Initiating event:** sự kiện ban đầu khởi phát luồng xử lý.
2. **Event channel:** nơi lưu trữ và phân phối event tới các service quan tâm. Channel có thể là topic hoặc queue.
3. **Event broker:** hệ thống chứa các event channel, ví dụ Kafka hoặc RabbitMQ.
4. **Event processor:** service chịu trách nhiệm xử lý event.
5. **Processing event:** event mới được tạo ra sau khi một service xử lý xong và trạng thái thay đổi.

## 5.5. Ví dụ broker topology: cửa hàng online

Bối cảnh: khách bấm "Đặt hàng" trên website.

- `OrderPlaced` là initiating event.
- `orders.topic` là event channel dùng để phát `OrderPlaced` cho các dịch vụ quan tâm.
- Kafka hoặc RabbitMQ là event broker.
- Inventory Service nhận `OrderPlaced`, giữ hàng và phát `StockReserved`.
- Payment Service nhận `OrderPlaced`, thu tiền và phát `PaymentCaptured`.
- Order Service nhận `StockReserved` và `PaymentCaptured`, cập nhật trạng thái đơn hàng và phát `OrderConfirmed`.
- Notification Service nghe `OrderConfirmed` để gửi email.

Luồng tóm tắt:

```text
UI
-> OrderPlaced
-> Kafka/orders.topic
-> Inventory Service + Payment Service
-> StockReserved + PaymentCaptured
-> Order Service
-> OrderConfirmed
-> Notification Service
```

Trong mô hình này, service publish event theo kiểu fire-and-forget. Ví dụ Inventory Service publish `StockReserved` rồi kết thúc trách nhiệm của mình, không cần chờ Order Service hoặc Payment Service phản hồi trực tiếp.

## 5.6. Ví dụ broker topology: gọi xe công nghệ

Bối cảnh: khách nhấn "Đặt xe".

- `RideRequested` là initiating event.
- `rides.topic` phân phối event cho Dispatch Service và Pricing Service.
- RabbitMQ hoặc Kafka đóng vai trò broker.
- Pricing Service nhận `RideRequested`, tính giá động và phát `FareQuoted`.
- Dispatch Service nhận `RideRequested`, tìm tài xế phù hợp và phát `DriverAssigned`.
- Trip Service nhận `FareQuoted` và `DriverAssigned`, tạo chuyến và phát `TripStarted` hoặc `TripCompleted`.
- Billing, Notification và Analytics có thể subscribe các event liên quan.

## 5.7. Luồng tin nhắn trong broker topology

1. Initiating event được gửi tới event channel trong broker.
2. Event processor lấy event từ channel để xử lý.
3. Sau khi xử lý xong, processor phát processing event mới.
4. Các processor khác lắng nghe và tiếp tục phản ứng.

Điểm quan trọng: processor có thể gửi processing event dù hiện tại chưa có consumer nào lắng nghe. Điều này giúp hệ thống dễ mở rộng khi có nghiệp vụ mới.

## 5.8. Khi nào nên dùng broker topology?

Nên dùng khi:

- Cần giảm phụ thuộc giữa các thành phần.
- Định tuyến đơn giản.
- Các thành phần cần hoạt động độc lập.
- Workflow không cần điều phối tập trung.

Không nên dùng khi:

- Logic xử lý giữa các thành phần quá phức tạp.
- Cần tích hợp và chuẩn hóa dữ liệu từ nhiều nguồn.
- Cần kiểm soát trung tâm và quản lý workflow chặt chẽ.

## 5.9. Mediator topology

Trong mediator topology, có một thành phần trung gian gọi là mediator. Mediator điều phối, quản lý thứ tự xử lý, theo dõi trạng thái và ra quyết định cho luồng sự kiện.

Nếu broker topology giống choreography, nơi các service tự phối hợp qua event, thì mediator topology giống orchestration, nơi có một bộ điều phối trung tâm.

### 5.9.1. Thành phần chính

1. **Initiating event:** sự kiện ban đầu khởi phát workflow.
2. **Event queue:** lưu các event ban đầu để tránh mất mát và điều tiết tốc độ xử lý.
3. **Event mediator:** trung tâm điều phối luồng sự kiện.
4. **Event channel:** kênh phân phối event hoặc command.
5. **Event processor:** service xử lý từng bước cụ thể.

## 5.10. Ví dụ mediator topology: cửa hàng online

Bối cảnh: khách đặt hàng.

- `OrderPlaced` là initiating event.
- `orders.incoming.queue` lưu event ban đầu để tránh mất dữ liệu.
- `OrderFlowMediator` đọc event từ queue và quyết định các bước tiếp theo.
- Mediator gửi command giữ hàng tới Inventory Service.
- Mediator gửi command thu tiền tới Payment Service.
- Inventory Service phát `StockReserved`.
- Payment Service phát `PaymentCaptured`.
- Mediator subscribe các event kết quả, khi đủ điều kiện thì phát `OrderConfirmed`.

Luồng tóm tắt:

```text
UI
-> OrderPlaced
-> orders.incoming.queue
-> OrderFlowMediator
-> inventory.cmd.reserve + payment.cmd.capture
-> StockReserved + PaymentCaptured
-> OrderFlowMediator
-> OrderConfirmed
-> Notification/Analytics
```

Mediator có thể theo dõi trạng thái từng bước, quyết định commit hoặc compensation nếu một bước thất bại.

## 5.11. Ví dụ mediator topology: đặt xe

Bối cảnh: khách yêu cầu chuyến xe.

- `RideRequested` là initiating event.
- `rides.incoming.queue` nhận yêu cầu để chống mất mát và hỗ trợ back-pressure.
- `RideOrchestratorMediator` đọc yêu cầu và điều phối các bước.
- Mediator gửi yêu cầu báo giá tới Pricing Service.
- Mediator gửi yêu cầu gán tài xế tới Dispatch Service.
- Pricing Service trả về `FareQuoted`.
- Dispatch Service trả về `DriverAssigned`.
- Mediator nhận đủ kết quả, xử lý timeout hoặc compensation nếu cần, rồi phát `TripCreated`.

## 5.12. Ví dụ mediator topology: hệ thống bán lẻ

Một hệ thống bán lẻ có thể dùng mediator để xử lý đơn hàng qua nhiều bước:

1. **Tạo đơn hàng:** mediator nhận event `PlaceOrder`, tạo event `create-order` và gửi tới `order-placement queue`. Processor xác thực thông tin, tạo đơn hàng và trả lại ID đơn hàng.
2. **Xử lý đơn hàng:** mediator gửi đồng thời `email-customer`, `apply-payment` và `adjust-inventory` tới các queue tương ứng.
3. **Thực hiện đơn hàng:** mediator gửi `fulfill-order` và `order-stock` tới fulfillment và warehouse.
4. **Giao hàng:** mediator gửi `email-customer` và `ship-order`.
5. **Thông báo khách hàng:** mediator gửi email cập nhật trạng thái đơn hàng.

## 5.13. Khi nào nên dùng mediator topology?

Nên dùng khi:

- Cần quản lý tập trung.
- Workflow phức tạp.
- Cần điều phối thứ tự, nhánh xử lý và compensation.
- Cần tích hợp nhiều hệ thống khác nhau.
- Cần kiểm soát quyền truy cập và bảo mật chặt chẽ.

Không nên dùng khi:

- Yêu cầu hiệu suất rất cao và độ trễ thấp.
- Muốn hệ thống thật đơn giản và dễ mở rộng.
- Cần tách biệt mạnh giữa các thành phần.
- Muốn tránh điểm lỗi đơn.

## 5.14. So sánh broker và mediator topology

| Tiêu chí | Broker topology | Mediator topology |
| --- | --- | --- |
| Điều phối trung tâm | Không | Có |
| Độ phức tạp | Thấp | Cao |
| Quản lý workflow | Hạn chế | Mạnh |
| Loose coupling | Cao | Trung bình |
| Điểm lỗi đơn | Ít hơn | Có thể nằm ở mediator |
| Phù hợp với | Hệ thống đơn giản, event flow tự nhiên | Hệ thống phức tạp, cần orchestration |

Tóm tắt:

- Broker topology: các service giao tiếp gián tiếp qua broker; không có "não trung tâm". Mỗi service tự xử lý nghiệp vụ khi nhận event.
- Mediator topology: có mediator quyết định thứ tự, nhánh xử lý, compensation và tổng hợp kết quả.

## 5.15. Vấn đề mất dữ liệu trong EDA

Mất dữ liệu là một vấn đề trọng tâm trong EDA. Lỗi có thể xảy ra tại các điểm:

1. Event processor đẩy event vào event channel, nhưng broker lỗi làm event bị mất.
2. Event channel đẩy event tới processor, nhưng processor lỗi trước khi nhận hoặc xử lý xong.
3. Processor xử lý xong nhưng không lưu được dữ liệu vào database.

## 5.16. Giải pháp xử lý mất dữ liệu

### 5.16.1. Synchronous send

Dùng persisted message queue hỗ trợ guaranteed delivery. Broker lưu message cả trong memory và kho bền vững như filesystem hoặc database.

### 5.16.2. Client acknowledge mode

Event không bị xóa khỏi queue ngay khi được lấy ra. Nó chỉ được xác nhận sau khi processor xử lý thành công. Nếu processor lỗi, event vẫn còn trong queue để xử lý lại.

### 5.16.3. Last participant support

Tận dụng ACID để đảm bảo dữ liệu được lưu thành công trước khi xác nhận event. Event chỉ được acknowledge sau khi dữ liệu đã ghi vào database.

## 5.17. Ưu điểm của EDA

- **Khả năng mở rộng:** dễ thêm thành phần mới bằng cách subscribe event.
- **Phản ứng thời gian thực:** xử lý sự kiện ngay khi chúng xảy ra.
- **Tách biệt thành phần:** giảm phụ thuộc giữa producer và consumer.
- **Khả năng phục hồi:** một số thành phần lỗi không nhất thiết làm toàn hệ thống dừng.

## 5.18. Nhược điểm của EDA

- **Khó theo dõi luồng:** khi có nhiều event và processor, việc hiểu đường đi của nghiệp vụ trở nên khó hơn.
- **Xử lý lỗi phức tạp:** cần retry, idempotency, dead-letter queue và compensation.
- **Khó kiểm thử:** tính bất đồng bộ làm test end-to-end khó hơn.
- **Độ trễ tiềm ẩn:** nhiều lớp trung gian có thể làm tăng latency.
- **Nhất quán dữ liệu khó hơn:** thường phải chấp nhận eventual consistency.

## 5.19. Ứng dụng thực tế

- **Thương mại điện tử:** xử lý đơn hàng, quản lý kho, thanh toán, thông báo trạng thái đơn hàng.
- **Ngân hàng:** xử lý giao dịch, phát hiện gian lận, cập nhật tài khoản theo thời gian thực.
- **IoT:** xử lý dữ liệu từ thiết bị và cảm biến.
- **Mạng xã hội:** thông báo, cập nhật feed, phân tích hành vi người dùng.

## 5.20. Tổng kết

EDA là mô hình kiến trúc trong đó các thành phần giao tiếp bằng cách phát và tiêu thụ event.

Hai topology chính:

- Broker topology: đơn giản hơn, loose coupling cao, phù hợp khi các service có thể tự phản ứng với event.
- Mediator topology: kiểm soát workflow tốt hơn, phù hợp khi cần orchestration, điều phối và compensation.

Khi áp dụng EDA cần cân nhắc:

- Quản lý phụ thuộc giữa các event.
- Đảm bảo nhất quán dữ liệu.
- Tránh mất event.
- Theo dõi, tracing và debug.
- Chọn topology phù hợp với yêu cầu hệ thống.

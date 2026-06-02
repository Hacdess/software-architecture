<hr>

## 1. Mục tiêu buổi học

> "Kiến trúc bắt đầu từ chất lượng cần đặt, không bắt đầu từ công nghệ muốn dùng"

- Phân biệt: chức năng - thuộc tính chất lượng - ràng buộc - [[Thuật ngữ#^d19574|ASR]]
- Yêu cầu mơ hồ => kịch bản kiểm chứng được (nhanh nhưng nhanh trong trường hợp nào?)
- Yêu cầu quan trọng => thay đổi kiến trúc
- [[01 - KTPM là gì#^f13bf8|Trade-off]] balance
- Hệ thống với AI
## 1. Chất lượng kiến trúc
###  1.2. Đúng chức năng chưa đủ

> Chức năng trả lời hệ thống làm gì.
> Thuộc tính chất lượng trả lời hệ thống làm việc đó tốt đến mức nào và trong điều kiện nào.

- Chịu tải cao, lỗi mạng, lỗi phụ thuộc
- Data cần bảo vệ trước tấn công và sai quyền
- Nghiệp vụ thật luôn thay đổi sau khi chạy
- Vận hành bị giới hạn bởi chi phí và năng lực đội ngũ
- Kiến trúc = cách chuẩn bị cho điều kiện trên từ sớm

> "Ứng dụng có đủ chức năng, những điều đó chưa chứng minh hệ thống sẵn sàng mở bán"

### 1.3. Hệ thống thiếu chất lượng nào?

> **Hiệu năng**:
> Website phản hồi chậm khi tăng tải đột biến

> **Sẵn sàng:**
> Người dùng không thể tiếp tục thao tác khi một thành phần lỗi.

> **Độ tin cậy:**
> Tiền và trạng thái vé không còn nhất quán.

> **Khả năng quan sát:**
> Đội vận hành không thể truy vết giao dịch lỗi.

### 1.4. Bốn loại yêu cầu phân biệt

| Loại yêu cầu                   | Câu hỏi chính                  | Ví dụ                     |
| ------------------------------ | ------------------------------ | ------------------------- |
| **Chức năng**                  | Hệ thống làm gì                | Thanh toán đơn hàng       |
| **Thuộc tính chất lượng**      | Làm tốt đến mức nào?           | 95% giao dịch dưới 2 giây |
| **Ràng buộc**                  | Bị giới hạn bởi điều gì?       | Phải dùng cloud hiện có   |
| **[[Thuật ngữ#^d19574\|ASR]]** | Yêu cầu nào làm đổi kiến trúc? | Chịu 10.000 đơn mỗi phút  |
### 1.5. Chất lượng dẫn dắt kiến trúc

> Nhiều quyết định kiến trúc lớn xuất phát từ chất lượng cần đạt, không xuất phát trực tiếp từ chức năng.

- Tải => scale, cache, phân phối request
- Mức mất dữ liệu chấp nhận được => replicaation + recovery
- Rủi ro bảo mật => trust boundary + audit log
- Tốc độ thay đổi nghiệp vụ => module boundary + interface
- Khả năng điều tra lỗi => logging, metrics, tracing
### 1.6. Các thuộc tính về vận hành

> Chất lượng chỉ có ý nghĩa khi đặt trong bối cảnh và có thước đo.

- **Hiệu năng:** phản hồi nhanh đến đâu?
- **Khả năng mở rộng:** tải tăng mở rộng thế nào?
- **Sẵn sàng:** hệ thống có dùng được khi cần không?
- **Độ tin cậy:** kết quả có ổn định và đúng không?

### 1.7. Các thuộc tính về thay đổi và kiểm soát

> Một hệ thống chạy được hôm nay vẫn có thể thất bại nếu khó bảo vệ, khó sửa hoặc khó điều tra.

- **Bảo mật:** Ai được làm gì với dữ liệu nào?
- **Dễ thay đổi:** nghiệp vụ đổi thì sửa khó đến đâu?
- **Dễ bảo trì:** có dễ hiểu, test và vận hành không?
- **Khả năng quan sát:** lỗi xảy ra có nhìn thấy không

## 2. [[Thuật ngữ#^d19574|ASR]] và scenario

### 2.1. Từ khẩu hiệu đến yêu cầu kiểm chứng được

| Yêu cầu mơ hồ          | Yêu cầu tốt hơn                                                     |
| ---------------------- | ------------------------------------------------------------------- |
| Hệ thống phải nhanh    | 95% request tìm kiếm dưới 700ms khi có 5.000 người dùng đồng thời   |
| Hệ thống phải bảo mật  | Mọi API thay đổi dữ liệu phải xác thực, phân quyền và ghi audit log |
| Hệ thống phải dễ scale | Traffic tăng gấp 5 lần không đòi hỏi sửa code                       |
| Chatbot phải đáng tin  | Không có nguồn phù hợp thì trả lời không đủ thông tin               |

### 2.2. Công thức tư duy

> Mơ hồ $\rightarrow$ Có ngữ cảnh $\rightarrow$ Có phản ứng mong muốn $\rightarrow$ Có thước đo

- Bắt đầu từ điều stakeholder thật sự lo lắng
- Nêu rõ tình huống làm chất lượng bị thử thách
- Xác định thành phần hệ thống chịu tác động
- Mô tả phản ứng có thể quan sát được
- Chọn thước đo cho phép kiểm tra đạt hay chưa

### 2.3. ASR 

#### 2.3.1. ASR là gì?

> **Architecturally Significant Requirement (ASR):** là yêu cầu đủ quan trọng để ảnh hưởng đến cấu trúc, công nghệ hoặc chiến lược vận hành của hệ thống.

- Ảnh hưởng đến nhiều thành phần
- Khó sửa nếu quyết định sai từ đầu
- Tạo ra trade-off đáng kể
- Gắn với rủi ro kinh doanh, pháp lý hoặc đạo đức
- Đòi hỏi pattern, tactic hoặc hạ tầng đặc biệt

#### 2.3.2. Bộ lọc nhận diện ASR

> Hãy hỏi 5 câu trước khi 

#### 2.3.3. Ví dụ phân biệt ASR

| Requirement | ASR? | Lý do |
| ----------- | ---- | ----- |
|             |      |       |
### 3.4. Quality attribute scenario

> Scenario là cấu trúc 6 thành phần giúp biến mong muốn chất lượng thành yêu cầu có thể thiết kế và kiểm thử.

| Thành phần | Câu hỏi                            |
| ---------- | ---------------------------------- |
| Nguồn      | Ai hoặc cái gì gây ra sự kiện?     |
| Kích thích | Sự kiện gì xảy ra?                 |
| Môi trường | Xảy ra trong điều kiện nào>        |
| Thành phần | Phần nào của hệ thống bị tác động? |
| Phản ứng   | Hệ thống cần làm gì?               |
| Thước đo   | Kết quả được đo bằng gì?           |

### 3.5. Tactic và trade-off

#### 3.5.1. Tactic

#### 3.5.2. Không có kiến trúc tốt tuyệt đối

> Kiến trúc tốt là kiến trúc cân bằng lợi ích, rủi ro và chi phí theo bối cảnh.

| Muốn tăng      | Có thể làm khó hơn          |
| -------------- | --------------------------- |
| Bảo mật        | Usability, latency, chi phí |
| Sẵn sàng       |                             |
| Hiệu năng      |                             |
| Dễ thay đổi    |                             |
| Tự động hóa AI |                             |

## 4. Chất lượng hệ thống AI

#### 4.1. Vì sao hệ thống AI khó hơn?

#### 4.2. Thuộc tính chất lượng cho AI

> -  **Validity:** Phù hợp với mục tiêu sử dụng

#### 4.3. GenAI tạo thêm rủi ro chất lượng

> "LLM application không chỉ có lỗi code truyền thống"

#### 4.4. Scenario cho chatbot nội bộ

#### 4.5. Trách nhiệm của kiến trúc sư



## 4. Bài tập nhóm

### 4.1. Bài tập nhóm 1: Chatbot y tế




# Bài 5: Bài Tập Sáng Tạo - Khởi Tạo Workflow SRS Hoàn Chỉnh

## 1. Phân tích & Thiết kế luồng xử lý

### Mục tiêu

Xây dựng chức năng **AI Product Recommendation** giúp hệ thống tự động gợi ý các sản phẩm liên quan khi người dùng đang xem một sản phẩm, dựa trên lịch sử mua hàng và thông tin sản phẩm hiện tại.

### Input

- User ID
- Product ID đang xem
- Lịch sử mua hàng của người dùng
- Danh sách sản phẩm trong hệ thống
- Thông tin danh mục (Category), thương hiệu (Brand), từ khóa (Tags)

### Output

- Danh sách sản phẩm được đề xuất
- Mức độ liên quan (Recommendation Score)
- Lý do gợi ý (ví dụ: cùng danh mục, đã từng mua sản phẩm tương tự, nhiều người mua cùng...)

### Luồng xử lý (ASCII)

```text
Người dùng xem sản phẩm
           │
           ▼
Lấy Product ID và User ID
           │
           ▼
Kiểm tra lịch sử mua hàng
           │
           ▼
Phân tích sản phẩm đã mua
           │
           ▼
Tìm sản phẩm liên quan
(Category, Brand, Tags)
           │
           ▼
Loại bỏ sản phẩm đã mua
           │
           ▼
Tính điểm Recommendation Score
           │
           ▼
Sắp xếp theo độ phù hợp
           │
           ▼
Trả về danh sách sản phẩm gợi ý
```

---

# 2. Workflow Prompt

```text
# Vai trò (Role)

Act as a Senior System Analyst có kinh nghiệm xây dựng tài liệu SRS theo chuẩn IEEE cho hệ thống thương mại điện tử sử dụng AI Recommendation.

# Bối cảnh (Context)

Dự án Shop AI cần phát triển module AI Product Recommendation.

Yêu cầu ban đầu của khách hàng:

"Tôi muốn hệ thống tự động gợi ý các sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."

# Nhiệm vụ (Tasks)

Hãy chuyển yêu cầu trên thành tài liệu SRS hoàn chỉnh.

Bao gồm:

1. Use Case Description
- Use Case Name
- Actors
- Preconditions
- Trigger
- Main Flow
- Alternative Flow
- Postconditions

2. Functional Requirements
- Danh sách các chức năng chính
- Input
- Output
- Business Rules
- Exception Handling

3. Data Dictionary sơ bộ
Mô tả các thực thể:
- User
- Product
- Purchase History
- Recommendation
- Category

Bao gồm:
- Tên trường
- Kiểu dữ liệu
- Ý nghĩa

# Ràng buộc (Constraints)

- Chỉ đề xuất tối đa 10 sản phẩm.
- Không đề xuất sản phẩm đã mua nếu không còn hàng.
- Chỉ đề xuất sản phẩm còn tồn kho.
- Sắp xếp theo Recommendation Score giảm dần.
- Nếu người dùng chưa có lịch sử mua hàng thì gợi ý sản phẩm nổi bật hoặc bán chạy.
- Mọi Business Rule phải đánh số rõ ràng.

# Định dạng (Format)

Xuất kết quả bằng Markdown với các tiêu đề:

- System Overview
- Use Case Description
- Functional Requirements
- Business Rules
- Exception Handling
- Data Dictionary
```

---

# 3. Minh chứng chạy thực tế

Dưới đây là tài liệu Software Requirement Specification (SRS) hoàn chỉnh cho module AI Product Recommendation của hệ thống Shop AI, được thiết kế theo chuẩn IEEE và bám sát các ràng buộc của bạn.

```markdown
# Tài Liệu Đặc Tả Yêu Cầu Hệ Thống (SRS)

**Dự án:** Shop AI
**Module:** AI Product Recommendation (Gợi ý sản phẩm thông minh)
**Vai trò phân tích:** Senior System Analyst

---

## 1. System Overview (Tổng Quan Hệ Thống)

Module **AI Product Recommendation** là một thành phần cốt lõi của hệ thống Shop AI, nhằm tăng cường trải nghiệm mua sắm cá nhân hóa (Personalization) và thúc đẩy doanh thu (Cross-selling/Up-selling). Hệ thống sẽ tự động phân tích sản phẩm người dùng đang xem kết hợp với hành vi mua sắm trong quá khứ để đề xuất danh sách các sản phẩm liên quan nhất, phù hợp với sở thích của từng cá nhân.

---

## 2. Use Case Description (Đặc Tả Use Case)

| Thuộc tính                         | Chi tiết                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| :--------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Use Case Name**                  | Gợi ý sản phẩm liên quan (AI Product Recommendation).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **Actors**                         | Người dùng (Khách hàng đã đăng nhập hoặc Khách viếng thăm - Guest).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **Preconditions**                  | - Hệ thống tính toán Recommendation Engine đang hoạt động.<br>- Người dùng điều hướng thành công vào trang Chi tiết sản phẩm (Product Detail Page).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **Trigger**                        | Sự kiện tải trang (Page Load) hoặc người dùng cuộn chuột đến khu vực "Sản phẩm liên quan".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| **Main Flow (Luồng chính)**        | 1. Người dùng truy cập trang chi tiết sản phẩm A.<br>2. Client gửi API Request chứa `productId` (của sản phẩm A) và `userId` (nếu có).<br>3. Hệ thống truy xuất thông tin sản phẩm A (Category, Tags, Price).<br>4. Hệ thống truy xuất lịch sử mua hàng của `userId` từ cơ sở dữ liệu.<br>5. Recommendation Engine tính toán điểm phù hợp (Recommendation Score) cho các ứng viên dựa trên độ tương đồng và sở thích.<br>6. Hệ thống thực thi các bộ lọc (chỉ lấy hàng còn tồn kho, loại bỏ trùng lặp).<br>7. Hệ thống sắp xếp kết quả theo Score giảm dần và cắt lấy tối đa 10 sản phẩm.<br>8. Trả về danh sách cho Client và hiển thị trên giao diện. |
| **Alternative Flow (Luồng nhánh)** | - **Nếu `userId` trống (Guest) hoặc User chưa từng mua hàng:** Tại bước 4, hệ thống ghi nhận không có lịch sử mua. Tại bước 5, hệ thống chuyển sang thuật toán Fallback: Gợi ý các sản phẩm nổi bật, sản phẩm bán chạy nhất (Best Sellers) cùng Category với sản phẩm A, và tiếp tục từ bước 6.                                                                                                                                                                                                                                                                                                                                                         |
| **Postconditions**                 | Hệ thống ghi nhận log hành vi "Người dùng đã xem danh sách gợi ý" để dùng làm dữ liệu huấn luyện (training data) cho AI sau này.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |

---

## 3. Functional Requirements (Yêu Cầu Chức Năng)

| Mã Yêu Cầu    | Tên Chức Năng       | Mô Tả Chi Tiết                                                                                                             | Input                                       | Output                            |
| :------------ | :------------------ | :------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------ | :-------------------------------- |
| **FR-REC-01** | Khởi tạo Gợi ý      | API Endpoint cung cấp danh sách gợi ý sản phẩm cho một ngữ cảnh cụ thể.                                                    | `productId` (bắt buộc), `userId` (tùy chọn) | `List<Product>` (tối đa 10 items) |
| **FR-REC-02** | Phân tích Lịch sử   | Truy xuất và trích xuất các nhãn (tags), danh mục (categories) từ các đơn hàng hoàn tất của người dùng để làm trọng số AI. | `userId`                                    | `List<UserPreference>`            |
| **FR-REC-03** | Chấm điểm (Scoring) | Áp dụng thuật toán (Collaborative/Content-based) để gán Recommendation Score cho từng sản phẩm ứng viên.                   | `productId`, `UserPreference`               | Danh sách ứng viên kèm `score`    |
| **FR-REC-04** | Bộ lọc Tồn kho      | Loại bỏ hoàn toàn các sản phẩm không đáp ứng điều kiện kinh doanh (hết hàng, đã xóa) khỏi tập kết quả.                     | Danh sách ứng viên                          | Danh sách đã lọc                  |
| **FR-REC-05** | Cơ chế Fallback     | Kích hoạt thuật toán Gợi ý Sản phẩm Thịnh hành khi dữ liệu cá nhân hóa không đủ.                                           | `productId`                                 | Danh sách Best Sellers            |

---

## 4. Business Rules (Quy Tắc Nghiệp Vụ)

Toàn bộ quá trình lọc và trả về kết quả phải tuân thủ nghiêm ngặt các quy tắc sau:

- **BR-REC-01 (Giới hạn số lượng):** Hệ thống chỉ được phép đề xuất **tối đa 10 sản phẩm** trong một lần request.
- **BR-REC-02 (Ràng buộc Tồn kho):** Chỉ đề xuất các sản phẩm có số lượng tồn kho lớn hơn 0 (`stock > 0`) và đang ở trạng thái kinh doanh (`ACTIVE`).
- **BR-REC-03 (Sắp xếp kết quả):** Danh sách kết quả cuối cùng phải được sắp xếp theo `Recommendation Score` theo thứ tự **giảm dần** (Từ cao nhất xuống thấp nhất).
- **BR-REC-04 (Fallback Rule):** Nếu người dùng chưa có lịch sử mua hàng, hoặc người dùng chưa đăng nhập, hệ thống tự động chuyển sang gợi ý các sản phẩm "Nổi bật" hoặc "Bán chạy" (Best-selling) thuộc cùng danh mục với sản phẩm đang xem.
- **BR-REC-05 (Chống trùng lặp đang xem):** Tuyệt đối không đề xuất chính sản phẩm mà người dùng đang xem (Loại bỏ `productId` của request khỏi danh sách gợi ý).
- **BR-REC-06 (Lọc hàng đã mua):** Không đề xuất lại những sản phẩm người dùng đã mua nếu sản phẩm đó hiện tại không còn hàng (Quy tắc này được đáp ứng mặc định và bao hàm bởi **BR-REC-02**).

---

## 5. Exception Handling (Xử Lý Ngoại Lệ)

| Tình huống ngoại lệ                                         | Xử lý của hệ thống (System Action)                                                                                                                           |
| :---------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Thiếu tham số `productId` trong API.                        | Bắn lỗi `400 Bad Request` với thông báo: _"Yêu cầu không hợp lệ. Mã sản phẩm là bắt buộc."_                                                                  |
| `productId` truyền lên không tồn tại trong hệ thống.        | Hệ thống không thể lấy ngữ cảnh. Bỏ qua thuật toán cá nhân hóa, trả về danh sách sản phẩm Best-sellers của toàn hệ thống (HTTP 200 OK).                      |
| Recommendation Engine (AI Service) bị quá tải hoặc timeout. | Kích hoạt Circuit Breaker, bỏ qua việc gọi AI và trả về kết quả từ Cache hoặc trả về danh sách rỗng (`[]`) tĩnh để không làm gián đoạn việc tải trang chính. |

---

## 6. Data Dictionary (Từ Điển Dữ Liệu Sơ Bộ)

Cấu trúc lưu trữ và giao tiếp của các thực thể liên quan đến module gợi ý:

### 6.1. Thực thể: User (Người dùng)

| Tên trường (Field) | Kiểu dữ liệu (Type) | Ý nghĩa (Description)                            |
| :----------------- | :------------------ | :----------------------------------------------- |
| `userId`           | Long (PK)           | Mã định danh duy nhất của người dùng.            |
| `email`            | String              | Email người dùng (dùng để xác thực).             |
| `status`           | Enum                | Trạng thái tài khoản (ACTIVE, INACTIVE, LOCKED). |

### 6.2. Thực thể: Category (Danh mục)

| Tên trường (Field) | Kiểu dữ liệu (Type) | Ý nghĩa (Description)               |
| :----------------- | :------------------ | :---------------------------------- |
| `categoryId`       | Long (PK)           | Mã định danh duy nhất của danh mục. |
| `categoryName`     | String              | Tên danh mục (vd: Áo thun nam).     |

### 6.3. Thực thể: Product (Sản phẩm)

| Tên trường (Field) | Kiểu dữ liệu (Type) | Ý nghĩa (Description)                           |
| :----------------- | :------------------ | :---------------------------------------------- |
| `productId`        | Long (PK)           | Mã định danh duy nhất của sản phẩm.             |
| `categoryId`       | Long (FK)           | Mã danh mục sản phẩm trực thuộc.                |
| `productName`      | String              | Tên hiển thị của sản phẩm.                      |
| `price`            | BigDecimal          | Giá bán của sản phẩm.                           |
| `stock`            | Integer             | Số lượng tồn kho hiện tại (Dùng cho BR-REC-02). |
| `status`           | Enum                | Trạng thái bán (ACTIVE, HIDDEN, OUT_OF_STOCK).  |

### 6.4. Thực thể: Purchase History (Lịch sử Mua hàng)

| Tên trường (Field) | Kiểu dữ liệu (Type) | Ý nghĩa (Description)                           |
| :----------------- | :------------------ | :---------------------------------------------- |
| `historyId`        | Long (PK)           | Mã định danh bản ghi lịch sử.                   |
| `userId`           | Long (FK)           | Định danh người mua.                            |
| `productId`        | Long (FK)           | Định danh sản phẩm đã mua (Dùng cho BR-REC-06). |
| `purchaseDate`     | Timestamp           | Thời điểm hoàn tất đơn hàng.                    |

### 6.5. Thực thể: Recommendation (Dữ liệu trả về API)

_(Đây là Data Transfer Object - DTO hiển thị kết quả ra Client)_
| Tên trường (Field) | Kiểu dữ liệu (Type) | Ý nghĩa (Description) |
| :--- | :--- | :--- |
| `productId` | Long | ID sản phẩm được đề xuất. |
| `productName` | String | Tên sản phẩm. |
| `price` | BigDecimal | Giá sản phẩm. |
| `thumbnail` | String | Đường dẫn hình ảnh thu nhỏ. |
| `matchScore` | Float | Điểm số phù hợp từ AI (Ví dụ: 0.95 = 95%). (Dùng cho BR-REC-03) |
```

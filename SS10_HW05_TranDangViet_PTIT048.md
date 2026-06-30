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

# 3. Minh chứng chạy thực tế (Text Log)

```text
User:

Tôi muốn hệ thống tự động gợi ý các sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ.

------------------------------------------------

Assistant:

Đã phân tích yêu cầu.

Module: AI Product Recommendation

Use Case:
Người dùng xem chi tiết sản phẩm và hệ thống tự động gợi ý các sản phẩm phù hợp dựa trên lịch sử mua hàng và thông tin sản phẩm hiện tại.

Actors:
- Customer

Preconditions:
- Người dùng đăng nhập (không bắt buộc).
- Sản phẩm đang xem tồn tại.
- Hệ thống có dữ liệu sản phẩm.

Main Flow:
1. Người dùng mở trang chi tiết sản phẩm.
2. Hệ thống lấy Product ID.
3. Nếu có User ID thì lấy lịch sử mua hàng.
4. Phân tích danh mục, thương hiệu và lịch sử mua.
5. Tính Recommendation Score.
6. Loại bỏ sản phẩm không còn hàng.
7. Chọn tối đa 10 sản phẩm phù hợp.
8. Trả về danh sách gợi ý.

Functional Requirements:
- FR-01 Lấy thông tin sản phẩm hiện tại.
- FR-02 Phân tích lịch sử mua hàng.
- FR-03 Tính Recommendation Score.
- FR-04 Lọc sản phẩm hết hàng.
- FR-05 Sắp xếp theo mức độ phù hợp.
- FR-06 Trả về danh sách gợi ý.

Business Rules:
- BR-01 Chỉ đề xuất sản phẩm còn hàng.
- BR-02 Không đề xuất sản phẩm đã mua nếu đã hết hàng.
- BR-03 Tối đa 10 sản phẩm.
- BR-04 Nếu chưa có lịch sử mua hàng thì đề xuất sản phẩm bán chạy.
- BR-05 Sắp xếp theo Recommendation Score giảm dần.

Data Dictionary:
User(UserID, Name)
Product(ProductID, Name, CategoryID, Stock)
PurchaseHistory(UserID, ProductID, PurchaseDate)
Recommendation(ProductID, Score)
Category(CategoryID, CategoryName)
```

---

## 4. Kết quả

Workflow Prompt trên có khả năng chuyển yêu cầu nghiệp vụ thô của khách hàng thành tài liệu SRS tương đối đầy đủ, bao gồm **Use Case Description**, **Functional Requirements**, **Business Rules** và **Data Dictionary**. Đồng thời, prompt cũng quy định rõ các ràng buộc và định dạng đầu ra, giúp AI tạo tài liệu nhất quán và có thể sử dụng làm cơ sở cho quá trình thiết kế, phát triển và kiểm thử hệ thống.

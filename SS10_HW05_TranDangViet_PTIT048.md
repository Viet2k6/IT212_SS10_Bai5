## Bài 5: Khởi Tạo Workflow SRS Hoàn Chỉnh (Bài Tập Sáng Tạo)

**1. Phân tích & Thiết kế luồng (Input/Output/Sơ đồ)**

- **Input:**
  - `userId`: Định danh người dùng hiện tại (để lấy lịch sử mua hàng, null nếu là Guest).
  - `currentProductId`: Định danh sản phẩm người dùng đang xem.
- **Output:**
  - Danh sách `List<Product>` chứa các sản phẩm liên quan nhất (kèm điểm số phù hợp - matchScore).
- **Luồng xử lý (Business Flow):**
  1.  Client gọi API yêu cầu lấy danh sách gợi ý.
  2.  Hệ thống kiểm tra `userId`. Nếu có, lấy lịch sử mua hàng từ Order Database. Nếu không, chuyển sang chế độ Guest (chỉ dùng thông tin sản phẩm).
  3.  Lấy metadata (category, tags, price range) của `currentProductId`.
  4.  Chạy thuật toán Recommendation Engine để tìm danh sách ứng viên và chấm điểm (Scoring).
  5.  Lọc bỏ sản phẩm trùng lặp (`currentProductId`) và các sản phẩm đã mua ra khỏi danh sách.
  6.  Trả về Top N sản phẩm có điểm cao nhất cho Client.

- **Sơ đồ luồng (ASCII Diagram):**

```text
[Client Web/App]
       |
       | (1) GET /api/v1/recommendations?productId=123&userId=456
       v
  [API Gateway] ---> [Recommendation Service]
                               |
                               |-- (2) Get Product Metadata (từ Product DB)
                               |-- (3) Get Purchase History (từ Order DB)
                               |
                               |-- (4) Tính toán Scoring (Recommendation Engine)
                               |       * Trọng số lịch sử mua hàng
                               |       * Trọng số nội dung sản phẩm
                               |
                               |-- (5) Bộ lọc (Filter)
                               |       * Bỏ productId=123
                               |       * Bỏ hàng đã mua
                               v
                         [Top N Products]
                               |
[Client Web/App] <-------------+ (6) Trả về JSON Response hiển thị UI
```

**2. Mega-Prompt thiết kế (Workflow Prompt)**

```text
Act as a Senior Business Analyst. Khách hàng của hệ thống Shop AI vừa đưa ra một yêu cầu thô sơ bằng lời nói: "Tôi muốn hệ thống tự động gợi ý các sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."

Dựa trên yêu cầu thô này, hãy giúp tôi phân tích và xây dựng một tài liệu Đặc tả Hệ thống (SRS) hoàn chỉnh cho module "Gợi ý sản phẩm thông minh" (AI Product Recommendation).

Tài liệu cần trình bày dưới định dạng Markdown chuyên nghiệp và bắt buộc phải có 3 phần sau:
1. **Use Case Description:** Mô tả tóm tắt, Actor, Pre-conditions, Main Flow, và Alternate Flow (Đặc tả rõ cách xử lý luồng ngoại lệ khi người dùng là Guest chưa đăng nhập thì gợi ý ra sao).
2. **Functional Requirements (FRs):** Bảng danh sách các yêu cầu chức năng (FR-ID, Tên chức năng, Mô tả chi tiết) để team Dev biết phải code những API hoặc logic tính toán nào (bao gồm logic tính điểm, lọc trùng lặp).
3. **Data Dictionary sơ bộ:** Mô tả cấu trúc JSON mẫu của API Request và API Response.
```

**3. Minh chứng chạy thực tế (Text log trả về từ AI)**

````markdown
# Tài Liệu Đặc Tả (SRS) - Module Gợi Ý Sản Phẩm Thông Minh (AI Recommendation)

## 1. Use Case Description

- **Tên Use Case:** Gợi ý sản phẩm liên quan cho người dùng.
- **Mô tả tóm tắt:** Hệ thống hiển thị danh sách các sản phẩm gợi ý (Top 5-10) khi người dùng đang xem chi tiết một sản phẩm, dữ liệu được cá nhân hóa dựa trên lịch sử mua hàng của người đó.
- **Actor (Tác nhân):** Người dùng (User/Customer).
- **Pre-conditions (Điều kiện tiên quyết):** Người dùng đang ở trang Chi tiết sản phẩm. Module Recommendation Engine đã được bật.
- **Main Flow (Luồng chính - User đã đăng nhập):**
  1.  Người dùng truy cập vào trang Chi tiết sản phẩm A.
  2.  Hệ thống gửi request lấy danh sách gợi ý kèm theo `productId` (của A) và `userId`.
  3.  Hệ thống phân tích danh mục, tags của sản phẩm A.
  4.  Hệ thống truy xuất lịch sử đơn hàng của `userId`.
  5.  Thuật toán gợi ý chấm điểm (scoring) các sản phẩm trong DB dựa trên sự tương đồng với sản phẩm A và sở thích từ lịch sử mua hàng.
  6.  Hệ thống loại bỏ sản phẩm A và những sản phẩm user đã mua thành công khỏi danh sách kết quả.
  7.  Hệ thống sắp xếp và trả về danh sách Top N sản phẩm có điểm số cao nhất.
  8.  Giao diện hiển thị danh sách ở section "Có thể bạn cũng thích".
- **Alternate Flow (Luồng nhánh - User là Guest, chưa đăng nhập):**
  1.  Ở bước 2, hệ thống nhận diện `userId` là null.
  2.  Hệ thống bỏ qua bước truy xuất lịch sử đơn hàng.
  3.  Thuật toán chỉ chấm điểm dựa trên Content-based (Các sản phẩm có cùng danh mục, cùng mức giá với sản phẩm A) kết hợp với thuật toán Best Seller (Sản phẩm bán chạy nhất cùng danh mục).
  4.  Tiếp tục thực hiện bước 6, 7, 8 của luồng chính.

## 2. Functional Requirements (FRs)

| FR-ID         | Tên chức năng                     | Mô tả chi tiết (Description)                                                                                                                                                |
| :------------ | :-------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **FR-REC-01** | API Gợi ý sản phẩm                | Cung cấp endpoint GET `/api/v1/recommendations`. Tham số bắt buộc: `productId`. Tham số tùy chọn: `userId`, `limit`.                                                        |
| **FR-REC-02** | Logic tính điểm Content-Based     | Hệ thống lọc ra các sản phẩm có cùng `categoryId` hoặc trùng khớp trên 50% `tags` với sản phẩm đang xem để làm tập ứng viên.                                                |
| **FR-REC-03** | Logic phân tích lịch sử mua       | Nếu request có truyền `userId`, hệ thống lấy danh sách các `categoryId` user đã mua nhiều nhất trong 90 ngày qua để cộng thêm điểm ưu tiên (+30% match score) cho ứng viên. |
| **FR-REC-04** | Logic bộ lọc loại trừ (Exclusion) | Bắt buộc loại bỏ `productId` hiện tại và các sản phẩm user đã mua thành công ra khỏi tập kết quả.                                                                           |
| **FR-REC-05** | Giới hạn kết quả trả về           | Hệ thống phân trang hoặc trả về tối đa số lượng sản phẩm theo tham số `limit` (Mặc định `limit=5`, Max=`20`), sắp xếp theo điểm số (score) giảm dần.                        |

## 3. Data Dictionary sơ bộ (API Contract)

**API Request:**
`GET /api/v1/recommendations?productId=1055&userId=8902&limit=4`

**API Response (JSON):**

```json
{
  "status": "success",
  "data": {
    "recommendationId": "rec_982bca1",
    "strategy": "hybrid_history_content",
    "items": [
      {
        "productId": 2041,
        "productName": "Áo thun nam thể thao",
        "price": 150000,
        "thumbnailUrl": "https://cdn.shop.ai/images/2041.jpg",
        "matchScore": 0.95
      },
      {
        "productId": 1092,
        "productName": "Quần đùi chạy bộ",
        "price": 120000,
        "thumbnailUrl": "https://cdn.shop.ai/images/1092.jpg",
        "matchScore": 0.88
      }
    ]
  }
}
```
````

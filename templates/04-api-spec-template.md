<!--
TEMPLATE: API Specification (Đặc tả API)
Hướng dẫn: copy file này, xóa chú thích <!-- -->, điền nội dung.

Với dự án lớn, cân nhắc dùng song song OpenAPI/Swagger; tài liệu này là bản đọc-hiểu cho team.
-->

# API Spec — [Tên tính năng / dự án]

## Thông tin tài liệu (Document Metadata)

| Trường                           | Giá trị    |
| -------------------------------- | ---------- |
| Tên dự án (Project)              |            |
| Mã tài liệu (Doc ID)             | API-XXX    |
| Loại (Type)                      | API Spec   |
| Phiên bản (Version)              | 0.1.0      |
| Trạng thái (Status)              | Draft      |
| Người viết (Author)              |            |
| Người duyệt (Approver)           |            |
| Ngày tạo (Created)               | YYYY-MM-DD |
| Cập nhật lần cuối (Last updated) | YYYY-MM-DD |

## 1. Tổng quan (Overview)

- **Base URL:** `https://api.example.com`
- **Phiên bản API (API version):** `v1` (vd `/v1/...`)
- **Định dạng (Format):** JSON (UTF-8)

## 2. Xác thực & Phân quyền (Authentication & Authorization)

- **Cơ chế (Mechanism):** vd Bearer Token (JWT)
- **Header:** `Authorization: Bearer <token>`
- **Phạm vi quyền (Scopes / Roles):**

## 3. Quy ước chung (Common Conventions)

**Mã trạng thái HTTP (HTTP Status Codes):**

| Mã  | Ý nghĩa                           |
| --- | --------------------------------- |
| 200 | Thành công (OK)                   |
| 201 | Đã tạo (Created)                  |
| 400 | Sai dữ liệu đầu vào (Bad Request) |
| 401 | Chưa xác thực (Unauthorized)      |
| 403 | Không đủ quyền (Forbidden)        |
| 404 | Không tìm thấy (Not Found)        |
| 500 | Lỗi máy chủ (Server Error)        |

**Định dạng lỗi chuẩn (Error format):**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Mô tả lỗi dễ hiểu",
    "details": []
  }
}
```

**Phân trang (Pagination):** `?page=1&limit=20` → response kèm `total`, `page`, `limit`.
**Sắp xếp & lọc (Sorting & Filtering):** `?sort=-created_at&status=open`.
**Giới hạn tần suất (Rate limit):**

## 4. Danh sách Endpoint (Endpoints)

### [GET] /v1/resource

- **Mô tả:**
- **Quyền cần (Required permission):**
- **Tham số (Parameters):**

| Tên | Vị trí (in) | Kiểu | Bắt buộc | Mô tả |
| --- | ----------- | ---- | -------- | ----- |
|     | query/path  |      | có/không |       |

- **Request body:**

```json
{}
```

- **Response (200):**

```json
{}
```

- **Lỗi có thể gặp (Possible errors):** 400, 401, 404

<!-- Lặp lại block trên cho mỗi endpoint. -->

## 5. Mô hình dữ liệu dùng chung (Shared Schemas)

### Resource

| Trường (Field) | Kiểu (Type)   | Mô tả |
| -------------- | ------------- | ----- |
| id             | string (uuid) |       |

## 6. Webhooks (nếu có)

| Sự kiện (Event) | Payload | Mô tả |
| --------------- | ------- | ----- |
|                 |         |       |

---

## Lịch sử thay đổi (Change History / API Changelog)

| Phiên bản | Ngày       | Người sửa | Mô tả thay đổi             |
| --------- | ---------- | --------- | -------------------------- |
| 0.1.0     | YYYY-MM-DD |           | Khởi tạo bản nháp đầu tiên |

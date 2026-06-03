# API Spec — TeamTask

## Thông tin tài liệu (Document Metadata)

| Trường                           | Giá trị              |
| -------------------------------- | -------------------- |
| Tên dự án (Project)              | TeamTask             |
| Mã tài liệu (Doc ID)             | API-001              |
| Loại (Type)                      | API Spec             |
| Phiên bản (Version)              | 1.0.0                |
| Trạng thái (Status)              | Approved             |
| Người viết (Author)              | Lê Văn C (Tech Lead) |
| Người duyệt (Approver)           | Nguyễn Văn A (PM)    |
| Ngày tạo (Created)               | 2026-05-28           |
| Cập nhật lần cuối (Last updated) | 2026-06-02           |

## 1. Tổng quan (Overview)

- **Base URL:** `https://api.teamtask.example.com`
- **Phiên bản API (API version):** `v1` (tiền tố `/v1`)
- **Định dạng (Format):** JSON (UTF-8); thời gian theo ISO 8601 (UTC).

## 2. Xác thực & Phân quyền (Authentication & Authorization)

- **Cơ chế (Mechanism):** Bearer Token (JWT).
- **Header:** `Authorization: Bearer <access_token>`
- **Phạm vi quyền (Roles):** `manager` (toàn quyền trong dự án), `member` (tạo/cập nhật task được gán). Vai trò xác định qua `project_member.role`.

## 3. Quy ước chung (Common Conventions)

**Mã trạng thái HTTP (HTTP Status Codes):**

| Mã  | Ý nghĩa                                    |
| --- | ------------------------------------------ |
| 200 | Thành công (OK)                            |
| 201 | Đã tạo (Created)                           |
| 204 | Thành công, không có nội dung (No Content) |
| 400 | Sai dữ liệu đầu vào (Bad Request)          |
| 401 | Chưa xác thực (Unauthorized)               |
| 403 | Không đủ quyền (Forbidden)                 |
| 404 | Không tìm thấy (Not Found)                 |
| 422 | Dữ liệu không hợp lệ (Validation Error)    |
| 500 | Lỗi máy chủ (Server Error)                 |

**Định dạng lỗi chuẩn (Error format):**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Tiêu đề task không được để trống",
    "details": [{ "field": "title", "issue": "required" }]
  }
}
```

**Phân trang (Pagination):** `?page=1&limit=20`. Response bọc trong:

```json
{ "data": [], "meta": { "page": 1, "limit": 20, "total": 0 } }
```

**Sắp xếp & lọc (Sorting & Filtering):** vd `?status=todo&assignee_id=<uuid>&sort=-created_at`.
**Giới hạn tần suất (Rate limit):** 100 request/phút/người dùng (vượt → 429).

## 4. Danh sách Endpoint (Endpoints)

### [POST] /v1/auth/login

- **Mô tả:** đăng nhập, trả về token (FR-01).
- **Quyền cần:** không (công khai).
- **Request body:**

```json
{ "email": "an@example.com", "password": "matkhau123" }
```

- **Response (200):**

```json
{
  "access_token": "eyJhbGciOi...",
  "refresh_token": "def502...",
  "user": { "id": "u-1", "email": "an@example.com", "full_name": "Nguyễn Văn A" }
}
```

- **Lỗi có thể gặp:** 401 (sai thông tin), 422 (thiếu trường).

---

### [GET] /v1/projects

- **Mô tả:** danh sách dự án người dùng tham gia (FR-02).
- **Quyền cần:** đã đăng nhập.
- **Response (200):**

```json
{
  "data": [
    {
      "id": "p-1",
      "name": "Ra mắt website",
      "role": "manager",
      "created_at": "2026-06-01T09:00:00Z"
    }
  ],
  "meta": { "page": 1, "limit": 20, "total": 1 }
}
```

---

### [POST] /v1/projects

- **Mô tả:** tạo dự án mới (FR-02). Người tạo trở thành `manager`.
- **Quyền cần:** đã đăng nhập.
- **Request body:**

```json
{ "name": "Ra mắt website" }
```

- **Response (201):**

```json
{ "id": "p-1", "name": "Ra mắt website", "role": "manager", "created_at": "2026-06-01T09:00:00Z" }
```

- **Lỗi có thể gặp:** 401, 422.

---

### [GET] /v1/projects/{projectId}/tasks

- **Mô tả:** danh sách task của dự án, hỗ trợ lọc (FR-07).
- **Quyền cần:** thành viên dự án.
- **Tham số (Parameters):**

| Tên         | Vị trí (in) | Kiểu                             | Bắt buộc | Mô tả                    |
| ----------- | ----------- | -------------------------------- | -------- | ------------------------ |
| projectId   | path        | string (uuid)                    | có       | ID dự án                 |
| status      | query       | enum `todo`/`in_progress`/`done` | không    | Lọc theo trạng thái      |
| assignee_id | query       | string (uuid)                    | không    | Lọc theo người phụ trách |

- **Response (200):**

```json
{
  "data": [
    {
      "id": "t-1",
      "project_id": "p-1",
      "title": "Thiết kế trang chủ",
      "status": "todo",
      "assignee_id": "u-2",
      "due_date": "2026-06-10",
      "created_at": "2026-06-02T10:00:00Z"
    }
  ],
  "meta": { "page": 1, "limit": 20, "total": 1 }
}
```

---

### [POST] /v1/projects/{projectId}/tasks

- **Mô tả:** tạo task và (tùy chọn) gán người (FR-03, FR-04). Kích hoạt email thông báo (FR-08).
- **Quyền cần:** thành viên dự án.
- **Request body:**

```json
{
  "title": "Thiết kế trang chủ",
  "description": "Bản hi-fi",
  "assignee_id": "u-2",
  "due_date": "2026-06-10"
}
```

- **Response (201):**

```json
{
  "id": "t-1",
  "project_id": "p-1",
  "title": "Thiết kế trang chủ",
  "description": "Bản hi-fi",
  "status": "todo",
  "assignee_id": "u-2",
  "due_date": "2026-06-10",
  "created_at": "2026-06-02T10:00:00Z"
}
```

- **Lỗi có thể gặp:** 401, 403 (không thuộc dự án), 422 (thiếu `title`).

---

### [PATCH] /v1/tasks/{taskId}

- **Mô tả:** cập nhật task; dùng để đổi trạng thái khi kéo-thả Kanban (FR-05).
- **Quyền cần:** thành viên dự án.
- **Request body (gửi trường cần đổi):**

```json
{ "status": "in_progress" }
```

- **Response (200):** đối tượng task sau cập nhật.
- **Lỗi có thể gặp:** 401, 403, 404, 422 (status không hợp lệ).

---

### [GET] /v1/tasks/{taskId}/comments

- **Mô tả:** danh sách bình luận của task (FR-06).
- **Quyền cần:** thành viên dự án.
- **Response (200):**

```json
{
  "data": [
    {
      "id": "c-1",
      "task_id": "t-1",
      "author": { "id": "u-2", "full_name": "Trần B" },
      "content": "Đã xong bản nháp",
      "created_at": "2026-06-02T11:00:00Z"
    }
  ],
  "meta": { "page": 1, "limit": 20, "total": 1 }
}
```

---

### [POST] /v1/tasks/{taskId}/comments

- **Mô tả:** thêm bình luận (FR-06).
- **Quyền cần:** thành viên dự án.
- **Request body:**

```json
{ "content": "Đã xong bản nháp" }
```

- **Response (201):** đối tượng comment vừa tạo.
- **Lỗi có thể gặp:** 401, 403, 422 (nội dung rỗng).

## 5. Mô hình dữ liệu dùng chung (Shared Schemas)

### Task

| Trường (Field) | Kiểu (Type)           | Mô tả                           |
| -------------- | --------------------- | ------------------------------- |
| id             | string (uuid)         | Khóa chính                      |
| project_id     | string (uuid)         | Thuộc dự án                     |
| title          | string                | Tiêu đề (bắt buộc)              |
| description    | string \| null        | Mô tả                           |
| status         | enum                  | `todo` / `in_progress` / `done` |
| assignee_id    | string (uuid) \| null | Người phụ trách                 |
| due_date       | string (date) \| null | Hạn hoàn thành                  |
| created_at     | string (datetime)     | Thời điểm tạo                   |

### User

| Trường (Field) | Kiểu (Type)   | Mô tả           |
| -------------- | ------------- | --------------- |
| id             | string (uuid) | Khóa chính      |
| email          | string        | Email đăng nhập |
| full_name      | string        | Họ tên hiển thị |

## 6. Webhooks

Không có ở phiên bản v1.

---

## Lịch sử thay đổi (Change History / API Changelog)

| Phiên bản | Ngày       | Người sửa | Mô tả thay đổi                                                     |
| --------- | ---------- | --------- | ------------------------------------------------------------------ |
| 0.1.0     | 2026-05-28 | Lê Văn C  | Khởi tạo các endpoint nháp                                         |
| 1.0.0     | 2026-06-02 | Lê Văn C  | Hoàn thiện endpoint Auth/Project/Task/Comment, khớp data model TDD |

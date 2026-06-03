# Bộ chuẩn Tài liệu Đặc tả (Specification Standard)

Bộ tài liệu chuẩn hóa cách viết **spec** cho mọi dự án web/app của công ty, giúp các team làm việc nhất quán và bàn giao dễ dàng.

## Quy ước ngôn ngữ (Language Convention)

- Nội dung diễn giải viết bằng **tiếng Việt**.
- **Thuật ngữ chuyên môn (technical terms)** luôn để **song song tiếng Anh** trong ngoặc ở lần xuất hiện đầu tiên, ví dụ: "Tiêu chí nghiệm thu (Acceptance Criteria)".

## Bốn loại tài liệu (Document Types)

| #   | Tài liệu                                | Mục đích                                                 | Người phụ trách chính           |
| --- | --------------------------------------- | -------------------------------------------------------- | ------------------------------- |
| 1   | **PRD** (Product Requirements Document) | Cái gì & tại sao — mục tiêu, người dùng, yêu cầu         | Product Manager / Quản lý dự án |
| 2   | **Technical Design** (TDD)              | Làm thế nào — kiến trúc, công nghệ, dữ liệu              | Tech Lead / Kiến trúc sư        |
| 3   | **UI/UX Spec**                          | Giao diện & trải nghiệm — màn hình, luồng, design system | Designer                        |
| 4   | **API Spec**                            | Hợp đồng giao tiếp (contract) giữa các hệ thống          | Backend Developer               |

Thứ tự khuyến nghị: **PRD → UI/UX → Technical Design → API**. PRD chốt "làm gì" trước, các tài liệu sau bám theo.

## Cấu trúc thư mục (Folder Structure)

```
docs/
├── README.md              # File này
├── templates/             # Template TRỐNG — copy ra khi mở dự án mới
│   ├── 01-prd-template.md
│   ├── 02-technical-design-template.md
│   ├── 03-uiux-spec-template.md
│   └── 04-api-spec-template.md
└── projects/
    └── teamtask-example/  # Dự án VÍ DỤ điền đầy đủ để tham khảo cách viết
        ├── 01-prd.md
        ├── 02-technical-design.md
        ├── 03-uiux-spec.md
        └── 04-api-spec.md
```

## Cách dùng cho dự án mới (How to Start)

1. Copy thư mục `projects/teamtask-example/` thành `projects/<ten-du-an>/` (hoặc copy 4 file từ `templates/`).
2. Điền **bảng metadata** ở đầu mỗi file.
3. Viết PRD trước, rồi tới các tài liệu còn lại.
4. Mỗi lần chỉnh sửa lớn: tăng **phiên bản (version)** và ghi vào **Lịch sử thay đổi (Change History)** cuối file.

## Quy ước chung (Common Conventions)

- **Đặt tên thư mục dự án:** `kebab-case`, không dấu (vd: `dat-lich-salon`, `ban-hang-b2b`).
- **Phiên bản (Versioning):** theo [SemVer](https://semver.org/) — `MAJOR.MINOR.PATCH` (vd `1.2.0`).
- **Trạng thái (Status):** `Draft` (nháp) → `Review` (đang duyệt) → `Approved` (đã duyệt) → `Deprecated` (ngừng dùng).
- **Sơ đồ (Diagrams):** ưu tiên dùng [Mermaid](https://mermaid.js.org/) để version được bằng Git và render trực tiếp trên GitHub/GitLab. **Nhãn bên trong diagram dùng tiếng Anh** (vd `Login`, `Task Board`).
- **Độ ưu tiên (Priority):** dùng [MoSCoW](https://en.wikipedia.org/wiki/MoSCoW_method) — Must / Should / Could / Won't.

## Định dạng tài liệu (Formatting)

Repo dùng [Prettier](https://prettier.io/) để canh đều định dạng Markdown (canh cột bảng, khoảng cách...). Cấu hình ở `.prettierrc.json`.

```bash
npm install        # cài Prettier (lần đầu)
npm run format     # format toàn bộ file .md
npm run format:check  # chỉ kiểm tra, không sửa (dùng trong CI)
```

> Khuyến nghị chạy `npm run format` trước khi commit để tài liệu luôn đồng nhất.

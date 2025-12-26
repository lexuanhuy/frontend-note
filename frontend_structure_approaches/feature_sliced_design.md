# Feature-Sliced Design
Feature-Sliced Design (FSD) là một phương pháp tổ chức thư mục theo tính năng (feature) thay vì chỉ theo tầng UI. Nó giúp dự án frontend lớn dễ mở rộng, dễ bảo trì, và giảm phụ thuộc giữa các phần. FSD thường được áp dụng trong các dự án React/Next.js quy mô vừa và lớn.

## Cấu trúc thư mục 
Do cấu trúc FSD conflict với NextJS nên giải pháp là ta di chuyển thư mục `app/` và `pages/` của NextJS ra thư mục gốc. Phải có cả 2 thư mục ở thư mục gốc nếu không NextJS sẽ cố gắng sử dụng thư mục `src/pages` hoặc `src/app` làm bộ định tuyến trang. Ngoài ra, bạn cũng nên đặt một tệp README.md bên trong thư mục gốc của các trang này để mô tả lý do tại sao nó cần thiết, ngay cả khi tệp đó trống.
```
├── app                      # App folder (Next.js)
│   ├── api
│   │   └── get-example
│   │       └── route.ts
│   └── example
│       └── page.tsx
├── pages                            # Empty pages folder (Next.js)
│   └── README.md
└── src
    ├─ app/                  # Entry points, routing
    │   ├─ providers/        # Global providers (theme, store)
    │   └─ index.tsx
    │
    ├─ processes/            # Quy trình lớn (auth flow, checkout flow)
    ├─ widgets/              # Thành phần UI lớn, kết hợp nhiều features
    ├─ features/             # Các tính năng (search, auth, cart)
    │   ├─ auth/
    │   │   ├─ components/
    │   │   ├─ hooks/
    │   │   ├─ store/
    │   │   └─ index.ts      # Public API của module
    │   └─ search/
    │       ├─ components/
    │       ├─ hooks/
    │       └─ store/
    ├─ entities/             # Các entity/domain (user, product, order)
    ├─ shared/               # Thành phần dùng chung (UI, lib, utils)
    │   ├─ ui/
    │   ├─ api/
    │   ├─ config/
    │   └─ styles/
```
### Giải thích
- 

## Nguyên tắc khi áp dụng
- Isolation: Module không phụ thuộc trực tiếp vào module khác cùng tầng.
- Public API rõ ràng: Mỗi module chỉ export những gì cần thiết.
- Needs-driven: Chia theo nhu cầu business, không chia theo kỹ thuật thuần túy.
- Adaptability: Dễ thêm/bớt module mà không ảnh hưởng toàn hệ thống.

## Lợi ích
- Dễ mở rộng: Thêm tính năng mới chỉ cần thêm module mới.
- Giảm phụ thuộc: Module độc lập, dễ refactor.
- Onboarding dễ: Dev mới chỉ cần học module liên quan.
- Mapping với backend: Dễ đồng bộ với kiến trúc DDD ở backend.

## Nhược điểm
- Phức tạp hơn: Cấu trúc nhiều tầng, dễ gây khó hiểu nếu team nhỏ.
- Cần discipline: Nếu không tuân thủ nguyên tắc isolation/public API, dễ loạn.
- Ít tài liệu hơn: FSD còn mới, cộng đồng chưa lớn bằng Atomic Design.

## Khi nào nên áp dụng
- Dự án lớn, nhiều tính năng (e-commerce, SaaS, dashboard phức tạp).
- Team nhiều người: cần phân chia rõ ràng để tránh xung đột.
- Cần scale lâu dài: dự án sống nhiều năm, cần dễ refactor.
- Khi backend cũng dùng DDD: FSD giúp frontend align với backend.


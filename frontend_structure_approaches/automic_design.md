# Automic Design trong NextJS
Automic Design là phương pháp tổ chức UI theo các cấp độ: `Atoms → Molecules → Organisms → Templates → Pages`

## Nguyên tắc khi áp dụng
- Atoms: thành phần cơ bản, không phụ thuộc vào logic phức tạp. Ví dụ: Button.tsx, Input.tsx.
- Molecules: kết hợp atoms để tạo thành khối chức năng nhỏ. Ví dụ: SearchBar (gồm Input + Button).
- Organisms: phần UI lớn, có thể chứa nhiều molecules. Ví dụ: Header, Sidebar.
- Templates: định nghĩa bố cục trang, nhưng chưa có dữ liệu cụ thể.
- Pages: trang hoàn chỉnh, kết hợp template + dữ liệu từ API.

## Lợi ích
- Dễ tái sử dụng: atoms/molecules có thể dùng lại ở nhiều nơi.
- Dễ mở rộng: thêm tính năng mà không phá vỡ cấu trúc.
- Rõ ràng: phân biệt rõ giữa UI cơ bản và UI phức tạp.
- Phù hợp với Next.js App Router: dễ tổ chức layout và nested routes.

Tóm lại: bạn nên đặt thư mục components theo Atomic Design (atoms → molecules → organisms → templates → pages), kết hợp với cấu trúc chuẩn của Next.js (app/, lib/, hooks/, services/).

## Cấu trúc thư mục
```
src/
 ├─ app/                  # App Router (Next.js 13+)
 │   ├─ layout.tsx
 │   ├─ page.tsx
 │   └─ ...routes
 │
 ├─ components/           # Thư mục chính cho Atomic Design
 │   ├─ atoms/            # Thành phần nhỏ nhất (Button, Input, Icon)
 │   ├─ molecules/        # Kết hợp atoms (FormField, Card, NavbarItem)
 │   ├─ organisms/        # Thành phần lớn hơn (Header, Footer, Sidebar)
 │   ├─ templates/        # Layout cụ thể cho trang (DashboardLayout, AuthLayout)
 │   └─ pages/            # Trang hoàn chỉnh (HomePage, ProfilePage)
 │
 ├─ hooks/                # Custom hooks
 ├─ lib/                  # Hàm tiện ích, config
 ├─ styles/               # CSS/SCSS/Tailwind config
 ├─ types/                # Kiểu dữ liệu TypeScript
 └─ services/             # API call, logic kết nối backend
```
Như vậy, `page.tsx` chỉ đóng vai trò router entry và mount component từ `components/pages`.

## Vậy logic đặt ở đâu?
- UI logic (state, event handler) → đặt ngay trong `components/pages`.
- Business logic / API call → tách ra `services/` hoặc `lib/`.
- Hooks tái sử dụng → đặt trong `hooks/`.
- Data fetching server-side (App Router) → có thể viết trực tiếp trong `app/page.tsx` hoặc trong Server Component rồi truyền xuống `components/pages`.

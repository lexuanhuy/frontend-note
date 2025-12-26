# Folder Structure Approaches

## Các cách tiếp cận kiến trúc thư mục frontend phổ biến
| Kiến trúc | Ưu điểm | Nhược điểm |
| -------- | -------- | -------- |
| Atomic Design | Rõ ràng về phân tầng UI (atoms → pages); tốt cho design system | Quá tập trung vào UI, khó quản lý logic, dễ over-engineering |
| Feature-Sliced Design (module-based) | Chia theo tính năng; UI + logic + state nằm chung; dễ mở rộng, refactor | Cấu trúc phức tạp hơn, cần discipline cao |
| Component-Driven Development  (CDD) | Component là đơn vị chính, tự quản lý state và logic; dễ hiểu, thực tế | Có thể dẫn đến trùng lặp logic nếu không tổ chức tốt |
| Contextual Layering | Tổ chức theo vai trò trong user flow; linh hoạt, tránh phân tầng cứng nhắc | Thiếu chuẩn hóa, dễ gây khác biệt giữa team |

## Khi nào nên dùng kiến trúc nào?
- Nếu dự án nhỏ → Atomic Design hoặc CDD là đủ.
- Nếu dự án lớn, nhiều domain → FSD là lựa chọn tốt nhất.
- Nếu team thiên về design system → Atomic Design vẫn hữu ích, nhưng chỉ nên dùng cho UI library, không cho toàn bộ kiến trúc frontend.

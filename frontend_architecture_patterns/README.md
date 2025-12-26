# Frontend Architecture Patterns

## Các kiến trúc frontend phổ biến
| Kiến trúc | Đặc điểm chính | Ưu điểm | Nhược điểm |
| -------- | -------- | -------- | -------- |
| Monolithic | Toàn bộ frontend trong một codebase lớn, ít chia tách | Đơn giản, dễ bắt đầu | Khó scale, dễ thành “spaghetti code” khi dự án lớn |
| Micro-frontend | Chia ứng dụng thành nhiều app nhỏ, mỗi app có lifecycle riêng | Dễ scale team, deploy độc lập | Phức tạp về hạ tầng, cần CI/CD mạnh |
| Layered / Modular | Chia theo tầng (UI, logic, data) hoặc module | Rõ ràng, dễ bảo trì | Có thể cứng nhắc, khó thích ứng với domain phức tạp |
| Clean Architecture | Áp dụng nguyên tắc “use cases, entities, interfaces” vào frontend | Tách biệt rõ business logic và UI | Hơi nặng nề cho dự án nhỏ |
| Domain-Driven Frontend | Lấy domain/business làm trung tâm, chia code theo bounded context | Phù hợp với hệ thống lớn, dễ mapping với backend DDD | Đòi hỏi team hiểu rõ domain, khó áp dụng cho dự án nhỏ |


## Khi nào nên dùng kiến trúc nào?
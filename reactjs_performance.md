# Lộ trình tối ưu hóa chia theo các tầng
```
Đừng lạm dụng useMemo & useCallback. Phải hiểu khi nào thì chi phí tính toán lại rẻ hơn chi phí ghi nhớ.
```

## 1. Tối ưu hóa rendering

### Các kỹ thuật tối ưu rendering cơ bản

| Kỹ thuật | Khi nào dùng | Lưu ý |
| -------- | ------- | ------- |
| State Colocation | Khi state chỉ dùng cho 1 vùng nhỏ | Cách tốt nhất và rẻ nhất, không cần dùng Hook phức tạp. |
| React.memo | Cho component có UI phức tạp, props ít thay đổi | Đừng bọc tất cả mọi thứ, vì chính việc so sánh props cũng tốn phí. |
| useCallback | Khi truyền function xuống component con đã bọc memo | Chỉ dùng khi cần giữ tham chiếu (reference) ổn định. |
| useMemo | Khi có các phép tính toán nặng (filter, map mảng lớn) | Giúp tránh tính toán lại mỗi lần render. |

### Khi nào cần sử dụng useMemo?
Nhiều Dev thường lạm dụng useMemo cho những việc đơn giản như cộng 2 số. Đó là sai lầm, vì việc khởi tạo Hook và kiểm tra Dependency còn tốn bộ nhớ hơn. Chỉ memo những component thực sự nặng hoặc render quá nhiều lần. Việc so sánh props (shallow compare) cũng tốn tài nguyên.

- Tính toán nặng: Các hàm tốn trên 1ms để chạy (ví dụ: filter/sort mảng hàng ngàn phần tử, tính toán Fibonacci, xử lý đồ họa).
- Giữ tham chiếu: Khi bạn tạo một tham chiếu mới ở mỗi lần render và truyền nó làm props cho một Component con đã bọc React.memo

## 2. Chiến lược tải tài nguyên
## 3. Các chỉ số Core Web Vitals
## 4. Tối ưu hóa bundle size


> "Premature optimization is the root of all evil" (Tối ưu hóa sớm là nguồn cơn của mọi rắc rối).
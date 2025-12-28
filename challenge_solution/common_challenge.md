# Các vấn đề thường gặp

## 1. Các vấn đề về khả năng tương thích trình duyệt
Những trình duyệt khác nhau thường có cách render HTML, CSS và JavaScript khác nhau, điều này dẫn đến không nhất quán về thiết kế và chức năng.
### Giải pháp:
- Sử dụng công cụ phát hiện tính năng như `Modernizr` thay vì theo dõi từng trình duyệt.
- Apply CSS reset (`normalize.css`) để chuẩn hóa style trên các trình duyệt.
- Kiểm tra đa trình duyệt bằng công cụ `BrowserStack` hoặc `CrossBrowserTesting`
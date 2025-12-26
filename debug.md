# Những phương pháp debug reactjs

```
Nguyên tắc vàng của một Front-end: "Lỗi ở đâu, cô lập ở đó".
```

## 1. Console Logging

Đây là "vũ khí" quốc dân mà bất kỳ ai cũng bắt đầu. Ngoài console.log, chúng ta còn có console.table, console.group, và console.error.

- Ưu điểm: Nhanh, không cần cài đặt, hoạt động trên mọi trình duyệt.

- Nhược điểm: Làm bẩn code, khó kiểm soát khi log quá nhiều, không theo dõi được state thay đổi theo thời gian thực một cách trực quan.

- Khi nào dùng: Kiểm tra nhanh giá trị biến hoặc xác định xem một hàm có được thực thi hay không.

- Best Practice: Sử dụng các label rõ ràng: console.log('User Data:', data). Nhớ xóa log trước khi push code lên production.

## 2. React Developer Tools

Đây là Extension bắt buộc phải có trên Chrome/Firefox. Nó cung cấp hai tab chính: Components và Profiler.

- Phân tích:

  - Tab Components: Cho phép bạn soi trực tiếp Props, State, Hooks và Context của từng Component trong cây DOM ảo.

  - Tab Profiler: Ghi lại quá trình render để tìm ra "bottleneck" (điểm nghẽn hiệu năng).

- Ưu điểm: Trực quan hóa cấu trúc React, thay đổi trực tiếp Props/State để xem kết quả mà không cần sửa code.

- Nhược điểm: Chỉ dùng được trên môi trường có gắn extension.

- Khi nào dùng: Hầu như mọi lúc khi phát triển giao diện và xử lý logic luồng dữ liệu giữa các component.

## 3. Browser Debugger & Breakpoints

Thay vì log, bạn dừng thời gian lại tại một dòng code cụ thể bằng từ khóa debugger hoặc click vào dòng code trong tab Sources của Chrome DevTools.

- Ưu điểm: Cho phép bạn đi sâu vào từng bước (Step over, Step into), kiểm tra Call Stack (hàm nào gọi hàm nào).

- Nhược điểm: Đòi hỏi kiến thức về luồng thực thi JavaScript (Execution Context).

- Ví dụ: Khi bạn gặp một lỗi logic phức tạp trong hàm tính toán, hãy đặt debugger ngay đầu hàm để theo dõi biến thay đổi qua từng dòng.

## 4. Redux DevTools / TanStack Query DevTools (Chuyên biệt cho State)

Nếu ứng dụng của bạn dùng các thư viện quản lý state toàn cục, đây là những công cụ cực kỳ mạnh mẽ.

- Tính năng đặc biệt: Time Travel Debugging (Quay ngược thời gian). Bạn có thể "undo" các action đã xảy ra để xem trạng thái UI tại thời điểm đó.

- Ưu điểm: Theo dõi chính xác action nào đã làm thay đổi state và thay đổi như thế nào.

- Khi nào dùng: Khi xử lý các luồng dữ liệu phức tạp, gọi API nhiều nơi hoặc quản lý giỏ hàng, xác thực người dùng.

## 5. Network & Performance Profiling

Khi ứng dụng chạy đúng nhưng... chậm hoặc lỗi API.

- Network Tab: Kiểm tra payload gửi lên, response trả về và thời gian phản hồi của server.

- Lighthouse/Performance: Phân tích các lần "re-render" dư thừa.

- Best Practice: Sử dụng tính năng "Throttling" trong tab Network để giả lập mạng yếu (3G), đảm bảo trải nghiệm người dùng vẫn ổn định.

# Bảng so sánh tổng hợp

| Phương pháp       | Độ khó     | Độ chi tiết | Mục đích chính                         |
| ----------------- | ---------- | ----------- | -------------------------------------- |
| Console Log       | Thấp       | Thấp        | Kiểm tra giá trị biến nhanh            |
| React DevTools    | Trung bình | Cao         | "Soi State/Props, cấu trúc Component"  |
| Breakpoints       | Cao        | Rất cao     | Theo dõi logic luồng thực thi chi tiết |
| Redux/Query Tools | Trung bình | Rất cao     | Quản lý State toàn cục & Async data    |
| Network Tab       | Thấp       | Cao         | Debug lỗi API & Tốc độ tải             |

# Best Practices

- Hạn chế Console.log: Thay vào đó, hãy tập thói quen dùng Breakpoints. Nó sẽ nâng trình độ đọc code của bạn lên rất nhanh.

- Sử dụng Error Boundaries: Hãy bao bọc các phần quan trọng của app bằng React Error Boundaries để tránh việc một lỗi nhỏ làm "trắng trang" cả ứng dụng.

- Strict Mode: Luôn bật `<React.StrictMode>` ở môi trường dev. Nó sẽ giúp bạn phát hiện các side effects không mong muốn và các API cũ (deprecated).

- Debug từ ngoài vào trong: Kiểm tra Network (dữ liệu có về đúng không) -> Kiểm tra React DevTools (State có nhận đúng không) -> Cuối cùng mới dùng Breakpoints để soi logic xử lý.

# Error Boundary

> **<p style="color: tomato; text-align: center">Nguyên tắc vàng của một FrontEnd Engineer: "Lỗi ở đâu, cô lập ở đó".<p>**

## Cách thiết lập Error Boundary

Có 2 cách thiết lập Error Boundary cho ứng dụng ReactJS là tự viết 1 Component ErrorBoundary hoặc dùng thư viện `react-error-boundary`

### 1. Tự tạo Error Boundary (Class Component)

#### Bước 1: Tạo Component ErrorBoundary

```jsx
import React from "react";

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  // 1. Cập nhật state để hiển thị UI dự phòng (Fallback UI)
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  // 2. Dùng để log lỗi ra các dịch vụ bên thứ 3 (Sentry, LogRocket...)
  componentDidCatch(error, errorInfo) {
    console.error("ErrorBoundary caught an error", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // Bạn có thể render bất kỳ UI tùy chỉnh nào ở đây
      return (
        <div style={{ padding: "20px", textAlign: "center" }}>
          <h2>Đã có lỗi xảy ra.</h2>
          <button onClick={() => window.location.reload()}>Thử lại</button>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

#### Bước 2: Bao bọc ứng dụng hoặc từng phần của ứng dụng

```jsx
import ErrorBoundary from "./ErrorBoundary";

function App() {
  return (
    <ErrorBoundary>
      <MyHeader />
      <ErrorBoundary>
        {" "}
        {/* Vì lại ở đây lại có thêm ErrorBoundary */}
        <ComplexDashboard /> {/* Vì nếu Dashboard lỗi, Header vẫn hiển thị */}
      </ErrorBoundary>
    </ErrorBoundary>
  );
}
```

### 2. Sử dụng thư viện `react-error-boundary`

#### Cài đặt:

```
npm install react-error-boundary
```

#### Cách dùng:

```jsx
import { ErrorBoundary } from "react-error-boundary";

function Fallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Lỗi rồi nè:</p>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Thử lại lần nữa</button>
    </div>
  );
}

function MyComponent() {
  return (
    <ErrorBoundary
      FallbackComponent={Fallback}
      onReset={() => {
        // Reset state của ứng dụng ở đây để tránh crash tiếp
      }}
    >
      <ComponentGayLoi />
    </ErrorBoundary>
  );
}
```

### So sánh: Viết tay Class vs Dùng Thư viện

| Tiêu chí          | Tự viết Class Component             | Dùng react-error-boundary             |
| ----------------- | ----------------------------------- | ------------------------------------- |
| Kích thước Bundle | Nhỏ (0KB thêm vào)                  | Tốn một chút (nhưng rất ít)           |
| Độ linh hoạt      | Thấp (phải viết nhiều code lặp lại) | Cao (hỗ trợ reset fallback component) |
| Cú pháp           | Cũ (Class)                          | Hiện đại (Hooks/Props)                |
| Bắt lỗi Async     | Không                               | Có (thông qua hàm showBoundary)       |

### Những lỗi mà Error Boundary KHÔNG bắt được

- Event handlers: Lỗi trong các hàm onClick, onChange (Phải dùng try-catch bình thường).

- Asynchronous code: Lỗi trong setTimeout hoặc requestAnimationFrame.

- Server-side rendering (SSR).

- Lỗi xảy ra trong chính Error Boundary.

## Best Practices

- Phân cấp (Granular Control): Đừng chỉ bao bọc toàn bộ App. Hãy bao bọc các module độc lập. Ví dụ: Nếu Sidebar lỗi, người dùng vẫn nên dùng được Content chính.

- Tích hợp Logging: Luôn gửi lỗi về server (dùng Sentry hoặc Firebase Crashlytics) trong componentDidCatch.

- Cung cấp nút "Reset": Đừng chỉ hiện thông báo lỗi, hãy cho người dùng một con đường để quay lại (như nút "Trở về trang chủ" hoặc "Clear Cache").

- Error Boundary xử lý lỗi Render, còn try-catch xử lý lỗi Logic/Async.

#### Cách xử lý lỗi Logic/Async khi dùng thư viện `react-error-boundary`

Thư viện cung cấp hook useErrorBoundary để bạn có thể chủ động "bắn" lỗi từ các hàm Logic/Async vào Error Boundary.

```jsx
import { useErrorBoundary } from "react-error-boundary";

function MyComponent() {
  const { showBoundary } = useErrorBoundary();

  const handleAction = async () => {
    try {
      await apiCall(); // Giả sử API trả về lỗi 500
    } catch (err) {
      // Hàm này sẽ đẩy lỗi vào Error Boundary gần nhất bao bọc nó
      showBoundary(err);
    }
  };

  return <button onClick={handleAction}>Gửi dữ liệu</button>;
}
```

Giải thích:

- Lỗi Render: Xảy ra khi React đang chuyển đổi code của bạn thành DOM (ví dụ: data.map mà data là undefined). React tự ném lỗi này vào Error Boundary.

- Lỗi Logic/Async: Xảy ra bên ngoài luồng render của React. React không biết khi nào lỗi này xảy ra để mà "hứng".

#### Tại sao nên dùng showBoundary thay vì tự xử lý?

- Tính nhất quán (Centralization): Bạn chỉ cần thiết kế UI lỗi ở một nơi duy nhất (FallbackComponent).

- Trải nghiệm người dùng: Toàn bộ component (ví dụ: cái Card hoặc cả trang Dashboard) sẽ chuyển sang trạng thái lỗi một cách đồng bộ, thay vì mỗi chỗ hiện một kiểu thông báo lỗi khác nhau.

### Kết hợp `react-error-boundary` với TanStack Query (React Query)

```jsx
// Trong config của React Query
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      // Khi API lỗi, nó tự động "ném" lỗi vào Error Boundary gần nhất
      throwOnError: true,
    },
  },
});
```

# Áp dụng Error Boundary cho FSD và Atomic Design

## 1. Trong Feature-Sliced Design (FSD)

FSD rất coi trọng tính module hóa. Việc đặt Error Boundary trong FSD sẽ tuân theo các tầng (layers) từ cụm nhỏ đến cụm lớn.

- Tầng Shared: Tạo component ErrorBoundary dùng chung tại đây. Bạn không đặt Error Boundary ở tầng này để bắt lỗi, mà chỉ để định nghĩa nó.

- Tầng Features & Entities: Đây là nơi quan trọng nhất để đặt Error Boundary.

  - Ví dụ: Feature AddComment hoặc Entity UserCard. Nếu tính năng comment bị crash, bạn chỉ muốn cái khung comment hiện lỗi, còn nội dung bài viết (Entity Post) vẫn phải đọc được.

- Tầng Widgets: Các widget là các khối chức năng lớn (ví dụ: Header, Sidebar, UserFeed). Bạn nên bọc Error Boundary quanh mỗi Widget để đảm bảo một widget hỏng không làm sập cả trang.

- Tầng Pages: Một Error Boundary "tổng" bao quanh toàn bộ page để bắt những lỗi không lường trước được.

- Tầng App: Đây là "tấm lưới cuối cùng" (Global Error Boundary). Nếu lỗi lọt đến đây, bạn nên hiển thị một trang báo lỗi toàn màn hình (500 Page).

### Ví dụ:

```jsx
// Tầng App (Global)
<AppErrorBoundary>
  // Tầng Widget (Header)
  <Header />
  <main>
    {/* Tầng Widget (Product Details) */}
    <ErrorBoundary fallback={<ProductError />}>
      <ProductInfo />
    </ErrorBoundary>

    {/* Tầng Feature (Review Section) */}
    {/* Nếu Review lỗi, thông tin sản phẩm bên trên vẫn xem được */}
    <ErrorBoundary fallback={<small>Không thể tải bình luận</small>}>
      <ProductReviews />
    </ErrorBoundary>
  </main>
</AppErrorBoundary>
```

## 2. Trong Atomic Design

Atomic Design tập trung vào tính kế thừa và quy mô của component.

- Atoms & Molecules: KHÔNG đặt Error Boundary. Những component này quá nhỏ (Button, Input). Nếu chúng lỗi, thường là do lỗi logic ở component cha truyền props xuống.

- Organisms: CÓ. Đây là nơi bắt đầu đặt Error Boundary.

  - Ví dụ: Một ProductGrid là một Organism. Nếu việc render danh sách sản phẩm bị lỗi, bạn bọc nó lại để Header và Footer vẫn hiển thị bình thường.

- Templates & Pages: Đặt Error Boundary để bắt lỗi cấp cao (lỗi layout hoặc lỗi fetch dữ liệu trang).

## Lưu ý:

- Sử dụng key để reset: Khi người dùng chuyển trang (URL thay đổi), hãy đổi key của Error Boundary ở tầng Page để nó tự động reset trạng thái lỗi, cho phép người dùng thử lại ở trang mới.

- Thông báo lỗi tương ứng với quy mô: \* Lỗi ở Feature: Chỉ hiện một dòng text đỏ nhỏ hoặc icon cảnh báo.

- Lỗi ở Page: Hiện minh họa (Illustration) lỗi to và nút "Quay lại trang chủ".

- Hệ thống Log tập trung: Ở tầng App, componentDidCatch nên gửi đầy đủ thông tin về slice hoặc layer nào đang bị lỗi để team dev dễ khoanh vùng.

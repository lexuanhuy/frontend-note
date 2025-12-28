# Giải quyết vấn đề Dependency Hell và Shared Assets trong kiến trúc monorepo cho frontend

- Dependency Hell: Các team dùng version thư viện khác nhau (ví dụ team A dùng React 16, team B dùng React 18) nhưng phải chạy chung trên một Shell app.

- Shared Assets: Làm sao để chia sẻ UI Kit (Design System) mà không làm bundle size tăng vọt?

## 1. Giải quyết Dependency Hell

Trong Monorepo, vấn đề thường gặp là "Version Mismatch": App A dùng lodash v4.16, App B dùng lodash v4.17. Khi cài đặt, `node_modules` sẽ bị phình to và dễ gây lỗi runtime nếu chúng dùng chung một thư viện nội bộ.

### 1. Single Version Policy

Ép buộc toàn bộ Monorepo chỉ sử dụng duy nhất một phiên bản cho mỗi thư viện bên thứ ba. Tất cả `dependencies` được khai báo ở `package.json` gốc (root).

- Công cụ: Sử dụng thư viện `syncpack` để tự động kiểm tra và đồng bộ phiên bản giữa các sub-packages.

#### Cách thực hiện:

- Move to Root: Di chuyển các thư viện dùng chung phổ biến (như react, typescript, eslint, lodash, tailwindcss) lên package.json ở thư mục gốc.

- Sử dụng Catalog (với pnpm): Một tính năng mới và cực kỳ mạnh mẽ của pnpm là Catalogs. Bạn định nghĩa version tại một nơi duy nhất trong pnpm-workspace.yaml.

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "libs/*"

catalog:
  react: ^18.2.0
  typescript: ^5.0.0
```

_Ở các app con, bạn chỉ cần ghi: `"react": "catalog:"`. Khi cần nâng cấp React cho toàn bộ apps, bạn chỉ cần sửa đúng 1 dòng ở file root._

- Sử dụng Syncpack để kiểm tra sự đồng nhất giữa các package.
  - Cài đặt: `pnpm add syncpack -w`
  - Tạo file cấu hình `.syncpackrc` để ép buộc các package phải cùng version.
  - Thêm lệnh check vào CI pipeline:
  ```json
    "scripts": {
      "lint:versions": "syncpack list-mismatches"
    }
  ```
  _Nếu có sự sai lệch, lệnh này sẽ trả về exit code khác 0, làm pipeline bị đỏ._

### 2. Peer Dependencies

Đối với các thư viện dùng chung nội bộ (Shared Libs), đừng bao giờ cài các thư viện lớn như `React` hay `styled-components` vào mục dependencies. Hãy để chúng ở `peerDependencies` để tránh việc mỗi lib lại kéo theo một bản copy của React, gây lỗi "Multiple instances of React".

### 3. Resolution/Overrides

Trong trường hợp bất khả kháng phải dùng 2 version khác nhau, hãy sử dụng tính năng resolutions (Yarn/pnpm) hoặc overrides (npm) để ép trình quản lý gói chỉ chọn một bản duy nhất phù hợp nhất.

## 2. Quản lý Shared Assets (Design System & Utils)

Thách thức ở đây là làm sao để các ứng dụng con sử dụng chung tài nguyên (Icon, Image, Theme, UI Components) mà không gây nặng Bundle size và dễ bảo trì.

### 1. Tree-shakable Shared Libs

Thay vì export toàn bộ library, hãy cấu hình để các ứng dụng chỉ "bốc" đúng cái cần dùng. Sử dụng Barrel files (index.ts) một cách cẩn thận kết hợp với ESM (ES Modules). Đảm bảo `sideEffects: false` trong `package.json` của thư viện shared để Webpack/Vite có thể loại bỏ code thừa.

- Loại bỏ Side Effects: Trong package.json của thư viện UI, hãy thêm:

```json
"sideEffects": false
```

- Tránh Barrel Files (index.ts) "quá khổ": Thay vì một file `index.ts` export hàng trăm thứ, hãy cấu hình để user có thể import sâu: `import { Button } from '@my-org/ui/Button';` thay vì `import { Button } from '@my-org/ui';`. Mẹo: Bạn có thể dùng công cụ như Rollup hoặc tsup để phân tách mỗi component ra một file JS riêng biệt trong thư mục dist.

- Externalize Peer Dependencies:Khi build UI Kit, đừng bao giờ đóng gói React vào trong đó. Hãy cấu hình peerDependencies và đánh dấu React là external trong Rollup/Vite config.

### 2. Internal Packages

- Khai báo package nội bộ Trong `packages/ui/package.json`

```json
{
  "name": "@my-org/ui",
  "version": "0.0.0",
  "main": "./index.tsx",
  "types": "./index.tsx",
  "dependencies": {
    "react": "catalog:"
  }
}
```

- Kết nối vào App con Trong `apps/web/package.json`

```json
{
  "dependencies": {
    "@my-org/ui": "workspace:*"
  }
}
```

_Lưu ý: Cú pháp workspace:\* (của pnpm/yarn/npm) cực kỳ quan trọng. Nó bảo Bundler rằng: "Đừng tìm trên NPM, hãy tìm ngay trong máy này"._

### 3. Cấu hình Path Mapping

Sử dụng paths trong tsconfig.json để biến các thư mục shared thành các "alias" như `@shared-lib/ui-kit`. Việc này giúp code sạch và dễ refactor.

### 4. Turborepo Remote Caching

- Caching: Nếu bạn đã build thư viện ui rồi và không sửa gì nó, lần build sau Turborepo sẽ lấy kết quả từ cache thay vì build lại. Thời gian build có thể giảm từ phút xuống còn giây.

- Pipeline Task: Bạn định nghĩa các nhiệm vụ trong turbo.json

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
    },
    "test": {
      "dependsOn": ["build"]
    }
  }
}
```

Ký tự `^` trong `"dependsOn": ["^build"]` có nghĩa là: "Để build App này, hãy build toàn bộ các thư viện mà nó phụ thuộc vào trước".

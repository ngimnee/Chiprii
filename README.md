# Chiprii

Dựng bằng [Three.js](https://threejs.org/) r135 và [GSAP](https://gsap.com/) 3.8, không cần build. Dựa trên một CodePen về trái tim hạt.

## Chạy thử

Trang **phải được mở qua HTTP server**. Mở thẳng file `index.html` (`file://`) sẽ không chạy vì trình duyệt chặn ES module và `fetch` các file shader.

```bash
npx serve .
```

Hoặc dùng extension Live Server của VS Code, hay `python -m http.server`, rồi mở địa chỉ server in ra.

## Cấu trúc

| Đường dẫn | Nội dung |
|---|---|
| `index.html` | Canvas WebGL và nút phát nhạc |
| `css/styles.css` | Bố cục toàn màn hình, vị trí nút |
| `js/main.js` | Toàn bộ logic (class `World`): scene, camera, hạt, nhạc, ảnh nền. Cuối file chứa ảnh nền đã mã hoá |
| `shaders/vertex.glsl`, `fragment.glsl` | 1500 hạt chạy theo đường cong trái tim |
| `shaders/vertex1.glsl`, `fragment1.glsl` | 550 trái tim nhỏ bay lên |
| `models/heart.glb` | Mô hình trái tim 3D |
| `images/3.png` | Texture matcap (màu bạc) của trái tim 3D |
| `images/heart.png` | Sprite trái tim cho các hạt bay lên |
| `images/chiprii-qr.png` | Thẻ mã QR mở trang `https://ngimnee.github.io/Chiprii/` (không dùng trong trang) |
| `sounds/inlove.mp3` | Nhạc |

## Ảnh nền

Có hai ảnh nền: một cho màn hình rộng (từ 768px) và một cho điện thoại. Cả hai được **mã hoá AES-256-GCM** và nhúng ở cuối `js/main.js` cùng với khoá; repo không chứa file ảnh nào chưa mã hoá. Mở trang là trang tự giải mã và hiện ảnh, nguyên khung, kéo vừa toàn màn hình, không cắt.

Vì khoá nằm ngay trong code, việc mã hoá chỉ giấu ảnh khỏi người xem lướt qua code; người biết đọc code vẫn giải mã được.

Việc giải mã dùng Web Crypto của trình duyệt, chỉ chạy trên **HTTPS hoặc `localhost`**. Mở qua `http://` với địa chỉ IP trong mạng LAN (ví dụ thử trên điện thoại qua Live Server) sẽ không hiện ảnh, nền chuyển màu tối.

**Đổi ảnh** (cần Node.js):

```bash
node tools/encrypt-background.js anh-man-hinh-rong.jpg anh-dien-thoai.jpg
```

Lệnh này tạo khoá mới, mã hoá hai ảnh và ghi cả hai vào `js/main.js`. Giữ ảnh gốc ở ngoài thư mục dự án để không lỡ commit lên.

## Tuỳ chỉnh

- **Nhạc**: thay `sounds/inlove.mp3`, hoặc sửa đường dẫn trong `loadMusic()` ở `js/main.js`.
- **Số hạt, màu**: `parameters.count` và `colorChoices` trong `addHeart()`, `count` và `colorChoices` trong `addSnow()`.
- **Kích thước hạt**: uniform `uSize` của từng material.

Three.js và GSAP được tải từ jsDelivr với phiên bản cố định (`three@0.135.0`, `gsap@3.8.0`). Nâng Three.js lên bản mới hơn cần sửa code theo các thay đổi API của Three.js.
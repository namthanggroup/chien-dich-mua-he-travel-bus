# 🚌 Nam Thắng Travel Bus – Hướng dẫn Deploy

## Tổng quan kiến trúc

```
[QR Code trên xe] → [index.html trên Vercel/GitHub Pages]
                         ↓ POST JSON
              [Google Apps Script Web App]
                    ↙           ↘
          [Google Sheets]    [Google Drive]
          (data bảng)        (lưu ảnh)
```

---

## BƯỚC 1: Deploy Google Apps Script (Backend)

1. Truy cập **script.google.com** → New Project → đặt tên: `NamThang-Checkin`
2. Xóa nội dung mặc định, paste toàn bộ nội dung file `google-apps-script.js` vào
3. Nhấn **Save** (Ctrl+S)
4. Nhấn **Deploy → New Deployment**
5. Chọn Type: **Web App**
6. Cấu hình:
   - Description: `NamThang Checkin v1`
   - Execute as: **Me**
   - Who has access: **Anyone**
7. Nhấn **Deploy** → Cấp quyền khi được hỏi
8. **Copy URL** dạng: `https://script.google.com/macros/s/AKfy.../exec`

---

## BƯỚC 2: Cập nhật URL vào index.html

Mở `index.html`, tìm dòng:
```javascript
const SHEET_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```
Thay `YOUR_SCRIPT_ID` bằng ID thực từ Bước 1.

Cũng kiểm tra:
```javascript
const APP_LINK = 'https://onelink.to/namthang'; // link tải app thực tế
```

---

## BƯỚC 3A: Deploy lên GitHub Pages (Miễn phí)

```bash
# Tạo repo mới trên github.com, ví dụ: namthang-checkin
git init
git add index.html
git commit -m "init checkin page"
git remote add origin https://github.com/YOUR_USERNAME/namthang-checkin.git
git push -u origin main

# Vào Settings → Pages → Branch: main → /root → Save
```
→ URL: `https://YOUR_USERNAME.github.io/namthang-checkin`

---

## BƯỚC 3B: Deploy lên Vercel (Nhanh hơn, HTTPS tự động)

1. Vào **vercel.com** → New Project → Import GitHub repo
2. Framework Preset: **Other**
3. Root Directory: để mặc định
4. Deploy → Done!
5. Tùy chỉnh domain nếu muốn, ví dụ: `checkin.namthang.vn`

---

## BƯỚC 4: Tạo QR Code

Sau khi có URL cuối cùng (ví dụ: `https://checkin.namthang.vn`):

1. Vào **qr-code-generator.com** hoặc **qrcode-monkey.com**
2. Nhập URL
3. Tùy chỉnh màu: xanh lá (#1B5E20) + logo Nam Thắng
4. Tải về PNG độ phân giải cao → in lên sticker dán trong xe

---

## BƯỚC 5: Xem dữ liệu

Mở Google Sheet: `docs.google.com/spreadsheets/d/1cnn__aIqcCu2nBJq23ANHipJ-OlyyCIAtRkxLj1Zzzw`

Tab **Check-ins** sẽ có các cột:
| Thời gian | Họ tên | Số điện thoại | Link FB/TikTok | Ảnh Drive | Số ảnh | Nguồn |

Ảnh lưu trong Google Drive folder: **NamThang_Checkin_Photos**

---

## Lưu ý quan trọng

- **Ảnh**: Lưu trên Google Drive, Sheet chỉ lưu link → không lo dung lượng Sheet
- **CORS**: Google Apps Script xử lý tự động, không cần config thêm
- **HTTPS**: Bắt buộc để camera/upload ảnh hoạt động trên mobile → Vercel/GitHub Pages đều ok
- **Quota**: Google Apps Script miễn phí 6 phút/ngày runtime, đủ cho vài trăm submission/ngày

---

## Test nhanh sau deploy

Mở Console browser (F12) → Network tab → Submit form → Kiểm tra request POST có status 200 không.

Nếu lỗi CORS: Kiểm tra Apps Script đã deploy đúng với "Anyone" access chưa.

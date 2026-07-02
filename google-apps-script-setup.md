# 📋 Hướng Dẫn Thiết Lập Google Sheets + Apps Script

Hướng dẫn này giúp bạn tạo backend miễn phí để nhận dữ liệu RSVP từ web và lưu vào Google Sheets.

---

## Bước 1: Tạo Google Spreadsheet

1. Truy cập [Google Sheets](https://sheets.google.com)
2. Tạo **Spreadsheet mới** (bấm dấu `+`)
3. Đặt tên: `RSVP Lễ Tốt Nghiệp`
4. Ở **dòng 1** (header), nhập vào 3 cột:

| A1 | B1 | C1 |
|---|---|---|
| Họ và Tên | Xác nhận | Thời gian gửi |

---

## Bước 2: Mở Apps Script

1. Trong Google Sheets, vào menu **Extensions** (Tiện ích mở rộng)
2. Chọn **Apps Script**
3. Một tab mới sẽ mở ra với trình soạn thảo code

---

## Bước 3: Paste Code

**Xóa hết code mẫu** trong trình soạn thảo, rồi **paste đoạn code sau**:

```javascript
// Hàm xử lý POST request từ web
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);

    // Ghi dữ liệu vào dòng mới
    sheet.appendRow([
      data.name,        // Họ và Tên
      data.attendance,   // Xác nhận (Tham dự / Không tham dự)
      data.timestamp     // Thời gian gửi
    ]);

    // Trả về kết quả thành công
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// Hàm test (không bắt buộc)
function doGet(e) {
  return ContentService.createTextOutput('RSVP API is running! 🎓');
}
```

4. Bấm **Ctrl + S** để lưu
5. Đặt tên project: `RSVP Script`

---

## Bước 4: Deploy (Triển khai)

1. Bấm nút **Deploy** (Triển khai) → chọn **New deployment** (Triển khai mới)
2. Bấm **biểu tượng bánh răng** ⚙️ bên cạnh "Select type" → chọn **Web app**
3. Cấu hình:
   - **Description**: `RSVP Lễ Tốt Nghiệp`
   - **Execute as** (Thực thi với tư cách): `Me` (tài khoản của bạn)
   - **Who has access** (Ai có quyền truy cập): `Anyone` (Bất kỳ ai)
4. Bấm **Deploy** (Triển khai)
5. Google sẽ yêu cầu **xác nhận quyền**:
   - Bấm **Authorize access**
   - Chọn tài khoản Google của bạn
   - Nếu thấy "Google hasn't verified this app", bấm **Advanced** → **Go to RSVP Script (unsafe)**
   - Bấm **Allow**
6. 🎉 **Copy URL** hiển thị sau khi deploy xong!

   URL sẽ có dạng:
   ```
   https://script.google.com/macros/s/AKfycb.../exec
   ```

---

## Bước 5: Cập nhật URL vào Web

1. Mở file `script.js`
2. Tìm dòng:
   ```javascript
   const GOOGLE_SCRIPT_URL = 'YOUR_GOOGLE_APPS_SCRIPT_URL_HERE';
   ```
3. Thay `YOUR_GOOGLE_APPS_SCRIPT_URL_HERE` bằng URL bạn vừa copy:
   ```javascript
   const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
   ```
4. Lưu file

---

## Bước 6: Test

1. Mở file `index.html` trên trình duyệt
2. Nhập họ tên và chọn xác nhận
3. Bấm **Gửi Xác Nhận**
4. Kiểm tra Google Sheets — dữ liệu sẽ xuất hiện ở dòng mới!

---

## ⚠️ Lưu ý

- **Miễn phí**: Google Apps Script miễn phí với giới hạn ~20,000 request/ngày
- **Bảo mật**: URL deploy chỉ nhận POST request, không ai xem được sheet trừ bạn
- **Chỉnh sửa**: Nếu cần thay đổi code, sửa trong Apps Script → Deploy → **Manage deployments** → cập nhật version mới
- **Mode `no-cors`**: Web gửi request ở chế độ `no-cors`, nên response sẽ bị opaque nhưng dữ liệu vẫn được ghi thành công

---

## 🎉 Xong!

Bạn đã có một hệ thống RSVP hoàn chỉnh:
- 📱 Web mời đẹp, responsive
- 📊 Dữ liệu tự động lưu vào Google Sheets
- ✅ Xem ai tham dự, ai không, ngay trên Sheets

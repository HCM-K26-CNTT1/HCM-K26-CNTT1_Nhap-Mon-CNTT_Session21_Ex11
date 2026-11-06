# BÀI 11: THIẾT KẾ CẨM NANG XỬ LÝ SỰ CỐ VÀ CHUẨN HÓA BUG REPORTING FRAMEWORK TRẠM SẠC

## PHẦN 1: THIẾT KẾ MẪU PHIẾU BÁO LỖI CHUẨN HÓA (STANDARD BUG REPORT TEMPLATE)

### 1. Thông tin chung

- Tiêu đề lỗi: Kiosk số 01 không phản hồi sau khi thanh toán QR thành công.
- Mức độ nghiêm trọng: Critical (Nghiêm trọng).
- Địa điểm: Trạm sạc Trung tâm Hoàn Kiếm.
- Thiết bị: Kiosk sạc số 01.
- Người báo cáo: Kỹ thuật viên vận hành.

### 2. Nội dung báo cáo theo Bug Reporting Framework 4 bước

Khối 1 — Context (Bối cảnh môi trường)

- Tên thiết bị: Kiosk sạc VoltCharge số 01.
- Mã trạm: VoltCharge – Trung tâm Hoàn Kiếm.
- Phiên bản phần mềm: VoltCharge Controller v2.4.1.
- Hệ điều hành: Windows 11 IoT Enterprise.
- Kết nối mạng: LAN cáp quang, đang kết nối.
- Thời điểm xảy ra lỗi: Giả định 14:30 ngày 06/11/2026.

Khối 2 — Expected Behavior (Hành vi mong muốn)

Sau khi khách hàng quét mã QR và thanh toán thành công, hệ thống phải:

1. Xác nhận giao dịch thanh toán thành công.
2. Tự động cập nhật trạng thái trên màn hình Kiosk.
3. Mở khóa chốt súng sạc để khách hàng sử dụng.
4. Bắt đầu quá trình sạc khi súng được kết nối đúng cách.
5. Hiển thị thông số dòng điện và điện năng tiêu thụ (kWh) trên màn hình.

Khối 3 — Actual Behavior (Hiện tượng thực tế)

Sau khi thanh toán QR thành công, Kiosk xuất hiện các hiện tượng:

1. Màn hình vẫn giữ trạng thái chờ, không cập nhật giao dịch.
2. Nút BẮT ĐẦU SẠC chuyển sang trạng thái bị đơ, không thể thao tác.
3. Vòng tròn tải (Loading) xoay liên tục, không kết thúc.
4. Chốt khóa súng sạc không nhả, khiến khách hàng không thể bắt đầu sạc.
5. Hệ thống không hiển thị thông số điện năng tiêu thụ.

Khối 4 — Error Log & Evidence (Nhật ký lỗi và bằng chứng)

Giả định trích xuất 3 dòng từ tệp `debug.log`:

```
[2026-11-06 14:30:12] ERROR WebSocket connection closed abruptly. Code: 1006
[2026-11-06 14:30:13] ERROR Payment confirmation failed: WebSocket connection lost.
[2026-11-06 14:30:15] ERROR Charging session initialization timeout. Kiosk ID: 01
```

Nhận xét: Mã lỗi WebSocket 1006 cho thấy kết nối WebSocket bị đóng bất thường. Đây có thể là nguyên nhân khiến Kiosk không nhận được thông báo xác nhận thanh toán và không thể tiếp tục quy trình sạc.

Kết luận: Đề nghị đội Backend kiểm tra kết nối WebSocket, quá trình đồng bộ trạng thái thanh toán và chức năng điều khiển khóa súng sạc.

## PHẦN 2: CÁC BƯỚC TÁI HIỆN LỖI (STEPS TO REPRODUCE)

### 1. Mục đích

Mô tả các thao tác cụ thể để lập trình viên có thể tái hiện lỗi trên môi trường kiểm thử mà không cần đến trạm sạc thật.

### 2. Các bước thực hiện

1. Mở màn hình Kiosk: Khởi động phần mềm VoltCharge Controller v2.4.1 trên máy tính thử nghiệm và truy cập màn hình thanh toán.
2. Kết nối súng sạc: Cắm súng sạc vào cổng kết nối thử nghiệm và kiểm tra trạng thái thiết bị.
3. Quét mã QR: Sử dụng ứng dụng thanh toán thử nghiệm để quét mã QR được hiển thị trên màn hình Kiosk.
4. Xác nhận thanh toán: Thực hiện giao dịch thanh toán giả lập thành công và kiểm tra thông báo xác nhận từ hệ thống.
5. Nhấn BẮT ĐẦU SẠC: Quan sát màn hình Kiosk. Lỗi xuất hiện khi nút bấm không phản hồi, vòng tròn Loading xoay liên tục và chốt súng sạc vẫn bị khóa.

Kết quả tái hiện mong đợi: Lập trình viên có thể quan sát được hiện tượng lỗi và kiểm tra nguyên nhân từ nhật ký hệ thống.

Lưu ý: Chỉ thực hiện trên môi trường kiểm thử với giao dịch giả lập và thiết bị mô phỏng, không thao tác trên trạm sạc thật khi chưa có quy trình an toàn.

## PHẦN 3: QUY CHUẨN BẰNG CHỨNG HÌNH ẢNH VÀ AN TOÀN THÔNG TIN

### 1. Ba tiêu chuẩn bắt buộc khi chụp ảnh

Tiêu chuẩn 1: Chụp ảnh rõ ràng bằng phím tắt hệ thống

- Nhấn tổ hợp phím `Windows + Shift + S`.
- Chọn vùng màn hình cần chụp.
- Chụp trực tiếp màn hình Kiosk hoặc cửa sổ Terminal.
- Đảm bảo ảnh rõ nét, không bị rung hoặc mất nội dung quan trọng.

Tiêu chuẩn 2: Đánh dấu vị trí xảy ra lỗi

- Sử dụng công cụ chỉnh sửa ảnh để khoanh viền đỏ quanh nút BẮT ĐẦU SẠC bị đơ.
- Đánh dấu vòng tròn Loading xoay liên tục.
- Khoanh vùng dòng thông báo lỗi WebSocket Code 1006 trong Terminal.
- Không che mất nội dung kỹ thuật cần kiểm tra.

Tiêu chuẩn 3: Đảm bảo ảnh có đầy đủ thông tin

- Ảnh phải thể hiện rõ trạng thái lỗi.
- Giữ lại thời gian xảy ra lỗi và mã thiết bị nếu cần thiết.
- Chụp kèm thông báo lỗi trong Terminal hoặc tệp log.
- Không gửi ảnh mờ, ảnh thiếu nội dung hoặc ảnh không liên quan.

### 2. Bảo mật thông tin khách hàng (Data Security)

Trên màn hình Kiosk có hiển thị biển số xe và 4 số cuối tài khoản thanh toán. Đây là những thông tin không nên công khai trong báo cáo lỗi.

Cách xử lý trước khi gửi ảnh:

1. Mở ảnh chụp màn hình bằng công cụ Snipping Tool hoặc Paint.
2. Sử dụng hình chữ nhật màu đen để che hoàn toàn biển số xe.
3. Che 4 số cuối tài khoản thanh toán bằng hình chữ nhật màu đen hoặc công cụ làm mờ.
4. Kiểm tra lại ảnh để chắc chắn không còn thông tin nhạy cảm bị lộ.
5. Lưu ảnh đã chỉnh sửa thành tệp mới rồi mới đính kèm vào Bug Report.

Lưu ý bảo mật:

- Không gửi ảnh gốc chưa che thông tin khách hàng.
- Không để lộ mật khẩu, mã OTP, API Key hoặc thông tin đăng nhập của trạm sạc.
- Không sử dụng các công cụ chỉnh sửa trực tuyến không đáng tin cậy để xử lý ảnh chứa dữ liệu khách hàng.
- Ưu tiên che đen hoàn toàn thông tin thay vì chỉ làm mờ nhẹ.

## PHẦN 4: KẾT LUẬN

Qua bài tập, em hiểu rằng việc báo cáo lỗi cần có đầy đủ 4 thành phần: Context, Expected Behavior, Actual Behavior và Error Log & Evidence.

Một Bug Report rõ ràng giúp lập trình viên nhanh chóng hiểu được lỗi, xác định nguyên nhân và đưa ra hướng khắc phục phù hợp. Ngoài ra, việc chụp ảnh đúng cách và che thông tin nhạy cảm cũng giúp bảo vệ dữ liệu khách hàng.

Bài học rút ra: Khi gặp sự cố, không nên chỉ báo lỗi bằng những câu chung chung như "máy bị đơ", mà cần mô tả rõ môi trường, thao tác gây lỗi, kết quả thực tế và cung cấp bằng chứng cụ thể.

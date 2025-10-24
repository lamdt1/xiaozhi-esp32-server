# Câu hỏi Thường gặp ❓

### 1. Tại sao Xiaozhi nhận dạng ra tiếng Hàn, tiếng Nhật, tiếng Anh khi tôi nói tiếng Trung? 🇰🇷

**Gợi ý**: Kiểm tra xem thư mục `models/SenseVoiceSmall` đã có tệp `model.pt` chưa. Nếu chưa thì cần tải xuống, xem tại đây: [Tải xuống tệp mô hình nhận dạng giọng nói](Deployment.md#model-files)

### 2. Tại sao xuất hiện lỗi "TTS task error - file not found"? 📁

**Gợi ý**: Kiểm tra xem bạn đã cài đặt đúng thư viện `libopus` và `ffmpeg` bằng `conda` chưa.

Nếu chưa cài đặt, hãy cài đặt:

```
conda install conda-forge::libopus
conda install conda-forge::ffmpeg
```

### 3. TTS thường xuyên thất bại và timeout ⏰

**Gợi ý**: Nếu `EdgeTTS` thường xuyên thất bại, trước tiên hãy kiểm tra xem bạn có đang sử dụng proxy (VPN) không. Nếu có, hãy thử tắt proxy và thử lại;  
Nếu sử dụng Doubao TTS của Volcano Engine và thường xuyên thất bại, khuyến nghị sử dụng phiên bản trả phí vì phiên bản test chỉ hỗ trợ 2 kết nối đồng thời.

### 4. Có thể kết nối đến server tự xây dựng bằng WiFi, nhưng không thể kết nối ở chế độ 4G 🔐

**Nguyên nhân**: Firmware của Xiaoge, chế độ 4G cần sử dụng kết nối bảo mật.

**Giải pháp**: Hiện tại có hai phương pháp để giải quyết. Chọn một trong hai:

1. **Sửa đổi mã nguồn**. Tham khảo video này để giải quyết: https://www.bilibili.com/video/BV18MfTYoE85

2. **Sử dụng HTTPS/WSS**: Cấu hình server của bạn sử dụng giao thức HTTPS và WSS thay vì HTTP và WS.

### 5. Tại sao hệ thống phản hồi chậm hoặc không phản hồi? 🐌

**Nguyên nhân có thể và giải pháp**:

- **Độ trễ mạng**: Kiểm tra tốc độ kết nối mạng của bạn
- **Hiệu suất server**: Giám sát việc sử dụng CPU và bộ nhớ
- **Tải mô hình**: Đảm bảo tất cả các mô hình cần thiết được tải đúng cách
- **Giới hạn tốc độ API**: Kiểm tra xem bạn có vượt quá giới hạn tốc độ của nhà cung cấp dịch vụ

### 6. Làm thế nào để cải thiện độ chính xác nhận dạng giọng nói? 🎯

**Gợi ý**:
- Sử dụng microphone chất lượng tốt
- Nói rõ ràng và với âm lượng bình thường
- Giảm tiếng ồn xung quanh
- Đảm bảo kết nối mạng ổn định
- Cân nhắc sử dụng mô hình cục bộ để có quyền riêng tư và hiệu suất tốt hơn

### 7. Tại sao không thể kết nối đến WebSocket server? 🔌

**Các bước khắc phục sự cố**:
1. Kiểm tra xem server có đang chạy không: `docker-compose ps`
2. Xác minh URL WebSocket trong config.yaml
3. Kiểm tra cài đặt tường lửa
4. Đảm bảo cổng đúng được mở (mặc định: 8000)
5. Test kết nối bằng trang test: `test/test_page.html`

### 8. Làm thế nào để cấu hình nhiều nhà cung cấp dịch vụ AI? 🤖

**Các bước cấu hình**:
1. Chỉnh sửa `config.yaml`
2. Cập nhật phần `selected_module`
3. Cấu hình API key cho từng dịch vụ
4. Khởi động lại server: `docker-compose restart`

### 9. Sử dụng bộ nhớ quá cao 💾

**Gợi ý tối ưu hóa**:
- Sử dụng mô hình nhỏ hơn khi có thể
- Bật cache mô hình
- Giám sát việc sử dụng bộ nhớ: `docker stats`
- Cân nhắc sử dụng dịch vụ dựa trên đám mây cho xử lý nặng

### 10. Làm thế nào để thêm từ khóa đánh thức tùy chỉnh? 🎤

**Cấu hình**:
1. Chỉnh sửa phần `wakeup_words` trong `config.yaml`
2. Thêm cụm từ đánh thức tùy chỉnh của bạn
3. Khởi động lại server
4. Test với từ khóa đánh thức mới

### 11. Hệ thống plugin không hoạt động 🔌

**Khắc phục sự cố**:
1. Kiểm tra cấu hình plugin trong `config.yaml`
2. Xác minh API key cho các dịch vụ bên ngoài
3. Kiểm tra logs plugin trong logs ứng dụng
4. Đảm bảo các tệp plugin ở đúng thư mục

### 12. Làm thế nào để cập nhật hệ thống? 🔄

**Quy trình cập nhật**:
1. Sao lưu cấu hình của bạn: `cp config.yaml config.yaml.backup`
2. Kéo thay đổi mới nhất: `git pull`
3. Cập nhật Docker images: `docker-compose pull`
4. Khởi động lại dịch vụ: `docker-compose restart`
5. Xác minh tương thích cấu hình

### 13. Mối quan tâm bảo mật 🔒

**Khuyến nghị bảo mật**:
1. Sử dụng khóa xác thực mạnh
2. Bật HTTPS/WSS trong sản xuất
3. Cập nhật dependencies thường xuyên
4. Giám sát logs truy cập
5. Sử dụng biến môi trường cho dữ liệu nhạy cảm

### 14. Tối ưu hóa hiệu suất ⚡

**Mẹo tối ưu hóa**:
1. Sử dụng lưu trữ SSD cho mô hình
2. Phân bổ RAM đủ
3. Sử dụng tăng tốc GPU khi có sẵn
4. Tối ưu hóa cấu hình mạng
5. Giám sát tài nguyên hệ thống

### 15. Tích hợp với Home Assistant 🏠

**Các bước thiết lập**:
1. Cấu hình Home Assistant API trong `config.yaml`
2. Thiết lập các thực thể thiết bị
3. Test các lệnh điều khiển thiết bị
4. Xác minh chức năng điều khiển bằng giọng nói

**Lưu ý: Một số bước khắc phục sự cố có thể đã lỗi thời. Vui lòng tham khảo tài liệu dự án mới nhất để biết giải pháp hiện tại.**

## Nhận Trợ giúp

Nếu bạn gặp vấn đề không được đề cập trong FAQ này:

1. **Kiểm tra logs**: `docker-compose logs -f`
2. **Xem lại cấu hình**: Xác minh tất cả cài đặt trong `config.yaml`
3. **Test các thành phần**: Sử dụng trang test để xác minh kết nối WebSocket
4. **Hỗ trợ cộng đồng**: Kiểm tra các vấn đề và thảo luận của dự án
5. **Tài liệu**: Tham khảo tài liệu dự án mới nhất

## Xác minh Cấu hình

Trước khi báo cáo vấn đề, vui lòng xác minh:

- [ ] Tất cả tệp mô hình cần thiết đã được tải xuống
- [ ] API key được cấu hình đúng
- [ ] Kết nối mạng hoạt động
- [ ] Các cổng cần thiết được mở
- [ ] Dịch vụ Docker chạy đúng cách
- [ ] Cú pháp tệp cấu hình đúng

**Lưu ý: FAQ này có thể đã lỗi thời. Vui lòng tham khảo tài liệu dự án mới nhất và thảo luận cộng đồng để biết giải pháp hiện tại.**

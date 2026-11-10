# DANH SÁCH LINH KIỆN – SMART POWER GUARD

## 1. Bộ điều khiển trung tâm

| STT | Linh kiện | Số lượng | Giao tiếp | Chức năng |
|---|---|---:|---|---|
| 1 | ESP32-S3 N16R8 | 1 | GPIO, UART, I2C, Wi-Fi | Điều khiển trung tâm |

## 2. Khối cảm biến

| STT | Linh kiện | Số lượng | Giao tiếp | Chức năng |
|---|---|---:|---|---|
| 1 | PZEM-004T V3.0 | 1 | UART | Đo điện áp, dòng điện, công suất, điện năng |
| 2 | AHT20 | 1 | I2C | Đo nhiệt độ và độ ẩm |
| 3 | DS3231 RTC | 1 | I2C | Duy trì thời gian thực |

## 3. Khối chấp hành

| STT | Linh kiện | Số lượng | Giao tiếp | Chức năng |
|---|---|---:|---|---|
| 1 | Module Relay 4 kênh | 1 | GPIO | Đóng/ngắt độc lập 4 kênh tải |
| 2 | Buzzer 5V | 1 | GPIO qua Driver | Cảnh báo âm thanh |
| 3 | Transistor hoặc MOSFET Driver | 1 | GPIO | Điều khiển Buzzer |

Trong phiên bản V1, chỉ sử dụng một kênh Relay để điều khiển một ổ cắm. Ba kênh còn lại được dự phòng cho khả năng mở rộng.

## 4. Khối nguồn và bảo vệ

| STT | Linh kiện | Số lượng | Chức năng |
|---|---|---:|---|
| 1 | Ổ cắm điện đầu ra | 1–4 | Cấp điện cho thiết bị |
| 2 | Nguồn AC-DC cách ly | 1 | Cấp nguồn cho mạch điều khiển |
| 3 | Cầu chì | 1 bộ | Bảo vệ quá dòng |
| 4 | Thiết bị bảo vệ phù hợp | 1 bộ | Bảo vệ hệ thống điện |
| 5 | Vỏ hộp cách điện | 1 | Bảo vệ mạch và người sử dụng |
| 6 | PCB | 1 | Kết nối các linh kiện |

## 5. Khối truyền thông mở rộng

| STT | Linh kiện | Số lượng | Giao tiếp | Chức năng |
|---|---|---:|---|---|
| 1 | Module SIM 4G LTE | 1 | UART | Kết nối Internet dự phòng |
| 2 | SIM dữ liệu di động | 1 | Mạng di động | Cung cấp kết nối 4G |
| 3 | Anten LTE | 1 | RF | Thu phát tín hiệu di động |

Khối 4G được xem là chức năng nâng cao, chỉ tích hợp sau khi phiên bản Wi-Fi hoạt động ổn định.

## 6. Lưu ý kỹ thuật

- PZEM-004T V3.0 được sử dụng để đo điện năng tổng tại đầu vào hệ thống.
- AHT20 và DS3231 sử dụng chung bus I2C.
- PZEM giao tiếp với ESP32 thông qua UART.
- Cần kiểm tra mức logic, nguồn cấp và khả năng tương thích của từng module trước khi kết nối.
- Module Relay phải có định mức phù hợp với tải thực tế.
- Mạch điện lưới phải được thiết kế tách biệt với vùng điện áp thấp, có bảo vệ và cách điện phù hợp.

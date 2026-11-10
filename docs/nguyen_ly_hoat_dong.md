# NGUYÊN LÝ HOẠT ĐỘNG – SMART POWER GUARD

## 1. Tổng quan nguyên lý

SMART POWER GUARD hoạt động dựa trên ESP32-S3 làm bộ điều khiển trung tâm. Hệ thống thu thập dữ liệu từ các cảm biến, xử lý thông tin, điều khiển Relay và trao đổi dữ liệu với Node-RED Dashboard thông qua giao thức MQTT.

Người dùng có thể theo dõi thông số điện năng, trạng thái ổ cắm, nhiệt độ và độ ẩm, đồng thời gửi lệnh điều khiển từ máy tính hoặc điện thoại thông minh.

Hệ thống được thiết kế để các chức năng điều khiển và bảo vệ quan trọng vẫn hoạt động cục bộ khi mất kết nối Internet.

## 2. Quy trình khởi động hệ thống

Khi hệ thống được cấp nguồn:

1. Nguồn AC-DC cách ly cung cấp điện áp phù hợp cho mạch điều khiển.
2. ESP32-S3 khởi động và khởi tạo các thiết bị ngoại vi.
3. ESP32-S3 kiểm tra kết nối với PZEM-004T, AHT20 và DS3231.
4. Đọc thời gian thực từ DS3231.
5. Thiết lập trạng thái an toàn ban đầu cho Relay.
6. Kết nối Wi-Fi và MQTT Broker.
7. Bắt đầu chu kỳ thu thập dữ liệu và giám sát hệ thống.

Nếu không thể kết nối Internet, ESP32-S3 vẫn tiếp tục thực hiện các chức năng giám sát và bảo vệ cục bộ.

## 3. Quy trình thu thập dữ liệu cảm biến

### 3.1. Đo điện năng

PZEM-004T V3.0 được sử dụng để đo các thông số điện:

- Điện áp (V).
- Dòng điện (A).
- Công suất (W).
- Điện năng tiêu thụ (kWh).
- Tần số (Hz).
- Hệ số công suất.

Dữ liệu được truyền về ESP32-S3 thông qua giao tiếp UART.

ESP32-S3 xử lý và chuẩn hóa dữ liệu trước khi gửi lên MQTT Broker.

Trong phiên bản V1, PZEM được sử dụng để đo điện năng tổng của ổ cắm.

### 3.2. Đo nhiệt độ và độ ẩm

AHT20 thu thập dữ liệu nhiệt độ và độ ẩm bên trong hộp ổ cắm.

Dữ liệu được truyền đến ESP32-S3 thông qua giao tiếp I2C.

ESP32-S3 sử dụng thông tin nhiệt độ để đánh giá điều kiện hoạt động và phát hiện tình trạng nhiệt độ bất thường.

### 3.3. Duy trì thời gian thực

DS3231 cung cấp thời gian thực cho hệ thống.

ESP32-S3 đọc thời gian từ RTC để thực hiện các lịch bật/tắt ổ cắm đã được thiết lập trước đó.

Nhờ vậy, chức năng lập lịch không phụ thuộc hoàn toàn vào kết nối Internet.

## 4. Quy trình truyền dữ liệu MQTT

Sau khi thu thập và xử lý dữ liệu, ESP32-S3 đóng gói thông tin thành các bản tin MQTT và gửi đến MQTT Broker.

Node-RED đăng ký nhận các bản tin tương ứng để hiển thị trên Dashboard.

Các dữ liệu dự kiến truyền tải:

- Trạng thái ổ cắm.
- Điện áp và dòng điện.
- Công suất tiêu thụ.
- Điện năng tích lũy.
- Nhiệt độ và độ ẩm.
- Trạng thái cảnh báo.
- Trạng thái kết nối thiết bị.

Người dùng có thể gửi lệnh điều khiển từ Dashboard. Node-RED chuyển lệnh đến MQTT Broker, sau đó ESP32-S3 nhận và xử lý lệnh.

## 5. Quy trình điều khiển Relay

Khi người dùng nhấn nút bật/tắt trên Node-RED Dashboard:

1. Node-RED tạo bản tin điều khiển.
2. Bản tin được gửi đến MQTT Broker.
3. ESP32-S3 nhận bản tin thông qua MQTT.
4. ESP32-S3 kiểm tra trạng thái hệ thống và điều kiện bảo vệ.
5. Nếu lệnh hợp lệ và không có điều kiện khóa bảo vệ, ESP32-S3 điều khiển Relay.
6. Trạng thái mới được cập nhật và gửi lại Dashboard.

Trong phiên bản V1, một kênh Relay được sử dụng để điều khiển một ổ cắm đầu ra.

Ba kênh còn lại được dự phòng cho khả năng mở rộng.

## 6. Quy trình cảnh báo và bảo vệ

ESP32-S3 liên tục kiểm tra dữ liệu điện năng và nhiệt độ.

### 6.1. Cảnh báo quá tải

Khi dòng điện hoặc công suất vượt ngưỡng an toàn đã cấu hình:

1. ESP32-S3 phát hiện giá trị vượt ngưỡng.
2. Hệ thống chuyển sang trạng thái cảnh báo.
3. Buzzer phát tín hiệu âm thanh.
4. Hệ thống thực hiện quy trình ngắt tải nếu điều kiện bảo vệ yêu cầu.
5. Trạng thái cảnh báo được gửi lên Node-RED Dashboard.

### 6.2. Cảnh báo quá nhiệt

Khi nhiệt độ vượt ngưỡng cài đặt:

1. ESP32-S3 nhận dữ liệu nhiệt độ từ AHT20.
2. So sánh với ngưỡng nhiệt độ cho phép.
3. Kích hoạt cảnh báo bằng Buzzer.
4. Thực hiện ngắt Relay theo chính sách bảo vệ.
5. Gửi thông tin cảnh báo đến Dashboard.

Lưu ý: Chức năng bảo vệ bằng phần mềm không thay thế cầu chì, thiết bị bảo vệ quá dòng hoặc các biện pháp bảo vệ điện độc lập.

## 7. Quy trình hoạt động khi mất Internet

Khi hệ thống mất kết nối Wi-Fi hoặc MQTT Broker:

1. ESP32-S3 phát hiện trạng thái mất kết nối.
2. Các chức năng đọc cảm biến và giám sát cục bộ tiếp tục hoạt động.
3. Lịch bật/tắt được thực hiện dựa trên thời gian từ DS3231.
4. Các chức năng bảo vệ và cảnh báo tại chỗ vẫn được duy trì.
5. Dữ liệu cần thiết được lưu tạm trong bộ nhớ.
6. Khi kết nối được khôi phục, hệ thống kết nối lại MQTT và gửi dữ liệu còn tồn đọng.

Trong giai đoạn mở rộng, hệ thống sẽ nghiên cứu chuyển đổi từ Wi-Fi sang module SIM 4G LTE khi kết nối chính bị gián đoạn.

## 8. Hướng phát triển

Sau khi hoàn thiện phiên bản đầu tiên, hệ thống dự kiến được phát triển thêm:

- Mở rộng từ một lên bốn ổ cắm độc lập.
- Bổ sung khả năng giám sát điện năng theo từng ổ cắm.
- Nghiên cứu thuật toán phát hiện trạng thái tiêu thụ điện thấp kéo dài.
- Bổ sung kết nối dự phòng Wi-Fi và 4G LTE.
- Hoàn thiện giao diện giám sát và thống kê điện năng.
- Tối ưu cơ chế bảo vệ và vận hành độc lập khi mất Internet.


# IoT-HeThongGiamSat-KhongKhi
Xây dựng hệ thống giám sát chất lượng không khí trong nhà bằng ESP32 và 4 cảm biến (DHT22, BH1750, GP2Y1014AU, MQ135), điều khiển quạt DC 5V và LED RGB

**Hướng dẫn cài đặt và sử dụng hệ thống**
Yêu cầu phần cứng
  -	Bo mạch chính: ESP32 DevKit V1 (38 chân).
  -	Cảm biến:
    +	BH1750 (cường độ ánh sáng).
    +	DHT22 (nhiệt độ & độ ẩm).
    +	GP2Y1014AU (cảm biến bụi mịn PM2.5).
    +	MQ135 (khí độc hại & chất lượng không khí).
  -	Thiết bị điều khiển: Quạt lọc, LED RGB.
  -	Phụ kiện: Breadboard, dây cắm Dupont, nguồn 5V (USB hoặc adapter).
  -	Tiến hành nối mạch như sơ đồ


Yêu cầu phần mềm
  -	Arduino IDE (phiên bản ≥ 1.8.19 hoặc Arduino IDE 2.x).
  -	ESP32 Board Package (cài qua Board Manager).
  -	Thư viện cần thiết:
    +	BH1750 (đọc cường độ sáng).
    +	DHT (đọc DHT22).
    +	PubSubClient (MQTT).
    +	Wire (I2C).
      
-------------------------------------------------------------
Các bước cài đặt
  Bước 1: Mở Arduino IDE → vào File → Preferences → thêm link:
  https://dl.espressif.com/dl/package_esp32_index.json
  Bước 2: Vào Tools → Board Manager → tìm và cài esp32 by Espressif Systems.
  Bước 3: Cài đặt các thư viện trong Library Manager: BH1750, DHT, PubSubClient.
  Bước 4: Kết nối ESP32 với máy tính qua cáp USB.
  Bước 5: Mở file code KhongKhiTrongNha.ino → chỉnh sửa WiFi SSID, password, access_token MQTT.
    const char* ssid = "Tên wifi";                  
    const char* password = "Mật khẩu wifi";          
    const char* mqtt_server = "mqtt.thingsboard.cloud";
    const int   mqtt_port   = 1883;
    const char* access_token = "Token lấy trên thingsboard";
  Bước 6: Chọn board: ESP32 Dev Module, chọn cổng COM → Upload.
  Bước 7: Sau khi nạp xong, mở Serial Monitor để kiểm tra log.

--------------------------------------------------------------
Sử dụng hệ thống
  -	Sau khi khởi động, ESP32 sẽ:
    +	Kết nối WiFi → kết nối MQTT → log ra màn hình Serial.
    +	Đọc dữ liệu từ 4 cảm biến.
    +	Hiển thị trạng thái trên LED RGB:
      o	Xanh lá: môi trường an toàn.
      o	Đỏ: bụi/khí/nhiệt độ cao. 
      o	Xanh dương: cảnh báo sự cố.
    +	Điều khiển quạt lọc theo ngưỡng đã định.
    +	Gửi dữ liệu lên cloud ThingsBoard để người dùng theo dõi.
------------------------------------------------
Minh họa qui trình sử dụng ứng dụng
    +	Quy trình: Giám sát và cảnh báo chất lượng không khí
    +	Người dùng bật nguồn hệ thống.
      o	ESP32 tự động kết nối WiFi + MQTT.
    +	Hệ thống thu thập dữ liệu.
      o	BH1750 đo ánh sáng.
      o	DHT22 đo nhiệt độ, độ ẩm.
      o	GP2Y1014AU đo bụi mịn.
      o	MQ135 đo khí độc.
    +	Phân tích và điều khiển.
      o	Nếu bụi hoặc khí độc vượt ngưỡng → quạt bật.
      o	Nếu giá trị trở lại bình thường → quạt tắt.
      o	LED RGB đổi màu tùy trạng thái.
    +	Truyền dữ liệu lên Cloud.
      o	ESP32 gửi dữ liệu dạng JSON qua MQTT.
      o	Dashboard ThingsBoard hiển thị dạng gauge, chart.

Ví dụ:
Gauge nhiệt độ hiển thị 29.2°C.
Biểu đồ bụi PM2.5 tăng vọt khi đốt giấy.
    +	Người dùng theo dõi qua Dashboard.
      o	Đăng nhập vào ThingsBoard → chọn thiết bị.
      o	Quan sát giá trị cảm biến, lịch sử dữ liệu, biểu đồ thời gian thực.



  ------------------------------------------


**Sơ đồ kết nối phần cứng**
  -	Cảm biến DHT22 (đo nhiệt độ, độ ẩm)
    +	VCC → 3.3V (hoặc 5V, nhưng ESP32 dùng 3.3V an toàn hơn)
    +	GND → GND 
    +	DATA → GPIO 4 
  -	Cảm biến bụi GP2Y1014AU (Sharp Dust Sensor)
    +	LED (dây trắng) (chân điều khiển LED hồng ngoại) → GPIO 23
    +	VOUT (dây vàng) (ngõ ra tín hiệu analog) → GPIO 34
    +	VCC (dây đỏ) → 5V
    +	GND (dây đen) → GND
  -	Cảm biến khí MQ135
    +	AOUT (ngõ ra analog) → GPIO 35
    +	VCC → 5V
    +	GND → GND
  -	Cảm biến ánh sáng BH1750 (I2C)
    +	VCC → 3.3V
    +	GND → GND
    +	SDA → GPIO 21
    +	SCL → GPIO 22
  -	LED RGB (3 chân riêng, loại Cathode chung)
    +	Chân R → GPIO 25 (LED_R)
    +	Chân G → GPIO 26 (LED_G)
    +	Chân B → GPIO 27 (LED_B)
    +	Cathode chung → GND
  -	Quạt 5V (module relay 5V): Cắm quạt trực tiếp vào ESP sẽ khiến quạt hoạt động liên tục nên cần dùng module relay 5V để điều khiển quạt.
    -	Quạt:
      +	Quạt (dây đen) → GND (ESP32)
      +	Quạt (dây đỏ) → NO (module relay 5V)
    -	Module relay 5V 
      +	VCC → 5V (ESP32)
      +	GND → GND (ESP32)
      +	IN → GPOI8 (ESP32)
      +	COM → 5V
      +	NO → quạt (dây đỏ, đã nối ở trước)

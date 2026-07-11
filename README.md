Cảm biến sử dụng: icm20602

SPI là một giao thức truyền thông có tốc độ cao chuyên dùng để kết nối vi điều khiển với các thiết bị ngoại vi( ví dụ: màn hình OLED,....)

Truyền thông Full_Duplex: đây là kiểu giao tiếp mà hai thiết bị có thể vừa gửi vừa nhận. Đặc điểm này giúp tối ưu hóa băng thông và đẩy tốc độ truyền lên cao.
Khác biệt so với uart và i2c: SPI dùng 4 dây( MOSI( Master output slave input), MISO( Master input slave output), SS/CS( Chip select or Slave select), SCLK(Clock) so với hai dây ở i2c

Các đường dây tín hiệu trong giao thức SPI
  SCLK(Seril clock): Dây cấp xung nhịp giữ nhịp cho thiết bị Master tạo ra nhằm đồng bộ dữ liệu giữa hai bên.

  MOSI( Master output slave input): đường dây tín hiệu đi từ Master vào slave
  MISO( Master input Slave output): đường dây tín hiệu đi từ slave vào master

  SS/CS: chọn chip cần trao đổi dữ liệu

  Cấu hình SPI( CPOL và CPHA)

  CPOL( clock Polarity - cực tính xung nhịp): Quyết định xung clock ở trạng thái không làm gì sẽ ở mức cao hay mức thấp.
  CPHA( clock phase - pha xung nhịp): Quyết định dữ liệu sẽ được đọc/ lấy mẫu tại cạnh lên hay cạnh xuống của xung clock( đọc kĩ data sheet từng loại ic)

  Quá trình vật lý truyền nhận dữ liệu:

  B1: chọn slave
  B2: Tạo xung SCLK
  B3: truyền và nhận tùy vào cấu hình CPOL, CPHA đã cấu hình 
  B4: kéo cs lên 1 để kết thúc quá trình truyền nhận

  
-------------------------------------------------------------------------------------------------------------------------------
Thông số ICM20602

Thông sô nguồn cấp & phần cứng 
Điện áp hoạt động VDD 1.71V --> 3.45V 

Điện áp Logic giao điếp (VDDIO) 1.71V --> 3.45V. Mức logic của các chân giao điếp sẽ chạy theo điện áp cấp vào chân VDDIO này.
Dòng tiêu thụ: chế độ hoạt động full 6 trục xấp sỉ 2.79 mA
               chế độ tiết kiệm điện xấp xỉ 40uA
Giao thức truyền thông SPI
resolution: cả Gyro và Accel đều tích hợp bộ ADC 16-bit, dữ liệu thô đầu ra là dạng mã bù 2. Một trục gồm 2 byte dữ liệu(16bit) cao và thấp.
Gyroscope( cảm biến vận tốc góc):
  Tầm đo full-scale range - FSR: có thể cấu hình +- 250, +- 500, +- 1000, +- 2000 dps qua bit FS_SEL trong thanh ghi GYRO_CONFIG
    Accelermeter(cảm biến gia tốc):
    Tầm đo(FSR) có thể cấu hình: +-2g, +-4g, +-8g, +-16g qua bit Á_SEL trong thanh ghi iACCEL_CONFIG
    Độ nhạy mặc định ở tầm +- 2g: 16384 LSB/g 
Quản lý nguồn & Khởi động 
  Trạng thái mặc định khi khởi động chip tự rơi vào trạng thái sleep mode. bắt buộc phải ghi cấu hình vào thanh ghi quản lý nguồn để wake up chip trước khi thực hiện bất kì thao tác đọc dữ liệu nào.
  Thời gian khởi động(start-up time): sau khi bật nguồn hoặc phát lênh device_reset cần ít nhất 2ms trước khi tiến hành đọc ghi thanh ghi khác.
  Cấu hình xung Clock: Cấu hình bit CLKSEL[2:0] trong thanh ghi PWR_MGMT_1 thành 001
Cơ chế Interrupt & FIFO:
  Bộ đệm FiFO: dung lượng 1Kb (1008 bytes). Hỗ trợ chế độ đọc cụm(Brust Read) dữ liệu Gyro, accel, nhiệt độ để giảm tân số truy cập bus của MUC chủ, giúp hệ thống tiết kiệm pin. Có tính năng ngắt ngưỡng (FIFO Watermark).
  Các nguồn ngắt chính:
    DATA_RDY_INT: Kích hoạt khi có mẫu dữ liệu từ ADC.
    WOM_INT: Kích hoạt khi gia tốc một trong các trục vượt ngưỡng chỉ định.
  Bộ thanh ghi cần nhớ: 
  0x75 (WHO_AM_I): Thanh ghi đọc chỉ số định danh (Device ID).
  0x6B (PWR_MGMT_1): * Ghi 0x80 để Reset mềm toàn bộ chip (DEVICE_RESET)
  0x6C (PWR_MGMT_2): Cho phép Standby (tắt) riêng lẻ từng trục X, Y, Z của Gyro hoặc Accel nếu ứng dụng của bạn không sử dụng đến nhằm tối ưu dòng điện. 
  0x1B (GYRO_CONFIG): Cấu hình dải đo FSR ($\pm250$ đến $\pm2000\text{ dps}$).  
  0x1C (ACCEL_CONFIG): Cấu hình dải đo FSR ($\pm2g$ đến $\pm16g$).  
  0x3B đến 0x40 (ACCEL_XOUT_H $\rightarrow$ ACCEL_ZOUT_L): 6 thanh ghi liên tiếp chứa dữ liệu thô của Accelerometer. Bạn        nên thực hiện 1 lệnh đọc Burst Read 6 byte liên tục tại địa chỉ
  0x3B để lấy trọn vẹn dữ liệu gia tốc 3 trục nhằm tránh lệch pha thời gian giữa các trục.
  0x43 đến 0x48 (GYRO_XOUT_H $\rightarrow$ GYRO_ZOUT_L): 6 thanh ghi liên tiếp chứa dữ liệu thô của Gyroscope. Tương tự như     Accel, bạn nên thực hiện Burst Read 6 byte từ địa chỉ 0x43.

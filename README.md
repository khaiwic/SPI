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

  

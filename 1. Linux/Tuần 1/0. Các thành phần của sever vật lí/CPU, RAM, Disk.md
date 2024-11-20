# CPU, RAM, Disk	

## 1. CPU

CPU là bộ vi xử lí trung tâm của máy tính, giống như "bộ não" của hệ thống nơi thực hiện tính toán, điều khiển mọi tác vụ của hệ thống 

### Các thông số quan trọng để đánh giá hiệu năng của CPU

- Xung nhịp: Tốc độ xử lí của CPU (trên 1 nhân)

  ```
  cpu MHz		: 804.707
  #tốc độ xử của lí của 1 nhân cpu hiện tại 
  ```

- Core (nhân): CPU có càng nhiều core thì tốc độ xử lí của cả CPU càng nhanh

- Bộ nhớ đệm cache: nó giúp CPU xử lí dữ liệu nhanh hơn, về cơ bản CPU khi chạy một chương trình, phần mềm (nó sẽ bao gồm rất nhiều các câu lệnh cho CPU thực hiện) các câu lệnh này được lấy từ RAM do tốc độ của RAM chậm hơn so với CPU  vì vậy CPU sẽ phải chờ RAM chuyển hết tất cả các câu lệnh lên thì mới hoàn thành nhiệm vụ -> đây là lí do mà cache xuất hiện để lưu các bản sao dữ liệu mà ram đã gửi lên CPU vì nó nghĩ rằng CPU sẽ cần truy cập lặp đi lặp lại nhiều lần -> vì vậy mà khi CPU hoạt động nó luôn phải kiểm tra bộ nhớ cache trước để xem có data không nếu kh có thì nó phải quay ra RAM để lấy dữ liệu 

- ```
  cache size: 8192 KB là kích thước bộ nhớ cache L3 (chia sẻ giữa các lõi).
  ```

  để kiểm tra bộ nhớ cache chính xác hơn sử dụng lscpu

  - Cache hiện nay được chia làm 3L

    - L1: cache nhanh nhất, nhưng ít dung lượng nhất, nhúng trực tiếp vào CPU, chạy cùng tốc độ với CPU
    - L2: lưu lại dữ liệu đc truy cập gần đây của CPU mà kh được lưu bởi L1
    - L3: tương tự nó sẽ lưu các dữ liệu không được lưu bởi L2

    Cách kiểm tra Level của cache:

    ```
    lscpu | grep 'cache'
    ```

- Tubbo boot là khẳ năng CPU có thể tăng tốc độ xử lí 

- Kích thước bóng bán dẫn: bóng bán dẫn càng nhỏ thì tức là các tiết kiệm điện năng, càng có nhiều bóng bán dẫn tốc độ càng nhanh 

## 2. RAM

RAM (Random Access Memory) là bộ nhớ lưu trữ tạm thời. Các dữ liệu, chương trình máy tính được lưu trữ trong ổ cứng sẽ được tải vào RAM, sau khi tải vào RAM, CPU có thể truy cập và xử lí các dữ liệu này 

- đường dẫn sử dụng dữ liệu 64 bit và 32 bit được truyền tại 1 thời điểm, càng có nhiều dữ liệu bit được truyền đi trong 1 chu kỳ xung nhịp thì pc càng chạy nhanh 

- tốc độ của ram: ốc độ **2400 MT/s** là tốc độ truyền tải dữ liệu của bộ nhớ **DDR4** (Double Data Rate 4). MT/s là viết tắt của **Mega Transfers per second** (triệu phép truyền mỗi giây). Đây là một đơn vị đo lường cách bộ nhớ truyền tải dữ liệu, và nó mô tả số lần bộ nhớ có thể truyền dữ liệu mỗi giây.ốc độ xung đồng hồ là **2400 MT/s ÷ 2 = 1200 MHz**.

- ![image-20241120172403765](/home/phungna/.config/Typora/typora-user-images/image-20241120172403765.png)

- với 8 byte wide bus là 64bit 

- loại ram: 

  **DDR (Double Data Rate):**

  - **DDR1**: Là thế hệ đầu tiên của bộ nhớ DDR, đã khá lỗi thời và ít sử dụng hiện nay. Tốc độ tối đa của DDR1 thường từ 200 đến 400 MT/s.
  - **DDR2**: Cải thiện tốc độ và hiệu suất so với DDR1. Tốc độ thường từ 400 đến 1066 MT/s.
  - **DDR3**: Phổ biến trong các máy tính cá nhân và máy chủ trước khi DDR4 xuất hiện. Tốc độ có thể lên đến 2133 MT/s và điện áp sử dụng là 1.5V.
  - **DDR4**: Hiện tại là chuẩn bộ nhớ phổ biến nhất trong các máy tính và máy chủ, với tốc độ từ 1600 đến 3200 MT/s và điện áp thấp hơn (1.2V).
  - **DDR5**: Là thế hệ mới nhất với tốc độ cao hơn và hiệu suất tốt hơn. Tốc độ của DDR5 có thể vượt quá 6400 MT/s và hỗ trợ dung lượng bộ nhớ cao hơn.

cách kiểm tra thông số hoạt động của RAM

```
free -h
               total        used        free      shared  buff/cache   available
Mem:            15Gi       8.6Gi       3.9Gi       5.0Gi       8.3Gi       6.9Gi
Swap:          4.0Gi          0B       4.0Gi

```

- thông số buff/cache cả 2 loại bộ nhớ này giúp tăng hiệu suất hệ thống
  - **Buffer** (Bộ đệm) là vùng bộ nhớ tạm thời mà hệ điều hành sử dụng để **lưu trữ dữ liệu sắp được ghi vào ổ đĩa hoặc đọc từ ổ đĩa**.buff được sử dụng ví dụ bạn muốn lưu dữ liệu vào ổ cứng thì ram sẽ kh lưu ngay mà nó được dữ lại ở buff và lưu dần dần điều này giảm thiểu số lần truy cập vào ổ cứng 
  - **Cache** (Bộ nhớ đệm): giúp việc đọc dữ liệu nhanh hơn bằng cách lưu trữ dữ liệu mà hệ thống hoặc ứng dụng có khả năng sẽ sử dụng lại trong tương lai. Giúp giảm thời gian truy cập dữ liệu trong các lần sử dụng tiếp theo, thay vì phải đọc dữ liệu từ ổ đĩa (vốn rất chậm).

- available: là vùng bộ nhớ sẵn sàng nó sẽ bằng free + buff/cache có thể giải phóng ngay lập tức ở đây sẽ là 3.9g free và 3G buff/cache

  
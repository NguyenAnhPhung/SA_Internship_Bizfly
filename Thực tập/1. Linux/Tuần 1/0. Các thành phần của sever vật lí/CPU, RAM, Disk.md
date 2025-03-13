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

### **Vai trò của RAM trong quy trình**
1. **Lưu trữ tạm thời**:
   - RAM là nơi dữ liệu từ ổ cứng được **tạm thời lưu trữ** để CPU có thể xử lý nhanh hơn.
   - RAM giúp giảm thời gian trễ vì truy cập dữ liệu từ RAM nhanh hơn truy cập từ ổ cứng (do tốc độ của ổ cứng thấp hơn nhiều so với RAM).

2. **CPU xử lý dữ liệu trong RAM**:
   - CPU chỉ xử lý dữ liệu nằm trong RAM. Nếu dữ liệu chưa có trong RAM, nó phải được lấy từ ổ cứng và nạp vào RAM trước.
   - CPU không trực tiếp làm việc với ổ cứng trong hầu hết các tác vụ thông thường.

3. **Tương tác với ổ cứng và RAM**:
   - Hệ điều hành (OS) chịu trách nhiệm quản lý việc tải dữ liệu từ ổ cứng vào RAM và quyết định dữ liệu nào cần giữ trong RAM để tối ưu hiệu năng.



```
sudo dmidecode -t memory
```



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

thông số buff/cache cả 2 loại bộ nhớ này giúp tăng hiệu suất hệ thống
- **Buffer** (Bộ đệm) là vùng bộ nhớ tạm thời mà hệ điều hành sử dụng để **lưu trữ dữ liệu sắp được ghi vào ổ đĩa hoặc đọc từ ổ đĩa**.buff được sử dụng ví dụ bạn muốn lưu dữ liệu vào ổ cứng thì ram sẽ kh lưu ngay mà nó được dữ lại ở buff và lưu dần dần điều này giảm thiểu số lần truy cập vào ổ cứng 
- **Cache** (Bộ nhớ đệm): khi cpu muốn dữ liệu từ ram thì nó sẽ đọc dữ liệu từ cache ram trước vì ở cache ram sẽ lưu những dữ liệu được cho là sẽ sử dụng trong tương tại và phần cache ram này có tốc độ truy cập nhanh hơn so vơi ram (tốc độ của cache ram nhanh hơn so với ram vì nó ở gần cpu hơn)
- Khi CPU yêu cầu dữ liệu mà không có trong **L1, L2, L3 Cache** của **CPU**, **cache RAM** có thể cung cấp dữ liệu này. 

available: là vùng bộ nhớ sẵn sàng nó sẽ bằng free + buff/cache có thể giải phóng ngay lập tức ở đây sẽ là 3.9g free và 3G buff/cache

## 3. Disk 

Disk là một thiết bị lưu trữ dữ liệu trong máy tính có chức năng ghi đọc và lưu trữ dữ liệu.

### 3.1. Các loại Disk

#### 1. **HDD (Hard Disk Drive)**:

- **HDD** là loại đĩa cứng truyền thống sử dụng các đĩa từ tính (platters) để lưu trữ dữ liệu. Các đĩa này quay ở tốc độ cao và các đầu đọc/ghi sẽ truy cập dữ liệu trên bề mặt của các đĩa này.
- **Đặc điểm:**
  - Dung lượng lưu trữ lớn.
  - Giá thành rẻ hơn so với SSD cùng dung lượng.
  - Tốc độ đọc/ghi chậm hơn so với SSD.
  - Kích thước và trọng lượng lớn hơn.
- **Ứng dụng**: Lưu trữ dữ liệu lâu dài, phổ biến trong máy tính bàn, máy chủ, và các hệ thống yêu cầu dung lượng lớn với chi phí hợp lý.

#### 2. **SSD (Solid State Drive)**:

- **SSD** là loại ổ đĩa sử dụng bộ nhớ flash (NAND) để lưu trữ dữ liệu, thay vì sử dụng các đĩa quay như HDD. Vì vậy, SSD không có bộ phận cơ học, làm cho nó nhanh hơn và bền bỉ hơn.
- **Đặc điểm:**
  - Tốc độ đọc/ghi nhanh hơn nhiều so với HDD.
  - Tiết kiệm năng lượng và có ít tiếng ồn.
  - Kích thước và trọng lượng nhẹ hơn.
  - Giá thành đắt hơn so với HDD cùng dung lượng.
- **Ứng dụng**: Thường được sử dụng trong các máy tính cá nhân, laptop, và các hệ thống yêu cầu tốc độ truy xuất dữ liệu nhanh.

#### **Khi nào chọn SSD và HDD?**

- **Chọn SSD**: Nếu bạn cần tốc độ cao, hiệu suất tốt và sử dụng cho hệ điều hành, ứng dụng, hoặc game.
- **Chọn HDD**: Nếu bạn cần lưu trữ dữ liệu lớn với chi phí thấp, chẳng hạn như lưu video, ảnh, hoặc sao lưu.

**Lựa chọn tối ưu:** Một số hệ thống sử dụng kết hợp SSD (cho hệ điều hành và phần mềm) và HDD (cho lưu trữ dữ liệu lớn).

### 3.2. Giao diện 

**Giao diện** trong ngữ cảnh của ổ cứng và máy tính là **cách thức kết nối** giữa ổ cứng (hoặc thiết bị lưu trữ) và các phần cứng khác trong hệ thống (như bo mạch chủ hoặc máy tính) để **truyền tải dữ liệu**.

#### 1. Các loại giao diện phổ biến 

1. **SATA (Serial ATA)**

- **Tốc độ tối đa**: ~600 MB/s (SATA III).
- **Sử dụng**: HDD, SSD SATA.
- **Đặc điểm**: Là giao diện phổ biến cho ổ cứng HDD và SSD, dễ dàng cài đặt và sử dụng trong các máy tính cá nhân.

2. **PCIe (Peripheral Component Interconnect Express)**

- **Tốc độ tối đa**:
  - PCIe Gen 3 x4: ~3500 MB/s.
  - PCIe Gen 4 x4: ~7000 MB/s.
- **Sử dụng**: SSD NVMe.
- **Đặc điểm**: Giao diện hiện đại và tốc độ cao, thường được dùng cho SSD NVMe, cho phép tốc độ truyền dữ liệu rất nhanh.

3. **M.2**

- **Tốc độ tối đa**:
  - M.2 SATA: ~550 MB/s (giống SATA).
  - M.2 NVMe: ~3500–7000 MB/s.
- **Sử dụng**: SSD M.2 (cả SATA và NVMe).
- **Đặc điểm**: Kích thước nhỏ gọn, hỗ trợ cả SATA và NVMe, thường được sử dụng trong laptop và các máy tính nhỏ.

4. **USB (Loại USB 3.0 và mới hơn)**

- **Tốc độ tối đa**: 
  - USB 3.0: ~500 MB/s.
  - USB 3.1/3.2: ~1250 MB/s đến 2500 MB/s.
  - Thunderbolt 3/4: ~5000 MB/s.
- **Sử dụng**: Ổ cứng gắn ngoài (HDD, SSD).
- **Đặc điểm**: Dễ dàng kết nối với máy tính thông qua cổng USB, sử dụng cho các ổ cứng di động.

5. **SAS (Serial Attached SCSI)**

- **Tốc độ tối đa**: ~12 Gbps.
- **Sử dụng**: Ổ cứng trong máy chủ và hệ thống lưu trữ doanh nghiệp.
- **Đặc điểm**: Dành cho môi trường doanh nghiệp, tốc độ cao và độ tin cậy tốt.

#### 2. Phân biệt một cách dễ hiểu giữa tốc độ giao diện và tốc độ đọc ghi ổ cứng 

**1. Tốc độ của ổ cứng = tốc độ của xe tải**

- Xe tải (ổ cứng) có thể chở hàng nhanh hay chậm, tùy thuộc vào động cơ và thiết kế của nó:
  - **Xe tải chậm (HDD)**: Chở hàng từ từ, ví dụ 80–150 MB/s.
  - **Xe tải nhanh (SSD)**: Chở hàng rất nhanh, ví dụ 500–7000 MB/s.

=> **Tốc độ ổ cứng** là khả năng mà ổ cứng tự nó đọc/ghi dữ liệu.

------

**2. Tốc độ của giao diện = tốc độ con đường**

- Con đường (giao diện kết nối) có thể rộng hay hẹp:
  - **Đường hẹp (SATA)**: Chỉ cho phép xe tải chạy tối đa 600 MB/s, dù xe tải nhanh đến đâu.
  - **Đường rộng (PCIe NVMe)**: Rộng rãi, xe tải có thể chạy rất nhanh, ví dụ 7000 MB/s.

=> **Tốc độ giao diện** là khả năng tối đa mà đường truyền cho phép dữ liệu chạy qua.
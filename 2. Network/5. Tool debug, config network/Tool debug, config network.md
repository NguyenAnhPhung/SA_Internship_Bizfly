# Tool debug, config network

## 1. ip link, ip route, ip address

### 1.1. `ip link`

- **Chức năng:**

  - Quản lý các giao diện mạng (network interface) trên hệ thống.
  - Hiển thị thông tin chi tiết về các giao diện.
  - Thay đổi trạng thái của giao diện (up, down).

- **Cách dùng phổ biến:**

  - Hiển thị danh sách các giao diện:

    ```
    ip link hoặc ip l
    ```

  - Hiển thị thông tin chi tiết về một giao diện:

    ```
    ip l show eth0
    ```

  - Kích hoạt/vô hiệu hóa một giao diện:

    ```
    ip l set eth0 up
    ip l set eth0 down
    ```

  - Thay đổi tên của một giao diện:

    ```
    ip l set eth0 name new_name
    ```

### 2. `ip route`

- **Chức năng:**

  - Quản lý bảng định tuyến (routing table).
  - Thêm, xóa, sửa đổi các tuyến đường.
  - Hiển thị bảng định tuyến.

- **Cách dùng phổ biến:**

  - Hiển thị bảng định tuyến:

    ```
    ip route show
    ```

  - Thêm một tuyến đường mặc định:

    ```
    ip route add default via 192.168.1.1 dev eth0
    ```

  - Thêm một tuyến đường tĩnh:

    ```
    ip route add 192.168.2.0/24 via 192.168.1.254 dev eth0
    ```

  - Xóa một tuyến đường:

    ```
    ip route delete 192.168.2.0/24 or ip r d 192.168.2.0/24
    ```

### 3. `ip address`

- **Chức năng:**

  - Quản lý địa chỉ IP trên các giao diện mạng.
  - Cấu hình địa chỉ IP, netmask, gateway.

- **Cách dùng phổ biến:**

  - Hiển thị địa chỉ IP của một giao diện:

    Bash

    ```
    ip address show eth0
    ```

  - Cấu hình địa chỉ IP cho một giao diện:

    Bash

    ```
    ip address add 192.168.1.100/24 dev eth0
    ```

  - Xóa địa chỉ IP:

    Bash

    ```
    ip address delete 192.168.1.100/24 dev eth0
    ```

**Ví dụ thực tế:**

Giả sử bạn muốn cấu hình một giao diện mạng có tên `eth0` với địa chỉ IP `192.168.1.100/24`, gateway là `192.168.1.1` và tuyến đường mặc định qua giao diện này. Bạn có thể thực hiện như sau:

Bash

```
# Kích hoạt giao diện
ip link set eth0 up

# Cấu hình địa chỉ IP
ip address add 192.168.1.100/24 dev eth0

# Cấu hình tuyến đường mặc định
ip route add default via 192.168.1.1 dev eth0
```

**Ứng dụng:**

- **Cấu hình mạng tĩnh:** Khi bạn muốn thiết lập một mạng với các địa chỉ IP cố định.
- **Quản lý nhiều giao diện mạng:** Trên các máy chủ có nhiều card mạng.
- **Xử lý sự cố mạng:** Khi gặp vấn đề về kết nối mạng, bạn có thể sử dụng các lệnh này để kiểm tra và sửa chữa cấu hình.
- **Tạo các kịch bản tự động:** Để cấu hình mạng một cách tự động.

## 2. `arp` - bảng ánh xạ địa chỉ IP sang MAC

Lệnh `arp` trong Linux được sử dụng để quản lý bảng ARP (Address Resolution Protocol). Bảng ARP lưu trữ ánh xạ giữa địa chỉ IP và địa chỉ MAC của các thiết bị trong mạng cục bộ (LAN). 

### 2.1. Hiển thị bảng arp

```
arp -a
```

đầu ra tương ứng:

```
pc0898-MS (10.10.0.1) at 00:50:56:c0:00:02 [ether] on ens33
_gateway (10.10.0.2) at 00:50:56:fa:a1:bd [ether] on ens33
? (10.10.0.254) at 00:50:56:e2:92:dd [ether] on ens33
```

pc0898-MS: tên thiết bị trong mạng 

10.10.0.1: đc ip của thiết bị trong mạng 

00:50:56:c0:00:02: địa chỉ MAC của thiết bị 

ether:  Loại giao thức mạng (ethernet trong trường hợp này).

`on ens33`: Giao diện mạng của máy tính cục bộ mà thiết bị này được phát hiện.

**Tại sao máy tính của tôi lại có được bảng ARP này?**

- Nó đã giao tiếp hoặc nhận broadcast từ các thiết bị trong mạng cục bộ.
- Hệ thống tự động lưu thông tin IP-MAC của các thiết bị đã giao tiếp gần đây.
- Đây là một cơ chế bình thường và thiết yếu trong mạng Ethernet để đảm bảo việc giao tiếp hoạt động chính xác và hiệu quả.

ví dụ ngay cả việc ping từ máy này qua máy khác, hay việc cấp dhcp cũng là một cách học đc MAC nhưng chỉ có thể hiển thị các **máy có trong cùng mạng LAN**



### 2.2. Thêm, xóa trong arp

xóa 1 ánh xạ cụ thể 

```
sudo arp -d <IP-ADDRESS>

```

thêm 1 ánh xạ tĩnh vào arp

```
sudo arp -s <IP-ADDRESS> <MAC-ADDRESS>

```

xóa toàn bộ bảng arp 

```
ip -s -s neigh flush all

```

## 3. `telnet` - Kiểm tra kết nối mạng

telnet về cơ bản thì nó là một công cụ giống như ssh nhưng nó không được sử dụng nhiều vì thiếu tính bảo mật vì vậy mà nó chỉ sử dụng để kiểm tra kết nối mạng từ xa 

### 3.1. Cài đặt telnet 

```
sudo apt update
sudo apt install telnet

```

### 3.2. Cách sử dụng telnet

```
telnet <hostname> <port>

```

ví dụ

```
telnet example.com 80

```

- Nếu cổng mở, Telnet sẽ hiển thị kết nối thành công.
- Nếu cổng đóng hoặc không có phản hồi, Telnet sẽ không thể kết nối.

**gửi lệnh đến máy chủ từ xa** 

Bạn có thể sử dụng Telnet để gửi lệnh đến một máy chủ HTTP hoặc bất kỳ dịch vụ nào chạy trên một cổng cụ thể.

```
telnet example.com 80

```

Sau khi kết nối thành công, nhập:

```
GET / HTTP/1.1
Host: example.com

```

Nhấn **Enter hai lần** để gửi lệnh. Máy chủ sẽ trả về phản hồi HTTP.

### 3.3. Ứng dụng phổ biến của telnet 

**Kiểm tra kết nối mạng**:

- Xem một cổng trên máy chủ từ xa có mở không.
- Ví dụ: Kiểm tra cổng SMTP (25) của máy chủ email:

```
telnet mail.example.com 25

```

**Kiểm tra dịch vụ trên máy chủ**:

- Ví dụ: Kiểm tra dịch vụ MySQL trên cổng 3306.

```
telnet db.example.com 3306

```

**Truy cập và cấu hình thiết bị mạng**:

- Nhiều thiết bị như switch, router hỗ trợ giao diện Telnet để cấu hình từ xa.

**Kiểm tra phản hồi HTTP hoặc các dịch vụ khác**:

- Sử dụng Telnet để kiểm tra máy chủ web hoặc dịch vụ TCP khác.

## 4. `Traceroute` - Kiểm tra lộ trình mà gói tin đi từ máy của bạn đến máy đích	

`traceroute` là một công cụ mạnh mẽ để kiểm tra và chẩn đoán các vấn đề mạng. Nó cho phép bạn xem lộ trình mà gói tin đi qua từ máy tính của bạn đến một máy đích, đồng thời đo thời gian cần thiết để đến từng nút mạng trung gian.

### 4.1. Cài đặt traceroute

```
sudo apt update
sudo apt install inetutils-traceroute

```

### 4.2. Cách sử dụng traceroute

Kiểm tra lộ trình từ máy bạn đến một máy chủ đích:

```
traceroute <địa chỉ IP hoặc tên miền>

```

Ví dụ 

```
traceroute google.com

```

Kết quả sẽ hiển thị:

- Số thứ tự của mỗi "hop" (nút mạng).
- Địa chỉ IP hoặc tên miền của router trung gian.
- Thời gian đáp ứng (RTT - Round Trip Time) của mỗi router.

```
traceroute to google.com (142.250.71.238), 64 hops max
  1   10.10.0.2  0.010ms  0.010ms  0.009ms 
  2   192.168.18.2  1.905ms  4.422ms  0.589ms 
  3   10.0.1.2  0.009ms  1.198ms  0.009ms 
  4   27.68.226.173  2.778ms  1.832ms  2.959ms 
  5   116.104.81.232  3.888ms  4.192ms  3.630ms 
  6   27.68.194.93  4.379ms  3.298ms  3.650ms 
  7   *  *  * 
  8   27.68.255.101  50.208ms  48.747ms  48.225ms 
  9   125.212.189.26  47.154ms  47.423ms  47.521ms 
 10   74.125.119.136  46.916ms  45.990ms  46.823ms 
 11   *  *  * 
 12   209.85.143.152  46.289ms  45.763ms  46.144ms 
 13   142.250.71.238  45.268ms  45.911ms  44.774ms 
```

Giải thích 

Kết quả của lệnh `traceroute` hiển thị tuyến đường các gói tin đi từ máy tính của bạn tới đích (ở đây là `google.com`, địa chỉ IP: `142.250.71.238`). Mỗi dòng trong kết quả biểu diễn một bước (hop) trong hành trình, gồm thông tin về router hoặc thiết bị mạng mà gói tin đi qua và thời gian hồi đáp.

```
<Hop Number> <IP hoặc Hostname> <Thời gian trả lời lần 1> <Thời gian trả lời lần 2> <Thời gian trả lời lần 3>
```

------

#### **Giải thích chi tiết từng dòng:**

1. **Dòng 1:**

   ```
   1   10.10.0.2  0.010ms  0.010ms  0.009ms
   ```

   - **Hop 1**: Đây là router cục bộ (default gateway) của mạng nội bộ của bạn.
   - **10.10.0.2**: Địa chỉ IP của router.
   - **Thời gian**: 0.01ms là thời gian trả lời từ router này đến máy của bạn. Khoảng thời gian rất thấp, vì router này nằm trong cùng mạng LAN.

2. **Dòng 2:**

   ```
   2   192.168.18.2  1.905ms  4.422ms  0.589ms
   ```

   - **Hop 2**: Router tiếp theo trong mạng nội bộ hoặc ISP.
   - **192.168.18.2**: Địa chỉ IP của router, thuộc dải IP nội bộ (private).
   - **Thời gian**: Thời gian phản hồi lớn hơn một chút vì gói tin phải qua thêm một bước.

3. **Dòng 3:**

   ```
   3   10.0.1.2  0.009ms  1.198ms  0.009ms
   ```

   - **Hop 3**: Một router khác trong mạng nội bộ hoặc ISP của bạn.
   - **10.0.1.2**: Địa chỉ IP nội bộ.
   - **Thời gian**: Thời gian thấp cho thấy router này gần với mạng của bạn.

4. **Dòng 4:**

   ```
   4   27.68.226.173  2.778ms  1.832ms  2.959ms
   ```

   - **Hop 4**: Đây là router thuộc mạng của ISP (Internet Service Provider).
   - **27.68.226.173**: Địa chỉ IP công cộng, thuộc nhà mạng của bạn.
   - **Thời gian**: Khoảng 2-3ms, vì router này vẫn gần máy của bạn.

5. **Dòng 5:**

   ```
   5   116.104.81.232  3.888ms  4.192ms  3.630ms
   ```

   - **Hop 5**: Router tiếp theo trong mạng ISP.
   - **116.104.81.232**: IP công cộng thuộc ISP.
   - **Thời gian**: Tăng nhẹ do khoảng cách lớn hơn.

6. **Dòng 6:**

   ```
   6   27.68.194.93  4.379ms  3.298ms  3.650ms
   ```

   - **Hop 6**: Router khác trong mạng ISP.
   - **27.68.194.93**: IP công cộng.
   - **Thời gian**: Khoảng 3-4ms, vẫn hợp lý trong mạng ISP.

7. **Dòng 7:**

   ```
   7   *  *  * 
   ```

   - **Hop 7**: Không có phản hồi từ router này.
   - **`\*`**: Có thể router này chặn ICMP (giao thức mà `traceroute` sử dụng), hoặc mất gói tin.
   - Điều này không gây ảnh hưởng, vì gói tin vẫn tiếp tục đến các bước sau.

8. **Dòng 8:**

   ```
   8   27.68.255.101  50.208ms  48.747ms  48.225ms
   ```

   - **Hop 8**: Một router xa hơn trong mạng ISP hoặc điểm trao đổi với mạng quốc tế.
   - **Thời gian**: Tăng đáng kể (khoảng 50ms), do khoảng cách xa hoặc qua nhiều mạng trung gian.

9. **Dòng 9:**

   ```
   9   125.212.189.26  47.154ms  47.423ms  47.521ms
   ```

   - **Hop 9**: Router thuộc mạng quốc tế, nằm gần hệ thống của Google.
   - **Thời gian**: 47ms, hợp lý với khoảng cách đến các server quốc tế.

10. **Dòng 10:**

    ```
    10   74.125.119.136  46.916ms  45.990ms  46.823ms
    ```

    - **Hop 10**: Router gần server của Google.
    - **Thời gian**: Tương tự như Hop 9, khoảng 45-46ms.

11. **Dòng 11:**

    ```
    11   *  *  * 
    ```

    - **Hop 11**: Không có phản hồi, lý do tương tự như Hop 7.

12. **Dòng 12:**

    ```
    12   209.85.143.152  46.289ms  45.763ms  46.144ms
    ```

    - **Hop 12**: Router trong mạng của Google.
    - **Thời gian**: Khoảng 45ms, gói tin đang tiến gần đến đích.

13. **Dòng 13:**

    ```
    13   142.250.71.238  45.268ms  45.911ms  44.774ms
    ```

    - **Hop 13**: Máy chủ đích của Google.
    - **Thời gian**: Khoảng 45ms, đây là thời gian phản hồi từ máy chủ đích.

### 4.3. Các tùy chọn phổ biến 

**Sử dụng ICMP thay vì UDP (mặc định)**:

```
traceroute -I <địa chỉ>

```

ICMP thường được dùng khi UDP bị chặn bởi tường lửa.

**Giới hạn số lượng bước (hop)**:

```
traceroute -m <số bước> <địa chỉ>
traceroute -m 10 google.com

```

mặc định là 30 bước:

### 4.4. ứng dụng của Traceroute 

**Xác định vị trí sự cố mạng:**

- Xem tại bước nào gói tin bị chặn hoặc gặp trễ.
- Ví dụ, nếu kết nối đến một máy chủ bị lỗi, bạn có thể dùng `traceroute` để kiểm tra nút mạng nào gây ra vấn đề.

**Phân tích hiệu suất mạng:**

- Đo thời gian trễ giữa các router trong lộ trình.
- Tìm hiểu về hiệu suất của từng phân đoạn mạng.

**Kiểm tra kết nối quốc tế:**

- Xem lộ trình gói tin đi qua các quốc gia hoặc vùng địa lý khác nhau.

**Chẩn đoán tường lửa hoặc vấn đề lọc mạng:**

- Nếu một bước (hop) không trả về kết quả, điều đó có thể chỉ ra rằng router đó bị chặn bởi tường lửa.

**Hỗ trợ thiết lập mạng:**

- Traceroute giúp kiểm tra xem mạng có được định tuyến đúng cách hay không.

### **Một số lưu ý**

- **Router có thể chặn ICMP hoặc UDP:** Một số router được cấu hình để không trả về phản hồi cho `traceroute`. Bạn sẽ thấy các dấu `* * *` trong kết quả.
- **Không phải tất cả các bước đều hiển thị hostname:** Đôi khi chỉ hiển thị địa chỉ IP nếu hostname không được phân giải.

## 5. `MTR` trong linux

`MTR` (My Traceroute hoặc Matt's Traceroute) là một công cụ mạng kết hợp tính năng của cả `ping` và `traceroute`. Nó không chỉ hiển thị đường đi của gói tin từ máy bạn đến một đích, mà còn liên tục đo hiệu suất của mỗi bước (hop) trên lộ trình, giúp chẩn đoán mạng hiệu quả hơn.

### 5.1. Cài đặt MTR

```
sudo apt update
sudo apt install mtr

```

### 5.2. Cách sử dụng MTR 

```
mtr <địa chỉ IP hoặc tên miền>

```

```
mtr google.com

```

![image-20241212162128396](/home/phungna/.config/Typora/typora-user-images/image-20241212162128396.png)

**Bỏ qua phân giải tên miền (hiển thị địa chỉ IP thay vì hostname)**:

```
mtr --no-dns <địa chỉ>

```

### 5.2. **Ý nghĩa các cột trong MTR**

| **Cột**   | **Ý nghĩa**                                                  |
| --------- | ------------------------------------------------------------ |
| **Host**  | Địa chỉ IP hoặc hostname của mỗi bước (hop).                 |
| **Loss%** | Tỉ lệ mất gói tin tại mỗi hop.                               |
| **Snt**   | Số gói tin đã gửi đến hop đó.                                |
| **Last**  | Thời gian phản hồi (RTT) của gói tin cuối cùng.              |
| **Avg**   | Thời gian phản hồi trung bình từ hop đó.                     |
| **Best**  | Thời gian phản hồi nhanh nhất (RTT thấp nhất).               |
| **Wrst**  | Thời gian phản hồi chậm nhất (RTT cao nhất).                 |
| **StDev** | Độ lệch chuẩn của thời gian phản hồi (biến động của kết quả). |

### 5.3. **Ứng dụng phổ biến của MTR**

1. **Chẩn đoán sự cố mạng**:
   - Xác định vị trí xảy ra mất gói tin hoặc trễ cao trong mạng.
   - Ví dụ: Nếu bạn gặp mất gói tại một hop cụ thể, có thể router đó hoặc đường truyền đang gặp sự cố.
2. **Kiểm tra hiệu suất mạng**:
   - Đánh giá mức độ ổn định và tốc độ phản hồi của mạng.
3. **Phân tích kết nối đến máy chủ từ xa**:
   - Kiểm tra xem đường truyền đến một máy chủ có bị gián đoạn ở bước nào.
4. **Xác minh tường lửa hoặc cấu hình mạng**:
   - Xem liệu các gói tin có bị chặn bởi tường lửa hoặc bị định tuyến sai.

### 5.4. So sánh MTR với Traceroute

| **Tiêu chí**                | **MTR**                                | **Traceroute**                        |
| --------------------------- | -------------------------------------- | ------------------------------------- |
| **Phản hồi thời gian thực** | Có                                     | Không                                 |
| **Thống kê chi tiết**       | Có (mất gói, thời gian trung bình,...) | Không                                 |
| **Mục đích chính**          | Giám sát mạng liên tục                 | Chẩn đoán đường truyền tĩnh           |
| **Khả năng tương tác**      | Có (cập nhật kết quả trực tiếp)        | Không (kết quả cố định khi chạy xong) |

## 6. `netstat` - theo dõi các port, giao thức trong mạng

`netstat` (Network Statistics) là một công cụ dòng lệnh trong Linux dùng để hiển thị các thông tin liên quan đến kết nối mạng và các giao thức mạng. Nó cung cấp cái nhìn tổng quan về các kết nối đang hoạt động, các cổng đang mở và các giao thức mạng mà hệ thống đang sử dụng. `netstat` giúp chẩn đoán và kiểm tra mạng, đặc biệt hữu ích khi bạn muốn xem trạng thái của các kết nối mạng hoặc phân tích tường lửa.

### 6.1. Cài đặt netstat

```
sudo apt update
sudo apt install net-tools

```

### 6.2. Cách sử dụng netstat cơ bản 

```
netstat

```

Lệnh này sẽ hiển thị một danh sách các kết nối mạng với các thông tin cơ bản như: trạng thái kết nối, địa chỉ cổng, địa chỉ IP.

![image-20241212164156932](/home/phungna/.config/Typora/typora-user-images/image-20241212164156932.png)

#### Phân tích kết quả của lệnh netstat

**Tiêu đề:**

- **Active Internet connections (w/o servers):** Hiển thị các kết nối mạng đang hoạt động (không bao gồm các server).
- **Active UNIX domain sockets (w/o servers):** Hiển thị các kết nối socket Unix đang hoạt động (không bao gồm các server).

**Các cột trong bảng:**

- **Proto:** Giao thức sử dụng (ví dụ: tcp, unix). (unix là một giao thức giao tiếp nội bộ không qua mạng)
- **Recv-Q:** Số lượng gói tin đang chờ được nhận.
- **Send-Q:** Số lượng gói tin đang chờ được gửi.
- **Local Address:** Địa chỉ IP và cổng của máy cục bộ tham gia vào kết nối.
- **Foreign Address:** Địa chỉ IP và cổng của máy bên ngoài tham gia vào kết nối.
- **State:** Trạng thái của kết nối (ví dụ: ESTABLISHED, LISTEN, CLOSE_WAIT).
- **RefCnt:** Số lượng tham chiếu đến socket.
- **Flags:** Các cờ liên quan đến socket.
- **Type:** Loại socket (ví dụ: DGRAM).
- **State:** Trạng thái của socket.
- **I-Node:** Số hiệu inode của file hệ thống liên quan đến socket.
- **Path:** Đường dẫn đến file hệ thống liên quan đến socket.

#### 6.2.1. Hiển thị kết nố đang mở (Listening port)

```
netstat -l 
```

thêm 

`-u` nếu muốn xem các port udp

`-t` nếu muốn xem các port tcp

```
netstat -tuln
```

`-n` hiển thị số thay vì hiển thị tên (domain))

#### 6.2.2. Hiển thị giao thức mạng 

udp 

```
netstat -u
```

tcp

```
netstat -t
```

thống kê giao thức mạng 

```
netstat -s
```

kiểm tra bảng định tuyến 

```
netstat -r
```

### 6.3. Ứng dụng của netstat 

**Chẩn đoán kết nối mạng**:

- Giúp kiểm tra kết nối giữa hệ thống của bạn và các máy chủ từ xa. Bạn có thể xem các kết nối hiện tại và trạng thái của chúng.

**Kiểm tra các cổng đang lắng nghe**:

- Nếu bạn cần đảm bảo rằng một dịch vụ mạng như HTTP (port 80) hoặc SSH (port 22) đang chạy và lắng nghe kết nối, `netstat` có thể cung cấp thông tin chi tiết.

**Xác định ứng dụng sử dụng cổng**:

- Nếu bạn muốn biết một ứng dụng nào đó đang sử dụng một cổng cụ thể (ví dụ, nếu có sự xung đột cổng), `netstat` có thể giúp bạn tìm ra PID của ứng dụng đó.

**Kiểm tra bảng định tuyến và cấu hình mạng**:

- Giúp bạn kiểm tra bảng định tuyến để xác nhận các tuyến đường mà các gói tin sẽ đi qua.

**Kiểm tra tường lửa**:

- `netstat` cũng có thể giúp bạn xác định các cổng mà tường lửa của hệ thống có thể chặn hoặc cho phép.



## 7. `tcpdumb` - bắt và phân tích gói tin

### 7.1. Cài đặt tcpdumb

```
sudo apt update
sudo apt install tcpdump

```

### **Tcpdump trong Linux**

`tcpdump` là một công cụ dòng lệnh mạnh mẽ để bắt và phân tích gói tin mạng. Nó giúp bạn theo dõi, phân tích lưu lượng mạng và kiểm tra các gói tin đang đi qua một giao diện mạng cụ thể trên hệ thống. Với `tcpdump`, bạn có thể bắt các gói tin và lọc chúng theo nhiều tiêu chí khác nhau, giúp bạn hiểu rõ về các giao thức mạng, tìm sự cố và giám sát mạng.

------

### **Cách cài đặt Tcpdump**

Trên hầu hết các hệ điều hành Linux, bạn có thể cài đặt `tcpdump` thông qua trình quản lý gói:

- **Debian/Ubuntu**:

  ```bash
  sudo apt update
  sudo apt install tcpdump
  ```

- **CentOS/RHEL**:

  ```bash
  sudo yum install tcpdump
  ```

- **Fedora**:

  ```bash
  sudo dnf install tcpdump
  ```

- **Arch Linux**:

  ```bash
  sudo pacman -S tcpdump
  ```

------

### **Cách sử dụng Tcpdump**

#### **1. Câu lệnh cơ bản**

Để bắt các gói tin từ giao diện mạng mặc định, sử dụng lệnh đơn giản:

```bash
sudo tcpdump
```

Lệnh này sẽ bắt đầu ghi lại và hiển thị tất cả các gói tin trên giao diện mạng mặc định của bạn. Bạn sẽ thấy các gói tin với thông tin chi tiết như địa chỉ nguồn, đích, và giao thức.

------

#### **2. Bắt gói tin từ một giao diện mạng cụ thể**

Nếu hệ thống có nhiều giao diện mạng (ví dụ: `eth0`, `wlan0`, `lo`), bạn có thể chỉ định giao diện mạng mà bạn muốn bắt gói tin từ đó:

```bash
sudo tcpdump -i eth0
```

Để xem danh sách tất cả các giao diện mạng có sẵn, bạn có thể sử dụng lệnh sau:

```bash
sudo tcpdump -D
```

------

#### **3. Bắt gói tin với một bộ lọc**

Một trong những tính năng mạnh mẽ của `tcpdump` là khả năng lọc gói tin. Bạn có thể lọc theo địa chỉ IP, giao thức, cổng và nhiều yếu tố khác.

- **Lọc theo địa chỉ IP**:

  ```bash
  sudo tcpdump host 192.168.1.1
  ```

  Lệnh này sẽ chỉ bắt gói tin có địa chỉ nguồn hoặc đích là `192.168.1.1`.

- **Lọc theo địa chỉ IP nguồn hoặc đích**:

  - Địa chỉ nguồn:

    ```bash
    sudo tcpdump src 192.168.1.1
    ```

  - Địa chỉ đích:

    ```bash
    sudo tcpdump dst 192.168.1.1
    ```

- **Lọc theo giao thức** (TCP, UDP, ICMP, v.v.):

  - Lọc TCP

    :

    ```bash
    sudo tcpdump tcp
    ```

  - Lọc UDP

    :

    ```bash
    sudo tcpdump udp
    ```

  - Lọc ICMP (Ping)

    :

    ```bash
    sudo tcpdump icmp
    ```

- **Lọc theo cổng** (source port hoặc destination port):

  - Cổng nguồn:

    ```bash
    sudo tcpdump src port 80
    ```

  - Cổng đích:

    ```bash
    sudo tcpdump dst port 80
    ```

- **Kết hợp nhiều bộ lọc**: Bạn có thể kết hợp nhiều bộ lọc bằng cách dùng `and`, `or`, `not` để tùy chỉnh các điều kiện lọc:

  ```bash
  sudo tcpdump src 192.168.1.1 and dst port 80
  ```

------

#### **4. Hiển thị thông tin chi tiết của gói tin**

Để hiển thị thông tin chi tiết về các gói tin, bạn có thể sử dụng tùy chọn `-v`, `-vv` hoặc `-vvv`. Các tùy chọn này sẽ cho bạn mức độ chi tiết tăng dần.

- **Hiển thị thông tin chi tiết cơ bản**:

  ```bash
  sudo tcpdump -v
  ```

- **Hiển thị chi tiết hơn**:

  ```bash
  sudo tcpdump -vv
  ```

- **Hiển thị chi tiết nhất**:

  ```bash
  sudo tcpdump -vvv
  ```

------

#### **5. Ghi lại gói tin vào tệp**

Để lưu các gói tin đã bắt được vào một tệp thay vì hiển thị trực tiếp trên màn hình, sử dụng tùy chọn `-w`:

```bash
sudo tcpdump -w filename.pcap
```

Bạn có thể mở lại tệp này bằng cách sử dụng `tcpdump` hoặc công cụ khác như **Wireshark**.

```bash
sudo tcpdump -r filename.pcap
```

------

#### **6. Dừng quá trình bắt gói tin**

Để dừng quá trình bắt gói tin, bạn chỉ cần nhấn `Ctrl + C`.

------

#### **7. Giới hạn số lượng gói tin được bắt**

Để giới hạn số lượng gói tin mà `tcpdump` sẽ bắt, bạn có thể sử dụng tùy chọn `-c`:

```bash
sudo tcpdump -c 100
```

Lệnh này sẽ bắt 100 gói tin và sau đó dừng lại.

------

### **Ứng dụng phổ biến của Tcpdump**

1. **Phân tích sự cố mạng**:
   - Nếu bạn gặp sự cố mạng, `tcpdump` có thể giúp bạn theo dõi các gói tin và xác định nơi xảy ra sự cố, ví dụ như mất gói tin, độ trễ cao hoặc kết nối bị gián đoạn.
2. **Giám sát và bảo mật mạng**:
   - Giúp theo dõi hoạt động mạng và phát hiện các cuộc tấn công mạng hoặc các kết nối bất thường.
3. **Kiểm tra giao thức và dịch vụ**:
   - Kiểm tra xem các giao thức và dịch vụ như HTTP, DNS, FTP có hoạt động bình thường không bằng cách bắt các gói tin liên quan.
4. **Xác minh kết nối đến các máy chủ từ xa**:
   - Xác nhận các kết nối mạng và kiểm tra dữ liệu đang truyền tải giữa các máy tính hoặc máy chủ.
5. **Chẩn đoán vấn đề DNS hoặc HTTP**:
   - Nếu DNS không hoạt động, bạn có thể bắt gói tin DNS để kiểm tra quá trình truy vấn DNS. Tương tự, bạn có thể theo dõi các gói tin HTTP để xem liệu các yêu cầu và phản hồi có đúng không.

------

### **Một số ví dụ thực tế**

1. **Bắt gói tin từ giao diện mạng `eth0` và hiển thị thông tin chi tiết**:

   ```bash
   sudo tcpdump -i eth0 -v
   ```

2. **Bắt gói tin đến và đi từ địa chỉ IP 192.168.1.1**:

   ```bash
   sudo tcpdump host 192.168.1.1
   ```

3. **Bắt gói tin TCP từ cổng 80**:

   ```bash
   sudo tcpdump tcp port 80
   ```

4. **Lưu các gói tin vào tệp và sau đó đọc lại**:

   ```bash
   sudo tcpdump -w capture.pcap
   sudo tcpdump -r capture.pcap
   ```

------

### **Tổng kết**

`tcpdump` là một công cụ rất mạnh mẽ để giám sát và phân tích lưu lượng mạng trong Linux. Với khả năng lọc linh hoạt, chi tiết về từng gói tin và khả năng lưu trữ kết quả, `tcpdump` là công cụ quan trọng trong việc phân tích mạng và chẩn đoán các sự cố liên quan đến mạng.

Nếu bạn có thêm câu hỏi hoặc muốn tìm hiểu sâu hơn về các lệnh hoặc tính năng của `tcpdump`, đừng ngần ngại yêu cầu thêm nhé! 😊
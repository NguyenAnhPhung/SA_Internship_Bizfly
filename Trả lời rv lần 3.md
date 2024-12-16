# Cơ chế hoạt động của DNS

DNS là dịch vụ phân giải tên miền thành địa chỉ ip vì các máy tính giao tiếp với nhau qua ip chứ kp tên miền 

**Tại sao cần sử dụng đển DNS?**

- để cho dễ nhớ thay vì phải truy cập bằng địa chỉ ip thì có thể truy cập bằng tên miền 
- trong trường hợp trang web thay đổ máy chủ, thay đổi địa chỉ ip 

​	![https://media.geeksforgeeks.org/wp-content/uploads/20240619160023/Working-of-DNS.gif](https://media.geeksforgeeks.org/wp-content/uploads/20240619160023/Working-of-DNS.gif)

​	Nếu tên miền là **geekforgeek.stu.com**, quá trình phân giải DNS sẽ diễn ra tương tự nhưng cần thêm một cấp độ xử lý để tìm đúng **Authoritative DNS Server** chịu trách nhiệm cho miền con. Cụ thể như sau:

------

### **1. Bước 1: Host gửi yêu cầu đến DNS Resolver**

- Máy tính của người dùng gửi yêu cầu phân giải tên miền **geekforgeek.stu.com** đến **DNS Resolver**.

------

### **2. Bước 2: DNS Resolver hỏi Root DNS Server**

- DNS Resolver gửi yêu cầu đến **Root DNS Server**.
- **Root DNS Server** không biết địa chỉ IP của tên miền đầy đủ nhưng trả lời thông tin về **Top-Level DNS Server** của miền **.com**.

------

### **3. Bước 3: DNS Resolver hỏi Top-Level DNS Server (TLD)**

- DNS Resolver gửi yêu cầu đến **Top-Level DNS Server** của miền **.com**.
- **Top-Level DNS Server** không biết địa chỉ IP của **geekforgeek.stu.com** nhưng trả về thông tin về **Name Server** chịu trách nhiệm cho miền **stu.com**.

------

### **4. Bước 4: DNS Resolver hỏi Name Server của stu.com**

- DNS Resolver gửi yêu cầu đến **Name Server** chịu trách nhiệm cho miền **stu.com**.
- **Name Server** này không trực tiếp biết địa chỉ IP của **geekforgeek.stu.com** nhưng cung cấp thông tin về **Authoritative DNS Server** chịu trách nhiệm cho miền con **geekforgeek.stu.com**.

------

### **5. Bước 5: DNS Resolver hỏi Authoritative DNS Server của geekforgeek.stu.com**

- DNS Resolver gửi yêu cầu cuối cùng đến **Authoritative DNS Server** của **geekforgeek.stu.com**.
- Server này trả về địa chỉ IP chính xác của tên miền **geekforgeek.stu.com**.

------

### **6. Bước 6: DNS Resolver trả kết quả về Host**

- DNS Resolver nhận địa chỉ IP và gửi lại kết quả cho máy tính của người dùng.
- Máy tính sử dụng địa chỉ IP này để kết nối đến máy chủ đích.

------

### **Tóm tắt quy trình**

Quá trình phân giải DNS sẽ truy vấn qua nhiều cấp độ:

1. **Root DNS Server** → cung cấp thông tin **Top-Level DNS Server** của **.com**.
2. **Top-Level DNS Server** → cung cấp thông tin **Name Server** của **stu.com**.
3. **Name Server của stu.com** → cung cấp thông tin **Authoritative DNS Server** của **geekforgeek.stu.com**.
4. **Authoritative DNS Server** → trả về địa chỉ IP chính xác.

Quá trình này có thể có thêm nhiều bước tùy vào số lượng miền con (subdomain), nhưng cơ chế vẫn giống nhau.

Authoritative DNS Server là máy chủ DNS chịu trách nhiệm cung cấp thông tin chính xác về tên miền cụ thể mà bạn muốn truy cập. Nó không phải là máy chủ dịch vụ của nhà cung cấp website mà bạn truy cập, nhưng nó lưu trữ thông tin quan trọng về tên miền như:





# Gateway là gì? 

### **Gateway là gì?**

**Gateway** là một thiết bị phần cứng hoặc phần mềm trung gian được sử dụng để **kết nối và giao tiếp giữa các mạng** có giao thức, kiến trúc, hoặc định dạng dữ liệu **khác nhau**. Nó đóng vai trò như một "cổng giao tiếp" giúp các mạng không tương thích có thể trao đổi dữ liệu một cách hiệu quả.

Gateway không chỉ đơn thuần là một thiết bị chuyển tiếp dữ liệu, mà còn thực hiện chức năng **chuyển đổi giao thức** hoặc định dạng dữ liệu để các mạng hiểu và sử dụng dữ liệu của nhau.

------

### **Chức năng chính của Gateway**

1. **Chuyển đổi giao thức (Protocol Conversion):**

   - Gateway chuyển đổi dữ liệu giữa các giao thức khác nhau, ví dụ từ **TCP/IP** sang **IPX/SPX**, hoặc từ **HTTP** sang **MQTT**.
   - Chức năng này rất quan trọng trong các mạng sử dụng giao thức không tương thích.

   **Ví dụ:** Một thiết bị IoT dùng giao thức **MQTT** (Message Queuing Telemetry Transport) có thể gửi dữ liệu qua Gateway để chuyển sang giao thức **HTTP** cho một ứng dụng web.

------

1. **Kết nối mạng khác loại:**

   - Gateway kết nối các mạng với kiến trúc khác biệt, chẳng hạn như:
     - **Mạng LAN (Ethernet)** với **mạng viễn thông (SS7)**.
     - **Mạng công ty nội bộ** với **Internet**.

   **Ví dụ:** Một doanh nghiệp sử dụng Gateway để kết nối hệ thống nội bộ của họ với các dịch vụ đám mây bên ngoài.

------

1. Phân biệt giữa bên trong và bên ngoài mạng:
   - Gateway giúp phân biệt **dữ liệu nội bộ** (trong cùng một mạng) và **dữ liệu cần gửi ra ngoài** (ra mạng khác).
   - Dữ liệu cần gửi ra ngoài được Gateway xử lý và định dạng sao cho phù hợp với mạng đích.

------

1. **Bảo mật và kiểm soát truy cập:**

   - Gateway thường đi kèm với các tính năng bảo mật như kiểm soát quyền truy cập (Access Control) và ngăn chặn các kết nối không được phép giữa các mạng.

   **Ví dụ:** Một **Firewall Gateway** kiểm tra tất cả dữ liệu ra/vào mạng nội bộ, chỉ cho phép các kết nối được phê duyệt.

------

1. Tối ưu hóa truyền thông:
   - Gateway có thể nén dữ liệu, sửa đổi định dạng, hoặc thực hiện các thao tác khác để tối ưu hóa luồng dữ liệu giữa các mạng.

------

### **Ví dụ thực tế về Gateway**

1. **Internet Gateway:**
   - Là cổng kết nối mạng nội bộ (LAN) của bạn với Internet. Router của bạn đôi khi đóng vai trò Gateway, giúp mạng LAN chuyển đổi dữ liệu sang định dạng Internet sử dụng giao thức TCP/IP.
2. **VoIP Gateway:**
   - Chuyển đổi tín hiệu thoại truyền thống (analog) sang tín hiệu kỹ thuật số (digital) để sử dụng qua Internet (Voice over IP).
      **Ví dụ:** Một tổng đài VoIP kết nối điện thoại cố định với dịch vụ gọi qua Internet.
3. **IoT Gateway:**
   - Chuyển đổi dữ liệu từ các thiết bị IoT sử dụng giao thức như **MQTT** hoặc **CoAP** sang các giao thức như **HTTP** để giao tiếp với dịch vụ web hoặc đám mây.
4. **Email Gateway:**
   - Dùng để chuyển đổi email giữa các hệ thống không tương thích, ví dụ từ giao thức SMTP sang X.400.

------

### **Chức năng khác biệt giữa Gateway và Router**

- **Router:** Tập trung vào việc tìm tuyến đường tốt nhất để dữ liệu đi qua giữa các mạng có giao thức **tương thích**.
- **Gateway:** Làm việc với các mạng có giao thức hoặc cấu trúc **không tương thích**, giúp dịch và chuyển đổi dữ liệu.

------

### **Tóm lại về Gateway**

- Gateway là một **cầu nối thông minh** giữa các mạng sử dụng các giao thức khác nhau, giúp các hệ thống không đồng nhất giao tiếp được với nhau.
- Các chức năng chính của Gateway bao gồm **chuyển đổi giao thức**, **kết nối các mạng khác loại**, và **bảo mật dữ liệu truyền qua các mạng**.

Gateway rất quan trọng trong các môi trường mạng phức tạp, đặc biệt là trong doanh nghiệp, hệ thống IoT, và môi trường công nghệ đa giao thức.



# DCHP là gì? cách hoạt động?

​	**DHCP** (Dynamic Host Configuration Protocol) là giao thức hoạt động ở tầng ứng dụng của giao thức UDP. Mục đích chính của DHCP là tự động cấp phát địa chỉ IP và thông tin cấu hình TCP/IP cho các thiết bị trong mạng.

### **Nguyên lý hoạt động của DHCP**

DHCP hoạt động theo mô hình client-server và sử dụng các dịch vụ của UDP. Giao thức này sử dụng cổng **67** cho máy chủ và cổng **68** cho máy khách. Địa chỉ IP được cấp phát từ một **pool địa chỉ** (kho địa chỉ) được định nghĩa trước.

Trong quá trình giao tiếp, client và server trao đổi 4 thông điệp chính, được gọi là quá trình **DORA**:

1. **Discover**
2. **Offer**
3. **Request**
4. **Acknowledge**

Tuy nhiên, tổng cộng có **8 loại thông điệp DHCP**, bao gồm:

------

### **8 thông điệp trong DHCP**

#### **1. DHCP Discover Message**

- **Mục đích**: Tìm kiếm máy chủ DHCP trong mạng.

- Cách hoạt động

  :

  - Client phát đi thông điệp broadcast với địa chỉ IP nguồn là `0.0.0.0` (chưa có IP) và địa chỉ IP đích là `255.255.255.255` (broadcast).
  - Địa chỉ MAC nguồn là MAC của client, còn địa chỉ MAC đích là `FF:FF:FF:FF:FF:FF` (broadcast MAC).

#### **2. DHCP Offer Message**

- **Mục đích**: Server phản hồi với một đề nghị, cung cấp một địa chỉ IP chưa được sử dụng và các thông tin cấu hình TCP/IP khác.

- Cách hoạt động

  :

  - Server gửi thông điệp broadcast với địa chỉ IP nguồn là IP của server và địa chỉ IP đích là `255.255.255.255`.
  - Địa chỉ MAC nguồn là MAC của server, và MAC đích là MAC của client.

#### **3. DHCP Request Message**

- **Mục đích**: Client chấp nhận đề nghị từ server và gửi yêu cầu xác nhận.

- Cách hoạt động

  :

  - Client phát đi một thông điệp broadcast yêu cầu sử dụng địa chỉ IP được đề nghị.
  - Trước khi gửi, client sử dụng ARP để kiểm tra xem địa chỉ IP có bị trùng lặp hay không.

#### **4. DHCP Acknowledge Message**

- **Mục đích**: Server xác nhận và cấp phát địa chỉ IP với thời gian thuê (lease time).

- Cách hoạt động

  :

  - Server lưu thông tin của client với địa chỉ IP được cấp và các thông tin cấu hình TCP/IP.
  - Sau đó, server gửi thông điệp xác nhận (ACK) đến client.

------

#### **5. DHCP Negative Acknowledgment (NAK)**

- **Mục đích**: Server thông báo rằng địa chỉ IP yêu cầu không hợp lệ hoặc không còn địa chỉ nào khả dụng.

#### **6. DHCP Decline**

- **Mục đích**: Client từ chối địa chỉ IP được cung cấp do phát hiện địa chỉ đã bị sử dụng bởi thiết bị khác (qua kiểm tra ARP).

#### **7. DHCP Release**

- **Mục đích**: Client gửi thông báo đến server để giải phóng địa chỉ IP và hủy thời gian thuê còn lại.

#### **8. DHCP Inform**

- **Mục đích**: Client đã được gán IP thủ công nhưng vẫn cần lấy thông tin cấu hình khác (như domain name).
- **Cách hoạt động**: Client gửi thông điệp yêu cầu thông tin (Inform), và server phản hồi bằng thông điệp ACK với các thông tin cấu hình.

------

### **Tóm tắt quy trình DORA**

1. **Discover**: Client phát hiện server.
2. **Offer**: Server đề nghị địa chỉ IP.
3. **Request**: Client yêu cầu xác nhận địa chỉ IP.
4. **Acknowledge**: Server xác nhận và cấp phát IP.

**DHCP Offer** là một bước trung gian quan trọng để:

1. Đảm bảo client và server thỏa thuận một cách rõ ràng về địa chỉ IP và thông số cấu hình.
2. Tránh các vấn đề xung đột địa chỉ IP.
3. Cho phép client lựa chọn server phù hợp trong môi trường mạng có nhiều DHCP server.

# SSh bằng key 

| **Public Key** (Khóa công khai) | **Private Key** (Khóa riêng tư) |
| ------------------------------- | ------------------------------- |
|                                 |                                 |

| Là khóa **công khai**, có thể chia sẻ tự do với bất kỳ ai. | Là khóa **bí mật**, chỉ chủ sở hữu giữ và không được tiết lộ. |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

| Được sử dụng để **mã hóa dữ liệu** hoặc **xác thực chữ ký số**. | Được sử dụng để **giải mã dữ liệu** hoặc **tạo chữ ký số**. |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
|                                                              |                                                             |

tạo khóa 

```
sudo ssh-keygen -t rsa

```

cp khóa đến máy chủ

```
sudo ssh-copy-id nameserver@ip
```


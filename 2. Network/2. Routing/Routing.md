# Routing 

## 1. Routing là gì?

### 1.1. Khái niệm

Định tuyến (Routing) là quá trình các router (bộ định tuyến) quyết định đường đi tối ưu để gửi gói tin IP đến đúng đích trong mạng. Đây là một phần thiết yếu của mạng vì nó đảm bảo rằng các gói tin di chuyển hiệu quả giữa các thiết bị trong cùng một mạng hoặc giữa các mạng khác nhau.

- Router sẽ lưu tất cả các tuyến đường đến tất cả các điểm đến đã biết của chúng trong bảng định tuyến 
- Khi một router nhận được một gói tin, nhiệm vụ của nó là **gửi gói tin này đến đích**. Điều này được thực hiện bằng cách kiểm tra **bảng định tuyến**.

**Ví dụ:** Nếu gói tin cần đến địa chỉ IP **192.168.1.10**, router sẽ kiểm tra trong bảng định tuyến để xác định cách gửi gói tin đến địa chỉ này.

### 1.2. Hai loại bảng định tuyến chính 

- **Bảng MAC trên switch:**
  Switch sử dụng bảng MAC để định tuyến trong cùng một mạng LAN. Nó dựa trên địa chỉ MAC của các thiết bị.
- **Bảng định tuyến trên router:**
  Router sử dụng bảng định tuyến, dựa trên **địa chỉ IP** và các mạng mà nó biết để gửi gói tin ra ngoài hoặc đến đích.

### 1.**3. Hai loại thông tin trong bảng định tuyến:**

1. **Connected Routes (C):**
   - Là thông tin về các mạng kết nối trực tiếp với router thông qua các giao diện vật lý (interface).
   - Ví dụ: Nếu giao diện G0/1 trên router có IP **192.168.1.1/24**, thì mạng **192.168.1.0/24** sẽ được thêm tự động vào bảng định tuyến.
2. **Local Routes (L):**
   - Là thông tin về các địa chỉ IP cụ thể được cấu hình trên các giao diện của router.
   - Ví dụ: Nếu G0/1 được cấu hình với IP **192.168.1.1**, thì route **192.168.1.1/32** (địa chỉ cụ thể của giao diện) sẽ được thêm vào bảng định tuyến.

### **1.4. Phương pháp định tuyến:**

Có hai phương pháp để router "học" các route:

#### a. **Định tuyến động (Dynamic Routing):**

- Router sử dụng các giao thức định tuyến (như OSPF, RIP) để tự động chia sẻ thông tin định tuyến giữa các router khác.
- Ưu điểm: Tự động, dễ quản lý khi mạng lớn.
- Nhược điểm: Cần tài nguyên xử lý.

#### b. **Định tuyến tĩnh (Static Routing):**

- Quản trị viên cấu hình thủ công các route.
- Ưu điểm: Ít tốn tài nguyên, đơn giản khi mạng nhỏ.
- Nhược điểm: Không linh hoạt, khó quản lý khi mạng lớn.

### **1.5. Hoạt động của định tuyến:**

Khi router nhận được một gói tin, nó sẽ thực hiện các bước sau:

1. **Kiểm tra bảng định tuyến:**
   Router sẽ so sánh địa chỉ IP đích trong gói tin với các mục trong bảng định tuyến để tìm ra route phù hợp nhất.
2. **Chọn route:**
   - Nếu có nhiều route phù hợp, router sẽ chọn route có độ ưu tiên cao hơn hoặc "cụ thể nhất" (most specific route).
   - Ví dụ: Route **192.168.1.0/24** bao gồm tất cả các địa chỉ từ **192.168.1.0** đến **192.168.1.255**, nhưng route **192.168.1.1/32** chỉ dành riêng cho IP **192.168.1.1**. Route **192.168.1.1/32** được ưu tiên vì cụ thể hơn.
3. **Gửi gói tin:**
   Gói tin sẽ được gửi đi thông qua giao diện tương ứng với route.

# Routing (redhat)

tham khảo tại [redhat](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/networking_guide/ch-configuring_static_routes_and_the_default_gateway#sec-Introduction_to_Understanding_Routing_and_Gateway)

## 1. Routing and default gateway

### 1.1. Routing 

Routing là một cơ chế cho phép hệ thống tìm đường truyền mạng để kết nối đến một hệ thống khác. Thông thường, việc định tuyến được thực hiện bởi các thiết bị chuyên dụng trong mạng như router, nhưng bất kỳ thiết bị nào cũng có thể được cấu hình để thực hiện định tuyến nếu cần. Trên các máy chủ hoặc máy khách Red Hat Enterprise Linux, hầu hết không cần phải cấu hình các tuyến tĩnh (static routes) vì các tuyến thường được tự động quản lý. Tuy nhiên, trong một số trường hợp đặc biệt, bạn có thể cần cấu hình các tuyến tĩnh, ví dụ như:

- **Lưu lượng cần đi qua một đường hầm VPN được mã hóa.**
- **Lưu lượng cần đi theo một tuyến cụ thể vì lý do chi phí hoặc bảo mật.**

Khi các giao diện mạng (network interface) của hệ thống được kích hoạt (hoạt động), bảng định tuyến sẽ tự động được tạo ra với các tuyến tới những mạng được kết nối trực tiếp.

Để kết nối tới một mạng hoặc hệ thống từ xa, hệ thống cần biết địa chỉ của một **cổng mặc định (default gateway)**, nơi sẽ nhận và chuyển tiếp lưu lượng đến mạng khác. Nếu giao diện mạng của hệ thống được cấu hình bằng **DHCP**, địa chỉ cổng mặc định thường được chỉ định tự động.

### 1.2.**Default gateway và vai trò của nó:**

- **Default Gateway** là nơi mà hệ thống gửi tất cả các lưu lượng không thuộc mạng cục bộ (local network) hoặc không có tuyến nào tốt hơn trong bảng định tuyến.
- Thông thường, địa chỉ của cổng mặc định sẽ là địa chỉ IP đầu tiên hoặc cuối cùng trong dải mạng. Ví dụ:
  - Địa chỉ mạng: `192.168.10.0`.
  - Địa chỉ cổng mặc định: `192.168.10.1` hoặc `192.168.10.254`.
  - Địa chỉ broadcast: `192.168.10.255`.

Cổng mặc định thường là một **router mạng**. Nó chịu trách nhiệm chuyển tiếp lưu lượng ra ngoài mạng cục bộ đến các mạng khác, bao gồm cả mạng Internet, nếu không có tuyến ưu tiên nào trong bảng định tuyến của hệ thống.

## 2. So sánh định tuyến tĩnh và định tuyến động 

Dưới đây là bảng so sánh giữa **định tuyến tĩnh** và **định tuyến động**:

| **Đặc điểm**              | **Định tuyến tĩnh**                                          | **Định tuyến động**                                          |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Cách cấu hình**         | Cấu hình thủ công bởi người quản trị mạng.                   | Tự động học và cập nhật bảng định tuyến thông qua các giao thức định tuyến. |
| **Sự thay đổi của mạng**  | Không thay đổi tự động khi mạng thay đổi (nếu có thay đổi, phải cấu hình lại thủ công). | Tự động cập nhật và thay đổi khi có sự thay đổi trong mạng (như mất kết nối hoặc thêm mạng). |
| **Tính linh hoạt**        | Ít linh hoạt, phải cấu hình lại khi có thay đổi trong mạng.  | Rất linh hoạt, tự động thích ứng với các thay đổi mạng.      |
| **Tài nguyên sử dụng**    | Tốn ít tài nguyên vì chỉ có cấu hình tĩnh.                   | Tốn nhiều tài nguyên hơn do yêu cầu cập nhật bảng định tuyến và giao tiếp liên tục giữa các router. |
| **Bảng định tuyến**       | Bảng định tuyến tĩnh, được cấu hình thủ công và không thay đổi tự động. | Bảng định tuyến động, cập nhật tự động dựa trên thông tin từ các router khác. |
| **Phương thức giao thức** | Không sử dụng giao thức định tuyến.                          | Sử dụng các giao thức định tuyến động như OSPF, RIP, EIGRP, BGP. |
| **Độ phức tạp**           | Đơn giản, dễ cấu hình trong mạng nhỏ.                        | Phức tạp hơn, cần cấu hình và hiểu rõ về các giao thức định tuyến. |
| **Ổn định**               | Rất ổn định, ít gặp sự cố nếu cấu hình đúng.                 | Có thể gặp sự cố nếu có sự cố trong giao thức định tuyến hoặc thông tin không đồng bộ. |
| **Phạm vi sử dụng**       | Phù hợp cho mạng nhỏ hoặc mạng có ít thay đổi.               | Phù hợp cho mạng lớn hoặc mạng có sự thay đổi thường xuyên.  |
| **Ví dụ**                 | Cấu hình định tuyến tĩnh trên các router của mạng nội bộ.    | Cấu hình định tuyến động giữa các router sử dụng OSPF, RIP hoặc BGP. |

## 3. Hướng dẫn cấu hình định tuyến tĩnh 

tham khảo thêm tại [redhat](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/networking_guide/sec-configuring_static_routes_with_ip_commands#sec-Configuring_Static_Routes_with_ip_Commands)

Để tạo một **lab định tuyến tĩnh** trên VMware, bạn cần tạo ít nhất 2 máy ảo và cấu hình mạng để mô phỏng môi trường định tuyến. Dưới đây là các bước hướng dẫn chi tiết:

### **Bước 1: Cài đặt VMware và tạo các máy ảo**

1. **Cài đặt VMware Workstation** (hoặc VMware Player) trên máy tính của bạn.
2. **Tạo hai máy ảo**:
   - Mở VMware và chọn **Create a New Virtual Machine**.
   - Chọn hệ điều hành bạn muốn cài đặt (ví dụ: Ubuntu Server hoặc CentOS, vì chúng hỗ trợ tốt cho việc cấu hình mạng và định tuyến).
   - Tạo **2 máy ảo**, mỗi máy ảo sẽ đóng vai trò là một router hoặc một thiết bị đầu cuối trong mạng của bạn.

### **Bước 2: Cấu hình mạng cho các máy ảo**

1. **Tạo các mạng ảo (Virtual Networks)**:

   - Trong VMware, đi đến **Edit** → **Virtual Network Editor**.
   - Tạo hai mạng ảo, ví dụ:
     - **VMnet1**: Mạng nội bộ cho mạng `192.168.1.0/24`.
     - **VMnet2**: Mạng nội bộ cho mạng `192.168.2.0/24`.

2. **Cấu hình các adapter mạng cho các máy ảo**:

   - Máy ảo 1

      (Router A):

     - Vào **Settings** của máy ảo 1, chọn **Network Adapter** và chọn **Custom (VMnet1)**.
     - Gán IP cho máy ảo 1 trong mạng `192.168.1.0/24`, ví dụ `192.168.1.1/24`.

   - Máy ảo 2

      (Router B):

     - Vào **Settings** của máy ảo 2, chọn **Network Adapter** và chọn **Custom (VMnet2)**.
     - Gán IP cho máy ảo 2 trong mạng `192.168.2.0/24`, ví dụ `192.168.2.1/24`.

3. **Cấu hình kết nối giữa các máy ảo**:

   - Tạo một kết nối mới giữa Router A và Router B bằng cách chọn **Custom Network** và gán IP cho giao diện kết nối giữa 2 máy ảo (ví dụ: `192.168.1.2` cho Router A và `192.168.2.2` cho Router B).

### **Bước 3: Cài đặt và cấu hình hệ điều hành trên các máy ảo**

1. **Cài đặt hệ điều hành** (Ubuntu, CentOS, hoặc bất kỳ hệ điều hành nào bạn muốn).

   - Sau khi cài xong hệ điều hành, bạn cần đảm bảo rằng các máy ảo có thể giao tiếp với nhau trong mạng nội bộ.

2. **Cài đặt các gói mạng** (Nếu cần):

   - Ví dụ với Ubuntu, bạn có thể cần cài đặt các công cụ như 

     ```
     net-tools
     ```

     :

     ```bash
     sudo apt update
     sudo apt install net-tools
     ```

### **Bước 4: Cấu hình định tuyến tĩnh trên các router**

1. **Cấu hình IP trên các giao diện của router**:

   - **Trên Router A**:

     ```bash
     sudo ip addr add 192.168.1.1/24 dev eth0   # Giao diện kết nối với mạng 192.168.1.0
     sudo ip addr add 192.168.1.2/24 dev eth1   # Giao diện kết nối với Router B
     sudo ip link set eth0 up
     sudo ip link set eth1 up
     ```

   - **Trên Router B**:

     ```bash
     sudo ip addr add 192.168.2.1/24 dev eth0   # Giao diện kết nối với mạng 192.168.2.0
     sudo ip addr add 192.168.2.2/24 dev eth1   # Giao diện kết nối với Router A
     sudo ip link set eth0 up
     sudo ip link set eth1 up
     ```

2. **Cấu hình định tuyến tĩnh**:

   - **Trên Router A**:

     - Router A cần biết cách định tuyến đến mạng `192.168.2.0/24` thông qua Router B (IP là `192.168.1.2`):

     ```bash
     sudo ip route add 192.168.2.0/24 via 192.168.1.2
     ```

   - **Trên Router B**:

     - Tương tự, Router B cần biết cách định tuyến đến mạng `192.168.1.0/24` thông qua Router A (IP là `192.168.2.2`):

     ```bash
     sudo ip route add 192.168.1.0/24 via 192.168.2.2
     ```

### **Bước 5: Kiểm tra kết nối giữa các mạng**

1. **Ping giữa các máy ảo**:

   - **Trên máy ảo 1** (IP `192.168.1.1`), thử ping đến máy ảo 2 (IP `192.168.2.1`):

   ```bash
   ping 192.168.2.1
   ```

   - **Trên máy ảo 2** (IP `192.168.2.1`), thử ping đến máy ảo 1 (IP `192.168.1.1`):

   ```bash
   ping 192.168.1.1
   ```

2. **Kiểm tra bảng định tuyến**:

   - Bạn có thể kiểm tra bảng định tuyến của các router bằng lệnh:

   ```bash
   ip route
   ```

### **Bước 6: Tinh chỉnh và giám sát**

1. **Giám sát lưu lượng**:
   - Bạn có thể sử dụng các công cụ như `traceroute` hoặc `tcpdump` để theo dõi lưu lượng đi qua router và xác nhận rằng định tuyến tĩnh hoạt động đúng.
2. **Kiểm tra bảng định tuyến**:
   - Đảm bảo rằng bảng định tuyến của các router đã được cấu hình chính xác và không có lỗi.

### **Tóm lại**

Việc cấu hình định tuyến tĩnh trong VMware mô phỏng mạng nội bộ giúp bạn hiểu rõ về cách các router giao tiếp và cách định tuyến giữa các mạng con khác nhau. Sau khi hoàn thành các bước trên, bạn có thể mở rộng mạng và thử nghiệm thêm với nhiều máy ảo và các giao thức định tuyến động nếu cần.
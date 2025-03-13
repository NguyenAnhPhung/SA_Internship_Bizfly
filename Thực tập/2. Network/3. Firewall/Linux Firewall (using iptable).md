## Linux Firewall (using iptable)

## 1. Tường lửa Linux và Firewalld là gì?

**Tường lửa Linux** là một hệ thống bảo mật, đóng vai trò như "bức tường ảo" bảo vệ hệ thống khỏi lưu lượng không mong muốn và các truy cập trái phép. Nó giám sát và quản lý lưu lượng mạng bao gồm các kết nối đi và đến (outbound/inbound connections). Tường lửa có thể được sử dụng để:

- Chặn quyền truy cập vào các địa chỉ IP cụ thể.
- Giới hạn quyền truy cập trên các subnet (mạng con).
- Kiểm soát các cổng (ports) và dịch vụ.

### 1.1. Một số nguyên tắc của tường lửa

Tường lửa được thiết kế để:

1. Bảo vệ hệ thống khỏi các truy cập trái phép.
2. Kiểm soát lưu lượng mạng đi và đến, bao gồm các tùy chỉnh cổng, địa chỉ, giao thức.

## 2. Các công cụ quản lí tường lửa

### 2.1. Firewalld

**Firewalld** là một công cụ quản lý tường lửa động trên hệ điều hành Linux, hoạt động theo nguyên tắc **vùng (zones)**. Firewalld cho phép cập nhật các chính sách tường lửa theo thời gian thực khi có thay đổi trong môi trường mạng.

Chúng ta có thể kiểm tra trạng thái của dịch vụ Firewalld bằng lệnh:

```bash
sudo systemctl status firewalld
```

------

#### **1. Cho phép lưu lượng SSH (Secure Shell)**

Cấu hình này cho phép tất cả lưu lượng truy cập vào cổng SSH, giúp chúng ta kết nối từ xa đến hệ thống.

**Cách thực hiện:**

```bash
sudo firewall-cmd --zone=public --add-service=ssh --permanent
sudo firewall-cmd --reload
```

**Giải thích:**

- `--zone=public`: Đặt cấu hình vào vùng "public".
- `--add-service=ssh`: Cho phép dịch vụ SSH.
- `--permanent`: Lưu cấu hình vĩnh viễn.
- `--reload`: Áp dụng các thay đổi mới.

------

#### **2. Cho phép lưu lượng vào trên cổng cụ thể**

Ví dụ, chúng ta cho phép lưu lượng truy cập trên cổng TCP 8080.

**Cách thực hiện:**

```bash
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

**Giải thích:**

- `--add-port=8080/tcp`: Cho phép truy cập qua cổng TCP 8080.

------

#### **3. Chặn lưu lượng từ một địa chỉ IP cụ thể**

Ví dụ, chặn lưu lượng từ địa chỉ IP `192.168.52.1`.

**Cách thực hiện:**

```bash
sudo firewall-cmd --zone=public --add-rich='rule family="ipv4" source address="192.168.52.1" reject'
sudo firewall-cmd --reload
```

**Giải thích:**

- `--add-rich`: Thêm một quy tắc "rich rule".
- `family="ipv4"`: Áp dụng cho giao thức IPv4.
- `source address="192.168.52.1"`: Chỉ định địa chỉ IP cần chặn.
- `reject`: Từ chối lưu lượng từ địa chỉ này.

------

### 2.2. iptables

**iptables** là một phần mềm dựa trên Linux dùng để thực hiện các chức năng như:

1. Lọc gói tin (**packet filtering**).
2. Chuyển đổi địa chỉ mạng (**NAT - Network Address Translation**).
3. Kiểm soát luồng lưu lượng mạng.

Hệ thống quản trị viên có thể sử dụng iptables để kiểm soát lưu lượng đi và đến bằng cách thiết lập các **quy tắc (rules)**.
Khi một gói tin được nhận vào hệ thống Linux, nó phải đi qua **các bảng (tables)** và **chuỗi (chains)** trong tường lửa iptables.

#### 2.2.1. Các loại bảng (tables) trong iptables

**Bảng Security**

- Sử dụng với các công cụ bảo mật như SELinux.
- Dùng để thiết lập các quy tắc **MAC (Mandatory Access Control)** như gán nhãn bảo mật và kiểm soát truy cập.
- Các chuỗi có sẵn: **INPUT**, **OUTPUT**, **FORWARD**, **SECMARK**.

**Bảng Mangle**

- Dùng để thay đổi các gói tin, như chỉnh sửa trường ToS/DSCP, sửa đổi tiêu đề, hoặc thay đổi dấu (mark) của gói tin.
- Các chuỗi có sẵn: **PREROUTING**, **POSTROUTING**, **INPUT**, **OUTPUT**, **FORWARD**.

**Bảng Nat**

- Thực hiện dịch địa chỉ mạng (**NAT**), giúp chia sẻ một địa chỉ IP công cộng cho nhiều thiết bị.
- Các chuỗi có sẵn: **PREROUTING**, **POSTROUTING**.

**Bảng Raw**

- Dùng để xử lý gói tin ở cấp độ thấp, thiết lập các quy tắc xử lý sơ cấp cho gói tin trước khi chúng được xử lý bởi các bảng khác.
- Ít chuỗi mặc định nhưng có thể thêm chuỗi mới theo yêu cầu.

**Bảng Filter**

- Được sử dụng để lọc gói tin dựa trên các quy tắc xác định.
- Các chuỗi có sẵn: **INPUT**, **OUTPUT**, **FORWARD**.
- Dùng để kiểm tra nguồn, đích, số cổng và loại giao thức của gói tin.

#### 2.2.2. **Các loại chuỗi (Chains)**

Trong mỗi bảng, các **chuỗi (chains)** chứa các **quy tắc (rules)** được định nghĩa để thực hiện các nhiệm vụ cụ thể. Có ba loại chuỗi chính:

1. **INPUT Chain:**
   - Lọc lưu lượng **đến** hệ thống (incoming traffic).
   - Ví dụ: Chặn hoặc cho phép truy cập vào hệ thống từ một địa chỉ IP nhất định.
2. **OUTPUT Chain:**
   - Lọc lưu lượng **đi** từ hệ thống (outgoing traffic).
   - Ví dụ: Cho phép hệ thống gửi dữ liệu đến một địa chỉ IP cụ thể.
3. **FORWARD Chain:**
   - Xử lý các gói tin **chuyển tiếp** qua hệ thống (forwarded traffic).
   - Ví dụ: Gói tin từ một máy chủ khác đi qua hệ thống trước khi đến máy đích.

#### 2.2.3. **Hoạt động của iptables**

1. Khi một gói tin đến, nó sẽ đi qua **bảng** tương ứng dựa trên chức năng (lọc, NAT, chỉnh sửa).
2. Trong mỗi bảng, gói tin được kiểm tra dựa trên các **quy tắc (rules)** trong **chuỗi (chains)** tương ứng.
3. Dựa trên quy tắc, gói tin có thể được:
   - **Chấp nhận (ACCEPT):** Cho phép tiếp tục.
   - **Từ chối (DROP):** Loại bỏ gói tin.
   - **Chuyển tiếp (FORWARD):** Gửi qua hệ thống khác.

### So sánh iptables và firewalld

| **Tiêu chí**                  | **iptables**                                                 | **firewalld**                                                |
| ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Định nghĩa**                | Công cụ quản lý tường lửa truyền thống trên Linux.           | Công cụ quản lý tường lửa động, hiện đại hơn.                |
| **Kiểu quản lý**              | Tĩnh, cần khởi động lại dịch vụ sau khi thay đổi quy tắc.    | Động, cho phép áp dụng thay đổi ngay lập tức mà không cần khởi động lại. |
| **Cách hoạt động**            | Quản lý dựa trên các bảng (**tables**) và chuỗi (**chains**). | Quản lý dựa trên khái niệm **vùng (zones)**.                 |
| **Giao diện**                 | Chủ yếu dựa vào dòng lệnh, cần hiểu sâu về các chuỗi và bảng. | Dễ sử dụng hơn, cung cấp cả dòng lệnh và giao diện đồ họa (GUI). |
| **Phạm vi quản lý**           | Quản lý cụ thể từng gói tin và quy tắc thủ công.             | Tích hợp sẵn các **dịch vụ (services)** và cấu hình linh hoạt theo vùng. |
| **Tính năng nổi bật**         | - Kiểm soát chi tiết gói tin.                                | - Cập nhật thời gian thực.  - Tích hợp NAT và IPv6 dễ dàng.  |
| **Thao tác với dịch vụ**      | Không tích hợp sẵn dịch vụ; cần cấu hình thủ công (ví dụ, mở cổng SSH cần chỉ định cổng). | Hỗ trợ sẵn dịch vụ, chỉ cần chỉ định tên dịch vụ (ví dụ: `ssh`). |
| **Tích hợp IPv6**             | Hỗ trợ IPv6 qua công cụ riêng (ip6tables).                   | Tích hợp sẵn IPv6, không cần công cụ riêng.                  |
| **Thân thiện với người dùng** | Khó sử dụng đối với người mới.                               | Thân thiện hơn, dễ tiếp cận với người mới.                   |



## 3. Cấu hình firewall linux sử dụng iptables

tham khảo tại [đây](https://www.geeksforgeeks.org/linux-firewall/)

### **3.1. Cài đặt `iptables`**

Sử dụng lệnh sau để cài đặt `iptables` (nếu chưa được cài đặt):

```bash
sudo dnf install iptables
```

------

### **3.2 Cú pháp cơ bản**

```bash
sudo iptables [option] CHAIN-rule [-j target]
```

- **`option`**: Các tùy chọn để xác định hành động (xem mục 4).
- `CHAIN-rule` Chuỗi mà quy tắc áp dụng. Bao gồm:
  - **INPUT**: Xử lý lưu lượng vào hệ thống.
  - **OUTPUT**: Xử lý lưu lượng ra khỏi hệ thống.
  - **FORWARD**: Xử lý lưu lượng đi qua hệ thống.
- **`-j target`**: Hành động áp dụng khi quy tắc khớp (ACCEPT, DROP, REJECT...).

------

### **3.3. Các tùy chọn thường dùng**

| **Tùy chọn (`option`)** | **Mô tả**                                 |
| ----------------------- | ----------------------------------------- |
| `-A`                    | Thêm (append) một quy tắc vào chuỗi.      |
| `-D`                    | Xóa một quy tắc khỏi chuỗi.               |
| `-I`                    | Chèn (insert) quy tắc vào vị trí cụ thể.  |
| `-L`                    | Liệt kê các quy tắc trong chuỗi.          |
| `-F`                    | Xóa tất cả quy tắc khỏi chuỗi.            |
| `-p`                    | Chỉ định giao thức (TCP, UDP, ICMP).      |
| `-s`                    | Xác định địa chỉ nguồn của gói tin.       |
| `-j`                    | Hành động áp dụng (ACCEPT, DROP, REJECT). |

------

### **3.4. Các hành động (targets)**

| **Hành động** | **Mô tả**                                                    |
| ------------- | ------------------------------------------------------------ |
| **ACCEPT**    | Cho phép gói tin đi qua.                                     |
| **DROP**      | Chặn gói tin mà không thông báo cho nguồn.                   |
| **REJECT**    | Chặn gói tin và gửi thông báo cho nguồn rằng gói tin bị từ chối. |

------

### **3.5. Tạo các quy tắc tường lửa cơ bản**

#### **3.5.1. Cho phép ICMP (ping)**

Cú pháp:

```bash
sudo iptables -A INPUT -p icmp -j ACCEPT
```

- **`-A INPUT`**: Thêm quy tắc vào chuỗi INPUT.
- **`-p icmp`**: Áp dụng quy tắc cho giao thức ICMP.
- **`-j ACCEPT`**: Cho phép gói tin.

#### **3.5.2. Cho phép kết nối SSH từ một IP cụ thể**

Cú pháp:

```bash
sudo iptables -A INPUT -s 192.168.160.51 -p tcp --dport 22 -j ACCEPT
```

- **`-s 192.168.160.51`**: Chỉ định địa chỉ nguồn.
- **`-p tcp --dport 22`**: Áp dụng quy tắc cho giao thức TCP trên cổng 22 (SSH).
- **`-j ACCEPT`**: Cho phép kết nối.

#### **3.5.3. Chặn (DROP) một IP cụ thể**

Cú pháp:

```bash
sudo iptables -A INPUT -s 192.168.160.51 -j DROP
```

- Chặn mọi lưu lượng đến từ IP `192.168.160.51`.

#### **3.5.4. Xóa tất cả các quy tắc**

Cú pháp:

```bash
sudo iptables -F
```

- Xóa tất cả quy tắc trong bảng `iptables`.

------

### **3.6. Kiểm tra quy tắc đã cấu hình**

Để liệt kê tất cả các quy tắc hiện tại:

```bash
sudo iptables -L -v
```

- **`-L`**: Liệt kê các quy tắc.
- **`-v`**: Hiển thị chi tiết hơn.

------

### **3.7. Một số lưu ý khi sử dụng `iptables`**

- Giao thức và cổng:
  - Giao thức: TCP, UDP, ICMP.
  - Cổng: Sử dụng `--dport` để chỉ định cổng đích (destination port).
- Quản lý chuỗi:
  - Chuỗi INPUT: Xử lý gói tin đến hệ thống.
  - Chuỗi OUTPUT: Xử lý gói tin rời khỏi hệ thống.
  - Chuỗi FORWARD: Xử lý gói tin đi qua hệ thống (routing).


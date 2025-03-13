# Keepalived

## 1. Keepalived là gì?

Keepalived là một công cụ mạnh mẽ đảm bảo tính sẵn sàng cao và cân bằng tải trong hệ thống mạng 

### 1.1. Cân bằng tải (Load Balancing)

- **Cân bằng tải** là cách chia đều lưu lượng truy cập mạng (IP traffic) đến một nhóm các máy chủ thật (real servers).
- Mục tiêu: đảm bảo các máy chủ không bị quá tải và duy trì dịch vụ hoạt động ổn định.
- **Keepalived** sử dụng **Linux Virtual Server (IPVS)**, một module trong kernel Linux, để thực hiện cân bằng tải ở **Layer 4** (tầng giao vận, như TCP/U

### 1.2. Kiểm tra sức khỏe (Health Checking)

- Keepalived theo dõi tình trạng hoạt động của các máy chủ trong nhóm (real servers).
- Nếu phát hiện một máy chủ bị lỗi, nó sẽ loại máy chủ đó ra khỏi danh sách nhận lưu lượng. Khi máy chủ hoạt động lại, Keepalived sẽ tự động thêm vào lại.
- Điều này giúp duy trì dịch vụ ổn định ngay cả khi một hoặc nhiều máy chủ bị sự cố.

### 1.3. Đảm bảo tính sẵn sàng cao (HA - High Availability)

- Để đảm bảo hệ thống cân bằng tải luôn hoạt động (ngay cả khi bản thân bộ cân bằng tải bị lỗi), Keepalived sử dụng giao thức **VRRP (Virtual Redundancy Routing Protocol)**.
- VRRP cho phép tạo ra một IP ảo (virtual IP) được chia sẻ giữa nhiều máy chủ cân bằng tải.
  - về cơ bản thì hệ thống sẽ có 1 máy master và 1 máy backup 
  - sử dụng ip ảo giữa các máy này là để đảm bảo ip có thể thay đổi qua lại giữa các máy chủ 
  - khi master hỏng thì ip ảo sẽ được chuyển qua cho backup để các client có thể tiếp tục truy cập 
- Nếu máy chủ chính (master) gặp sự cố, một máy chủ dự phòng (backup) sẽ tự động thay thế mà người dùng không nhận thấy gián đoạn.

### Ví dụ: 

Bạn có một cửa hàng online phục vụ rất nhiều khách hàng.

Máy chủ chính phục vụ bị quá tải hoặc hỏng, bạn cần có một nhóm máy chủ khác để thay thế (cân bằng tải).

Keepalived như một "quản lý cửa hàng", sẽ đảm bảo:

1. Chia khách hàng đều đến các quầy (máy chủ).
2. Đóng quầy bị hỏng và hướng khách hàng đến quầy hoạt động tốt.
3. Nếu "quản lý chính" bận hoặc gặp sự cố, một "quản lý dự phòng" sẽ tiếp quản ngay lập tức.

## 2. Nguyên lí hoạt động keepalived

Keepalived là một dịch vụ quản lý địa chỉ IP ảo (VIP) và cân bằng tải trong các hệ thống mạng, sử dụng giao thức **VRRP (Virtual Router Redundancy Protocol)** để cung cấp tính sẵn sàng cao (High Availability) cho các dịch vụ.

------

### 2.1. **Thành phần chính của Keepalived**

1. **VRRP (Virtual Router Redundancy Protocol)**:
   - Đây là giao thức chính giúp duy trì địa chỉ IP ảo (VIP) giữa các máy chủ trong cùng một nhóm (gọi là VRRP instance).
   - Mỗi nhóm VRRP sẽ có một máy chủ đóng vai trò **MASTER** và các máy khác làm **BACKUP**.
2. **Cân bằng tải (Load Balancing)**:
   - Keepalived có thể cấu hình để làm nhiệm vụ cân bằng tải giữa các máy chủ thực, giúp chia sẻ tải công việc và tăng hiệu năng.
3. **Kiểm tra sức khỏe (Health Checking)**:
   - Keepalived giám sát trạng thái của các máy chủ thực (real servers) và tự động loại bỏ các máy không hoạt động khỏi hệ thống cân bằng tải.

------

### 2.2. **Nguyên lý hoạt động**

#### 2.2.**1. Cấu trúc cơ bản của VRRP**

- **VRRP Instance**: Một nhóm máy chủ được cấu hình với cùng một địa chỉ IP ảo (VIP). Mỗi máy trong nhóm được gán một **priority** (độ ưu tiên).
- MASTER và BACKUP:
  - Máy có độ ưu tiên cao nhất sẽ trở thành MASTER, chịu trách nhiệm quản lý VIP.
  - Các máy còn lại là BACKUP, ở trạng thái chờ (standby).

#### 2.2.**2. Chuyển đổi MASTER và BACKUP**

- Hoạt động ban đầu:
  - Khi hệ thống khởi động, máy có priority cao nhất sẽ trở thành MASTER.
  - Máy MASTER gửi gói tin **VRRP Advertisement** để thông báo trạng thái của mình cho các máy BACKUP.
- Chuyển đổi vai trò (Failover):
  - Nếu máy MASTER không gửi gói tin quảng bá trong một khoảng thời gian (mặc định là `advert_int` giây), các máy BACKUP sẽ thực hiện bầu chọn.
  - Máy có priority cao nhất trong nhóm BACKUP sẽ được chọn làm MASTER mới.

#### 2.2**3. Giao tiếp thông qua địa chỉ IP ảo (VIP)**

- VIP được gán trên giao diện mạng của máy MASTER và chỉ tồn tại trên máy MASTER.
- Khi máy MASTER nhận được gói tin gửi tới VIP, nó sẽ chuyển tiếp gói tin đến các máy chủ thực dựa trên cấu hình cân bằng tải.

#### 2.2**4. Health Checking**

- Keepalived liên tục kiểm tra trạng thái các máy chủ thực (real servers) bằng cách:
  - Ping hoặc kết nối TCP đến các máy chủ thực.
  - Thực hiện các kiểm tra nâng cao (ví dụ: kiểm tra HTTP, dịch vụ cụ thể).
- Nếu phát hiện một máy chủ thực không khả dụng, Keepalived sẽ loại bỏ máy đó khỏi hệ thống cân bằng tải và chỉ định lại lưu lượng đến các máy chủ còn lại.

------

### 2.3. **Quy trình hoạt động tổng quát**

1. Cấu hình VRRP

   - Keepalived thiết lập VIP trên máy MASTER.
   - Máy BACKUP theo dõi trạng thái của máy MASTER thông qua gói tin VRRP Advertisement.

2. Cân bằng tải

    (nếu được cấu hình):

   - Keepalived phân phối lưu lượng truy cập đến các máy chủ thực dựa trên thuật toán (Round Robin, Weighted, Least Connection,...).

3. Chuyển đổi khi có lỗi (Failover)

   - Nếu máy MASTER gặp sự cố, một máy BACKUP sẽ đảm nhận vai trò MASTER, gán VIP cho giao diện mạng của nó.

4. Khôi phục trạng thái ban đầu

   - Khi máy MASTER cũ hoạt động trở lại và có priority cao hơn, nó sẽ giành lại vai trò MASTER (nếu tính năng preempt được bật).

------

### 2.4.**Ví dụ minh họa**

#### **Cấu hình mẫu:**

- **VIP**: `192.168.1.100`
- **MASTER**: `192.168.1.10` (priority: 100)
- **BACKUP**: `192.168.1.20` (priority: 90)

#### **Hoạt động:**

1. Ban đầu, `192.168.1.10` là MASTER, giữ VIP `192.168.1.100`.
2. Nếu máy `192.168.1.10` ngừng hoạt động, máy `192.168.1.20` sẽ trở thành MASTER, gán VIP `192.168.1.100` cho mình.
3. Khi `192.168.1.10` trở lại và có priority cao hơn, nó sẽ giành lại vai trò MASTER.

------

### **Ưu điểm của Keepalived**

1. Tính sẵn sàng cao (High Availability)
   - Giảm thiểu thời gian chết của dịch vụ nhờ khả năng chuyển đổi nhanh giữa MASTER và BACKUP.
2. Cân bằng tải:
   - Phân phối tải đều giữa các máy chủ thực.
3. Dễ cấu hình và mở rộng
   - Phù hợp cho cả các môi trường nhỏ và hệ thống lớn.

------

### **Nhược điểm**

- Chỉ hoạt động hiệu quả trong cùng một mạng LAN (hoặc cần định tuyến phức tạp hơn trong WAN).
- Yêu cầu cấu hình chính xác để tránh xung đột IP.

Nếu cần thêm chi tiết, hãy cho mình biết nhé!

## 3. Lab keepalived

Use case: tạo 2 máy chủ 1 master, 1 backup cấp Virtual ip cho 2 máy, kiểm tra tính sẵn sàng khi 1 máy ngắt thì VIP từ máy có độ ưu tiên cao (master) chuyển sang máy backup có độ ưu tiên thấp hơn 

Dưới đây là hướng dẫn tạo lab Keepalived trên VMware với 2 máy (Master và Backup) để đảm bảo khi máy Master chết, VIP sẽ tự động chuyển sang Backup:

------

### **2. Cấu hình mạng**

- Gán IP tĩnh cho các máy:
  - **Master**: `192.168.1.101/24`
  - **Backup**: `192.168.1.102/24`
  - **Virtual IP (VIP)**: `192.168.1.200/24`

------

### **3. Cài đặt Keepalived**

#### **3.1. Cài đặt phần mềm**

Trên cả hai máy:

```bash
sudo apt update && sudo apt install -y keepalived
```

------

#### **3.2. Cấu hình Keepalived**

##### **3.2.1. Master**

Chỉnh sửa file cấu hình:

```bash
sudo nano /etc/keepalived/keepalived.conf
```

Thêm nội dung sau:

```bash
vrrp_instance VI_1 {
    state MASTER
    interface ens33                 # Đổi 'ens33' thành tên card mạng của bạn
    virtual_router_id 51
    priority 100                    # Priority cao hơn để làm Master
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass 1234
    }

    virtual_ipaddress {
        192.168.1.200              # VIP
    }
}
```

Khởi động dịch vụ:

```bash
sudo systemctl enable keepalived
sudo systemctl start keepalived
```

##### **3.2.2. Backup**

Chỉnh sửa file cấu hình:

```bash
sudo nano /etc/keepalived/keepalived.conf
```

Thêm nội dung sau:

```bash
vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 90                     # Priority thấp hơn để làm Backup
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass 1234
    }

    virtual_ipaddress {
        192.168.1.200              # VIP
    }
}
```

Khởi động dịch vụ:

```bash
sudo systemctl enable keepalived
sudo systemctl start keepalived
```

------

### **4. Kiểm tra**

#### **4.1. Xác thực VIP**

Trên bất kỳ máy nào trong cùng dải mạng:

```bash
ping 192.168.1.200
```

- Khi Master hoạt động, VIP sẽ trả lời từ `192.168.1.101`.

- Dừng dịch vụ trên Master:

  ```bash
  sudo systemctl stop keepalived
  ```

  VIP sẽ tự động chuyển sang Backup và trả lời từ 

  ```
  192.168.1.102
  ```

#### **4.2. Kiểm tra log**

Dùng lệnh để kiểm tra log trên cả hai máy:

```bash
sudo tail -f /var/log/syslog  # Ubuntu
```

hoặc có thể kiểm tra bằng 

```
systemctl status keepalived 
```

or

```
journalctl -u keepalived | tail 
```

để xem hành động chuyển trạng thái 


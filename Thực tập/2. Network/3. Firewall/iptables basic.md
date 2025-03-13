# iptables basic 

## 1. Lệnh hiển thị iptables

```bash
iptables -L -v -n
```

### Các tùy chọn:

1. **`-L` (list)**:
   - Hiển thị tất cả các chain (như `INPUT`, `OUTPUT`, `FORWARD`) và các quy tắc bên trong từng chain.
   - Mặc định, lệnh này chỉ hiển thị tên chain, policy mặc định, và danh sách các quy tắc.
2. **`-v` (verbose)**:
   - Hiển thị thông tin chi tiết hơn, bao gồm:
     - **Số lượng gói tin** (`pkts`) đã khớp với từng quy tắc.
     - **Tổng kích thước** (`bytes`) của các gói tin đã khớp.
     - Tên giao diện mạng đầu vào (`in`) và đầu ra (`out`).
     - Thông tin thêm về quy tắc (như trạng thái kết nối, module được sử dụng).
3. **`-n` (numeric)**:
   - Hiển thị địa chỉ IP và cổng dưới dạng số, không phân giải thành tên miền hoặc tên cổng.
   - Tùy chọn này giúp lệnh chạy nhanh hơn, tránh việc chờ đợi phân giải DNS.
   - Ví dụ:
     - Thay vì hiển thị `80 (http)`, sẽ hiển thị `80`.
     - Thay vì `google.com`, sẽ hiển thị địa chỉ IP thực tế như `142.250.182.46`.

------

### 1.1. Các thành phần trong bảng 

Khi chạy lệnh:

```bash
sudo iptables -L -v -n
```

Kết quả có thể như sau:

```
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
   15   980 ACCEPT     tcp  --  any    any     192.168.1.100       0.0.0.0/0            tcp dpt:22
    0     0 DROP       tcp  --  any    any     0.0.0.0/0           0.0.0.0/0            tcp dpt:80
```

Ngay đoạn thứ nhất, chúng ta có các cột như TARGET, PROT, OPT, IN, OUT, SOURCE, DESTINATION, ý nghĩa của mỗi cột là như sau:

- **TARGET**: Hành động sẽ thực thi cho mỗi chuỗi quy tắc.
- **PROT**: Là viết tắt của chữ Protocol, nghĩa là giao thức. Tức là các giao thức sẽ được áp dụng để thực thi quy tắc này. Ở đây chúng ta có 3 lựa chọn là *all*, *tcp* hoặc *udp*. Các ứng dụng như SSH, FTP, sFTP,..đều sử dụng giao thức kiểu *TCP*.
- **IN**: Thiết bị mạng nhận kết nối vào được áp dụng cho quy tắc, chẳng hạn như lo, eth0, eth1.
- **OUT**: Thiết bị mạng phục vụ nhu cầu gửi kết nối ra ngoài được áp dụng quy tắc.
- **DESTINATION**: Địa chỉ của lượt truy cập được phép áp dụng quy tắc.

**Ý nghĩa của các số trong cột `pkts` và `bytes`**

- **`pkts`**: Số lượng gói tin (**packets**) đã khớp với quy tắc.
- **`bytes`**: Tổng kích thước (tính bằng byte) của tất cả các gói tin đã khớp với quy tắc.

**Chain**: liên quan đến thứ tự xử lí gói tin 

Iptables kiểm tra các gói tin theo thứ tự các quy tắc trong chain.

Khi một gói tin khớp với một quy tắc, nó sẽ không tiếp tục kiểm tra các quy tắc phía sau.

Vì vậy, các gói tin SSH khớp với quy tắc đầu tiên (`ACCEPT tcp dpt:ssh`), và không bị xét đến quy tắc `DROP tcp dpt:ssh`.

```
pkts bytes target     prot opt in     out     source               destination         
  396 30298 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:ssh
    0     0 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:http
    0     0 DROP       all  --  any    any     anywhere             anywhere            
    0     0 DROP       tcp  --  any    any     anywhere             anywhere             tcp dpt:ssh
```



## 2. Tạo, xóa rules

### 2.1. Tạo rules mới 

Tại đoạn trên, bạn sẽ thấy nó được chia ra làm 3 nhóm với 3 kiểu quy tắc đó là:

- **INPUT**: áp dụng cho các kết nối đi vào.
- **FORWARD**: áp dụng cho các kết nối đã được trỏ đến một vị trí khác.
- **OUTPUT**: áp dụng cho các kết nối ra ngoài từ máy chủ.

**ví dụ 1:**

```
iptables -A INPUT -i lo -j ACCEPT
```

Đoạn trên có nghĩa là:

- **-A INPUT**: khai báo kiểu kết nối sẽ được áp dụng (A nghĩa là Append).
- **-i lo**: Khai báo thiết bị mạng được áp dụng (i nghĩa là Interface).
- **-j ACCEPT**: khai báo hành động sẽ được áp dụng cho quy tắc này (j nghĩa là Jump).

**ví dụ 2:**

Lệnh trên cho phép bất kỳ kết nối TCP nào đến máy tính qua cổng 22 được chấp nhận,

```
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

- **`-A INPUT`**: Thêm (Append) một quy tắc vào **chain INPUT**, tức là xử lý các gói dữ liệu đến máy.
- **`-p tcp`**: Áp dụng quy tắc này cho giao thức TCP.
- **`--dport 22`**: Chỉ định cổng đích là **22**, đây là cổng mặc định cho dịch vụ SSH.
- **`-j ACCEPT`**: Chỉ định hành động **ACCEPT** (chấp nhận gói tin) khi thỏa mãn các điều kiện ở trên.

**ví dụ 3**: thêm một nguồn cụ thể 

```
iptable -A INPUT -p tcp --dropt 22 -s 192.150.10.12 -j ACCEPT
```

### 2.2. Xóa rules

#### 2.2.1. Xóa rules dựa trên số thứ tự (line number)

xem số thứ tự của rule 

```
sudo iptables -L INPUT --line-numbers

```

kết quả

```
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     tcp  --  192.168.1.100       anywhere             tcp dpt:22
2    DROP       tcp  --  anywhere            anywhere             tcp dpt:80

```

xóa các rule theo số thứ tự của nó 

```
sudo iptables -D INPUT 2

```

#### 2.2.2. Xóa rule dựa trên nội dung của nó 

cũng giống như tạo rule chỉ cần chuyển `-A` thành `-D`

```
sudo iptables -D INPUT -p tcp --dport 22 -s 192.168.1.100 -j ACCEPT

```

#### 2.2.3. Xóa tất cả các rules

```
sudo iptables -F INPUT

```

xóa tất cả 

```
sudo iptables -F 
```

## 3. Chèn quy tắc mới vào vị trí cụ thể 

```
sudo iptables -I INPUT 1 -p tcp --dport 22 -s 192.168.1.101 -j ACCEPT

```

vì không thể duy chuyển trực tiếp một quy tắc

1. Xóa quy tắc cũ bằng số thứ tự.
2. Chèn lại quy tắc mới vào vị trí mong muốn.

có thể chỉnh sửa quy tắc trực tiếp trong file cấu hình 

```
sudo nano /etc/iptables/rules.v4

```

## 4. Lưu lại cấu hình sau khi sửa đổi 

cài đặt 

```
sudo apt install iptables-persistent
```



```
sudo iptables-save > /etc/iptables/rules.v4

```


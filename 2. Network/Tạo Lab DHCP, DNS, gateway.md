# Tạo Lab DHCP, DNS, gateway

## Yêu cầu:

```
Dựng lab gồm 1 server có nhiệm vụ làm gateway cho mạng
Có thể cấp DHCP cho client
Có thể phân giải được domain				
```

```
"DHCP: isc-dhcp-server
DNS: bind9, powerdns
iptables to NAT"			
```

## Chuẩn bị 

sử dụng 2 máy ảo:

máy server : 2 nic (1 pub 1 private)

máy client: 1 nic (chung dải private với server)

về cơ bản cấu hình dhcp và dns giống như 2 bài lab trước đó 

#### **cấu hình gateway forward interface intertnal ra external sử dụng iptables nat như sau:**

##### **bật ip forwarding vĩnh viễn** 

```
sudo nano /etc/sysctl.conf

```

kiểm tra lại 

```
cat /proc/sys/net/ipv4/ip_forward

```

##### thêm luật NAT

định tuyến gói tin từ mạng nôi bộ ra ens33

```
sudo iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE

```

- Chỉ định bảng NAT (**Network Address Translation**) trong iptables. NAT được sử dụng để thay đổi địa chỉ IP trong các gói tin khi chúng được định tuyến qua máy.

**`-A POSTROUTING`:**

- Thêm (`-A`, append) một quy tắc vào chain **POSTROUTING**.
- Chain này xử lý các gói tin **sau khi đã được định tuyến**, trước khi chúng rời khỏi interface mạng.

**`-o ens33`:**

- Quy tắc áp dụng cho các gói tin **đi qua giao diện mạng** `ens33` (giao diện mạng ngoài, được kết nối với mạng public).

**`-j MASQUERADE`:**

- `-j` (jump): Xác định hành động sẽ được áp dụng cho các gói tin.

- MASQUERADE

  :

  - Thay đổi địa chỉ nguồn của các gói tin thành địa chỉ IP của giao diện mạng `ens33`.
  - Kỹ thuật này thường được sử dụng khi địa chỉ IP của giao diện thay đổi (ví dụ: khi sử dụng DHCP).
  - Đảm bảo rằng các gói tin từ mạng nội bộ đi ra internet sẽ có địa chỉ nguồn trùng với địa chỉ IP của `ens33`.

cho phép chuyển tiếp từ ens37 đến ens33

```
sudo iptables -A FORWARD -i ens37 -o ens33 -j ACCEPT
sudo iptables -A FORWARD -i ens33 -o ens37 -m state --state RELATED,ESTABLISHED -j ACCEPT


```

lưu cấu hình iptables

```
sudo apt install iptables-persistent
```

```
sudo iptables-save > /etc/iptables/rules.v4
```


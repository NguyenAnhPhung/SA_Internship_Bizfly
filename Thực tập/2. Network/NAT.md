# NAT

## 1. NAT là gì?

#### **1. Network Address Translation (NAT) là gì?**

NAT (Network Address Translation) là quá trình "viết lại địa chỉ nguồn và/hoặc đích của các gói tin IP khi chúng đi qua router hoặc firewall". Điều này thường được sử dụng để:

- **Kết nối mạng nội bộ (private network) với Internet:** NAT giúp nhiều thiết bị trong mạng nội bộ có thể sử dụng một địa chỉ IP công cộng duy nhất để giao tiếp với Internet.
- **Bảo mật mạng:** NAT ẩn địa chỉ IP nội bộ, giảm khả năng bị tấn công từ bên ngoài.
- **Quản lý tài nguyên:** Giảm số lượng địa chỉ IP công cộng cần thiết.

#### **2. Cấu trúc cơ bản của một gói tin IP**

Gói tin IP (dùng trong giao thức TCP hoặc UDP) có hai trường quan trọng:

- **Địa chỉ nguồn (Source Address):** Là địa chỉ IP của thiết bị gửi dữ liệu.
- **Địa chỉ đích (Destination Address):** Là địa chỉ IP của thiết bị nhận dữ liệu.

#### **3. Port và socket**

Mỗi tiến trình (process) trong mạng sử dụng một **port** để gửi và nhận dữ liệu.
 Ví dụ:

- HTTP (Web Server): Cổng 80.
- SSH: Cổng 22.

**Socket** là sự kết hợp giữa địa chỉ IP và số cổng, tạo thành một định danh duy nhất cho mỗi kết nối. Ví dụ:

- Một trình duyệt trên máy khách có IP `123.123.123.123` và sử dụng cổng `65432` để kết nối tới máy chủ có IP `112.112.112.112` ở cổng `80`.
- Socket của máy khách: `123.123.123.123:65432`.
- Socket của máy chủ: `112.112.112.112:80`.

Các **well-known ports** (cổng nổi tiếng) là những cổng được quy định chuẩn, thường từ 0-1023 (ví dụ: HTTP = 80, SSH = 22).

#### **4. Ứng dụng chính của NAT**

NAT thường được dùng để kết nối mạng nội bộ với Internet bằng cách sử dụng một router hoặc máy chủ Linux với công cụ như `iptables`. Ngoài ra, NAT còn hỗ trợ:

- Chuyển hướng (redirection).
- Vượt qua các proxy giới hạn.

## 2. Netfilter và iptables trong Linux

#### **1. Netfilter và NAT trong Linux**

Linux kernel tích hợp sẵn một framework mạnh mẽ tên là **Netfilter**, cho phép thực hiện lọc gói tin (packet filtering), NAT, và nhiều chức năng mạng khác. Với Netfilter, một máy Linux có thể trở thành router hỗ trợ NAT nếu được trang bị đủ các card mạng (interfaces). Công cụ **iptables** được sử dụng để thiết lập các quy tắc xử lý gói tin.

#### **2. Bảng `nat` và các chuỗi (chains)**

Bảng **`nat`** trong iptables là nơi chứa các quy tắc liên quan đến NAT, với 3 chuỗi chính:

- **PREROUTING:** Xử lý các gói tin ngay khi chúng đến interface mạng. Tại đây chưa có quyết định định tuyến, nên gói tin chưa biết sẽ được xử lý tại máy cục bộ hay được chuyển tiếp.
- **POSTROUTING:** Xử lý các gói tin ngay trước khi chúng rời khỏi interface mạng.
- **OUTPUT:** Xử lý gói tin được tạo từ chính máy Linux cục bộ.

**Quá trình xử lý gói tin:**

![https://www.karlrupp.net/en/computer/computer/graphics/nat-chains.gif](https://www.karlrupp.net/en/computer/computer/graphics/nat-chains.gif)

1. Khi gói tin đến, nó sẽ đi qua **PREROUTING**.
2. Nếu máy nhận là chính máy cục bộ, gói tin sẽ được chuyển tới tiến trình tương ứng.
3. Nếu gói tin cần chuyển tiếp (forward) đến một interface khác, sau khi qua PREROUTING, nó sẽ được định tuyến (routing) tới interface đích. Trước khi rời khỏi interface đó, gói tin đi qua **POSTROUTING**.
4. Đối với gói tin được tạo từ máy cục bộ, chúng bỏ qua PREROUTING, đi qua **OUTPUT** và sau đó đến **POSTROUTING**.

**Lưu ý:** Phải **kích hoạt tính năng IP forwarding** trong kernel để cho phép chuyển tiếp gói tin giữa các mạng.

------

#### **3. Cách kích hoạt và nạp các module cần thiết**

Trước khi cấu hình NAT hoặc thực hiện bất kỳ thao tác nào với gói tin, cần đảm bảo các tính năng và module liên quan được kích hoạt.
 Các bước thực hiện:

1. **Kích hoạt IP forwarding:**
    Mặc định, IP forwarding bị vô hiệu hóa. Để bật tính năng này:

   ```bash
   echo "1" > /proc/sys/net/ipv4/ip_forward
   ```

2. **Nạp các module liên quan:**
    (Những lệnh này thường không cần thiết trên các phiên bản kernel mới vì module đã được tự động nạp, nhưng nếu gặp lỗi, hãy thử các lệnh sau):

   - Nạp module iptables:

     ```bash
     modprobe ip_tables
     ```

   - Kích hoạt theo dõi kết nối (connection tracking):

     ```bash
     modprobe ip_conntrack
     ```

   - Nạp module hỗ trợ giao thức IRC:

     ```bash
     modprobe ip_conntrack_irc
     ```

   - Nạp module hỗ trợ giao thức FTP:

     ```bash
     modprobe ip_conntrack_ftp
     ```

## 3. Giải thích NAT bằng ví dụ kết nối subnet nội bộ với Internet

#### **1. Phép tương tự (analogon): Chủ nhà và người thuê nhà**

 Hãy xem NAT hoạt động tương tự như một câu chuyện về chủ nhà và những người thuê nhà:

- **Chủ nhà (router):** Người trung gian kết nối giữa thuê nhà (máy tính nội bộ) và bưu tá (Internet).
- **Người thuê nhà (máy tính trong subnet):** Không có địa chỉ nhận thư riêng (tương tự như IP công khai).
- **Hộp thư chung (router's address):** Mọi thư từ đều phải gửi qua chủ nhà, người sẽ gắn địa chỉ riêng cho từng thuê nhà dựa trên "pigeon hole" (port).

**Quy trình:**

1. **Gửi thư từ subnet:**
    Người thuê gửi thư đến chủ nhà, chủ nhà sẽ thay đổi địa chỉ gửi (địa chỉ máy tính trong subnet) bằng địa chỉ của chính mình (địa chỉ router) và kèm theo số pigeon hole (port).
2. **Nhận thư trả lời:**
    Khi thư trả lời đến địa chỉ của chủ nhà (router), chủ nhà sẽ tra cứu pigeon hole để biết thuê nhà nào đã gửi yêu cầu ban đầu, sau đó chuyển tiếp thư lại cho thuê nhà tương ứng.
3. **Tương tác trong mạng nội bộ:**
    Quá trình tương tự diễn ra trong mạng:
   - Máy khách trong subnet gửi gói tin tới router.
   - Router thay thế địa chỉ socket của máy khách bằng địa chỉ socket của chính nó.
   - Khi nhận được phản hồi, router dịch địa chỉ đích về socket ban đầu của máy khách.

------

#### **2. NAT trong subnet nội bộ**

 Cách hoạt động của NAT trong mạng nội bộ tương ứng với phép tương tự:

- Máy khách (trong subnet nội bộ) gửi gói tin tới máy chủ trên Internet, sử dụng router làm cổng mặc định (default gateway).
- Router thay đổi địa chỉ nguồn (source socket) trong gói tin để sử dụng địa chỉ của chính nó.
- Khi phản hồi từ Internet quay lại, router biết địa chỉ socket nào tương ứng với máy khách nào và chuyển tiếp gói tin về đúng máy đó.

#### 3. Cách thiết lập quy tắc NAT

**1. Yêu cầu:**
 Gói tin từ mạng nội bộ (local net) gửi ra Internet cần thay đổi địa chỉ IP của người gửi thành địa chỉ của router.

**2. Câu lệnh mẫu để chia sẻ kết nối Internet:**

```bash
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
```

**Giải thích lệnh:**

- **`iptables`:** Công cụ dòng lệnh để cấu hình kernel.
- **`-t nat`:** Chọn bảng "nat" để cấu hình các quy tắc NAT.
- **`-A POSTROUTING`:** Thêm (append) quy tắc vào chain POSTROUTING.
- **`-o eth1`:** Quy tắc áp dụng cho gói tin ra từ giao diện mạng thứ hai (`eth1`).
- **`-j MASQUERADE`:** Thực hiện hành động "masquerade", thay địa chỉ IP của người gửi bằng địa chỉ của router.

**Lưu ý:**

- Các gói tin do router tự tạo cũng đi qua chain POSTROUTING, nhưng thường không thay đổi vì kernel cố gắng giữ nguyên cổng nguồn.

- Giao diện `eth1` có thể là bất kỳ loại nào như ISDN, DSL (`ppp0`, `ippp0`).

- Kiểm tra danh sách các giao diện mạng khả dụng bằng:

  ```bash
  ifconfig
  ```

------

#### 4. Hạn chế của NAT

- **Chỉ hỗ trợ kết nối từ mạng nội bộ ra Internet:**
   Máy tính nội bộ có thể truy cập Internet, nhưng máy từ Internet không thể kết nối trực tiếp vào máy tính nội bộ.
- **Giới hạn kết nối từ bên ngoài:**
  - Máy tính từ Internet chỉ có thể gửi gói tin đến router và hy vọng cổng đó khả dụng.
  - Thông thường, các cổng không khả dụng, dẫn đến gói tin bị từ chối.
- **Giải pháp cho các dịch vụ cụ thể:**
   Có thể ánh xạ tĩnh các cổng trên router đến máy trong mạng nội bộ, ví dụ:
  - Chuyển tiếp gói tin đến cổng 80 trên router đến máy chủ HTTP trong mạng nội bộ.
  - Thường được sử dụng cho chơi game trực tuyến, đặc biệt khi muốn làm máy chủ.

## 4. Cấu trúc và cách hoạt động của `iptables`

#### 1. Cấu trúc chung của một lệnh `iptables`:

```bash
iptables [-t table] command [match pattern] [action]
```

- **`-t table`:** Chọn bảng cần thao tác. Trong NAT, luôn sử dụng bảng `nat`.
- **`command`:** Chỉ định hành động (như thêm, liệt kê, hoặc xóa quy tắc).
- **`match pattern`:** Mô tả gói tin nào sẽ bị ảnh hưởng (ví dụ: địa chỉ IP, giao thức, cổng).
- **`action`:** Hành động sẽ thực hiện nếu gói tin khớp mẫu (ví dụ: thay đổi địa chỉ nguồn hoặc đích).

------

#### 2. Các bảng chính trong `iptables`

- **`nat`:** Dùng để thực hiện NAT (Network Address Translation).
- **`filter`:** Dùng để lọc gói tin (default).
- **`mangle`:** Dùng để điều chỉnh các trường của gói tin (ít liên quan đến NAT).

Trong NAT, bảng mặc định là `filter`, nên cần chỉ định rõ `-t nat` trong lệnh.

------

#### 3. Các lệnh cơ bản với bảng `nat`

1. **Thêm quy tắc vào một chuỗi (chain):**

   ```bash
   iptables -t nat -A chain [...]
   ```

   - **`chain`:** Có thể là `PREROUTING`, `POSTROUTING`, hoặc `OUTPUT`.

2. **Liệt kê các quy tắc trong bảng:**

   ```bash
   iptables -t nat -L
   ```

3. **Xóa một quy tắc cụ thể bằng chỉ số (`myindex`):**

   ```bash
   iptables -t nat -D chain myindex
   ```

4. **Xóa tất cả các quy tắc trong một chuỗi:**

   ```bash
   iptables -t nat -F chain
   ```

5. **Xem hướng dẫn chi tiết từ manual:**

   ```bash
   man iptables
   ```

------

#### 5. Các mẫu khớp (match patterns) phổ biến

1. **TCP packets từ IP cụ thể:**

   ```bash
   iptables -t nat -A POSTROUTING -p tcp -s 192.168.1.2 [...]
   ```

2. **UDP packets đến một IP cụ thể:**

   ```bash
   iptables -t nat -A POSTROUTING -p udp -d 192.168.1.2 [...]
   ```

3. **Tất cả gói tin từ 192.168.x.x qua giao diện `eth0`:**

   ```bash
   iptables -t nat -A PREROUTING -s 192.168.0.0/16 -i eth0 [...]
   ```

4. **Tất cả gói tin không phải TCP và không từ IP 192.168.1.2:**

   ```bash
   iptables -t nat -A PREROUTING -p ! tcp -s ! 192.168.1.2 [...]
   ```

5. **Gói tin rời qua `eth1`:**

   ```bash
   iptables -t nat -A POSTROUTING -o eth1 [...]
   ```

6. **TCP packets từ một IP với phạm vi port:**

   ```bash
   iptables -t nat -A POSTROUTING -p tcp -s 192.168.1.2 \
      --sport 12345:12356 -d 123.123.123.123 --dport 22 [...]
   ```

------

#### 6.Hành động cho các gói tin khớp

1. **Thay đổi địa chỉ nguồn (SNAT):**

   ```bash
   iptables [...] -j SNAT --to-source 123.123.123.123
   ```

   - Dùng khi muốn thay địa chỉ nguồn thành một địa chỉ IP cố định.

2. **Masquerade (dùng địa chỉ giao diện ra ngoài):**

   ```bash
   iptables [...] -j MASQUERADE
   ```

   - Hữu ích khi IP giao diện mạng thay đổi động, ví dụ trên kết nối PPPoE.

3. **Thay đổi địa chỉ đích (DNAT):**

   ```bash
   iptables [...] -j DNAT --to-destination 123.123.123.123:22
   ```

   - Chuyển tiếp gói tin đến một máy cụ thể trong mạng nội bộ.

4. **Chuyển tiếp đến một cổng cục bộ:**

   ```bash
   iptables [...] -j REDIRECT --to-ports 8080
   ```

   - Dùng để chuyển hướng gói tin đến một dịch vụ chạy cục bộ trên router.

## 5. Chi tiết về các hành động trong iptables

#### 1. **Source-NAT (SNAT): Thay đổi địa chỉ nguồn tĩnh**

- **Chức năng:** SNAT thay đổi địa chỉ IP nguồn của gói tin thành một địa chỉ cố định (IP tĩnh).

- **Ứng dụng:** Thường được sử dụng khi router có địa chỉ IP tĩnh, giúp giảm tải vì không cần kiểm tra địa chỉ IP của giao diện mạng mỗi lần xử lý gói tin.

- **Chuỗi áp dụng:** Chỉ có ý nghĩa trong chuỗi **POSTROUTING** (gói tin rời khỏi router).

- **Cú pháp tùy chọn:**

  ```bash
  --to-source <ipaddr>[-<ipaddr>][:port-port]
  ```

  - **`<ipaddr>`:** Địa chỉ IP thay thế.
  - **`port-port` (tùy chọn):** Phạm vi cổng được ánh xạ.

- **Ưu điểm:** Hiệu suất cao hơn MASQUERADE, phù hợp với môi trường IP cố định.

- **Hạn chế:** Không linh hoạt với IP động.

------

#### 2. **MASQUERADE: Thay đổi địa chỉ nguồn thành IP của router**

- **Chức năng:** Thay đổi địa chỉ IP nguồn của gói tin thành địa chỉ IP của giao diện mạng ra ngoài.
- **Ứng dụng:** Hữu ích khi sử dụng địa chỉ IP động, như trong kết nối PPPoE.
- **Chuỗi áp dụng:** Chỉ có ý nghĩa trong chuỗi **POSTROUTING**.
- **Cú pháp tùy chọn:** Không có.
- **Ưu điểm:** Không cần cập nhật quy tắc nếu IP giao diện mạng thay đổi.
- **Hạn chế:** Hiệu suất thấp hơn SNAT vì phải kiểm tra IP động ở mỗi gói tin.

------

#### 3. **Destination-NAT (DNAT): Thay đổi địa chỉ đích**

- **Chức năng:** DNAT thay đổi địa chỉ đích của gói tin, thường dùng để chuyển tiếp gói tin đến một máy chủ phía sau firewall.

- **Ứng dụng:**

  - Máy chủ chạy phía sau firewall.
  - Chuyển hướng gói tin đến một địa chỉ IP và/hoặc cổng khác.

- **Chuỗi áp dụng:**

  - **PREROUTING:** Gói tin nhận được trước khi quyết định định tuyến.
  - **OUTPUT:** Gói tin do router tạo ra.

- **Cú pháp tùy chọn:**

  ```bash
  --to-destination <ipaddr>[-<ipaddr>][:port-port]
  ```

  - **`<ipaddr>`:** Địa chỉ IP thay thế.
  - **`port-port` (tùy chọn):** Phạm vi cổng được ánh xạ.

- **Ưu điểm:** Hữu ích cho việc triển khai máy chủ trong mạng nội bộ.

------

#### 4. **REDIRECT: Chuyển hướng gói tin về router**

- **Chức năng:** REDIRECT là một trường hợp đặc biệt của DNAT, chuyển hướng gói tin đến một cổng cục bộ trên router.

- **Ứng dụng:**

  - Cấu hình proxy trong suốt (transparent proxy).
  - Chuyển hướng gói tin đến một dịch vụ cục bộ.

- **Chuỗi áp dụng:**

  - **PREROUTING:** Xử lý gói tin từ bên ngoài vào trước khi định tuyến.
  - **OUTPUT:** Xử lý gói tin do router tạo ra.

- **Cú pháp tùy chọn:**

  ```bash
  --to-ports <port>[-<port>]
  ```

  - **`port` (bắt buộc):** Cổng cục bộ được chuyển hướng.

- **Ưu điểm:** Đơn giản hóa việc triển khai các dịch vụ proxy hoặc kiểm tra lưu lượng.

------

### Tổng kết so sánh

| **Hành động**  | **Chức năng chính**                        | **Chuỗi áp dụng**  | **Tùy chọn**       | **Ưu điểm**                                   |
| -------------- | ------------------------------------------ | ------------------ | ------------------ | --------------------------------------------- |
| **SNAT**       | Thay đổi địa chỉ nguồn tĩnh                | POSTROUTING        | `--to-source`      | Hiệu suất cao, phù hợp IP tĩnh.               |
| **MASQUERADE** | Thay đổi địa chỉ nguồn thành IP của router | POSTROUTING        | Không              | Linh hoạt với IP động.                        |
| **DNAT**       | Thay đổi địa chỉ đích                      | PREROUTING, OUTPUT | `--to-destination` | Hữu ích cho mạng nội bộ, server sau firewall. |
| **REDIRECT**   | Chuyển hướng gói tin về router             | PREROUTING, OUTPUT | `--to-ports`       | Dễ cấu hình proxy hoặc dịch vụ cục bộ.        |

## 6. Ứng dụng của NAT (Network Address Translation)

#### 1.**Proxy trong suốt (Transparent Proxying)**

![https://www.karlrupp.net/en/computer/computer/graphics/transparent-proxy-en.gif](https://www.karlrupp.net/en/computer/computer/graphics/transparent-proxy-en.gif)

Trong trường hợp này, NAT được sử dụng để chuyển hướng (redirect) tất cả các gói tin đến cổng 80 (HTTP) sang cổng 8080 (nơi một proxy server đang hoạt động). Lệnh iptables được sử dụng như sau:

```bash
# Proxy trong suốt:
# (Mạng nội bộ tại eth0, proxy server chạy trên cổng 8080)
$> iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 \
   -j REDIRECT --to-ports 8080
```

**Điều kiện yêu cầu**:

1. Proxy server phải đang hoạt động trên cổng 8080.
2. Có thể cần cấu hình đặc biệt hoặc biên dịch lại proxy server để hỗ trợ proxy trong suốt.

**Nhược điểm**:

- Tăng tải CPU, đặc biệt trong mạng lớn.
- Một số trình duyệt cũ hoặc đơn giản có thể không hỗ trợ tốt cách này.

------

#### **Vượt qua tường lửa hạn chế**

![https://www.karlrupp.net/en/computer/computer/graphics/port110-en.gif](https://www.karlrupp.net/en/computer/computer/graphics/port110-en.gif)

Nếu bạn đang bị hạn chế bởi một tường lửa chỉ mở một số cổng nhất định, NAT có thể giúp bạn vượt qua bằng cách chuyển hướng các cổng được phép sang các dịch vụ cần thiết. Ví dụ:

1. **Xác định cổng mở bằng `nmap`:**

   ```bash
   $> nmap www.example.com
   ```

2. **Chuyển hướng SSH qua cổng mở:**

   ```bash
   # Chuyển hướng SSH từ cổng 5000 sang cổng 22:
   $> iptables -t nat -A PREROUTING -p tcp --dport 5000 -j REDIRECT --to-ports 22
   ```

3. **Truy cập POP3 qua cổng mở khác:**

   ```bash
   # Chuyển hướng cổng 5001 tới POP3 (cổng 110) trên 123.123.123.123:
   $> iptables -t nat -A PREROUTING -p tcp --dport 5001 \
      -j DNAT --to-destination 123.123.123.123:110
   
   # Thay đổi địa chỉ nguồn để tương ứng với máy chuyển hướng:
   $> iptables -t nat -A POSTROUTING -p tcp --dport 110 \
      -j MASQUERADE
   ```

4. **Tránh proxy của nhà cung cấp dịch vụ (nếu hợp pháp):**

   ```bash
   # Chuyển hướng lưu lượng HTTP sang proxy cá nhân tại cổng 5002:
   $> iptables -t nat -A OUTPUT -p tcp --dport 80 \
      -j DNAT --to-destination 111.111.111.111:5002
   ```

------

#### **Chạy máy chủ sau router NAT**

Nếu bạn muốn chạy một máy chủ (ví dụ HTTP) trong mạng nội bộ phía sau một router NAT, cần thiết lập NAT để chuyển hướng lưu lượng từ ngoài vào máy chủ.

**Ví dụ**:

- **Máy chủ HTTP trong mạng nội bộ**: `192.168.1.2`
- Router NAT:
  - Mạng nội bộ: `192.168.1.1`
  - Mạng ngoài (internet): `123.123.123.123`

Lệnh để chuyển hướng lưu lượng HTTP từ internet đến máy chủ:

```bash
# Chuyển hướng lưu lượng HTTP tới máy chủ 192.168.1.2:
$> iptables -t nat -A PREROUTING -p tcp -i eth1 --dport 80 -j DNAT --to 192.168.1.2
```

**Lợi ích**:

- Người dùng có thể truy cập máy chủ HTTP qua địa chỉ IP công khai của router (`123.123.123.123`).

------

#### **Cài đặt proxy bắt buộc trong mạng nội bộ**

Để tiết kiệm băng thông, một proxy server có thể được triển khai trên mạng nội bộ để xử lý tất cả lưu lượng HTTP.

1. **Cách đơn giản**:
   - Yêu cầu người dùng cấu hình trình duyệt để sử dụng proxy tại cổng 8080.
   - Sau đó chặn lưu lượng ra ngoài trực tiếp qua cổng 80.
2. **Sử dụng proxy trong suốt**:
   - Triển khai NAT để tự động chuyển hướng tất cả lưu lượng HTTP qua proxy mà không cần cấu hình trên từng thiết bị.

Cách này hữu ích trong mạng lớn vì tiết kiệm công sức cấu hình thủ công, nhưng có thể tăng tải hệ thống proxy và gây vấn đề với một số ứng dụng không hỗ trợ proxy trong suốt.

------

Những ví dụ trên chỉ là một phần nhỏ ứng dụng của NAT. Tùy thuộc vào nhu cầu cụ thể, bạn có thể sáng tạo thêm cách sử dụng NAT để đáp ứng các yêu cầu mạng phức tạp hơn.
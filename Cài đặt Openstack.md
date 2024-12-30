# Cài đặt Openstack 

## 1. Cài đặt môi trường 

### 1.1. Setup mysql trên controller node

Keystone cần một cơ sở dữ liệu để lưu trữ các loại dữ liệu sau:

- **Người dùng (Users):** Thông tin về tài khoản người dùng, như tên, mật khẩu, và ID.
- **Dự án (Projects):** Tổ chức hoặc nhóm trong OpenStack để quản lý tài nguyên.
- **Vai trò (Roles):** Quyền hạn mà người dùng có trong một dự án cụ thể.
- **Tokens:** Token được phát hành khi người dùng hoặc dịch vụ xác thực.
- **Endpoints:** URL của các dịch vụ khác trong OpenStack.
- **Policies:** Chính sách quản lý quyền truy cập

Ý nghĩa của việc chỉnh sửa tệp cấu hình `/etc/mysql/mariadb.conf.d/99-openstack.cnf` với các tham số trên là tối ưu hóa MariaDB để phù hợp với yêu cầu của môi trường OpenStack, đảm bảo cơ sở dữ liệu hoạt động ổn định, an toàn và hiệu quả.

```
[mysqld]
bind-address = 10.0.0.11

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```

------

**1. Đảm bảo truy cập đúng mạng (bind-address):**

- **Mục tiêu:** Chỉ cho phép các dịch vụ và máy trong mạng quản lý (management network) kết nối với MariaDB.
- Ý nghĩa:
  - Tăng cường bảo mật bằng cách giới hạn các kết nối từ các nguồn đáng tin cậy.
  - Hạn chế rủi ro truy cập trái phép từ mạng khác hoặc internet.

------

**2. Tăng hiệu năng và tính tin cậy (InnoDB storage engine):**

- **Mục tiêu:** Sử dụng engine lưu trữ InnoDB mặc định để quản lý dữ liệu trong MariaDB.
- Ý nghĩa:
  - InnoDB hỗ trợ các tính năng cao cấp như giao dịch (transactions) và khóa cấp độ dòng (row-level locking), rất cần thiết để xử lý khối lượng lớn truy vấn trong OpenStack.
  - Tăng khả năng khôi phục dữ liệu nếu xảy ra sự cố.

------

**3. Quản lý dữ liệu hiệu quả hơn (innodb_file_per_table):**

- **Mục tiêu:** Lưu dữ liệu của mỗi bảng trong một tệp riêng thay vì gộp chung vào một tệp lớn.
- Ý nghĩa:
  - Dễ dàng quản lý và tối ưu hóa cơ sở dữ liệu.
  - Giảm kích thước tệp lưu trữ tổng thể nếu xóa hoặc thu nhỏ bảng.

------

**4. Đảm bảo khả năng xử lý kết nối lớn (max_connections):**

- **Mục tiêu:** Tăng số lượng kết nối đồng thời mà MariaDB có thể xử lý lên 4096.
- Ý nghĩa:
  - Trong môi trường OpenStack, nhiều dịch vụ cần kết nối tới MariaDB cùng lúc, ví dụ: Nova, Neutron, Keystone.
  - Đảm bảo dịch vụ không bị lỗi do giới hạn kết nối thấp.

------

**5. Hỗ trợ dữ liệu đa ngôn ngữ (UTF-8 character set):**

- **Mục tiêu:** Đặt bộ ký tự mặc định là UTF-8 và thứ tự sắp xếp là `utf8_general_ci`.
- Ý nghĩa:
  - Đảm bảo MariaDB có thể lưu trữ và xử lý các ký tự từ nhiều ngôn ngữ khác nhau, phù hợp với các môi trường đa quốc gia hoặc sử dụng dữ liệu phức tạp.
  - Tránh lỗi hiển thị hoặc xử lý dữ liệu khi làm việc với ký tự đặc biệt.

### 1.2. Cài Message Queue

**Message Queue** để sử dụng cho **Keystone** trong OpenStack, mục tiêu là đảm bảo **Keystone** có thể giao tiếp hiệu quả với các thành phần khác thông qua hệ thống hàng đợi tin nhắn (**Message Queue**, thường là RabbitMQ). 

Keystone không trực tiếp phụ thuộc vào Message Queue như các dịch vụ Nova, Neutron, hay Cinder, nhưng nếu bạn triển khai Keystone trong môi trường OpenStack tích hợp, Message Queue hỗ trợ:

- **Đồng bộ hóa trạng thái dịch vụ Keystone với các dịch vụ khác.**
- **Truyền thông tin token, trạng thái xác thực, hoặc dữ liệu liên quan giữa Keystone và các dịch vụ sử dụng.**

**Ví dụ giao tiếp thực tế message trong openstack:**

1. **Tạo máy ảo:**
   - Người dùng gửi yêu cầu tạo máy ảo qua Horizon hoặc API.
   - Nova sẽ gửi một thông điệp tới RabbitMQ để yêu cầu các dịch vụ khác (Glance cung cấp image, Neutron thiết lập mạng, Cinder gắn ổ đĩa).
   - Các dịch vụ xử lý yêu cầu và gửi phản hồi qua RabbitMQ.
2. **Quản lý trạng thái:**
   - Nếu có sự cố (như không thể tạo mạng), dịch vụ sẽ gửi thông báo qua Message Queue để Nova biết và cập nhật trạng thái.

### 1.3. cài Etcd for Ubuntu

**Etcd** là một kho dữ liệu phân tán **key-value**, được thiết kế để đảm bảo tính nhất quán mạnh (strong consistency) trong môi trường phân tán.

Nó được sử dụng phổ biến trong các hệ thống cloud và container như Kubernetes, và cũng được OpenStack sử dụng để quản lý trạng thái và phối hợp các dịch vụ.

**2.1. Quản lý trạng thái dịch vụ:**

- Các dịch vụ OpenStack cần chia sẻ trạng thái hoặc thông tin cấu hình giữa các node (máy chủ).

- Etcd

   đóng vai trò trung tâm trong việc lưu trữ các thông tin này, giúp các node đồng bộ với nhau, đặc biệt trong các trường hợp như:

  - Lập lịch máy ảo (Nova).
  - Đồng bộ trạng thái mạng (Neutron).
  - Quản lý khối lưu trữ (Cinder).

**2.2. Hỗ trợ cơ chế lock (khóa):**

- Etcd cung cấp cơ chế khóa phân tán, được sử dụng để tránh xung đột khi nhiều node hoặc dịch vụ cần truy cập hoặc thay đổi dữ liệu cùng một lúc.
  - Ví dụ: Tránh tình trạng nhiều máy tính chủ cố gắng gán tài nguyên mạng trùng lặp.

**2.3. Tăng tính khả dụng:**

- Etcd hỗ trợ môi trường phân tán với khả năng chịu lỗi cao. Trong OpenStack, Etcd giúp:
  - Đảm bảo dữ liệu vẫn có sẵn ngay cả khi một số node gặp sự cố.
  - Cung cấp thông tin chính xác và đáng tin cậy cho các dịch vụ cần sử dụng.

**2.4. Sử dụng bởi Neutron (Networking):**

- Trong OpenStack, **Neutron** sử dụng Etcd để lưu trữ thông tin cấu hình mạng và phối hợp giữa các tác nhân mạng (agents).
- Etcd giúp đảm bảo rằng các thay đổi trong cấu hình mạng được áp dụng đồng bộ trên toàn hệ thống.

**2.5. Tích hợp với các dịch vụ khác:**

- Một số thành phần mới của OpenStack, như **Placement**, cũng có thể sử dụng Etcd để quản lý thông tin về tài nguyên và trạng thái.

### **Ví dụ thực tế: Quản lý mạng với Neutron và Etcd**

Giả sử bạn có một OpenStack với **Neutron** đảm nhiệm việc quản lý các mạng ảo cho các máy ảo (VM). Trong môi trường phân tán, mỗi máy chủ (node) có thể có **network agents** giúp thực thi các tác vụ mạng (như cài đặt và cấu hình mạng cho các máy ảo).

Khi bạn tạo một mạng mới hoặc thay đổi cấu hình mạng, các trạng thái cần được lưu trữ và đồng bộ hóa giữa các node. **Etcd** là nơi giúp lưu trữ các thay đổi này và đảm bảo mọi node trong OpenStack có cùng thông tin về trạng thái mạng.

**SQL** giống như một **ngăn kéo tài liệu** lớn, nơi bạn lưu trữ tất cả các thông tin quan trọng và cần truy xuất khi cần (như danh sách khách hàng, đơn hàng, v.v.).

**Etcd** giống như một **bảng điều khiển** theo dõi trạng thái hoạt động của toàn bộ hệ thống (như các máy chủ, dịch vụ đang chạy, cấu hình mạng, v.v.). Etcd giúp hệ thống biết "hiện tại tình trạng của mọi thứ như thế nào" và giúp các dịch vụ giao tiếp với nhau.

## 2. Cài đặt Keystone 

### 2.1. 

```
keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne

```

Lệnh `keystone-manage bootstrap` là bước quan trọng trong quá trình cài đặt và cấu hình **Keystone** — dịch vụ xác thực và quản lý danh tính trong OpenStack. 



```
export OS_USERNAME=admin
export OS_PASSWORD=ADMIN_PASS
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3

```

đây là bước thiết lập biến môi trường 

Lệnh này thiết lập các **biến môi trường (environmental variables)** cần thiết để bạn có thể tương tác với dịch vụ **Keystone** thông qua **OpenStack CLI**. Các biến môi trường này cung cấp thông tin xác thực và cấu hình cho OpenStack, đặc biệt là cho tài khoản quản trị viên (`admin`), giúp bạn thực hiện các lệnh trong OpenStack với quyền quản trị.
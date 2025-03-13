# File Host Inventory

## 1. Inventory là gì?

Inventory trong Ansible là một thành phần quan trọng 

chứa danh sách các máy chủ và các thông tin cần thiết để quản lí chúng 

- Mặc định Inventory được lưu trong tệp /etc/ansible/hosts
- Hoặc cũng có thể tạo file Inventory tùy chỉnh theo yêu cầu 

## 2. Cấu trúc của Inventory

**Cú pháp cơ bản:**

Inventory sử dụng định dạng INI, chia các máy chủ thành các nhóm (groups) và khai báo các biến (variables) liên quan

Ví dụ:

```
[web]
192.168.1.10 ansible_user=ubuntu
192.168.1.11 ansible_user=ubuntu

[db]
192.168.1.20 ansible_user=root

```

Giải thích:

- `[web]`và `[db]`: Tên của các nhóm máy chủ 
- `192.168.1.10`: địa chỉ ip hoặc hostname của máy chủ
- `ansible_user`: Biến chỉ định người dùng để kết nối

Có thể ghi chi tiết hơn như sau:

```
[web_servers]
web1 ansible_host=10.10.0.140 ansible_user=server1
web2 ansible_host=10.10.0.141 ansible_user=server2

[host_server]
host1 ansible_host=10.10.0.144 ansible_user=host1
host2 ansible_host=10.10.0.145 ansible_user=host02
~                                                         
```

Việc nay giúp đặt tên có các host để dễ dàng theo dõi

## 3. Cách sử dụng Inventory

### 3.1. Inventory tĩnh 

- lưu thông tin cố định về các máy chủ
- giống như ví dụ trên 

### 3.2. Inventory động 

- Tự động lấy danh sách máy chủ từ nguồn bên ngoài
  - AWS EC2
  - OpenStack
  - Các cơ sở dữ liệu nội bộ

### 3.3. Cú pháp nâng cao của Inventory 

#### 3.3.1. Sử dụng biến toàn cục và biến nhóm 

- Khai báo biến toàn cục:

  ```
  [all:vars]
  ansible_user=ubuntu
  ansible_ssh_private_key_file=~/.ssh/id_rsa
  
  ```

- Khai báo biến nhóm:

  ```
  [web:vars]
  ansible_port=22
  
  ```

#### 3.3.2. Sử dụng biến cho từng host

```
[web]
192.168.1.10 ansible_user=admin
192.168.1.11 ansible_user=root ansible_port=2222

```

**Chọn hosts bằng patterns**

- Chọn tất cả: `all`.
- Chọn nhóm: `[web]`.
- Dải IP: `192.168.1.[10:20]`.
- Loại trừ: `all:!db`.
- Regex: `~(web|db).*`.

## 4. Các lệnh cơ bản khi làm việc với Inventory

- Kiểm tra kết nối 

  ```
  ansible all -i /path/to/your_inventory -m ping
  ```

- Lệnh liệt kê các host

  ```
  ansible-inventory -i /path/to/your_inventory --list
  ```

- Liệt kê nhóm host cụ thể

  ```
  ansible web -i /path/to/your_inventory --list-hosts
  ```

  
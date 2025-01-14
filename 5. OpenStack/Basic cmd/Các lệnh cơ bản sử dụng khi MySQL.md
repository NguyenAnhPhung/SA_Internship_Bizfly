# Các lệnh cơ bản sử dụng khi MySQL 

## 1. Đăng nhập vào MySQL bằng tài khoản root

```
mysql -u root -p
```

## 2. Quản lí database

- hiển thị danh sách csdl

  ```
  show databases;
  ```

- tạo csdl 

  ```
  create database database_name;
  ```

- xóa cơ sở dữ liệu 

  ```
  drop database database_name;
  ```

- sử dụng 1 csdl

  ```
  use database_name
  ```

## 3. Quản lí người dùng 

- Hiển thị danh sách người dùng

  ```
  select user, host from msql.user;
  ```

- tạo người dùng mới 

  ```
  create user 'username'@'loacalhost' identified by 'password';
  ```

  **`localhost`** chỉ ra rằng người dùng **`username`** chỉ có thể đăng nhập vào MySQL từ máy cục bộ (máy chủ nơi MySQL đang chạy).

  **Khi nào cần thay đổi localhost ?**

  - nếu muốn người dùng kết nối từ một địa chỉ cụ thể 

    ```
    CREATE USER 'username'@'192.168.1.100' IDENTIFIED BY 'password';
    ```

  - nếu muốn người dùng có thể kết nối từ mọi IP

    ```
    CREATE USER 'username'@'%' IDENTIFIED BY 'password';
    ```

    Ký tự **`%`** là ký tự đại diện (wildcard) để chỉ mọi địa chỉ IP.

  

- cấp quyền cho người dùng 

  ```
  GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';
  FLUSH PRIVILEGES;
  ```

## 4. Quy trình tạo cell trong Openstack 

### 4.1. Đăng kí cell0

cell0 được sử dụng để lưu thông tin về các yêu cầu không thành công (ví dụ không lập lịch được máy ảo)

```
sudo nova-manage cell_v2 map_cell0
```

### 4.2. Tạo cell1 (hoặc cell khác)

Tạo cell để quản lí các tài nguyên tính toán 

```
sudo nova-manage cell_v2 create_cell --name=cell1 --verbose

```

Xác minh danh sách cell

```
sudo nova-manage cell_v2 list_cells
```

### 4.3. Khởi tạo csdl cell 

đồng bộ csdl cho cell

```
sudo nova-manage db sync
```

Mỗi cell cần có một cơ sở dữ liệu Nova riêng (nếu muốn phân tách hoàn toàn).

Nếu có nhiều cell (ví dụ: cell1, cell2), cần cấu hình chính xác `nova.conf` để chỉ định thông tin kết nối đến cell.
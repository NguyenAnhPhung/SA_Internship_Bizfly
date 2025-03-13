# Các lệnh cơ bản sử dụng trong Openstack

## 1. lệnh xác thực và thông tin 

- kiểm tra token ( xác thực keystone)

  ```
  openstack token issue
  ```

  - lấy một token tạm thời để thực hiện thao tác trên openstack 
  - token chứng thực rằng người đùng đã được chứng thực thành công qua keystone 

- Liệt kê API endpoints:

  ```
  openstack endpoint list
  
  ```

  - endpoint là URL để truy cập vào một dịch vụ cụ thể

  - là cầu nối giữa các thành phần của openstack và dịch vụ keystone 

  - được cấu hình trong quá trình vận hành openstack 

  - **Dạng cơ bản của Endpoint**: Một endpoint thường bao gồm:

    - **Hostname hoặc IP** của dịch vụ.
    - **Cổng** mà dịch vụ lắng nghe (ví dụ: 8774 cho Nova, 9292 cho Glance).
    - **Giao thức** (HTTP hoặc HTTPS).
    - **Đường dẫn dịch vụ** (nếu có).

    Ví dụ:

    ```
    http://controller:8774/v2.1
    
    ```

    **Quá trình Endpoint hoạt động**

    1. Người dùng hoặc thành phần gửi yêu cầu xác thực đến **Keystone**.
    2. Keystone cấp một **token** và danh sách các endpoint cho người dùng.
    3. Người dùng sử dụng endpoint tương ứng để truy cập dịch vụ (ví dụ, URL của Nova để tạo máy ảo).
  
- Lệnh liệt kêt các project có trong openstack 

  ```
  openstack project list
  ```

  
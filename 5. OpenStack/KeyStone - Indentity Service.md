# KeyStone - Indentity Service 

## 1. Tổng quan

### 1.1. Keystone là gì?

Keystone là dịch vụ cung cập cơ chế quản lí danh tính, 

- Xác thực (authentication),
- Ủy quyền (authorization), 
- Và mộ số danh mục dịch vụ (service catalog)

Là dịch vụ quan trọng thường là điểm tiếp xúc đầu tiên giữa người dùng hoặc dịch vụ với hệ thống Openstack 

### 1.2. Chức năng chính 

- **Quản lí xác thực và ủy quyền:**
  - User cần đăng nhập (authentication) thông qua keystone để truy cập các dịch vụ khác của openstack
  - Các dịch vụ của Openstack cũng sử dụng keystone để kiểm tra danh tính người dùng và xác minh quyền truy cập
- **Danh mục dịch vụ (Service Catalog)**
  - Là danh sách các dịch vụ sẵn có trong môi trường Openstack
  - Các dịch vụ và user có thể sử dụng catalog để tìm thông tin về các endpoint (điểm kết nôi API) của dịch vụ khác 
  - Mỗi dịch trong danh mục có thể có nhiều endpoint, với 3 loại chính:
    - Admin: Endpoint dành cho quản trị viên, thường được bảo vệ và chỉ truy cập từ mạng quản trị nội bộ 
    - Internal: Endpoint sử dụng nội bộ giữa các dịch vụ Openstack
    - Public: Endpoint công khai, có thể truy cập từ internet
- f


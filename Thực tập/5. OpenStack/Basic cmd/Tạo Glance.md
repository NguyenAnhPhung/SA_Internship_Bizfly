# Tạo Glance 

Glance là dịch vụ quản lí image trong Openstack cung cấp các cơ chế 

- tạo 
- quản lí 
- phân phối images

**Hoạt động yêu cầu cung cấp image khi tạo máy ảo **

![img](https://docs.openstack.org/install-guide/_images/openstack-arch-kilo-logical-v1.png)

**Trong Glance Image Service**

1. Nova API gửi yêu cầu đến Glance API ( Yêu cầu về thông tin của image cần thiết)
2. Glance API xử lí yêu cầu truy xuất dư liệu từ glance registry ( bao gồm kích thước, định dạng, trạng thái .....)
3. Glance registry tương tác với Glance database để lấy thông tin metadata về image này 
4.  Glance API trả metadata của images cho Nova API để thằng này biết thêm thông tin về image (tức là nó cần thông tin của image này để biết được là phần cứng ram, cpu có đủ để đáp ứng không )
5. Glance API xác định vị trí của image trong Glance store sau đó gửi image này cho nova API
6. nova API nhận được image này rồi nó chuyển image tới compute node
7. compute node này sẽ sử dụng image này để tạo máy ảo 

## 1. Tạo Glance database 

**Bước 1 : Tạo Glance Database**

- Vai trò của Glance Database trong Openstack 
  - Lưu trữ metadata của image 
  - Theo dõi quyền sở hữu và truy cập: Đảm bảo chỉ những người dùng được ủy quyền mới có thể truy cập và sử dụng các image này 
  - Theo dõi trạng thái và sự kiện: lưu trữ thông tin về trạng thái hiện tại của các image và các sự kiện liên quan đến chúng 
- Cấp quyền truy cập csdl cho người dùng glance
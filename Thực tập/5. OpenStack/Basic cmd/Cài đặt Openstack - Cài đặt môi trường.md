# Cài đặt Openstack - Cài đặt môi trường



yêu cầu 2 máy ảo 
1 controller : 2CPU, 8G Ram, 20G Disk

1 compute: 2CPU, 8G Ram, 20G Disk

Mỗi máy 2 NIC:

1 managment network: 10.0.0.0/24

1 provider network: 213.0.113.0/24



## Bước 1: setup mạng cho các node 

![img](https://docs.openstack.org/install-guide/_images/networklayout.png)

Các kiến trúc ví dụ giả định sử dụng các mạng sau:

- Quản lý trên 10.0.0.0/24 với cổng 10.0.0.1

  Mạng này yêu cầu một cổng để cung cấp quyền truy cập Internet cho tất cả các nút cho mục đích quản trị như cài đặt gói, cập nhật bảo mật, [DNS](https://docs-openstack-org.translate.goog/install-guide/common/glossary.html?_x_tr_sl=en&_x_tr_tl=vi&_x_tr_hl=vi&_x_tr_pto=wapp#term-Domain-Name-System-DNS) và [NTP](https://docs-openstack-org.translate.goog/install-guide/common/glossary.html?_x_tr_sl=en&_x_tr_tl=vi&_x_tr_hl=vi&_x_tr_pto=wapp#term-Network-Time-Protocol-NTP) .

- Nhà cung cấp trên 203.0.113.0/24 với cổng 203.0.113.1

  Mạng này yêu cầu một cổng để cung cấp quyền truy cập Internet cho các phiên bản trong môi trường OpenStack của bạn.

## Bước 2:  Cài đặt NTP

NTP là giao thức dùng để đồng bộ hóa thời gian giữa các hệ thống máy tính qua mạng 

**Tại sao cần dùng nó trong hệ thống OpenStack?**

- Đồng bộ dữ liệu: 
  - Các thành phần của Openstach như controller, compute, storage, networking, giao tiếp với nhau thông qua API và dịch vụ nhắn tin vì vậy cần thời gian đồng bộ 
  - Log và debugging: Log file cần có thời gian chính xác để dễ dàng phân tích và sửa lỗi 
  - Tích hợp các dịch vụ khác: Openstack có thể kết nối các hệ thống bên ngoài, yêu cầu thời gian đồng bộ để đảm bảo tính toàn vẹn

**Cách cài đặt**


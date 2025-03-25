

# Network File System (NFS)

## 1. What is NFS?

NFS là một giao thức mạng được sử dụng để chia sẻ file phân tán. Nó cho phép các hệ thống máy tính truy cập và thao tác với các file trên hệ thống từ xa như thể chúng được lưu trữ cục bộ 

- File system: là cách dữ liệu được tổ chức, lưu trữ và truy xuất từ các thiết bị lưu trữ như ổ cứng SSD, HDD
- NFS: Là giao thức chia sẻ file qua mạng, cho phép nhiều máy tính truy cập vào cùng một hệ thống file từ xa như thể nó nằm trên máy cục bộ

**Cách hoạt động:**

- Mô hình client-server:
  - Server: Lưu trữ file hệ thống, chia sẻ 1 phần hoặc toàn bộ thư mục 
  - Client: Mount file hệ thống file từ xa vào hệ thống cục bộ, sau đó truy cập như file local 
- Sử dụng RPC (Remote Procedure Call)
  - NFS sử dụng RPC để định tuyến yêu cầu giữa client và server *
  - Client gửi yêu cầu đọc/ghi file, server xử lí và trả kết quả 

**Khi nào thì cần dùng đến NFS?**

- Chia sẻ dữ liệu

- Hệ thống cluster: nhiều máy tính dùng chung file hệ thống để xử lí song song *

  - quản lí dữ liệu tập chung, lưu log tập trung từ nhiều server ứng dụng 

  - 

  -     Bài toán: Dịch vụ cần chạy trên nhiều server nhưng dùng chung dữ liệu.
        
        Giải pháp: Dùng NFS kết hợp với Pacemaker/DRBD để failover.
        
        Ví dụ:
        
            Cluster Active Directory (Samba) dùng NFS để đồng bộ file cấu hình.
        
            Load balancer (HAProxy) chia sẻ session data qua NFS.
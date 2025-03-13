# Note về icinga 

**Kiểm tra cấu hình **

```
icinga2 daemon -C

```

**Check plugins**

*Dự án Monitoring plugins cung cấp tập hợp các plugins có thể kiểm tra nhiều loại dịch vụ khác nhau*

*Icinga2 sẽ không biết cách để kiểm tra trạng thái của các dịch vụ bên ngoài (như HTTP, SSH, CPU Load, RAM, Disk...).*

```
apt install monitoring-plugins

```

lệnh này cài đặt tập hợp các plugin phổ biến 

xem các check_plugin này trong /usr/lib/nagios/plugins/

**Thiết lập API cho Icinga 2**

*Việc thiết lập API rât quan trọng vì nó dùng để:*

- *Kết nối với Icinga web 2*
- *Hỗ trợ Icinga DB* 
- *Cho phép giao tiếp giữa các node trong mô hình phân tán hoặc HA* 
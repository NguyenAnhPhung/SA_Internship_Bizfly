# Hiểu về Pacemacker

## Note linh tinh



```
# Cấu hình iSCSI target
sudo targetcli
/> backstores/block create name=nfs_ha dev=/dev/sdX  # Thay /dev/sdX bằng device thực tế
/> iscsi/ create iqn.2023-12.com.example:nfs.ha
/> iscsi/iqn.2023-12.com.example:nfs.ha/tpg1/luns create /backstores/block/nfs_ha
/> iscsi/iqn.2023-12.com.example:nfs.ha/tpg1/acls create iqn.2023-12.com.example:node1
/> iscsi/iqn.2023-12.com.example:nfs.ha/tpg1/acls create iqn.2023-12.com.example:node2
/> saveconfig
/> exit

```

 

```
# Cài đặt gói cần thiết
sudo apt-get update
sudo apt-get install -y open-iscsi

# Cấu hình initiator name (thực hiện riêng trên từng node)
# Trên node1:
echo "InitiatorName=iqn.2023-12.com.example:node1" | sudo tee /etc/iscsi/initiatorname.iscsi

# Trên node2:
echo "InitiatorName=iqn.2023-12.com.example:node2" | sudo tee /etc/iscsi/initiatorname.iscsi

# Khởi động dịch vụ
sudo systemctl enable --now iscsid open-iscsi

# Discovery và kết nối đến target
sudo iscsiadm -m discovery -t st -p <storage_server_ip>
sudo iscsiadm -m node -T iqn.2023-12.com.example:nfs.ha -p <storage_server_ip> -l

# Kiểm tra thiết bị
lsblk  # Sẽ thấy thêm disk mới (thường là /dev/sdb)
```

3260/tcp là port của dịch vụ iSCSI Target

resource-agents-base: giúp Pacemaker điều khiển các tài nguyên (ví dụ: dịch vụ iSCSI, IP ảo, NFS,...)

watchdog dùng để giám sát và theo dõi phần cứng để có vấn đề gì thì nó reboot



```
# Trên target server:
lvextend -L +5G /dev/vg_iscsi/lv_iscsi
xfs_growfs /dev/vg_iscsi/lv_iscsi  # Nếu dùng XFS

# Trên initiator (client):
sudo iscsiadm -m node -R  # Rescan device
lsblk  # Kiểm tra kích thước mới (VD: /dev/sdb)
sudo resize2fs /dev/sdb    # Resize filesystem
```



## 1. STONITH

Kĩ thuật bảo vệ dữ liệu khỏi các node xảy ra sự cố 

- Trong trường hợp node không phản hồi cluster nhưng không chắc chắn về trạng thái của node, cách tốt nhất để đảm bảo dữ liệu an toàn là tắt nóng, đảm bảo node đó đã thực sự offline
- STONITH là một kĩ thuật như thế và hành động cách ly node có vấn đề khỏi các node còn lại được gọi là fence
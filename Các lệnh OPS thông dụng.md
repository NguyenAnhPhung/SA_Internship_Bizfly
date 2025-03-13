# Các lệnh OPS thông dụng 

##### **1. Tạo instance từ volume có sẵn** 

Check volume 

```
openstack volume list
```

Tạo Instance từ volume có sẵn

```
openstack server create --flavor small \
  --volume ubuntu-volume \
  --network private-network \
  --key-name mykey \
  --avability-zone HN1
  ubuntu-server
```

##### **2.  Start/stop/soft reboot/hard reboot instance, phân biệt soft reboot và hard reboot**

```
openstack server start/stop <Server_ID>
```

```
openstack server reboot --hard/soft <Server_ID>
```

**Khác biệt giữa Soft Reboot và Hard Reboot**

| Loại reboot | Cơ chế hoạt động           | Ảnh hưởng                                    |
| ----------- | -------------------------- | -------------------------------------------- |
| Soft Reboot | Gửi tín hiệu reboot đến OS | OS có thời gian đóng ứng dụng và lưu dữ liệu |
| Hard Reboot | Cắt nguồn rồi bật lại      | Giống như tắt đột ngột, có thể mất dữ liệu   |

*Sử dụng lệnh reboot không có tùy chọn soft và hard thì mặc định nó sẽ là soft*

**Khi nào nên dùng soft/hard?**

Chỉ nên dùng hard khi soft reboot không hoạt động, khi os bị treo 

##### **3. Show thông tin VM**

```
openstack server show <ID_VM>
```

##### **4. Resize instance**

```
openstack server resize --flavor <NEW_FLAVOR> <INSTANCE_ID>
```

##### **5.  Show console log, url  của instance **

```
openstack console log show <VM_ID>
```

```
openstack console url show <vm_ID>
```

##### **6. Lệnh xóa Instance**

```
openstack server delete <VM_ID>
```

##### **7. List toàn bộ Instance của 1 project bất kì**, **tất cả project**

```
openstack server list --project <PROJECT_ID>
```

```
openstack server list --all-projects
```

##### **8. Tạo volume mới**

```
openstack volume create --size 20 <volume_name> --type <type_id> --availability-zone HN1 
```

Xem type, availability zone 

```
openstack volume type list 
```

```
openstack availability zone list
```

##### **9. Show thông tin volume**

```
openstack volume show <volume_id>
```

##### **10. Gắn volume mới vào Instance**

```
openstack server add volume <vm_id> <volume_id>
```

##### **11. Gỡ volume ra khỏi instance**

```
openstack server remove volume <vm_id> <volume_id>
```

##### **12. Resize volume không cần reboot **

```
cinder --os-volume-api-version 3.42 extend <Volume_ID><Size>
```

##### **13. Xoá volume**

```
openstack volume delete <volume_id>
```

##### **14. List volume của 1 project, của toàn hệ thống**

```
openstack volume list --project <project_id>
```

```
openstack volume list --all-projects
```

##### **15. Show thông tin network, subnet**

Show network 

```
openstack network list 
openstack network show <net_id>
```

Show subnet

```
openstack subnet list 
openstack subnet show <sub_id>
```

##### **16. List subnet, network của 1 project, của toàn bộ hệ thống**

*Nêu không dùng option nào thì mặc địch sẽ hiển thị thông tin thuộc project hiện tại đang đăng nhập*

**List subnet** 

```
openstack subnet list --project <project_id>
```

```
openstack subnet list 
```

*`openstack subnet list` sẽ liệt kêt hết tất cả các subnet dưới project admin (toàn bộ hệ thống)*

**List network** 

```
openstack subnet list --project <project_id>
```

```
openstack network list 
```

*Tương tự như lệnh liệt kê subnet*

##### **17.  Check xem network/subnet đó đã dùng bao nhiêu IP**

**Network**

```
openstack port list --network <net_id> 
```

hoặc

```
openstack port list --network <net_id> | grep ip | wc -l
```

**Subnet**

```
openstack port list | grep <sub_id>
```

hoặc 

```
openstack port list | grep <sub_id> | wc -l
```

*để biết số lượng ip* 

##### 18. Tạo, xóa port 

**Tạo port**

```
openstack port create --network <net_id> <tên port>
```

*mặc định khi tạo port sẽ ở trạng thái down vì chưa được gắn vào vm nào*

**Xóa port**

```
openstack port delete <port_id>
```

##### 19. Gán/gỡ port vào vm 

```
openstack server add port <server_id> <port_id>
```

```
openstack server remove port <server_id> <port_id>
```

##### **20. Set/edit security group cho port**

Khi set security Group, port sẽ chỉ có SG mới các SG cũ sẽ bị thay thế 

check list SG

```
openstack security group list 
```

check port id của vm 

```
openstack port list --server <vm_id>
```

**Gán SG vào port** 

```
openstack port set --security-group <security_group_id> <port_id>
```

kiểm tra SG gán vào port

```
openstack port show <port_id>
```

**Xóa SG khỏi port** 

```
openstack port unset --security-group <security_group_id> <port_id>
```

##### 20. Set/Edit QoS Policy cho port

QoS Policy (Quality of Service Policy) là chính sách quản lí chất lượng dịch vụ mạng, giúp giới hạn băng thông, độ trễ, hoặc số lượng gói tin trên port mạng.

- Giới hạn băng thông (Bandwidth Limit): Hạn chế tốc độ tối đa (ingress/egress) của port.
- Ưu tiên gói tin (DSCP Marking): Đánh dấu gói tin để ưu tiên truyền tải trên mạng.
- Hàng đợi tối thiểu (Minimum Bandwidth Guarantee): Đảm bảo mức băng thông tối thiểu cho port.
- Chính sách cổng hàng đợi (Queue Policy): Điều chỉnh cách xử lý các gói tin trong hàng đợi

**List qos policy**

```
openstack network qos policy list
```

**Tạo qos policy**

```
openstack network qos policy create my-qos-policy
```

**Thêm quy tắc giới hạn băng thông** 

```
openstack network qos rule create --type bandwidth-limit \
  --max-kbps 5000 --max-burst-kbits 1000 my-qos-policy
```

`--max-kbps` (Kilobits per second - kbps)

- Xác định băng thông tối đa (tốc độ tối đa) mà port có thể sử dụng.
- Đơn vị: kilobits mỗi giây (kbps).
- Ví dụ: `--max-kbps 5000` nghĩa là giới hạn port chỉ có thể truyền 5 Mbps (5000 kbps = 5 Mbps).

`--max-burst-kbits` (Kilobits - kbits)

- Xác định kích thước burst tối đa của băng thông.
- Burst là lượng dữ liệu có thể truyền vượt quá giới hạn `max-kbps` trong một khoảng thời gian ngắn.
- Đơn vị: kilobits (kbits).
- Ví dụ: `--max-burst-kbits 1000` nghĩa là cho phép burst thêm 1000 kbits (~125 KB) trước khi giới hạn tốc độ.

**Gán Qos plolicy vào port** 

```
openstack port set --qos-policy my-qos-policy PORT_ID
```

**Verify**

```
openstack port show PORT_ID
```

**Gỡ qos policy khỏi port**

```
openstack port unset --qos-policy <port_id>
```

**Xóa qos policy** 

```
openstack network qos policy delete <qos_policy_id>
```

##### 21. List all port của 1 project, all port của hệ thống

```
openstack port list --project <project_id>
```

```
openstack port list
```

đang đứng ở admin nên lệnh này sẽ liệt kêt tất cả các port của hệ thống 

##### 22.  Tìm ID của 1 port từ địa chỉ IP

```
openstack port list --fixed-ip ip-address=IP_ADDRESS 
```

#####  23. Show thông tin project/user

```
openstack project show PROJECT_ID
```

```
openstack user show USER_ID
```


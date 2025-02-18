# Thực hiện Case vận hành cơ bản 

## 1. Tạo 3 Server 

### 1.1. Chuẩn bị môi trường 

Tạo 3 server (Ubuntu/CentOS/Windows) với 1 ổ rootdisk trên cloud:

*Mỗi server được tạo ra với chỉ một ổ đĩa chính (rootdisk) để chứa hệ điều hành (OS), mà không thêm một ổ đĩa phụ nào khác (data disk)*

**Check image list**

```
openstack image list | grep -i ubuntu
```

**Check flavor list** 

```
openstack flavor list 
```

**Check network list**

```
openstack network list
```

**Check keypair**

```
openstack keypair list
```

Nếu không có thì tạo key

```
ssh-keygen -q -N ""
```

`-q`: tạo khóa ở chế độ quiet, không hiển thị output

`-N ""`: không đặt pass cho key 

Lệnh tạo key trên openstack 

```
openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
```

### 1.2. Tạo Server

**Có 2 cách để tạo server** 

- Boot từ image
- Boot từ Volume

**Boot từ volume dữ liệu không bị mất khi xóa server*

**Tạo volume boot từ image**

```
openstack volume create --image <image_id> --size 20 <volume_name> --type <type_id> --availability-zone HN1 --bootable 
```

**Giải thích:**

**`--type <type_id>`** *(Tuỳ chọn)*

- Loại storage backend cho volume (ví dụ: `SSD`, `HDD`).

- Giá trị `<type_id>` có thể phụ thuộc vào cấu hình của OpenStack.

  ```
  openstack volume type list 
  ```

**`--availability-zone HN1`** *(Tuỳ chọn)*

- Chỉ định **availability zone** (`HN1` là một ví dụ).

- Giúp phân bổ volume vào một khu vực cụ thể (dùng trong hạ tầng multi-zone).

  ```
  openstack availability zone list
  ```

**`--bootable`**

- Đánh dấu volume này là **bootable**, tức là có thể dùng nó để boot máy ảo.	

**Boot Server từ volume đã tạo**

```
openstack server create --flavor small \
  --volume ubuntu-volume \
  --network private-network \
  --key-name mykey \
  --avability-zone HN1
  ubuntu-server
```

ex

```
openstack server create --flavor 005676bb-df31-42ad-b77d-58e9f108c42a \
  --volume phungna_volume_test_src \
  --network ff5bec48-c7b0-44a2-b4ef-15b7ae00de40 \
  --key-name mykey \
  --availability-zone HN1 \
  phungna-ubuntu-volumetest1

```



## 2. FSCK disk lỗi 

Trường hợp tắt máy đột ngột hoặc 1 vài lý do nào đó làm lỗi phân vùng khởi động.

*B1. Stop server*

*B2. Map ổ cứng* 

***cd /opt/cephtools/\*** 

chạy script

***map_disk.sh\***

B3. Tiến hành chạy lệnh ntfsfix:

*+ Với windown* 

***# ntfsfix < phân vùng cần fix>***

*+ Với linux*

***# e2fsck < phân vùng >***

B5. Start server

### Chi tiết:

**Bước 1: Stop server**

```
openstack server stop <server_id>
```

**Bước 2: Map ổ cứng**

```
rbd-nbd map volumes/volume-<Volume-ID>
```

Lệnh này được sử dụng để map một RBD (RADOS Block Device) từ Ceph vào hệ thống dưới dạng một thiết bị block thông qua nbd **(Network Block Device)**. đây là cách để một VM có thể sử dụng một ổ đĩa ceph RBD như 1 ổ cứng cục bộ 

**Cách hoạt động của lệnh này:**

1. Khi chạy lệnh này Ceph sẽ map volume `volume-<Volume-ID>`. thành một thiết bị block (`dev/nbdx`) trên máy chủ (controller)
2. dùng lsblk để check 

**Bước 3: Sửa lỗi**

Chạy các lệnh sau: 

**Windows:**

```
sudo ntfsfix /dev/nbd5p1
```

- Công dụng:
  - Reset một số giá trị trong NTFS metadata.
  - Đánh dấu phân vùng NTFS là "clean" để Windows có thể kiểm tra lại.
  - Hỗ trợ sửa lỗi cơ bản giúp phân vùng có thể mount được trên Linux.

**Linux:**

```
sudo e2fsck /dev/nbd5p1
```

- Công dụng:

  - Kiểm tra lỗi metadata trên phân vùng Linux.

  - Khắc phục lỗi inode, block, superblock.

  - Đánh dấu lại sector bị hỏng để tránh ghi dữ liệu vào đó.

  - `e2fsck` quét toàn bộ hệ thống file để tìm lỗi và sửa chữa chúng.

    Nếu hệ thống bị tắt đột ngột, `e2fsck` giúp khôi phục dữ liệu và tránh lỗi mount phân vùng.

**Bước 3: Gỡ Volume**

```
sudo rbd-nbd unmap /dev/nbdX
```

## 3. Reset password

*Reset password khi không thể truy cập vào vm theo cách thông thường, quên pass của vm* 

**Bước 1: Stop Server:**

**Bước 2: Map ổ cứng với rootdisk:**

```
sudo rbd-nbd map volumes/volume-<Volume_ID>
```

Với ổ Ceph:

```
mkdir /tmp/mnt
```

Mount device vào /tmp/mnt

```
sudo mount /dev/nbdx /tmp/mnt
```

**Bước 3: Thay đổi password:**

*Với Linux:*

```
sudo chroot /tmp/mnt
```

Lệnh chroot /tmp/mnt có nghĩa là thay đổi root filesystem của môi trường hiện tại sang thư mục /tmp/mnt,  giúp thao tác như thể /tmp/mnt là hệ thống gốc (`/`)

```
passwd root
```

```
exit
```

*Với Windows:*

```
chntpw -u Administrator /tmp/mnt/Windows/System32/config/SAM
```

```
– – – – User Edit Menu:

1 – Clear (blank) user password

2 – Edit (set new) user password (careful with this on XP or Vista)

3 – Promote user (make user an administrator)

(4 – Unlock and enable user account) [seems unlocked already]

q – Quit editing user, back to user select

Select: [q] > 1

Password cleared!

-- Sau đó ta bâm "Enter"

Hives that have changed:

#  Name

0  </data/Windows/System32/config/SAM>

Write hive files? (y/n) [n] : y

0  </data/Windows/System32/config/SAM> – OK
```

**Bước 4: Umount và Unmap device:**

```
sudo umount /tmp/mnt
```

```
sudo rbd-nbd unmap volumes/volume-<ID_volume>
```

**Bước 5: Identify:**

*Ubuntu:*

Đăng nhập vào Ubuntu với pass vừa được thay đổi 

*Windows:*

Password của Windows đã được xóa, vào Windows console mà không cần đăng nhập 
Sau đó, đổi mật khẩu của Windowns trên Powersell

```
set user Administrator <new_password>
```

## 4. Đổi IP của 2 VM 

*Đổi IP của  2 VM bằng cách thay đổi port (detach và attach port) của chúng cho nhau*

**Bước 1: Xác định Port ID của mỗi IP:**

```
openstack port list server <VM_ID_1>
openstack port list server <VM_ID_2>
```

**Bước 2: Xóa Port của VM cũ:**

```
openstack server remove port <VM_ID_1> <port_ID_1>
openstack server remove port <VM_ID_2> <port_ID_2>
```

**Bước 3: Gán Port lại cho VM mới:**

```
openstack server add port  <VM_ID_1> <port_ID_2>
openstack server add port  <VM_ID_2> <port_ID_1>
```

**Bước 4: Identify**

```
openstack server list 
```

## 5. Snapshot VM

tham khảo tại [đây](https://docs.ceph.com/en/reef/rbd/rbd-snapshot/)

*Tạo snapshot volume*

**Bước 1: Tạo Snapshot:**

```
rdb snap create volumes/volume-<Volume_ID>@<snap_name>
```

**Bước 2: List Snapshot:**

Kiểm tra xem snapshot đã được tạo chưa

```
rbd snap ls volumes/volume-<Volume_ID>
```

**Bước 3: Shutdown vm**

**Bước 4: RollBack Snapshot:**

```
rbd snap rollback volumes/volume-<Volume_ID>@<snap_name>
```

## 6. Tạo VIP gắn vào Server

*Về cơ bản mỗi IP tương ứng với 1 port và một MAC khác nhau. Mặc định khi VM được tạo trên port của VM sẽ có 1 rule của iptable giới hạn CHỈ GÓI TIN CÓ ĐỊA CHỈ MAC +IP của VM đó mới được phép đi ra ngoài. Vì vậy mà nếu đổi IP hay MAC trong file config của instance thì sẽ mất mạng  ngoài ra các tính năng như VIP khi dùng keepalived cũng sẽ không sử dụng được. Allowed pair address cho phép chúng ta add thêm các cặp IP + MAC có thể sử dụng cho instance (mặc định chỉ được 10 cặp IP + MAC).*

Về bản chất thì đây là việc cho phép port allow địa chỉ VIP đó và ta có thể cấu hình trong file netconf

**Bước 1: Tạo port có fixed-ip là VIP:**

```
openstack port create --network <net_id_project> <VIP_IP1_IP2_IPn_project_name>
```

`<VIP_IP1_IP2_IPn_project_name>`: đặt tên theo nguyên tắc này giúp dễ dàng tìm kiếm 

**Bước 2: Lấy ID port của VM cần thêm VIP**

```
openstack port list –server <instance_ID>
```

**Bước 3: Update VIP vào port cũ **

```
openstack port set --allowed-address ip-address=<VIP_IP> <port_id>
```

**Bước 4: Thêm description cho VM mô tả "có VIP x.x.x.x"**

```
openstack server set --property server_have_VIP='ip vip' <id vm>
```

**Bước 5: Identify**

Check xem IP đã được allow trên port chưa 

```
openstack port show <port_id> -c allowed_address_pairs
```

Vào VM cấu hình VIP

```
network:
  ethernets:
    eth0:
      addresses:
        - <VIP_IP>/24
```

Sử dụng VM khác ping thử đến VIP 

## 7. Resize volume không cần reboot 

**Bước 1: Tăng ở Openstack:**

```
cinder --os-volume-api-version 3.42 extend <Volume_ID><Size>
```

**Bước 2: Thực hiện tăng bên trong VM**

Đối với disk có tạo phân vùng như vda1,vda2,..

```
growpart /dev/vda 2   (lưu ý có dấu cách)
```

```
resize2fs /dev/vda2
```

## 8. Thực hiện clone rootdisk của server này ==> rootdisk của server kia (2 server cùng loại rootdisk)

**Bước 1: Tạo new_volume hoặc vm mới có dung lương bằng với old_volume (có thể khác type)**

**Bước 2: Tắt server mới:**

**Bước 3:  Snapshot old_volume**

```
rbd snap create volumes/volume-<old_volume_id>@SNAP-1
```

**Bước 4: Rename new_volume dưới ceph**

```
rbd rename volumes/volume-<new_volume_id> rename volumes/volume-<new_volume_id>_RES
```

**Bước 5: Protected volume **

Trước khi clone, bạn cần chạy lệnh sau để bảo vệ snapshot:

```
rbd snap protect volumes/volume-<old_volume_id>@SNAP-1
```

**Bước 6: Restore lại bản snapshot của old_volume vào new_volume**

```
rbd clone volumes/volume-<old_volume_id>@SNAP-1 volumes/volume-<new_volume_id>
```

**Bước 7: Vô vm check xem data có giống với old_vm không**

## 9. Check đồ thị tài nguyên 

**Overview với cụm Staing** 

Cụm Staing bao gồm:

![image-20250218110815983](/home/phungna/.config/Typora/typora-user-images/image-20250218110815983.png)

- **Staing_Instance_Compute:** Cho cái nhìn tổng quan của các thông số của các Instance trong từng Host Compute
- **Staing_Instance:** Chứa thông số chi tiết của các Instance trong cụm
- **Staing-Compute:** Thông số của các Host Compute
- **Staing-Controller:** Thông số của các Host Controller
- **Staing_Overview_Compute:** Cái nhìn tổng quan về các host Compute

### 9.1. Thông số của các Host

- CPU: thể hiện đồ thị trạng thái hoạt động của cpu, bên cạnh đó cho biết thêm các thông tin chi tiết từ đồ thị như:
  - % sử dụng min, max, current của CPU
  - Các đường biểu diễn khác nhau như:
    - **.idle:** Thời gian CPU ở trạng thái rảnh rỗi.
    - **.user usage:** Thời gian CPU được sử dụng bởi các tiến trình người dùng.

## 10. Cold/Live Migrate một vm sang 1 node compute khác 

**Cold Migrate**

Yêu cầu: 

- Các node phải cùng một zone với nhau
- Tắt VM trước khi cold

Thực hiện:

Bước  1: check VM thuộc node compute nào?

```
openstack server show <VM_ID>
```

Bước 2: check zone để xác định các compute cùng 1 zone 

```
openstack compute service list
```

Bước 3: Thực hiện Cold Migrate

```
nova mỉgrate --host $Dest_compute $VM_ID0
```

Bước 4: Kiểm tra xem VM đã chuyển sang node compute khác chưa 

```
openstack server show VM_ID
```

**Live Migrate**

Yêu cầu: 

- Kiểm tra tình trạng instance hiện tại (Nếu mức sử dụng CPU và iops thấp thì xác định có thể live-migrate). Kiểm tra check ping 
- Tìm node phù hợp thông qua đồ thị 

Các bước check tương tự như trên 

Lệnh live 

```
openstack --os-compute-api-version 2.30 server migrate --live-migration <VM_ID>  --host $DEST_HOST
```


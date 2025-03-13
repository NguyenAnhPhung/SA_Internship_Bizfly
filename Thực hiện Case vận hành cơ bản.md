Thực hiện Case vận hành cơ bản 

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

**Bước 5: Verify:**

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

**Bước 4: Verify**

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

**Bước 5: Verify **

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

#### **Quick overview** 

- Uptime: thời gian hoạt động liên tục của hệ thống 
- LA medium: mức tải trung bình của host 
- Zombies: 0: Số tiến trình "zombie" (tiến trình đã kết thúc nhưng vẫn còn trong danh sách tiến trình) là 0.
- Processes: 1,195: Số tiến trình đang chạy là 1,195.
- Threads: 4720: Số luồng đang chạy là 4,720.
- CPU usage: 13.8%: Mức sử dụng CPU trung bình là 13.8%.
- RAM usage: 72.3%: Mức sử dụng RAM là 72.3%.
- Swap usage: 43%: Mức sử dụng swap (bộ nhớ ảo) là 43%.
- RootFS used: 89%: Dung lượng đã sử dụng của phân vùng gốc (Root File System) là 89%.
- IOWait: 0.84%: Thời gian CPU chờ đợi I/O (Input/Output) là 0.84%.

#### **CPU**

**CPU usage (Biểu đồ)**: Hiển thị mức sử dụng CPU theo thời gian. Phần trăm CPU càng cao, CPU càng hoạt động nhiều.

- idle: Thời gian CPU ở trạng thái rảnh rỗi, không thực hiện bất kỳ tác vụ nào.
- softirq: Thời gian CPU xử lý các ngắt mềm (softirq). Ngắt mềm là các ngắt được xử lý bởi phần mềm, thường là do các thiết bị phần cứng hoặc các tiến trình phần mềm gây ra.
- iowait: Thời gian CPU chờ đợi các hoạt động nhập/xuất (I/O) hoàn thành. Ví dụ: đọc dữ liệu từ ổ cứng, ghi dữ liệu vào ổ cứng.
- user usage: Thời gian CPU được sử dụng bởi các tiến trình người dùng (user). Đây là các chương trình mà người dùng trực tiếp chạy.
- system usage: Thời gian CPU được sử dụng bởi hệ điều hành (kernel) để thực hiện các tác vụ quản lý hệ thống.

**Load Averages:** Là trung bình số lượng tiến trình (processes) đang chờ được thực thi hoặc đang chạy trên hệ thống tại một thời điểm nhất định

- **Ba giá trị Load averages**:*

  - **1 phút (short)**: Trung bình số tiến trình đang chờ CPU trong 1 phút gần nhất.

  - **5 phút (medium)**: Trung bình số tiến trình đang chờ CPU trong 5 phút gần nhất.

  - **15 phút (long)**: Trung bình số tiến trình đang chờ CPU trong 15 phút gần nhất.

**Memory usage**: Mức sử dụng bộ nhớ.

- **.total **: Tổng dung lượng RAM của hệ thống.
- **.used**: Dung lượng RAM đã được sử dụng.
- **.available**: Dung lượng RAM còn trống và có thể sử dụng ngay lập tức.

**Processes:** Số lượng tiến trình đang chạy.

- **.running:** Số lượng tiến trình đang chạy.
- **.blocked:** Số lượng tiến trình bị chặn.
- **.sleeping:** Số lượng tiến trình đang ngủ.
- **.total_threads:** Tổng số luồng đang chạy.



####  **Disk IOPS for /dev/$disk** 

*hoạt động I/O (Input/Output - Nhập/Xuất) của hệ thống, cụ thể là cho ổ đĩa `/dev/disk`*

**Write I/O requests for /dev/disk:** Số lượng yêu cầu ghi I/O vào ổ đĩa.

**Read I/O requests for /dev/disk:** Số lượng yêu cầu đọc I/O từ ổ đĩa.

**Write I/O bytes for /dev/disk:** Số lượng byte được ghi vào ổ đĩa.

**Read I/O bytes for /dev/disk:** Số lượng byte được đọc từ ổ đĩa

#### Network

- **Network TX:** Lưu lượng dữ liệu truyền từ máy chủ, thể hiện tổng băng thông gửi đi. 
- **Network RX:** Lưu lượng dữ liệu nhận vào máy chủ, thể hiện tổng băng thông nhận. 
- **Network Packets TX:** Số lượng gói tin được truyền đi từ máy chủ, đơn vị là kilo packets per second (kp/s). 
- **Network Packets RX:** Số lượng gói tin được nhận vào máy chủ, đơn vị là kilo packets per second (kp/s).
- **Network Errors TX:** Số lỗi xảy ra khi truyền dữ liệu từ máy chủ. Không ghi nhận lỗi đáng kể.
- **Network Errors RX:** Số lỗi xảy ra khi nhận dữ liệu vào máy chủ. Không ghi nhận lỗi đáng kể.



### 9.2. Thông số của VM

Dựa vào hình ảnh dashboard giám sát hệ thống, các biểu đồ thể hiện trạng thái hoạt động của máy chủ như sau:

#### **CPU**

**CPU Percent:** Hiển thị mức sử dụng CPU của máy chủ. 

#### **Memory**

Memory Used: Dung lượng bộ nhớ RAM đang được sử dụng, .
Memory Free: Dung lượng bộ nhớ RAM còn trống, h

#### **IOPS (Input/Output Operations Per Second)**

IOPS: Số lượng yêu cầu I/O trên ổ đĩa mỗi giây. 

#### **Disk I/O**

Read I/O bytes: Lượng dữ liệu đọc từ ổ đĩa `vda`, hiển thị theo đơn vị B/s. 
 Write I/O bytes: Lượng dữ liệu ghi vào ổ đĩa `vda`, hiển thị theo đơn vị B/s. 

#### **Network**

Network TX: Lưu lượng dữ liệu truyền từ máy chủ, 
 Network RX: Lưu lượng dữ liệu nhận vào máy chủ, 
 TX Packets: Số lượng gói tin được truyền đi,
 RX Packets**:** Số lượng gói tin được nhận, .

#### **Disk Usage**

Total DiskSize: Tổng dung lượng ổ đĩa `vda`, 
 Used Disk Size: Dung lượng ổ đĩa đã sử dụng, 
 Disk Used Percent:

#### **CPU Steal**

CPU Steal: Lượng CPU bị lấy đi do tài nguyên bị chia sẻ trên máy ảo.

## 10.Cold/Live Migrate một vm sang 1 node compute khác 

**Cold Migrate**

Yêu cầu: 

- Các node phải cùng một zone với nhau
- Tắt VM trước khi cold

Thực hiện:

**Bước  1: check VM thuộc node compute nào?**

```
openstack server show <VM_ID>
```

**Bước 2: check zone để xác định các compute cùng 1 zone** 

```
openstack compute service list
```

**Bước 3: Thực hiện Cold Migrate**

```
nova mỉgrate --host $Dest_compute $VM_ID0
```

**Bước 4: Kiểm tra xem VM đã chuyển sang node compute khác chưa** 

```
openstack server show VM_ID
```

**thực hành**

**Bước 1: kiểm tra vm** 

```
openstack server show <VM_ID>
```

**Bước 2: đặt ping đến vm** 

**Bước 3: chọn host phù hợp để cold** 

- xem trên metric 

- host có load avg ổn định (thấp)
- host có ram đủ dùng ( yêu cầu ram luôn phải free 70G)

Sau khi cho được host check xem host đó có enable không ?

```
nova service-list | grep <tên host>
```

**Bước 4:  thực hiện cold** 

ví dụ chuyển sang host iron-compute-093

```
bash /opt/cold-(tab) 093 <vm_id>
```

**Bước 5: Check lại vm xem đã chuyển chưa** 

**Live Migrate**

Yêu cầu: 

- Kiểm tra tình trạng instance hiện tại (Nếu mức sử dụng CPU và iops thấp thì xác định có thể live-migrate). Kiểm tra check ping 
- Tìm node phù hợp thông qua đồ thị 

Các bước check tương tự như trên 

Lệnh live 

```
openstack --os-compute-api-version 2.30 server migrate --live-migration <VM_ID>  --host $DEST_HOST
```

## 11. Restore lại volume từ trash

*Khách hàng xóa server, volume, sau khi xóa mặc định volume sẽ được lưu trữ trong trash 14 ngày* 

**+ Thực hiện:**

Volume mới: ce0461f7-58cb-4ef1-8543-06d12ca9d769

Volume cũ: 89ba6f8f-1007-4383-bb69-aa5d01cf49d2

**Bước 1: Kiểm tra xem trong trash còn volume không** 

```
rbd trash ls volumes | grep 89ba6f8f-1007-4383-bb69-aa5d01cf49d2
```

**Bước 2: Tiến hành restore lại volume cũ trong trash về 1 volume mới theo trash ID đã lấy được ở trên:** 

```
#rbd trash restore volumes/ff38544f97e3e4 --image volume-89ba6f8f-1007-4383-bb69-aa5d01cf49d2-RESaaf22f 
```

**Bước 3: Đổi tên server hiện tại của server thành 1 volume khác** 

```
rbd mv volumes/volume-ce0461f7-58cb-4ef1-8543-06d12ca9d769 HDD1/volume-ce0461f7-58cb-4ef1-8543-06d12ca9d769-ERR43
```

**Bước 4: Tiến hành đổi tên volume mới vừa được restore từ trash về ID của volume của server**

```
rbd mv volumes/volume-89ba6f8f-1007-4383-bb69-aa5d01cf49d2-RESaaf22f  volumes/volume-ce0461f7-58cb-4ef1-8543-06d12ca9d769 
```

**Bước 5: Boot lại server và kiểm tra ping ,etc cho KH rồi thông báo lại cho KH**

## 12. Tăng quota của 1 project bất kì thêm 10 instance, 100G loại ổ HHD2

Tăng quota của một project tức là việc điều chỉnh giới hạn tài nguyên mà project đó có thể sử dụng

- Tăng 10 VM: tăng số lượng máy ảo mà project có thể tạo lên 10 đơn vị 
- Thêm 100G HHD2: tăng dung lương lưu trữ thuộc loại ổ HHD2 lên 100G

*Về cơ bản khi tạo 1 project mới thì giới hạn tài nguyên sẽ được quy định mặc định nên sau đó nếu muốn mở rộng giới hạn thì mình sẽ sử dụng quota set*

**Thực hiện:**

**Bước 1: Kiểm tra quota của project**

```
openstack quota show <project_id>
```

*Các giá trị -1 có nghĩa là không có giới hạn* 
khi tăng volume sẽ để ý tới thông số gigabytes (nếu là -1 thì không qua tâm, nếu gigabytes này quá ít thì phải tăng lên để có thể tăng được quota cho các disk mà mình mong muốn )

**Bước 2: set quota cho project** 

- Set 10 VM

```
openstack quota set --instances 10 <project_id>
```

- Set 100G 
  trước tiên cần kiểu tra xem có các loại volume nào 

```
openstack volume type list
```

- sau đó set volume 

```
openstack quota set --gigabytes 100 --volume-type HHD2 <projetc_id>
```

- set ram cho project

  ```
  openstack qouta set --ram (1024*số ram yêu cầu <G>) <project_id>
  ```

  ví dụ yêu cầu tăng 64G ram:

  ```
  openstack qouta set --ram 65536 <project_id> 
  ```

  

**Bước 3: Verify**

```
openstack quota show <project_id>
```

## 13. Gắn file iso hệ điều hành bất kì vào 1 VM test đã tạo xong trên Cloud, ssh vào  compute node từ ansible: 

*Thường dùng trong trường hợp khách hàng muốn cài riêng hệ điều hành mà khách hàng mong muốn*

Bước 1: check tên instance trên node cotroller 

```
openstack server show <vm_id>
```

Bước 2: stop vm 

```
openstack server stop <vm_id>
```

Bước 3: chỉnh sửa file cấu hình iinstance xml

```
virsh edit instance-xxxx
```

thêm đoạn cấu hình sau:

```
<disk type='file' device='cdrom'>
  <driver name='qemu' type='raw'/>
  <source file='/data/debian-amd64-netinst-3cx.iso'/> // phần này sẽ chỉnh sửa thành file iso cá nhân 
  <target dev='hdc' bus='ide'/>
  <readonly/>
  <address type='drive' controller='0' bus='1' target='0' unit='0'/>
</disk>
```

đảm bảo khu vực boot có thiết lập sau 

```
 <os>
<type arch='x86_64' machine='pc-i440fx-trusty'>hvm</type>
<boot dev='cdrom'/> //phần này cần thêm vào
<boot dev='hd'/>
<smbios mode='sysinfo'/>
</os>
```

Bước 4: Reset state instance sang active từ nova (controller)

```
nova reset-state --active <vm_id>
```

Bước 5: 

```
virsh start instance-000007e8
```

Bước 6:

Vào vnc để thao tác cần thiết
Sau khi chỉnh sửa xong, hard reboot lại instance để hủy bỏ các thiết lập ở file xml bị hủy bỏ

## 14. Test evacutae

*evacuate là một tính năng của openstack đây là quá trình di dời (evacuate) các instance từ một compute node bị lỗi sang một compute node khác*

Bước 1: check tình trạng host compute

```
ping <ip_compute>
```

Bước 2: nếu không ping được chạy script evacuate

```
bash /opt/evacuate.sh <compute_node>

hoặc: bash /opt/script-ops/evacuate.sh <compute-node> (nếu là thor-admin)

Ví dụ : bash /opt/evacuate.sh thor-compute-009
```

Bước 3: sau khi eva check lại vm 

```
openstack server show <vm-id>
```

```
ping <ip-vm>
```

ping không đươc thì check nmap, nmap không được mở console lên xem vm lên chưa 

Bước 4: check lại vm trên node lỗi 

Nếu compute cũ online trở lại kiểm tra xem vm còn chạy không nếu cần thì kill đi để tránh tình trạng 2 vm chạy cùng 1 disk

Bước 5: Disable node vừa rồi để tìm nguyên nhân lỗi 

```
openstack compute service set --disable <host> nova-compute 
```


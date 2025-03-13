# Docs vận hành 

## 1. Cài os

**Bước 1**: remote vào remica bằng ip 192.168.251.86

**Bước 2**: đăng nhập bằng 

```
user: phungna
pass: phungna@bizflycloud.vn1
```

**Bước 3**: lấy user pass của server trên tele, copy toàn bộ vào file tạm 

**Bước 4**: Plugin Isoimage/F:/Setup/

**Bước 5**: setup volume

/data 100

/var/log  100

/swap 100

/data còn lại 

**Bước 6**: đặt tên

2 cái đầu là tên compute 

Cái cuối username là ansibledeploy
**Bước 7**: cập nhật thông tin vào file sheet

https://docs.google.com/spreadsheets/d/1OBTyLFx3yKWkp1BsTV4xVsdI_BHhEPZohAydE4u9JiA/edit?pli=1&gid=2062155361#gid=2062155361

https://docs.google.com/spreadsheets/d/1hAFuZFY83jQ514lXi2Xn2rzZOTaZUZXS17Ach7l_dZE/edit?gid=0#gid=0

**Các thông số của server vật lí check như sau:**

**ram**

```
sudo lshw -class disk
```

**disk**

```
sudo lshw -class memory
```

**check loại ổ cứng gì**

```
lsblk -o lsblk -o NAME,SIZE,,ROTA
```

**cập nhật thông số motherboard và Bios version**

```
sudo dmidecode -t baseboard
sudo dmidecode -s bios-version
```

sau đó bổ sung thông tin trong keepass

https://docs-ops.vccloud.vn/pages/viewpage.action?pageId=17990238
remote vào server thao tác

vào this PC, keepass tạo shotcut

chạy với quyền admin

sửa thông tin User pass, ip truy cập vào server  (các thông tin này check trên IPMI)

sau đó lưu lại 

## 2. Stress test

https://docs-ops.vccloud.vn/pages/viewpage.action?pageId=59506833

Bước 1: plugin stress test ubuntu 
Bước 2: mount device vừa plugins vào thư mục tạm 

```
# dpkg -i *.deb

# byobu

# sh 0_cputest.sh

# sh 1_check_ram_edac.sh

# sh 2_memtest.sh
```

Bước 3:  cài đặt tool trong thư mục

```
dpkg -i *.deb
```

Bước 4: chạy scrip

Bước 5: check htop

## 3. Bonding 

https://docs-ops.vccloud.vn/pages/viewpage.action?pageId=360528

import device phù hợp

check lại bash 

```
vim 0-setup-bon-ubu20-pub-compute
```

check ip của server trong file sheets

chạy lệnh 

```
bash 0-..... inf1 inf2 ip1 ip2
```

check 

```
bash 1-......
```

check lại ip 

```
ip a
```



## 4. Resize instance (GPU)

*Resize  instance chính là việc thay đổi flavor của nó, đối với case thay đổi GPU này cũng làm tương tự như vậy. Mặt khác để thay đổi flavor thì cũng cần biết về các thông số vật lí của server vật lí, tài nguyên cần thiết để chuyển đổi*
***Ví dụ**: trong trường hợp instance muốn tăng từ 1 lên 2 GPU mà server vật lí chỉ có GPU thì nếu chuyển flavor trực tiếp từ 1 lên 2 GPU sẽ không được bởi vì khi gọi như vậy tức là hiện tại nó đã đang sử dụng 1 GPU chỉ còn lại 1 GPU nữa vì vậy sẽ không thể khởi tạo được flavor 2 GPU*

***Cách sử lí:** sẽ sử dụng 1 flavor khác không có GPU để thay thế, sau đó mới chuyển sang flavor có 2 GPU*

Để check các thông số của server vật lí cũng như GPU của nó xem tại [đây](https://docs.google.com/spreadsheets/d/1OBTyLFx3yKWkp1BsTV4xVsdI_BHhEPZohAydE4u9JiA/edit?gid=549227316#gid=549227316)

**Thao tác:**

Bước 1: nhận IP check vm của ip đó

```
openstack server list --ip <IP> --all
```

Bước 2: thực hiện thay đổi flavor 

```
openstack server resize --flavor <new_flavor> <vm_id>
```

*trong trường hợp không resize được là vì host đang disable ,enable host lên, sau khi resize xong lại disable*

```
openstack compute service set --enable hulk-compute-045 nova-compute
```

*lệnh xem trạng thái của server realtime*

```
watch openstack server show --max-width 150 <vm_id>
```

Bước 4: cập nhật lên file sheets

## 5. Xử lí cảnh báo lỗi xóa volume 

xem tại [đây](https://docs-ops.vccloud.vn/pages/viewpage.action?pageId=1509448)

lên [đây](https://publog.vccloud.vn/app/kibana#/discover) xem log

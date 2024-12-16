# Ôn tập review2

## 1. Crontab

lưu ý khi cấu hình file .sh trong lệnh nên để đường dẫn tuyệt đối:

mỗi user sẽ có một crontab khác nhau lưu ý về quyền sở hữu của các file 

ví dụ cấu hình file bash.sh 

```
echo "say hi" >> /home/user/hello.txt
```

sau đó cấp quyền thực thi 

cấu hình crontab -e

chạy mỗi lần reboot

```
@reboot /path/to/file.sh
```

mỗi phút chạy 1 lần 

```
* * * * * /path/to/file.sh
```

chạy vào 2 giờ sáng 

```
00 2 * * * /path/to/file.sh
```

chạy vào 3h sáng 2 ngày một lần 

```
0 3 */2 * * /path/to/file.sh
```

## 2. phân biệt sự khác nhau giữa update và upgrade

tham  khảo tại [funix](https://funix.edu.vn/chia-se-kien-thuc/apt-update-apt-upgrade/)

#### 1.update

update chỉ cập nhật thông tin các gói tin trên repo chứ không thực hiện tải xuống hay cài đặt bất kì gói phần mềm nào 

#### 2. upgrade

upgrade tải xuống và cài đặt những phien bản mới của các gói đã được cài đặt trên hệ thống nếu có khả năng. nó chỉ dừng lại ở phạm vi các gói đã có trên hệ thống 

#### về cơ bản 

Hệ thống linux cần duy trì bộ nhớ cache của các gói phần mềm, chứa các metadata cần thiết để xác định phiên bản và phụ thuộc của các gói 

update sẽ cập nhật cache này upgrade sẽ dựa và cache để cập nhật những phiên bản có sẵn 

các metadata này thì nó chứa các thông tin về tên, phiên bản, kích thước, và vị trí tải về của các gói. vì vậy mà cần update trước khi cài phần mềm vì nếu không hệ thống sẽ không thể tìm thấy phiên bản ứng dụng  mà bạn muốn cài đặt

các repo chia làm 2 loại 

/etc/apt/sources.list: tệp chính lưu trữ các kho lưu trữ gói 

/etc/apt/sources.list.d/. tệp phụ lưu trữ các gói cuả bên thứ 3 , các gói người dùng 

có thể lưu trữ các repo này ở thư mục khác nhưng phải thêm 

## 3. log rotate

tham khảo tại [vietnix](https://vietnix.vn/logrotate-la-gi/)

rotate log giúp hỗ trợ quản lí các log files 

rotate ở đây được hiểu là tiến trình xử lí file log cũ theo quy trình có sẵn (nén/xóa/di chuyển) đồng thời tạo ra file log mới theo thời gian 

logrotate hoạt độgn tự độgn

**tính năng của rotate log**:

- Xoay vòng file log cho đến khi đáp ứng dung lượng đã được thiết lập.
- Ghi tiếp dữ liệu log vào file mới khi đã hoàn thành xoay vòng.
- Nén file log đã xoay vòng.
- Tùy chọn kiểu nén file log.
- Thêm ngày tháng vào tên file khi thực hiện xoay vòng.
- Thực thi [shell script](https://vietnix.vn/linux-shell-va-shell-script/) khi xoay vòng file log.
- Xóa các file xoay vòng cũ.

**Cách cài đặt logrotate:**

```
# sudo apt-get update
# sudo apt-get install logrotate
```


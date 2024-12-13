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

## 2. rotate log 


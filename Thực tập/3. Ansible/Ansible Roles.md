## Ansible Roles

## 1. Ansible Role là gì?

- Role là cách tổ chức các Playbook trong Ansible để bảo trì, tái sử dụng và quản lí 
- Thay vì viết tất cả các tasks, variable, handlers.. trong một Playbook, Role chia nhỏ chúng vào các thư mục cấu trúc chuẩn 
- Mỗi role thường đại diện cho một thành phần hoặc ứng dụng (như nginx, mysql)

## 2. Cấu trúc thư mục Role

Cấu trúc chuẩn của Role như sau:

```
roles/
  └── role_name/
       ├── tasks/
       │     └── main.yml
       ├── handlers/
       │     └── main.yml
       ├── templates/
       ├── files/
       ├── vars/
       │     └── main.yml
       ├── defaults/
       │     └── main.yml
       ├── meta/
       │     └── main.yml
       └── README.md

```

**Ý nghĩa của các thư mục trong Role:**

- tasks/main.yml: Nơi định nghĩa các tasks chính để thực thi 
- handlers/main.yml: Chứa các hành động cần thực hiện khi có sự thay đổi, như khởi động lại dịch vụ 
- templates/: chứa các tệp template Jinja2
- files/: chứa các tệp tĩnh (static files) cần sao chép đến máy chủ 
- vars/main.yml: Định nghĩa các biến được sử dụng trong role (ưu tiên cao hơn `defaults`)
- defaults/main.yml: định nghĩa các biến mặc định cho Role
- meta/main.yml: khai báo các thông tin về role như dependencies (phụ thuộc)
- README.md: ghi chú, hướng dẫn sử dụng role

## 3. Cách tạo và sử dụng Role

Tạo role mới 

```
ansible-galaxy init role_name
```

Ví dụ:

```
ansible-galaxy init nginx
```

**Sử dụng role trong Playbook**

```
---
- name: triển khai web server 
  hosts: web
  roles:
    - nginx
```

## 4. 
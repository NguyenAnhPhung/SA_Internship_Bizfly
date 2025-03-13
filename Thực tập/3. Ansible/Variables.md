# Variables 

## 1. Biến trong Ansibkle là gì?

Biến trong Ansible giúp playbook trở nên linh hoạt và dễ dàng tái sử dụng. Có thể khai báo biến trong nhiều nơi khác nhau và sử dụng trong các module, template hoặc điều kiện

Biến là các giá trị động mà có thể gán và sử dụng lại trong các playbook, roles hoặc inventory

Biến có thể chứa 

- Dữ liệ cơ bản: số, chuỗi, danh sáchm từ điển

- Dữ liệu phức tạp: thông tin đầu ra từ các module hoặc từ các tệp dữ liệu bên ngoài 

  ```
  vars:
    app_name: myapp
    port: 8080
  
  ```

  

- `app_name`: chứa giá trị "myapp"
- `port`: chứa giá trị 8080

## 2. Khai báo và sử dụng biến cơ bản 

### 2.1. Khai báo biến trực tiếp trong playbook

Có thể khai báo biến bằng từ khóa `var` trong playbook.

ví dụ: 

```
---
- name: ví dụ sử dụng biến 
  hosts: all
  vars:
    app_name: myapp
    port: 8080
  tasks:
    - name: hien thi thong tin ung dung 
      debug:
        msg: "ung dung {{app_name}} chay tren cong {{port}}"
```

Ket qua:

```
Ứng dụng myapp chạy trên cổng 8080

```

### 2.2.  Sử dụng biến trong playbook

- Sử dụng biến bằng cú pháp {{ variable_name }}
- Biến có thể được dùng trong:
  - Nội dung hiển thị (debug)
  - Tham số của module
  - Template (.j2)

Ví dụ:

```
---
- name: cấu hình file với biến 
  hosts: all 
  vars:
    file_path: "/etc/app.conf"
    owner: "root"
  tasks:
    - name: Tao tep cau hinh 
      file:
        path: "{{ file_path }}"
        state: touch
        owner: "{{ owner }}"
```



## 3. Khai báo biến trong các thành phần khác 

### 3.1. Biến trong Inventory

Có thể định nghĩa biến trong tệp inventory theo nhóm hoặc theo host cụ thể 

Invenotry  dạng INI:

```
[web]
web1 ansible_host=192.168.1.10 app_name=my_web_app
web2 ansible_host=192.168.1.11 app_name=my_other_web_app
```

trong playbook có thể truy cập biến `app_name`như sau: 

```
tasks:
  - name: Hiển thị ứng dụng của từng máy
    debug:
      var: app_name

```

### 3.2. Biến từ tệp ngoài (`var_files`)

Có thể lưu biến trong một tệp YAML riêng biệt và nạp chúng vào playbook bằng `vars_files`

Tệp `variables.yml`

```
app_name: my_app
port: 8080

```

Playbook sử dụng `vars_files`

```
---
- name: Nạp biến từ tệp ngoài
  hosts: all
  vars_files:
    - variables.yml
  tasks:
    - name: Hiển thị thông tin ứng dụng
      debug:
        msg: "Ứng dụng {{ app_name }} chạy trên cổng {{ port }}"

```

### 3.3. Biến từ Command Line

Có thể truyền biến từ dòng lệnh khi chạy playbook bằng --extra-vars (hoặc `-e`)

Ví dụ:

```
ansible-playbook playbook.yml -e "app_name=my_app port=8080"

```

trong playbook có thể sử dụng biến như bình thường 

```
ansible-playbook playbook.yml -e "app_name=my_app port=8080"

```

## 4. Phạm vi và thứ tự ưu tiên của biến 

vì Ansible có nhiều nơi khai báo biến nên chúng cân có thứ tựu ưu khi xung đột thứ tự (cao -> thấp)

1. Biến từ dòng lệnh (--extra-vars).
2. Biến trong Play (được khai báo trong playbook)
3. Biến từ host ( trong inventory)
4. Biến nhóm ( trong inventory)
5. Biến mặc định trong role

## 5. Biến nâng cao 

#### 5.1. Biến dạng danh sách 

Có thể khai báo danh sách và lặp qua chúng 

```
vars:
  my_list:
    - item1
    - item2
    - item3
  tasks:
    - name: Hiển thị từng phần tử 
      debug:
        msg: "{{ item }}"
      with_items: "{{ my_list }}"
```

### 5.2. Biến dạng từ điển 

Biến dạng từ điểu cho phép lưu nhiều cặp `key: value`

```
vars:
  server_config:
    app_name: my_app
    port: 8080
    owner: root

tasks:
  - name: Hiển thị thông tin từ điển
    debug:
      msg: "Ứng dụng {{ server_config.app_name }} chạy trên cổng {{ server_config.port }}"

```

### 5.3. Biến mặc định 

Nếu biến không tồn tại bạn có thể gắn giá trị mặc định bằng filter `default`

```
tasks:
  - name: Hiển thị ứng dụng (mặc định nếu không có)
    debug:
      msg: "Ứng dụng {{ app_name | default('default_app') }}"

```

### 5.4. Tính toán và xử lí biến với filters

Ansible hỗ trợ rất nhiều filters để xử lí biến như:

- lower: chuyển chuỗi thành chữ thường 
- length: lấy độ dài của danh sách 
- replace: thay thế chuỗi

ví dụ 

```
vars:
  name: "MyApp"
  my_list: [1, 2, 3]

tasks:
  - name: Chuyển tên thành chữ thường
    debug:
      msg: "{{ name | lower }}"
  - name: Độ dài danh sách
    debug:
      msg: "{{ my_list | length }}"
  - name: Thay thế chuỗi
    debug:
      msg: "{{ name | replace('App', 'Application') }}"

```

## 6. Lỗi thường gặp với biến 

1. **Biến chưa được khai báo:**

   - dùng filter `default` để tránh lỗi 

2. **Cú pháp sai:**

   - Kiểm tra xem có dấu `{{` và `}}` đúng không.

   

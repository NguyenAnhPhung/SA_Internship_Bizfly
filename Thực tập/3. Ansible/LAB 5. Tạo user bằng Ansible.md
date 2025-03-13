# LAB 5. Tạo user bằng Ansible 

## Yêu cầu bài LAB

Viết role ansbile
 \+ tạo user 
 \+ Disable ssh bằng password
 \+ cấp quyền sudo cho user
 \+ Add thêm public key  để user ssh đựợc bằng ssh-key (public key giả định được user cấp )

## 1. Tạo cấu trúc cho thư mục role

```
 mkdir -p ansible_lab/roles/manage_user/{tasks,files,templates,vars,handlers}

```

- **tasks/**: chứa danh sách các nhiệm vụ mà role cần thực hiện 

- **files/:** chứa các file tĩnh cần được sao chép tới máy đích. Ví du file publickey od_rsa.pub, file cấu hình tùy chỉnh 

- **templates/:** lưu trữ các template được viết bằng jinja2

​	ví dụ: thêm file cấu hình (sshd_config) với nội dung động có thể chứa các 	biến như:

```
PasswordAuthentication {{ ssh_password_auth }}

```

​	Mục đích: dùng để tạo các file cấu hình mà nội dung thay đổi dựa trên 	biến ansible 



- **vars:/** chứa các biến tĩnh cho role 

- **handlers**: chứa các hành động đươc kích hoạt bởi các nhiệm vụ thường dùng cho việc khởi động lại dịch vụ 

- Tóm tắt lại:

  | **Thư mục**  | **Chức năng**                                                |
  | ------------ | ------------------------------------------------------------ |
  | `tasks/`     | Chứa danh sách các nhiệm vụ (tasks) cần thực hiện.           |
  | `files/`     | Lưu trữ các file tĩnh để sao chép tới máy đích.              |
  | `templates/` | Lưu các file mẫu Jinja2 để tạo file cấu hình động.           |
  | `vars/`      | Định nghĩa các biến tĩnh được sử dụng trong role.            |
  | `handlers/`  | Chứa các hành động cần kích hoạt sau khi thực hiện một số nhiệm vụ cụ thể. |

## 2. Tạo nội dung cho các file trong role 

### 2.1. File tasks/main.yml

```
---
# Tạo user với quyền sudo
- name: Tạo user mới
  user:
    name: "{{ user_name }}"
    state: present
    shell: /bin/bash
    groups: sudo
    create_home: yes

# Tạo thư mục .ssh
- name: Tạo thư mục .ssh
  file:
    path: "/home/{{ user_name }}/.ssh"
    state: directory
    owner: "{{ user_name }}"
    group: "{{ user_name }}"
    mode: '0700'

# Thêm public key vào authorized_keys
- name: Thêm public key vào authorized_keys
  copy:
    content: "{{ user_public_key }}"
    dest: "/home/{{ user_name }}/.ssh/authorized_keys"
    owner: "{{ user_name }}"
    group: "{{ user_name }}"
    mode: '0600'

# Vô hiệu hóa SSH bằng password
- name: Disable password authentication
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^#?PasswordAuthentication'
    line: 'PasswordAuthentication no'
    state: present

# Khởi động lại dịch vụ SSH
- name: Khởi động lại SSH
  service:
    name: sshd
    state: restarted
  when: ansible_service_mgr == "systemd"

- name: Khởi động lại SSH (sysvinit)
  service:
    name: ssh
    state: restarted
  when: ansible_service_mgr != "systemd"

```

### 2.2. file `vars/main.yml`

Khai báo các biến:

```
---
user_name: "exampleuser"
user_public_key: "ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAnExampleFakePublicKey...=="

```

### 2.3. file `handlers/main.yml`

xử lí các thay đổi 

```
---
- name: Restart SSH
  ansible.builtin.service:
    name: sshd
    state: restarted

```

## 3. Triển khai role

### 3.1. file `playbook.yml`

```
---
- name: Lab tạo user và cấu hình SSH
  hosts: all
  become: yes
  roles:
    - manage_user

```

### 3.2. Cấu hình inventory

```
[all]
vm1 ansible_host=10.10.0.140 ansible_user=server1 ansible_become_password=1


```

ansible_become_password: mk cho sudo 

## 4. chạy Playbook

```
ansible-playbook -i inventory playbook.yml

```


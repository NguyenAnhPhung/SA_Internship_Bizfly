# LAB 6 Viết roles cài đặt dhcp, dns

## Yêu cầu:

```
"Viết role ansible 
+ cài đặt DHCP server (trên cả ubuntu và centos 7)
+ cấu hình DNS cho các domain  (khi cần cấu hình thêm thì bổ sung vào list và chạy lại role là cấu hình được.)
    - test1.com 1.2.3.4
    - test2.com 1.2.3.4 "
```

## Cách làm:

### 1. DHCP

#### B1: Tạo cấu trúc cho role

```
ansible-galaxy init install_dhcp

```

Role sau khi tạo xong sẽ có cấu trúc như sau:

```
install_dhcp/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── tests/
│   ├── inventory
│   └── test.yml
└── vars/
    └── main.yml

```



#### B2: Cài đặt và cấu hình dhcp trong tasks/main.yml

```
---
# tasks file for install_dhcp
- name: Install DHCP server package
  apt:
    name: isc-dhcp-server
    state: present
    update_cache: yes


# Create file config DHCP
- name: copy DHCP configuration
  template:
    src: dhcpd.conf.j2
    dest: /etc/dhcp/dhcpd.conf
  notify: Restart DHCP Service

#cau hinh interface cho dhcp server 
- name: configure dhcp interface
  lineinfile:
    path: /etc/default/isc-dhcp-server
    regexp: '^INTERFACESv4=.*'
    line: 'INTERFACESv4="{{ dhcp_interface }}"'
  notify: Restart DHCP Service

# khoi dong va bat dhcp server 
- name:
  service:
    name: isc-dhcp-server
    state: started
    enabled: yes

```

#### B3: Tạo file cấu hình `templates/dhcpd.conf.j2`

```
subnet {{ subnet }} netmask {{ netmask }} {
    range {{ range_start }} {{ range_end }};
    option routers {{ gateway }};
    option domain-name-servers {{ dns_server }};
}

```

#### B4: Định nghĩa biến trong `defaults/main.yml`

```
---
# defaults file for install_dhcp
dhcp_interface: "ens37"
dns_server: "8.8.8.8"
subnet: "11.11.11.0"
netmask: "255.255.255.0"
range_start: "11.11.11.100"
range_end: "11.11.11.200"
gateway: "11.11.11.1"
```

#### B5: Cấu hình handler để restart dịch vụ

Trong tệp `handlers/main.yml`, thêm:

```
---
- name: Restart DHCP Service
  service:
    name: isc-dhcp-server
    state: restarted

```

#### B6: Kiểm tra và chạy role

Tạo một playbook gọi role này, ví dụ `site.yml`:

```
---
- hosts: dhcp_servers
  become: yes
  roles:
    - install_dhcp

```

Cập nhật file `inventory` với danh sách các máy chủ mà bạn muốn cài đặt DHCP:

```
[dhcp_servers]
dhcp1 ansible_host=10.10.0.147 ansible_user=dhcpserver ansible_become_password=1

```



#### B7: kiểm thử và chạy 

kiểm thử 

```
ansible-playbook -C playbook.yml
```

chạy

```
ansible-playbook -i inventory playbook.yml
```



## 2. DNS


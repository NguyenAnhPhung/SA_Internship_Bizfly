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

**B1: tạo thư mục role cho DNS**

```
ansible-galaxy init dns_ansible
```

**B2: Viết file tasks/main.yml**

```
---
# tasks file for dns_ansible
- name: install bind9
  apt:
    name: bind9
    state: present
    update_cache: yes

- name: config name.conf.options
  template:
    src: named.conf.options.j2
    dest: /etc/bind/named.conf.options

- name: config named.conf.local
  template:
    src: named.conf.local.j2
    dest: /etc/bind/named.conf.local

- name: config zone file
  template:
    src: zone.j2
    dest: "/etc/bind/db.{{ item.name }}"
  loop: "{{ domains }}"
  notify: Restart DNS Service
~                                    
```

B3: Cập nhật các mẫu template

1. named.conf.options.j2

   ```
   options {
       directory "/var/cache/bind";
   
       recursion no;
       allow-query { any; };
       listen-on { any; };
   };
   ```

2. named.conf.local.j2

   ```
   {% for domain in domains %}
   zone "{{ domain.name }}" {
       type master;
       file "/etc/bind/db.{{ domain.name }}";
   };
   {% endfor %}
   
   ```

3. zone.j2

   ```
   $TTL    604800
   @       IN      SOA     ns.{{ item.name }}. admin.{{ item.name }}. (
                           2         ; Serial
                           604800    ; Refresh
                           86400     ; Retry
                           2419200   ; Expire
                           604800 )  ; Negative Cache TTL
   
   @       IN      NS      ns.{{ item.name }}.
   @       IN      A       {{ item.ip }}
   ns      IN      A       {{ ip_nameserver }}
   ```

   

**B4: cập nhật biến default**

```
---
# defaults file for dns_ansible
ip_nameserver: "11.11.11.11"
domains:
  - name: "test1.com"
    ip: "1.2.3.4"
  - name: "test2.com"
    ip: "1.2.3.4"

```

**B5: cập nhật handler restart**

```
---
# handlers file for dns_ansible
- name: Restart DNS Service
  service:
    name: bind9
    state: restarted
  become: yes
~                  
```

**B6:  cấu hình ansible playbook **

```
---
- hosts: server
  become: yes
  roles:
    - dns_ansible
~                       
```

**B7: chạy ansible**

```
ansible-playbook -i inventoryfily playbook.yml
```


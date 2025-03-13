# Ansible Playbook

**Playbook**: Là một file khai báo kịch bản thực hiện tuần tự cho ansible 

Playbook gồm một hay nhiều các Plays - Là một nhóm các tác vụ/ role áp dụng lên nhóm host cố định xác định bằng key host trong file playbook

Trong mỗi play host có thể được phép apply nhiều steps được gọi là task. Các tasks được thực hiện theo thứ tự từ trên xuống. Tập hợp các tasks gọi là task list

Một khi task thì hành một module với đối số 

ansible có rất nhiều các module 

ví dụ

```
- host: all 
  tasks:
    - name: test ping 
      ping:
    - name: get ip
      command:
        cmd: ip a
```

Mỗi 

```
- name: test ping 
      ping:
```

được gọi là 1 task 

Mỗi task thì thực thi một module đối số

```
- host: all 
  tasks:
    - name: test ping 
      ping:  //module ping
    - name: get ip
      command:  //module command
        cmd: ip a  //cmd là đối số của module command
```


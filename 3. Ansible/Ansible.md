# Ansible

nguồn tham khảo sách **Ansible Playbook Essentials**

## 1. Ansible là gì?

Ansible là một công cụ quản lý cấu hình, sắp xếp và triển khai ứng dụng đơn giản, hiệu quả và nhanh chóng được kết hợp tất cả trong một.

Công cụ này cho phép bạn tự động hóa các tác vụ cơ sở hạ tầng phổ biến, chạy các lệnh ad hoc và triển khai các ứng dụng đa tầng trải dài trên nhiều máy. 

Ansible giúp tự động hóa các tác vụ phổ biến như:

- cài đặt phần mềm 
- quản lí tài nguyên mạng 
- cập nhật cấu hình hệ thống 

Triển khai ứng dụng đa tầng:

- Đáp ứng yêu cầu triển khai ứng dụng phức tạp trên nhiều máy chủ với các thành phần phụ thuộc khác nhau, như web server, database server, và load balancer.

Quản lí Playbook - sức mạnh thực sự cuả Ansible

- Playbook giúp bạn mô tả các tác vụ và quy trình một cách rõ ràng, có tổ chức.
- Nó đảm bảo tính lặp lại (reproducibility) và khả năng mở rộng.

Ansible không cần cài đặt phần mềm đặc biệt (agent) trên các máy chủ quản lý.

Chỉ yêu cầu dịch vụ SSH hoạt động trên các nút được quản lý.

### Các thuật ngữ cơ bản

tham khảo tại [viblo.asia](https://viblo.asia/p/phan-1-tim-hieu-ve-ansible-gwd43jarVX9)

- Inventory: Là file chứa thông tin các server cần quản lý. File này thường nằm tại đường dẫn /etc/ansible/hosts.
- Playbook: Là file chứa các task của Ansible được ghi dưới định dạng  YAML. Máy controller sẽ đọc các task trong Playbook và đẩy các lệnh thực thi tương ứng bằng Python xuống các máy con.
- Task: Một block ghi tác vụ cần thực hiện trong playbook và các thông số  liên quan. Ví dụ 1 playbook có thể chứa 2 task là: yum update và yum  install vim.
- Module: Ansible có rất nhiều module, ví dụ như moduel yum là module dùng để cài đặt các gói phần mềm qua yum. Ansible hiện có hơn ….2000 module  để thực hiện nhiều tác vụ khác nhau, bạn cũng có thể tự viết thêm các  module của mình nếu muốn.
- Role: Là một tập playbook được định nghĩa sẵn để thực thi 1 tác vụ nhất định (ví dụ cài đặt LAMP stack).
- Play: là quá trình thực thi của 1 playbook
- Facts: Thông tin của những máy được Ansible điều khiển, cụ thể là thông tin về OS, network, system…
- Handlers: Dùng để kích hoạt các thay đổi của dịch vụ như start, stop service.

## 2. Tại sao phải sử dụng Ansible?

Đơn giản, dễ sử dụng:

- Dùng ngôn ngữ YAML thân thiện, không yêu cầu người dùng học ngôn ngữ lập trình phức tạp.
- Cấu hình và triển khai nhanh chóng mà không cần tài liệu hướng dẫn dài dòng.

Hiệu quả và nhanh chóng:

- Tự động hóa hàng loạt tác vụ trên nhiều hệ thống cùng lúc, tiết kiệm thời gian và công sức.

- Quản lý máy chủ qua giao thức SSH mà không cần cài đặt thêm phần mềm trên các máy được quản lý.
- Giảm độ phức tạp và chi phí quản trị.

Tích hợp mạnh mẽ - "Batteries included":

- Hơn **1000 mô-đun tích hợp sẵn** để xử lý các tác vụ như:

  - Quản lý tệp, gói phần mềm, mạng, dịch vụ, và người dùng.

  - Tích hợp nền tảng đám mây (AWS, Azure, Google Cloud).

  - Quản lý cơ sở dữ liệu và ứng dụng (Jira, Apache, Nagios, v.v.).

Khả năng mở rộng:

- Dễ dàng viết mô-đun tùy chỉnh bằng ngôn ngữ bạn chọn (Python, Ruby, Bash, v.v.).

Tách biệt dữ liệu và mã:

- Giúp linh hoạt trong quản lý cấu hình, tăng khả năng tái sử dụng và chia sẻ mã.

Hỗ trợ nhiều môi trường:

- Dễ dàng quản lý các môi trường phát triển, thử nghiệm và sản xuất mà không trùng lặp cấu hình.

**Lợi ích khi sử dụng Ansible**

- giảm lỗi thủ công
- tăng tốc độ triển khai 

## 3. Tìm hiểu về Inventory và Playbook

### 3.1. Inventory trong Ansible

tham khảo tại [viblo.aisa](https://viblo.asia/s/phan-2-inventory-trong-ansible-GAWVpw9PL05)

#### Inventory là gì?

Một inventory chỉ định và mô tả các thiết bị mục tiêu ( tức là các server) trong môi trường ansible. Inventory có thể là 1 tệp đơn lẻ hoặc có thể là một loạt các tệp được viết theo định dạng INI hoặc YAML

- Các biến được định nghĩa trong tệp Ansible sẽ được sử dụng để giao tiếp và cấu hình các hệ thống mục tiêu. các máy chủ này có thể rất đa dạng, sử dụng các nền tảng khác nhau, hệ điều hành, nhà sản xuất khác nhau.
- Nói chung Ansible, không quan tâm đến bản chất của thiết bị là gì, chỉ qua tâm đến inventory được định dạnh chính xác 

Inventory mặc định của Ansible là

```
/etc/ansible/hosts
```

hoàn toàn có thể tự tạo một file riêng 

#### Làm sao để xây dựng  Inventory?

Danh sách các host được quản lí bởi ansible, file này nằm trong đường dẫn /etc/ansible/host 

Các host này có thể được viết dưới dạng partern hoặc có thể gom nhóm lại với nhau thành 1 Group

 Các hosts có thể được viết tắt ở dạng partern, hoặc có thể được gom nhóm lại với nhau thành một Group. 

- **Viết tắt ở dạng partern**

 	**Ví dụ**: 

​	Bạn có các máy chủ với tên:

```
web1.example.com
web2.example.com
web3.example.com

```

Thay vì viết từng dòng:

```
web1.example.com
web2.example.com
web3.example.com

```

thì có thể viết tắt thành 

```
web[1:3].example.com
```

- **Gom lại thành 1 group**

Ansible cho phép **gom các hosts vào một nhóm** để dễ quản lý. Thay vì gọi từng máy chủ, bạn chỉ cần gọi tên nhóm đó.

```
[web]
web1.example.com
web2.example.com
web3.example.com

[db]
db1.example.com
db2.example.com

```

**Kết quả:**

- Nhóm `web` chứa các máy chủ từ `web1` đến `web3`.
- Nhóm `db` chứa các máy chủ `db1` và `db2`.

thay vì phải gọi từng máy chủ 

```
ansible web1.example.com -m ping
ansible web2.example.com -m ping

```

bạn chỉ cần gọi

```
ansible web -m ping

```

Ansible sẽ tự động áp dụng lệnh tới tất cả các hosts trong nhóm `web`.

Kết hợp 2 phương thức này với nhau ta được 

```
[web]
web[1:3].example.com  # web1, web2, web3

[db]
db[1:2].example.com  # db1, db2

[all_servers:children]
web
db

```

`[all_servers:children]`: Đây là cách định nghĩa một **nhóm cha (`all_servers`)** chứa các nhóm con (`web` và `db`).

Sau khi viết xong file có thể dùng ansible-inventory để xác thực và lấy thông tin về kiểm kê Ansible 

```
ansible-inventory -i inventory --list

```

chạy test lại với lệnh ping trên group [web]

```
ansible mail -m ping
```

ping đến tất cả các module

```
ansible -i /path/to/inventor_file -m ping all 
```

ping đến group cụ thể 

```
ansible -i /path/to/inventory_file -m ping name_group
```

Ví dụ nội dung file inventory:

```
[web_servers]
vm1 ansible_host=<VM_IP> ansible_user=user

```

ngoài ra ansible -i có thể thực hiện nhiều thao tác liên quan đến lệnh khác 

```
ansible -i /path/to/inventory -m command -a "your command" all
```

nhưng nếu có muốn thực hiện thêm nhiều lệnh thì sao ?
vì vậy mà ta có playbook để viết sẵn các kịch bản cho các server 

### 3.2. Ansible Playbook

tham khảo tại [viblo.asia](https://viblo.asia/p/phan-3-tim-hieu-ve-ansible-playbooks-zXRJ8DqMJGq)

tham khảo tại [docs.ansible.com](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html)

#### Playbook là gì ?

Ansible Playbook là một công cụ mạnh mẽ giúp tự động hóa các tác vụ quản lý cấu hình và triển khai hệ thống trên nhiều máy chủ. 

nói chung playbook là cách để tự động hóa một tập hợp các tác vụ mà chúng ta muốn thực hiện trên máy chủ từ xa 

Chức năng chính của playbook:

- tự động hóa cấu hình 

  - định nghĩa và triển khai cấu hình hệ thống một cách chuẩn xác

- điều phối các bước triển khai

  - quản lí từng bước của một quy trình thủ công áp dụng trên nhiều nhóm máy chủ theo một quy trình cụ thể

- Hỗ trợ thực thi đồng bộ và bất đồng bộ 

  - chạy các tác vụ tuần tự hoặc xử lí các công việc song song khi cần 

- Kiểm tra trạng thái và tính **Idempotency** (tính bất biến khi lặp lại) :

  - đảm bảo trạng thái cuối cùng của hệ thống không bị thay đổi nếu palybook được chạy nhiều lần

  - ví dụ như 

    - Bạn viết một playbook để cài đặt Apache và bật nó lên.

      Nếu Apache đã được cài đặt và đang chạy, Ansible sẽ **không thực hiện lại** thao tác đó.

      Nếu Apache chưa được cài đặt, Ansible sẽ **thực hiện cài đặt** và đưa nó vào trạng thái như mong muốn.

#### Cú pháp Playbook

Playbook được viết bằng YAML . Mỗi playbook gồm nhiều plays (phần) chạy các tác vụ (tasks)

```
- name: tên của play 
  host: tên hoặc nhóm máy chủ 
  tasks:
    - name: tên của task 
      module_name:
        key: value
        
```



#### Cách thực thi Playbook

1.Tuần tự theo thứ tự:

- các playbook chạy từ trên xuống dưới 
- trong mỗi plays các task cũng chạy từ trên xuống dưới 

2.Kết hợp nhiều nhóm máy chủ 

- 1 playbook có thể kết hợp đồng thời nhiều nhóm máy chủ 

3.Cách chạy playbook 

- dùng lệnh `ansible-playbook`

  ```
  ansible-playbook playbook.yml
  ```

- Chạy ở chế độ kiểm tra (không thay đổi hệ thống)

  ```
  ansible-playbook --check playbook.yml
  ```

- Kiểm tra cú pháp và lỗi trong playbooks

  ```
  ansible-lint playbook.yml
  ```

## 4. Ansible hoạt động như thế nào?

Hướng dẫn cơ bản về cách hoạt động của Ansible

1. **Cấu trúc hệ thống Ansible**:

   - Có hai loại máy:

     - **Controller**: Là nơi cài đặt Ansible, tạo các tệp như `inventory` và `playbook`.
     - **Host**: Là các máy được quản lý bởi Controller (còn gọi là Managed Node). Không cần cài đặt Ansible trên các máy này vì Ansible hoạt động "agentless" (không cần cài phần mềm phụ trợ).

     ![https://images.viblo.asia/53be09a6-fe2d-464c-beeb-14b780623905.PNG](https://images.viblo.asia/53be09a6-fe2d-464c-beeb-14b780623905.PNG)

2. **Kết nối giữa Controller và Host**:

   - Chủ yếu thông qua SSH.
   - Controller có thể quản lý một hoặc nhiều máy Host.

3. **Tệp quan trọng trong Ansible**:

   - Inventory File:
     - Lưu thông tin về các máy Host như địa chỉ IP, tên miền, cách kết nối (username, password).
     - Có thể nhóm các máy Host để áp dụng cấu hình đồng thời.
     - Định dạng tệp thường là `.ini`.
   - Playbook File:
     - Được viết bằng YAML (đuôi `.yml`).
     - Ghi các lệnh hoặc cấu hình cần áp dụng trên máy Host.

4. **Cách hoạt động của Ansible**:

   - Controller thực hiện các tác vụ như cài đặt phần mềm, cập nhật hệ thống, hoặc thay đổi cấu hình trên các máy Host thông qua các Playbook đã định nghĩa.
   - Các lệnh trong Playbook áp dụng dựa trên danh sách Host trong Inventory.
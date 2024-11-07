# Manage Software - Package Management

## 1. Tổng quan 

tham khảo tại [scaler.com](https://www.scaler.com/topics/cyber-security/package-management-in-linux/)

## 1.1. Package là gì?

Package là tập hợp các tệp và thông tin cần thiết để cài đặt và duy trì phân mềm trên hệ thống Linux. Gói phần mềm chứa mã nguồn hoặc mã đã được biên dịch, cùng với các thông tin hướng dẫn cho trình quản lý gói (package manager) cách cài đặt, cấu hình và gỡ bỏ phần mềm này. Mỗi gói có thể bao gồm các thư viện, tệp thực thi, cấu hình, và tài liệu.

**Ví dụ:** Gói `vim` trên Ubuntu là một package chứa công cụ chỉnh sửa văn bản Vim. Khi cài đặt gói này, trình quản lý gói sẽ tải xuống tất cả các tệp cần thiết để sử dụng Vim.

### 1.2. Dependencies là gì?

Dependencies (phụ thuộc) là các phần mềm hoặc tài nguyên khác mà một package cần có để hoạt động đúng cách. Nếu một package yêu cầu một phần mềm hoặc thư viện nào đó đã được cài đặt trước, phần mềm đó được coi là dependency. Trình quản lý gói sẽ tự động tải về và cài đặt các dependencies nếu chưa có sẵn trong hệ thống.

**Ví dụ:** Khi cài đặt `vim`, nếu Vim yêu cầu thư viện `libncurses` để hiển thị giao diện dòng lệnh, trình quản lý gói sẽ tự động tải và cài `libncurses` nếu nó chưa có trong hệ thống.

### 1.3. **Binary Package là gì?**

Binary Package (gói nhị phân) là gói phần mềm đã được biên dịch từ mã nguồn thành mã máy. Khi tải về một gói nhị phân, người dùng có thể cài đặt ngay lập tức mà không cần phải biên dịch mã nguồn. Gói nhị phân giúp tiết kiệm thời gian và công sức, đặc biệt là đối với người dùng phổ thông.

**Ví dụ:** Khi tải và cài đặt `vim` từ gói `.deb` trên Ubuntu, đó là gói nhị phân chứa mã đã biên dịch sẵn của Vim.

Dưới đây là bảng so sánh giữa **Binary Package** và **Source Package**:

| **Tiêu chí**          | **Binary Package (Gói nhị phân)**                            | **Source Package (Gói mã nguồn)**                            |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Nội dung**          | Mã máy đã biên dịch, có thể chạy ngay                        | Mã nguồn chưa được biên dịch                                 |
| **Quá trình cài đặt** | Tải về và cài đặt trực tiếp, nhanh chóng                     | Cần tải mã nguồn, biên dịch, sau đó mới cài đặt              |
| **Tính linh hoạt**    | Ít tùy chỉnh vì đã được biên dịch sẵn                        | Cho phép tùy chỉnh trong quá trình biên dịch                 |
| **Hiệu suất**         | Phù hợp với đa số hệ thống, nhưng có thể thiếu tối ưu hóa    | Có thể tối ưu hóa theo phần cứng và cấu hình cụ thể của hệ thống |
| **Ví dụ tệp**         | `.deb` (Ubuntu), `.rpm` (CentOS)                             | `.tar.gz`, `.tar.bz2`                                        |
| **Công cụ cài đặt**   | `apt`, `yum`, `dnf`, v.v.                                    | `./configure`, `make`, `make install`                        |
| **Ưu điểm**           | Nhanh chóng, dễ sử dụng, thuận tiện cho người dùng phổ thông | Cho phép tối ưu hóa và tuỳ chỉnh cài đặt                     |
| **Nhược điểm**        | Không tối ưu cho mọi phần cứng, ít tùy chỉnh                 | Cài đặt phức tạp hơn, mất thời gian và công sức hơn          |

### 1.4. Repository là gì?

Repository (kho lưu trữ) là nơi chứa các gói phần mềm có thể cài đặt cho từng phiên bản và phân phối Linux. Repository có thể là kho lưu trữ chính thức của nhà phát triển Linux hoặc các kho lưu trữ do các bên thứ ba quản lý. Khi cài đặt phần mềm qua trình quản lý gói, hệ thống sẽ tìm kiếm và tải gói từ repository được cấu hình sẵn.

**Ví dụ:** Trên Ubuntu, có các repository như `Main`, `Universe`, và `Multiverse` chứa các gói được phát triển hoặc bảo trì bởi cộng đồng hoặc chính [Canonical](https://vi.wikipedia.org/wiki/Canonical_Ltd.)

Dưới đây là bảng chi tiết về các thành phần kho lưu trữ (repository) trên Ubuntu cùng với giải thích cụ thể:

| **Thành phần** | **Hỗ trợ bởi Canonical** | **Loại giấy phép**                                     | **Mô tả**                                                    | **Ví dụ phần mềm**                                           |
| -------------- | ------------------------ | ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Main**       | Có                       | Mã nguồn mở                                            | Chứa các phần mềm quan trọng, được Canonical hỗ trợ và đảm bảo cập nhật bảo mật. Đây là những phần mềm phổ biến và cần thiết cho hệ điều hành Ubuntu. | `bash`, `coreutils`, `apt` `bash` (trình shell cơ bản của hệ thống), `coreutils` (các công cụ dòng lệnh thiết yếu như `ls`, `cat`, `mv`), và `apt` (trình quản lý gói). |
| **Universe**   | Không                    | Mã nguồn mở                                            | Chứa các phần mềm mã nguồn mở do cộng đồng duy trì. Các gói này không được Canonical chính thức hỗ trợ nhưng rất đa dạng và phổ biến. | im` (trình soạn thảo văn bản), `gimp` (phần mềm chỉnh sửa ảnh), `ffmpeg` (công cụ xử lý video). |
| **Multiverse** | Không                    | Phần mềm không mã nguồn mở hoặc có vấn đề về bản quyền | Bao gồm phần mềm không phải mã nguồn mở, hoặc có vấn đề pháp lý về bản quyền. Các phần mềm này không có hỗ trợ từ Canonical và người dùng tự chịu trách nhiệm khi sử dụng. | Codec âm thanh, công cụ mã hóa, phát video                   |
| **Restricted** | Có                       | Phần mềm có bản quyền                                  | Chứa các driver và firmware cần thiết để hỗ trợ phần cứng nhưng không có mã nguồn mở. Canonical hỗ trợ các gói này để đảm bảo phần cứng hoạt động đúng cách. | Driver đồ họa Nvidia, firmware Wi-Fi Broadcom để hỗ trợ kết nối mạng không dây. |

---

**Để kiểm tra các repo trong máy:**

```
/etc/apt/sources.list
```

hoặc thư mục

```
/etc/apt/sources.list.d
```

### 1.5. Làm cách nào để cài đặt phần mềm?

Tham khảo tại [youtube.com](https://www.youtube.com/watch?v=bwOp46xAmkM)

#### 1.5.1. Package Manager (trình quản lí gói)

Trình quản lý gói là công cụ được sử dụng để cài đặt, cập nhật và quản lý các gói phần mềm trên hệ thống Linux. Các trình quản lý gói phổ biến bao gồm:

- **APT** (Advanced Package Tool) cho các hệ thống dựa trên Debian (như Ubuntu).
- **YUM** hoặc **DNF** cho các hệ thống dựa trên Red Hat (như Fedora).
- **Zypper** cho openSUSE.

#### 1.5.2. Sysnaptic/ Pamac

**Synaptic** và **Pamac** là các công cụ giao diện đồ họa (GUI) cho trình quản lý gói, giúp người dùng dễ dàng cài đặt, cập nhật và gỡ bỏ phần mềm mà không cần sử dụng dòng lệnh.

- **Synaptic** là một trình quản lý gói cho Debian và các bản phân phối dựa trên Debian.
- **Pamac** là công cụ tương tự cho hệ thống Arch Linux và các bản phân phối dựa trên Arch (như Manjaro).

**Ưu điểm**: Cung cấp giao diện đồ họa, rất dễ sử dụng cho người mới bắt đầu, không yêu cầu kiến thức về dòng lệnh

#### 1.5.3. Snap/Flatpak/ AppImage

- **Snap** và **Flatpak** là các hệ thống đóng gói phần mềm độc lập với hệ điều hành. Các gói Snap và Flatpak chứa tất cả các phụ thuộc và có thể chạy trên nhiều bản phân phối Linux mà không cần cài đặt thêm.
- **AppImage** cũng là một phương pháp tương tự, nhưng không cần cài đặt, bạn chỉ cần tải xuống và chạy trực tiếp.

**Snap** và **Flatpak** giúp dễ dàng cài đặt và cập nhật phần mềm, nhưng phần mềm cần được phát hành dưới dạng Snap hoặc Flatpak.

ví dụ 

Cài đặt gói Snap:

```
sudo snap install <tên_gói>
```

#### 1.5.4. Run and manual install

Đây là phương pháp cài đặt phần mềm từ các tệp cài đặt trực tiếp (thường là tệp `.tar.gz`, `.tar.bz2`, `.deb`, `.rpm`, hoặc mã nguồn) mà không cần qua trình quản lý gói. Bạn sẽ phải tự tay xử lý các bước cài đặt như giải nén tệp, cấu hình, biên dịch, và cài đặt.

**Ví dụ**:

- Cài đặt phần mềm từ mã nguồn:

  1. Tải tệp nguồn và giải nén:

     ```
     tar -xzvf <tên_gói>.tar.gz
     ```

     Chạy các lệnh để cấu hình, biên dịch và cài đặt:

     ```
     cd <thư_mục_mã_nguồn>
     ./configure
     make
     sudo make install
     ```

     
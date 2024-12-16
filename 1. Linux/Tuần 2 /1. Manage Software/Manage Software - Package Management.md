# Manage Software - Package Management

## 1. Tổng quan 

tham khảo tại [scaler.com](https://www.scaler.com/topics/cyber-security/package-management-in-linux/)

### 1.1. Package là gì?

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




## 2. Quản lí Package với Debian 

tham khảo tại [lpi.org](https://learning.lpi.org/en/learning-materials/101-500/102/102.4/102.4_01/)

### 2.1. The Debain Package Tool `dpkg`

#### 2.1.0. Các list các phầm mềm đã được cài đặt trên hệ thống và kiểm tra xem một ứng dụng đã được cài đặt trên hệ thống chưa

Cách 1: sử dụng `dpkg`

```
dpkg -l
```

Cách 2: sử dụng `apt`

```
apt list --installed
```

**Kiểm tra 1  ứng dụng đã được cài đặt hay chưa chỉ cần thêm lệnh grep để tìm kiếm theo tên ứng dụng**

#### 2.1.1. Cài đặt gói `-i`

`dpkg` với thao tác cơ bản nhất là cài đặt gói `.deb` 

```
dpkg -i packagename
```

nếu package đã có trong hệ thống thì nó sẽ được nâng cấp lên phiên bản mới 

#### 2.1.2. Xử lí dependencies (các phần phụ thuộc)

- Thông thường một gói sẽ phải phụ thuộc vào các gói khác để có thể hoạt động được như mong muốn 

- `dpkg` sẽ kiểm tra xem các phân phụ thuộc đó đã được cài đặt trên hệ thống hay chưa và nếu chưa thì việc cài đặt gói sẽ không được thực hiện. Trong trường hợp này nó sẽ liệt kê các gói bị thiếu. nó sẽ không tự xử lí được mà người dùng phải tự cài đặt

**ví dụ: **

```
# dpkg -i openshot-qt_2.4.3+dfsg1-1_all.deb
(Reading database ... 269630 files and directories currently installed.)
Preparing to unpack openshot-qt_2.4.3+dfsg1-1_all.deb ...
Unpacking openshot-qt (2.4.3+dfsg1-1) over (2.4.3+dfsg1-1) ...
dpkg: dependency problems prevent configuration of openshot-qt:
 openshot-qt depends on fonts-cantarell; however:
  Package fonts-cantarell is not installed.
 openshot-qt depends on python3-openshot; however:
  Package python3-openshot is not installed.
 openshot-qt depends on python3-pyqt5; however:
  Package python3-pyqt5 is not installed.
 openshot-qt depends on python3-pyqt5.qtsvg; however:
  Package python3-pyqt5.qtsvg is not installed.
 openshot-qt depends on python3-pyqt5.qtwebkit; however:
  Package python3-pyqt5.qtwebkit is not installed.
 openshot-qt depends on python3-zmq; however:
  Package python3-zmq is not installed.

```

#### 2.1.2. Gỡ bỏ gói `-r`

```
dpkg -r brave
```

- Khi một gói bị xóa các tệp cấu hình tương ứng sẽ được để lại trên hệ thống. Nếu muốn xóa tất cả mọi thứ liên quan đến package thì sử dụng `-P` thay vì `-r`

#### 2.1.3. Lấy thông tin gói `-I`

để lấy thông tin 1 gói `.deb` ta sử dụng: 

```
dpkg -I packagenam.deb
```

#### 2.1.4 `-L` Liệt kê các Gói đã được cài đặt và Nội dung Gói 

```
dpkg -L packagename
```

ví dụ:

```
# dpkg -L unrar
/.
/usr
/usr/bin
/usr/bin/unrar-nonfree
/usr/share
/usr/share/doc
/usr/share/doc/unrar
/usr/share/doc/unrar/changelog.Debian.gz
/usr/share/doc/unrar/copyright
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/unrar-nonfree.1.gz
```

#### 2.1.5. Kiểm tra gói nào đang sở hữu 1 tệp cụ thể `-S`

```
# dpkg-query -S /usr/bin/unrar-nonfree
unrar: /usr/bin/unrar-nonfree
```

#### 2.1.6. Cấu hình lại các Gói đã được cài đặt `dpkg-reconfigure`

khôi phục lại cấu hình ban đầu của 1 package

```
# dpkg-reconfigure tzdata
```

### 2.2. Advanced Package Tool (apt)

- `apt` là hệ thống quản lí gói bao gồm một bộ công cụ giúp đơn giản hóa việc cài đặt, nâng cấp, gỡ bỏ và quản lí package.

- `apt` có thể khắc được một số nhược điểm của dpkg ( chẳng hạn như giải pháp xử lí các phần phụ thuộc)
- `apt` hoạt đọng phối hợp với các kho phần mềm có sẵn để cài đặt 
- các kho này có để là do nhà phát hành os quản lí hoặc các kho khác có thể được các nhà phát triển hoặc nhóm người dùng duy trì để cung cấp phần mềm kh có sẵn từ bản phân phối chính 

Một số thao tác thường dùng trong `apt`

- `apt-get`: dùng để tải xuống, cài đặt, nâng cấp hoặc xóa
- `apt-cache`: được sử dụng để thực hiện các hoạt động như tìm kiếm trong chỉ mục gói 
- `apt-file`: được sử dụng để tìm kiếm các tệp trong các gói 
- `apt`:  thân thiện hơn, đơn giản là `apt` nó kết hợp các tùy chọn được sử dụng nhiều nhất là `apt-get` và `apt-cache` trong cùng một tiện ích. Nhiều lệnh dành cho apt sẽ giống với các lệnh dành cho `apt-get` vì vậy trong nhiều trường hợp chúng có thể hoán đổi được cho nhau.

#### 2.2.1. Cập nhật Package Index

"Updating the Package Index" trong Linux là quá trình làm mới thông tin về các gói phần mềm hiện có trong kho lưu trữ (repository) mà hệ thống đang kết nối. Package Index là danh sách chứa thông tin về tất cả các gói phần mềm, bao gồm các phiên bản, mô tả, và các phụ thuộc của từng gói.

Khi cập nhật Package Index, hệ thống sẽ tải về bản cập nhật của danh sách này từ các kho lưu trữ. Việc này giúp đảm bảo rằng khi bạn cài đặt hoặc cập nhật một phần mềm nào đó, hệ thống sẽ lấy phiên bản mới nhất có trong kho.

```
sudo apt update
```

#### 2.2.2. Cài đặt và gỡ cài đặt package

```
apt-get install packagename
```

```
apt-get remove packagename
```

cách gỡ cài đặt tất cả thành phần liên quan đến package 

```
sudo apt remove --purge virtualbox*
```



#### 2.2.3. Fixing Broken Dependencies (phần phụ thuộc bị hỏng)

Có thể sẽ có “phần phụ thuộc bị hỏng” trên một hệ thống. Điều này có nghĩa là một hoặc nhiều gói đã được cài đặt phụ thuộc vào các gói khác chưa được cài đặt hoặc không còn tồn tại nữa. Điều này có thể xảy ra do lỗi APT hoặc do gói được cài đặt thủ công.

Để giải quyết vấn đề này, hãy sử dụng lệnh `apt-get install -f`. Lệnh này sẽ cố gắng “sửa chữa” các gói bị hỏng bằng cách cài đặt các phần phụ thuộc bị thiếu, đảm bảo rằng tất cả các gói đều được nhất quán trở lại.

#### 2.2.4. Upgrading Packages (nâng cấp gói)

APT có thể được sử dụng để tự động nâng cấp mọi gói đã được cài đặt lên phiên bản mới nhất có sẵn từ kho lưu trữ. Điều này có thể được thực hiện bằng lệnh `apt-get upgrade`. Trước khi chạy nó, hãy cập nhật chỉ mục gói bằng `apt-get update` trước tiên:

#### 2.2.5. The Local Cache

Khi cài đặt hoặc cập nhật một gói, tệp `.deb` tương ứng sẽ được tải xuống thư mục bộ nhớ đệm cục bộ trước khi gói được cài đặt. Theo mặc định, thư mục này là `/var/cache/apt/archives`. Các tệp đã tải xuống một phần sẽ được sao chép vào `/var/cache/apt/archives/partial/`.

Khi cài đặt và nâng cấp các gói, thư mục bộ nhớ đệm có thể sẽ khá lớn. Để lấy lại dung lượng, ta có thể làm trống bộ nhớ đệm bằng cách sử dụng lệnh `apt-get clean`. Thao tác này sẽ xóa nội dung của các thư mục `/var/cache/apt/archives` và `/var/cache/apt/archives/partial/`.

#### 2.2.6. Tìm kiếm gói 

Tiện ích `apt-cache` có thể được sử dụng để thực hiện các thao tác trên chỉ mục gói (chẳng hạn như tìm kiếm một gói cụ thể hoặc liệt kê ra gói nào đang chứa một tệp cụ thể).

Để tiến hành tìm kiếm, hãy sử dụng `apt-cache search`, theo sau là mẫu tìm kiếm. Đầu ra sẽ là danh sách tất cả các gói có chứa mẫu, có thể là trong tên gói, mô tả hoặc tệp được cung cấp.

ví dụ 

```
# apt-cache search p7zip
liblzma-dev - XZ-format compression library - development files
liblzma5 - XZ-format compression library
forensics-extra - Forensics Environment - extra console components (metapackage)
p7zip - 7zr file archiver with high compression ratio
p7zip-full - 7z and 7za file archivers with high compression ratio
p7zip-rar - non-free rar module for p7zip
```

Trong ví dụ trên, mục nhập `liblzma5 - XZ-format compression library` dường như không khớp với mẫu. Tuy nhiên, nếu chúng ta hiển thị đầy đủ thông tin (bao gồm cả mô tả) cho gói bằng tham số `show`, ta sẽ tìm thấy mẫu ở đó:

```
# apt-cache show liblzma5
```

#### 2.2.7. Quản lí sources 

##### 1. Thêm kho lưu trữ mới 

```
sudo add-apt-repository <ppa:kho_lưu_trữ>
```

##### 2. Gỡ bỏ kho lưu trữ 

```
sudo add-apt-repository --remove <ppa:kho_lưu_trữ>
```

Cập nhật sau khi sửa thêm kho lưu trữ

```
sudo apt update 
```

tham khảo thêm tại [lpi.org](https://learning.lpi.org/vi/learning-materials/101-500/102/102.4/102.4_01/) 


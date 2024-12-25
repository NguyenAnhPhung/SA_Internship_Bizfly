# Git là gì?

## 1. Git là gì?

Git là một hệ thống dùng để quả lí mã nguồn. Hệ thống kiểm soát phiên bản ghi lại và lưu các thay đổi khi sau mỗi lần sửa đổi tệp. cho phép khôi phục phiên bản trước đó của công việc bất kì lúc nào.

- Giả sử như đang viết một tài liệu rất dài và muốn lưu lại nó sau mỗi lần sửa đổi nếu làm thủ công thì sẽ phải lưu ra thành nhiều file khác nhau việc này mất thời gian và dễ gây nhầm lẫn đặc biệt khi quay lại phiên bản cũ hoặc làm việc chung với người khác 
- Vậy nên git sẽ giúp làm tất cả các việc trên một cách tự động

### 1.1. Git làm gì ?

- **Ghi lại lịch sử mã nguồn:**

  - Mỗi lần chỉnh sửa và lưu mã ( gọi là "commit"), Git sẽ lưu lại trạng thái của mã đó.
  - Vì vậy mà có thể xem lại ai đã thay đổi gì, khi nào, lí do

- **Hỗ trợ cộng tác nhóm:**

  - Nhiều người làm chung 1 dự án git giúp mọi người làm việc đồng thời mà không lo ghi đè hay mất mã.
  - Tự động hợp nhất các thay đổi của từng người và cảnh báo nếu có xung đột 

- **Phục hồi dễ dàng**

  Nếu có lỗi thì có thể quay lại phiên bản trước đó chỉ với vài lệnh đơn giản thay vì loay hoay tìm lại thủ công 

### 1.2. Tại sao cần dùng git?

Nếu không có git thì sẽ:

- Tốn thời gian lưu phiên bản của từng mã nguồn 

- Gặp rắc rối khi không biết ai thay đổi tệp và khi nào 
- Dễ mất mã khi nhiều người cùng sửa trên một tệp 

​		

## 2. Thành phần của git 

Git có 3 thành phần chính

- **Các kho lưu trữ (repo):** theo dõi tất cả các thay đổi của dự án, chứa tất cả các commit được thực hiện bởi nhóm làm việc
- **Cây làm việc:** hay thự mục làm việc nó bao gồm các tệp bạn đang làm việc 
- **Chỉ mục** hoặc khu vực sắp xếp, là nơi các cam kết được chuẩn bị.
  - Sau khi được sắp xếp, các tệp từ cây làm việc được so sánh với các tệp trong kho lưu trữ 
  - Các thay đổi với tệp trong cây làm việc được đánh dấu là đã sửa đổi trước khi thực hiện chúng 

![img](https://nulab.com/static/1736ed1f6051c5c22605cb706d3bc08e/5a190/01.png)



#### **Quy trình công việc git cơ bản:**

1. **Sửa đổi tệp của bạn trong cây làm việc**
    Đây là bước bạn thực hiện các thay đổi trực tiếp trong mã nguồn trên máy tính của mình. Bạn chỉnh sửa, thêm, hoặc xóa các đoạn mã.

2. **Sắp xếp những thay đổi bạn muốn đưa vào lần cam kết tiếp theo**
    Sau khi chỉnh sửa, bạn cần nói với Git rằng bạn muốn "ghi nhận" những thay đổi nào. Bước này gọi là **staging** và bạn sử dụng lệnh `git add` để thêm tệp hoặc thay đổi vào "chỉ mục" (staging area).

   - Ví dụ thực tế

     : Bạn sửa 

     ```
     index.html
     ```

      và chạy lệnh:

     ```bash
     git add index.html
     ```

     Điều này giúp Git biết rằng tệp 

     ```
     index.html
     ```

      đã sẵn sàng để được cam kết.

3. **Cam kết thay đổi của bạn**
    Khi đã sẵn sàng, bạn sẽ "cam kết" các thay đổi để lưu chúng vào kho lưu trữ. Bước này tạo một **ảnh chụp nhanh (snapshot)**, lưu trạng thái hiện tại của tệp vào lịch sử. Sử dụng lệnh `git commit` kèm thông điệp mô tả thay đổi.

   - Ví dụ thực tế

     : Sau khi staging, bạn cam kết thay đổi với lệnh:

     ```bash
     git commit -m "Cập nhật tiêu đề trang trong index.html"
     ```

     Thông báo 

     ```
     "Cập nhật tiêu đề trang trong index.html"
     ```

      sẽ giúp bạn (và nhóm) hiểu thay đổi này là gì.

1. **Thay đổi mã** → Làm việc trong cây làm việc.
2. **Staging thay đổi** → Dùng lệnh `git add`.
3. **Lưu thay đổi vào lịch sử** → Dùng lệnh `git commit`.

#### **Ba trạng thái của tệp Git**

1. **Đã sửa đổi (Modified)**:
   Bạn chỉnh sửa tệp trong dự án. Git chưa biết về thay đổi này.
2. **Đã sắp xếp (Staged)**:
   Bạn dùng lệnh `git add` để nói với Git rằng thay đổi này cần được ghi nhận.
3. **Đã cam kết (Committed)**:
   Bạn dùng lệnh `git commit` để lưu thay đổi vào lịch sử Git, kèm theo tin nhắn mô tả.

![img](https://nulab.com/static/d13cdc1344230f603d17b31a5cbd1dae/5a190/02.png)

**Tóm tắt các luồng chuyển đổi:**

1. **Untracked → Staged**: Dùng `git add` để thêm tệp mới vào chỉ mục (staging area).
2. **Staged → Committed**: Dùng `git commit` để lưu thay đổi vào lịch sử Git.
3. **Committed → Modified**: Chỉnh sửa tệp, làm cho nó thay đổi so với phiên bản đã cam kết.
4. **Modified → Staged**: Dùng `git add` để sắp xếp các thay đổi cho cam kết mới.
5. **Staged → Untracked**: Nếu xóa tệp trước khi cam kết, nó quay lại trạng thái chưa được theo dõi.
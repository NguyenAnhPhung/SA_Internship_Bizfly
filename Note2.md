**Repo**

- tại sao addres repo ở bản 22 24 đc lưu ở vị trí này còn các phiên bản khác lại lưu ở vị trí khác
- lưu ở vị trí khác có được không 

**Top**

tiến trình zombie là gì?

tại sao kill rồi mà vẫn còn chiếm 

 xem lại phần ip private với các classs có thật là chúng được quy định như vậy không? 

## luồng hoạt động của 1 tiến trình 

Luồng hoạt động của một tiến trình trong hệ điều hành Linux/UNIX thường được chia thành các giai đoạn sau:

------

### **1. Tạo tiến trình (Process Creation)**

- Khởi tạo

  :

  - Một tiến trình mới được tạo thông qua lời gọi hệ thống (system call) như `fork()`, `vfork()` hoặc các cơ chế tương tự.
  - Tiến trình cha tạo một bản sao của chính nó, và tiến trình con sẽ thừa hưởng hầu hết tài nguyên từ tiến trình cha (ví dụ: không gian địa chỉ, biến môi trường, file descriptors).

- Ví dụ

  :

  - Khi bạn mở một ứng dụng từ terminal, một tiến trình mới được tạo ra.

------

### **2. Khởi chạy (Process Execution)**

- **Thực hiện chương trình mới**:
  - Sau khi tiến trình con được tạo ra, nó có thể gọi `exec()` để thay thế nội dung của nó bằng chương trình mới.
  - Ví dụ, khi bạn chạy một lệnh trong terminal (`ls`), shell sẽ gọi `fork()` để tạo tiến trình con, và tiến trình con sẽ gọi `exec()` để thực thi lệnh `ls`.
- **Chuyển sang trạng thái thực thi**:
  - Tiến trình được chuyển sang trạng thái `READY` và chờ bộ lập lịch (scheduler) cấp CPU để chạy.

------

### **3. Thực thi (Process Execution)**

- **Bộ lập lịch CPU**:
  - Tiến trình được bộ lập lịch chọn và cấp CPU để thực hiện các tác vụ.
  - Trong giai đoạn này, tiến trình nằm ở trạng thái **RUNNING**.
- **Tương tác tài nguyên**:
  - Trong khi thực thi, tiến trình có thể cần đọc/ghi dữ liệu, yêu cầu tài nguyên từ hệ điều hành (I/O, bộ nhớ, v.v.).
  - Nếu tiến trình chờ tài nguyên (như đợi dữ liệu từ đĩa), nó sẽ chuyển sang trạng thái **WAITING**.

------

### **4. Chờ đợi (Waiting State)**

- Chờ tài nguyên hoặc sự kiện

  :

  - Tiến trình rơi vào trạng thái chờ nếu nó cần tài nguyên hoặc đợi một sự kiện xảy ra (như I/O hoàn tất, nhận tín hiệu, hoặc hoàn thành thời gian ngủ).

- Trạng thái

  :

  - Tiến trình chuyển từ trạng thái **RUNNING** sang **WAITING** và tạm thời bị dừng thực thi.

------

### **5. Kết thúc (Termination)**

- **Kết thúc thực thi**:
  - Tiến trình gọi `exit()` để báo cho hệ điều hành rằng nó đã hoàn thành công việc.
  - Hệ điều hành giải phóng tài nguyên của tiến trình (bộ nhớ, mô tả file, v.v.).
- **Trạng thái Zombie**:
  - Sau khi tiến trình con kết thúc, nó vẫn tồn tại trong bảng tiến trình cho đến khi tiến trình cha đọc mã thoát bằng `wait()` hoặc `waitpid()`.
  - Nếu tiến trình cha không xử lý mã thoát, tiến trình con sẽ trở thành **zombie**.

------

### **6. Tái sử dụng tài nguyên**

- Sau khi tiến trình cha đọc mã thoát (nếu có), hệ điều hành xóa hoàn toàn tiến trình con khỏi bảng tiến trình, và tài nguyên của nó được giải phóng.

------

### **Mô hình trạng thái của tiến trình**

Hệ điều hành sử dụng một mô hình trạng thái để quản lý luồng hoạt động của các tiến trình. Các trạng thái chính:

1. **New**: Tiến trình đang được tạo.
2. **Ready**: Tiến trình sẵn sàng để chạy.
3. **Running**: Tiến trình đang thực thi.
4. **Waiting**: Tiến trình đang chờ sự kiện hoặc tài nguyên.
5. **Terminated**: Tiến trình đã hoàn thành.

------

### **Ví dụ minh họa: Luồng hoạt động của tiến trình trong Shell**

1. Người dùng gõ lệnh `ls` trong shell.
2. Shell tạo một tiến trình con bằng `fork()`.
3. Tiến trình con thực hiện `exec()` để chạy chương trình `ls`.
4. Tiến trình `ls` liệt kê các tệp, thực hiện I/O nếu cần.
5. Khi hoàn thành, `ls` gọi `exit()` và tiến trình kết thúc.
6. Shell (tiến trình cha) gọi `wait()` để nhận mã thoát và tiếp tục chờ lệnh mới.

------

Hệ điều hành sử dụng các cơ chế như **bộ lập lịch**, **quản lý tài nguyên**, và **liên lạc giữa các tiến trình (IPC)** để đảm bảo luồng hoạt động này diễn ra mượt mà.

**nguyên nhân dẫn đến trạng thái zobie là tiến trình cha chưa đọc trạng thái exit code của tiến trình con điều này có thể được giaỉ thích như sau:**

### **1. Tiến trình cha đang bận xử lý công việc khác**

- Tiến trình cha có thể đang thực hiện các tác vụ khác và không ưu tiên kiểm tra trạng thái của tiến trình con ngay lập tức.
  - **Ví dụ**: Một tiến trình cha là một **web server**, có thể đang xử lý các yêu cầu khác và trì hoãn việc kiểm tra trạng thái của các tiến trình con thực hiện các tác vụ phụ trợ.
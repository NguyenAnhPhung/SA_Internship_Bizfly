# API

https://viblo.asia/p/tim-hieu-kien-thuc-co-ban-ve-api-maGK7A4Mlj2



API hoạt động như trung gian cho phép các thành phần phần mềm giao tiếp với nhau, giúp tăng hiệu quả 

## 1. Phương pháp hoạt động của API 

**API REST** là một phương pháp hoạt động của API. Ở đó, máy khách sẽ gửi yêu cầu đến máy chủ theo hình thức dữ liệu. Sau đó máy chủ sẽ dùng chính các dữ liệu này để làm hàm nội bộ và trả kết quả máy khách trở lại 

REST là một dạng chuyển đổi cấu trúc dữ liệu của một kiểu kiến trúc để viết API. Nó sử dụng phương thức HTTP đơn giản để tào giao tiếp giữa các máy. vì vậy thay vì sử dụng 1 url cho việc xử lý một số thông tin người dùng , REST gửi một yêu cầu HTTP như GET, POST, DELETE..đến 1 URL để xử lí dữ liệu

### 1.1. Cách hoạt động của API

- khi người dùng đăng nhậ vào một ứng dụng, ứng dụng sẽ cần phải lấy thông tin chi tiết của nguời dùng đó, vì vậy về mặt API REST bạn có thể có được API endpoint
- 

![img](https://image.vietnix.vn/wp-content/uploads/2022/07/hoat-dong-cua-api-1200x393.png)

- Ứng dụng đang yêu cầu những chi tiết kể trên sẽ tiến hành gọi endpoint
- Các phương thức API sẽ là các từ mô tả hành động của API. Dưới đây là các hành động
  - GET (Read)
  - POST (Create)
  - PUT (Update)
  - DELETE (Delete)

**về request:**

http request có tất cả 9 loại **method,** 

- GET: sử dụng để lấy thông tin từ server thep URL đã cung cấp
- HEAD: giống GET nhưng response trả về không có body, chỉ có header
- POST: gưi thông tin tới server thông qua các parameters HTTP
- PUT: ghi đè tất cả thông tin của đối tượng với với những gì được gửi lên 
- DELETE: xáo resource trên server 
- CONNECT: thiết lập kết nối tới server theo URL
- OPTIONS: mô tả các tùy chọ giao tiếp cho resource 
- TRACE: thực hiện bài test loop-back theo đường dẫn đến resource 

**về response** 

Sau khi nhận được request từ phía client, server sẽ xử lí cái request đó và gửi ngược lại cho client 1 cái response. cấu trúc gồm 3 phần:

- status code 
- heathers
- body

heather và body tương đối giống request

**status code của response**

- 1xx: infomation: khi nhận được những mã như vậy tức là request đã được server chấp nhận quá trình sử lí request đang được tiếp tục 
- 2xx: Success: khi nhận được những mã như vậy request đã được server tiếp nhận, hiểu và xử lí thành công 
- 3xx: Redirection (chuyển hướng): mã trạng thái này cho biết client cần có thêm action để hoàn thành request
- 4xx: client Error: request chứ cú pháp không xác địh hoặc không được thực hiện 
- 5xx: server error: server thất bại vớ việc thực hiện 1 request nhìn như có vẻ khả thi
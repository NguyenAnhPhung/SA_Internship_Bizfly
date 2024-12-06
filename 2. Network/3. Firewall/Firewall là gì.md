# Firewall là gì?

tham khảo tại [đây](https://www-fortinet-com.translate.goog/resources/cyberglossary/firewall?_x_tr_sl=en&_x_tr_tl=vi&_x_tr_hl=vi&_x_tr_pto=wapp)

## 1. Firewall là gì?

Tường lửa là một thiết bị bảo mật mạng được thiết kế để giám sát, lọc và kiểm soát lưu lượng truy cập mạng đến và đi dựa trên các quy tắc bảo mật được xác định trước. Mục đích chính của tường lửa là thiết lập một rào cản giữa mạng nội bộ đáng tin cậy và mạng bên ngoài không đáng tin cậy.

Tường lửa có cả dạng phần cứng và phần mềm và chúng hoạt động bằng cách kiểm tra các gói dữ liệu và xác định xem cho phép hay chặn chúng dựa trên một tập hợp các quy tắc. Các tổ chức có thể định cấu hình các quy tắc này để cho phép hoặc từ chối lưu lượng truy cập dựa trên các tiêu chuẩn khác nhau, chẳng hạn như địa chỉ IP nguồn và đích, số cổng và loại giao thức.

## 2. Tường lửa hoạt động như thế nào?

Tường lửa hoạt động như một bộ phận bảo mật thiết yếu, giám sát và kiểm soát luồng dữ liệu mạng đến và đi, bảo vệ mạng khỏi truy cập trái phép và các mối đe dọa tiềm ẩn.  Cơ chế hoạt động của tường lửa có thể được chia thành các bước sau:

1. **Kiểm tra gói dữ liệu:** Khi một gói dữ liệu (packet) được gửi đến mạng, tường lửa sẽ kiểm tra gói dữ liệu đó.

2. **So sánh với quy tắc:** Tường lửa sẽ so sánh thông tin trong gói dữ liệu (như địa chỉ IP nguồn và đích, số cổng, giao thức) với tập hợp các quy tắc được cấu hình sẵn.

3. Quyết định:

    Dựa trên kết quả so sánh, tường lửa sẽ đưa ra quyết định cho phép gói dữ liệu đi qua hoặc chặn nó.

   - Nếu gói dữ liệu phù hợp với một quy tắc cho phép, nó sẽ được chuyển tiếp đến đích.
   - Nếu gói dữ liệu phù hợp với một quy tắc chặn, nó sẽ bị loại bỏ và kết nối bị từ chối.

4. **Ghi nhật ký:** Tường lửa thường ghi lại thông tin về các gói dữ liệu đã được xử lý, bao gồm thông tin về gói dữ liệu, quy tắc được áp dụng và kết quả của việc xử lý. Thông tin này có thể được sử dụng để phân tích bảo mật và khắc phục sự cố.

Tường lửa có thể được triển khai dưới dạng phần cứng hoặc phần mềm, và hoạt động ở các lớp khác nhau của mô hình OSI (Open Systems Interconnection) để thực hiện các chức năng bảo mật khác nhau:

- **Tường lửa Stateless:** Hoạt động ở tầng Giao vận (Transport Layer), kiểm tra từng gói dữ liệu độc lập mà không lưu trữ thông tin về trạng thái kết nối.
- **Tường lửa Stateful:** Theo dõi trạng thái của các kết nối đang hoạt động, sử dụng ngữ cảnh để xác định và xử lý các hoạt động đáng ngờ.
- **Cổng Ứng dụng (Application-Level Gateways):** Hoạt động ở tầng Ứng dụng (Application Layer), đóng vai trò trung gian giữa máy khách và máy chủ, chuyển tiếp yêu cầu và lọc phản hồi.
- **Tường lửa Thế hệ tiếp theo (NGFW):** Kết hợp các khả năng của các tường lửa trước đó với các tính năng nâng cao như kiểm tra gói dữ liệu sâu (DPI), ngăn chặn xâm nhập (IPS) và ngăn chặn mất dữ liệu (DLP).

Cần lưu ý rằng các mô tả trên chỉ là tổng quan về cơ chế hoạt động của tường lửa. Cụ thể, cách thức hoạt động của từng loại tường lửa sẽ có những điểm khác biệt nhất định.
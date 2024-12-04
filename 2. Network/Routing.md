# Routing 

## 1. What is IP Routing?

tham khảo tại [studyccna.com](https://study-ccna.com/what-is-ip-routing/)

**IP Routing** là quá trình chuyển tiếp gói tin (packet) từ một máy chủ trên một mạng đến một máy chủ khác trên một mạng khác nhau. Quá trình này thường được thực hiện bởi các thiết bị mạng gọi là **router**.

**Cách thức hoạt động:**

1. **Địa chỉ IP:** Mỗi thiết bị trên mạng đều có một địa chỉ IP duy nhất, giống như địa chỉ nhà của bạn.
2. **Bảng định tuyến:** Router sử dụng một bảng định tuyến, giống như một bản đồ, để xác định đường đi tốt nhất đến các mạng khác.
3. **Chuyển tiếp gói tin:** Khi một gói tin đến router, nó kiểm tra địa chỉ đích và tham khảo bảng định tuyến.
4. **Xác định hop tiếp theo:** Dựa trên bảng định tuyến, router xác định giao diện mạng tiếp theo để chuyển tiếp gói tin.
5. **Lặp lại:** Quá trình này tiếp tục cho đến khi gói tin đến đích cuối cùng.

**Default Gateway: Con đường mặc định**

**Default gateway** là một router mà các thiết bị sử dụng để giao tiếp với các thiết bị trên các mạng khác khi không có tuyến đường trực tiếp. Nó giống như một con đường chính dẫn đến các thành phố khác. Tất cả các gói tin mà router không biết cách chuyển tiếp sẽ được gửi đến default gateway, và từ đó được chuyển tiếp đến đích cuối cùng.

**Bảng định tuyến: Bản đồ của router**

Bảng định tuyến là một danh sách các tuyến đường mà router biết. Mỗi tuyến đường bao gồm ba phần chính:

1. **Mạng đích:** Mạng mà gói tin đang được gửi đến.
2. **Hop tiếp theo:** Địa chỉ của router tiếp theo hoặc mạng trực tiếp kết nối.
3. **Giao diện:** Giao diện mạng được sử dụng để đến hop tiếp theo.

**Cách thức cập nhật bảng định tuyến:**

Có ba cách chính để cập nhật bảng định tuyến:

1. **Mạng trực tiếp kết nối:** Các mạng được kết nối trực tiếp với router. Router tự động học được các mạng này.
2. **Định tuyến tĩnh:** Quản trị mạng cấu hình thủ công các tuyến đường trong bảng định tuyến. Phương pháp này phù hợp với các mạng nhỏ hoặc các tuyến đường cụ thể cần kiểm soát chính xác.
3. **Định tuyến động:** Các router trao đổi thông tin định tuyến với nhau thông qua các giao thức định tuyến như RIP, OSPF hoặc BGP. Điều này cho phép mạng tự động thích ứng với các thay đổi.

**Ví dụ: Gửi một gói tin**

Hãy tưởng tượng bạn muốn gửi một bức thư (gói tin) cho một người bạn ở một thành phố khác (mạng khác). Bạn đưa nó đến bưu điện địa phương (router). Bưu điện kiểm tra bản đồ (bảng định tuyến) để tìm tuyến đường tốt nhất đến thành phố của bạn bè. Sau đó, nó gửi bức thư đến bưu điện tiếp theo trên tuyến đường. Quá trình này tiếp tục cho đến khi bức thư đến tay bạn bè của bạn.

Hiểu các khái niệm này giúp bạn hiểu rõ hơn về mạng lưới phức tạp của các router, cho phép chúng ta giao tiếp và truy cập thông tin trên toàn cầu.


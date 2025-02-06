# Massage Queue

Openstack sử dụng hàng đợi tin nhắn để điều phối các hoạt động và thông tin trạng thái giữa các dịch vụ. Dịch vụ hàng đợi tin nhắn chạy trên node controller. Openstack hỗ trợ 1 số dịch vụ hàng đợi tin nhắn như RabbitMQ

### Tại sao lại cần có massage queue?

- nó là một phần quan trong trong hệ thống phân tán để đảm bảo các dịch vụ giao tiếp và phối hợp với nhau 1 cách hiệu quả 
- Khi tạo VM nova cần gửi yêu cầu mạng đến neutron hay volume đến storage các dịch vụ này sẽ nhận yêu cần lần lượt theo hàng đợi, đảm bảo tính nhất quán 
- Tăng độ tin cậy: Queue có khả năng lưu trữ tin nhắn cho đến khi được xử lí, nếu 1 service bận hoặc không hoạt động tin nhắn sẽ được lưu trong hàng đợi để xử lí sau, đảm bảo các request không bị mất ngay cả khi có sự cố 

### Nếu không có massage queue, hệ thống có họat động được không?

Hệ thống vẫn có thể hoạt động được nhưng nó sẽ gặp nhiều vấn đề làm giảm hiệu suất, độ tin cậy, khả năng mở rộng 
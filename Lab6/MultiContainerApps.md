# Multi container apps
***

### Tạo network
```sh
docker network create todo-app
```
### để xem ls network
```sh
docker network ls
```
### để xóa network
```sh
docker network remove todo-app
```

### Bắt đầu 1 container và add vào network
```sh
docker run -d `
     --network todo-app --network-alias mysql `
     -v todo-mysql-data:/var/lib/mysql `
     -e MYSQL_ROOT_PASSWORD=secret `
     -e MYSQL_DATABASE=todos `
     mysql:8.0
```
### Để xác nhận rằng bạn đã thiết lập và chạy cơ sở dữ liệu, hãy kết nối với cơ sở dữ liệu và xác minh rằng nó kết nối.
```sh
docker exec -it <mysql-container-id> mysql -u root -p
```
pass: secret

### show database (chú ý có dấu ';' cuối câu)
```sh
mysql> SHOW DATABASES;
```

### Bạn sẽ thấy kết quả trông như thế này:
```sh
+--------------------+
 | Database           |
 +--------------------+
 | information_schema |
 | mysql              |
 | performance_schema |
 | sys                |
 | todos              |
 +--------------------+
 5 rows in set (0.00 sec)
```

### Thoát khỏi trình bao MySQL để quay lại trình bao trên máy của bạn.
```sh
mysql> exit
```

### Connect to MySQL
#### Bây giờ bạn đã biết MySQL đã hoạt động và chạy, bạn có thể sử dụng nó. Nhưng làm thế nào để sử dụng nó? Nếu bạn chạy một container khác trên cùng mạng, bạn làm thế nào để tìm thấy container đó? Hãy nhớ rằng mỗi container có một địa chỉ IP riêng.
#### Để trả lời các câu hỏi trên và hiểu rõ hơn về việc liên kết mạng của container, bạn sẽ sử dụng container nicolaka/netshoot, container này đi kèm với nhiều công cụ hữu ích để khắc phục sự cố hoặc gỡ lỗi về vấn đề liên quan đến mạng.

### Bắt đầu vùng chứa mới bằng cách sử dụng hình ảnh nicolaka/netshoot. Đảm bảo kết nối nó với cùng một mạng.
```sh
docker run -it --network todo-app nicolaka/netshoot
```
### Bên trong vùng chứa, bạn sẽ sử dụng lệnh dig, đây là một công cụ DNS hữu ích. Bạn sẽ tra cứu địa chỉ IP cho tên máy chủ mysql.
```sh
dig mysql
```

### Run your app with MySQL
Ứng dụng todo hỗ trợ việc thiết lập một số biến môi trường để chỉ định các thiết lập kết nối MySQL. Chúng bao gồm:
MYSQL_HOST - tên máy chủ cho máy chủ MySQL đang chạy
MYSQL_USER - tên người dùng sử dụng cho kết nối
MYSQL_PASSWORD - mật khẩu sử dụng cho kết nối
MYSQL_DB - cơ sở dữ liệu được sử dụng sau khi kết nối
Lưu ý:
Trong khi sử dụng biến môi trường để thiết lập các cài đặt kết nối thường được chấp nhận trong quá trình phát triển, nhưng nó được khuyến nghị không sử dụng khi chạy ứng dụng trong môi trường sản xuất. Diogo Monica, người từng là người dẫn dắt bảo mật tại Docker, đã viết một bài đăng blog tuyệt vời để giải thích tại sao.
Một cơ chế bảo mật hơn là sử dụng hỗ trợ về bí mật do khung làm việc triển khai container cung cấp. Trong hầu hết các trường hợp, các bí mật này được gắn kết như các tệp trong container đang chạy. Bạn sẽ thấy nhiều ứng dụng (bao gồm cả hình ảnh MySQL và ứng dụng todo) cũng hỗ trợ biến môi trường với hậu tố _FILE để chỉ đến một tệp chứa biến.
Ví dụ, việc thiết lập biến MYSQL_PASSWORD_FILE sẽ khiến ứng dụng sử dụng nội dung của tệp được tham chiếu như mật khẩu kết nối. Docker không thực hiện bất kỳ điều gì để hỗ trợ các biến môi trường này. Ứng dụng của bạn sẽ cần biết để tìm biến và lấy nội dung của tệp.

### Chỉ định từng biến môi trường trước đó, cũng như kết nối vùng chứa với mạng ứng dụng của bạn. Đảm bảo rằng bạn đang ở trong thư mục ứng dụng bắt đầu khi chạy lệnh này.
```sh
docker run -dp 127.0.0.1:3000:3000 `
   -w /app -v "$(pwd):/app" `
   --network todo-app `
   -e MYSQL_HOST=mysql `
   -e MYSQL_USER=root `
   -e MYSQL_PASSWORD=secret `
   -e MYSQL_DB=todos `
   node:18-alpine `
   sh -c "yarn install && yarn run dev"
```

### Nếu bạn xem nhật ký của vùng chứa (docker logs -f <container-id>), bạn sẽ thấy một thông báo tương tự như sau, cho biết nó đang sử dụng cơ sở dữ liệu mysql.
```sh
docker logs -f <container-id>
```

```sh
nodemon src/index.js
 [nodemon] 2.0.20
 [nodemon] to restart at any time, enter `rs`
 [nodemon] watching dir(s): *.*
 [nodemon] starting `node src/index.js`
 Connected to mysql db at host mysql
 Listening on port 3000
```

### Kết nối với cơ sở dữ liệu mysql và chứng minh rằng các mục đang được ghi vào cơ sở dữ liệu. Hãy nhớ rằng, mật khẩu là bí mật
```sh
docker exec -it <mysql-container-id> mysql -p todos
```

mysql> select * from todo_items;
 +--------------------------------------+--------------------+-----------+
 | id                                   | name               | completed |
 +--------------------------------------+--------------------+-----------+
 | c906ff08-60e6-44e6-8f49-ed56a0853e85 | Do amazing things! |         0 |
 | 2912a79e-8486-4bc3-a4c5-460793a575ab | Be awesome!        |         0 |
 +--------------------------------------+--------------------+-----------+

 

172.19.0.2

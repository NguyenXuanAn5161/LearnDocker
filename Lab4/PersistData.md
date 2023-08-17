#Persist the Data - Lưu trữ dữ liệu

Mỗi container hoạt động, dù chạy cùng một image nhưng nó có không gian
"scratch space" riêng để CRUD.
Nghĩa là 1 image, run trên 2 container khác nhau thì data khác nhau.
Nó không được lưu trữ lại cho container khác sử dụng.

#Container Volumes

Khi container bị loại bỏ, bạn sẽ mất dữ liệu đã chạy trước đó trên container đó.
Để khắc phục điều này, bạn sẽ sử dụng khối lưu trữ (volumes) để lưu trữ dữ liệu.
-----------
|Container|
-----------
    | Container sẽ tương tác với dữ liệu được lưu trong volumes
    V và sau khi loại bỏ container này, dữ liệu cũng sẽ không bị mất.
---------
|Volumes| Sau này volumes sẽ bằng 1 cách nào đó (Chưa học tới), sẽ tương tác được với dữ liệu từ DB
---------

Đọc thêm Persist the todo data trên trang chủ để biết thêm chi tiết
https://docs.docker.com/get-started/05_persisting_data/#persist-the-todo-data

Tạo một khối lưu trữ và khởi động container
Tạo một khối lưu trữ bằng cách sử dụng lệnh docker volume create.
`docker volume create todo-db`
Remove một container nếu nó đang chạy `docker rm -f <id>`
Khởi động container, nhưng thêm tùy chọn --mount để chỉ định gắn kết một khối lưu trữ.
`docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started`
`-d`: chạy ở chế độ nền.
`-p 127.0.0.1:3000:3000`: Ánh xạ cổng 3000 (bên trái) của máy chủ (127.0.0.1) với cổng 3000 của container.
`--mount type=volume,src=todo-db,target=/etc/todos`: tùy chọn để gắn khối lưu trữ vào container.
`type=volume`: Loại khối lưu trữ sẽ được sử dụng là loại "volume".
`src=todo-db`: Tên của khối lưu trữ (volume) mà bạn đã tạo trước đó.
`target=/etc/todos`: Đường dẫn trong container mà khối lưu trữ sẽ được gắn kết vào.\

#Vậy Docker lưu dữ liệu ở đâu khi sử dụng volume
sử dụng lệnh `docker volume inspect todo-db`
Mountpoint là vị trí thực tế của dữ liệu trên đĩa. Lưu ý rằng trên hầu hết các máy, bạn sẽ cần có quyền truy cập root để truy cập thư mục này từ máy chủ.
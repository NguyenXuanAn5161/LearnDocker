# Use Docker Compose
#### Compose gom lại thành 1 cục
***
#### Docker Compose giúp đơn giản hóa vấn đề. Ví dụ như đổi pass chỉ cần 1 dòng, ...
#### Đối với Docker Compose bạn có thể tạo là file .yml để định nghĩa các dịch vụ và chỉ cần một lệnh duy nhất để khởi chạy hoặc tắt tất cả
***
## Định nghĩa dịch vụ ứng dụng
#### Định nghĩa image app. Tên sẽ tự động trở thành một định danh mạng (network alias), điều này sẽ hữu ích khi bạn định nghĩa dịch vụ MySQL của mình.
```sh
services:
  app:
    image: node:18-alpine
```
#### Định nghĩa image app. Tên sẽ tự động trở thành một định danh mạng (network alias), điều này sẽ hữu ích khi bạn định nghĩa dịch vụ MySQL của mình.
```sh
services:
  app:
    image: node:18-alpine
```
#### Thường thì bạn sẽ thấy command gần với định nghĩa image, mặc dù không có yêu cầu về thứ tự. Thêm command vào tệp compose.yaml của bạn.
```sh
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
```
#### Port.
```sh
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
```
#### Working directory: thư mục trong docker cần lưu trữ code. Volumes: định nghĩa nơi lưu trữ dữ liệu. `./`: lấy tại thư mục hiện tại, `:`: copy, câu đầy đủ `./:/app`: lấy code tại thư mục hiện tại `./`, sau đó sao chép `:`, và gửi vào app nơi lưu trữ code trong docker `/app`.
```sh
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
```
#### Định nghĩa môi trường
```sh
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
```
***
## Định nghĩa MySQL
#### Định nghĩa image MySQL
```sh
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
```
#### Copy sql bên trong docker container và tạo volume
```sh
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql

volumes:
  todo-mysql-data:
```
#### Định nghĩa các biến môi trường
```sh
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```
***
## Run the application stack
#### Remove containers
#### Start Docker compose
```sh
docker compose up -d
```
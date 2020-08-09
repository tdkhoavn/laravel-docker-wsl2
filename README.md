# Thiết lập môi trường dev Laravel với Docker + WSL2

![Docker Logo](/img/Moby-logo.png)


## 1. Cài đặt Windows Subsytem for Linux 2 (WSL2)

- Để sử dụng WSL2 máy tính của bạn phải cài đặt Windows 10 version 2004 trở lên.
- Cách cài thì làm theo [hướng dẫn sau](https://docs.microsoft.com/en-us/windows/wsl/install-win10#update-to-wsl-2)
- Distro mình sử dụng là Ubuntu 20.04
- Sau khi cài đặt thành công kiểm tra phiên bản WSL bằng lệnh sau `wsl --list -v`, cột VERSION hiện 2 là OK rồi.

![wsl --list -v](/img/01.png)

## 2. Cài đặt Windows Terminal

- Đây là ứng dụng hỗ trợ thao tác commandline của Microsoft. Bạn lên ứng dụng Store để cài nha. Cái này chủ yếu để cho nhìn cool ngầu thôi không cài cũng không sao😁.

![Windows Terminal](/img/02.png)


## 3. Cài đặt, cấu hình Docker for Windows

- Bạn cài đặt và cấu hình theo hướng dẫn sau https://docs.docker.com/docker-for-windows/wsl/
- Để tránh tình trạng Docker sử dụng quá mức RAM cần thiết, bạn nên giới hạn dung lượng RAM cấp cho WSL bằng cách tạo file `.wslconfig` trong thư mục `C:\Users\[your_username]`, và thêm vào nội dung sau
```
[wsl2]
memory=4GB # Limits VM memory in WSL 2 to 4 GB
```
- Sau khi cấu hình xong gõ lệnh `wsl --shutdown` để WSL nhận setting mới.
- Vào lại Ubuntu và gõ lệnh `docker run hello-world` để kiểm tra, hiện thông báo như sau là thành công.

![Docker for Windows](/img/03.png)

## 4. Cấu hình Docker Compose

- Đến bước này cần source code mẫu Laravel, có thể dụng compose để cài hoặc sử dụng source có sẵn
>※Lưu ý: Không nên đặt source code Laravel trong ổ /mnt/[driver_letter], trong một số thử nghiệm tốc độ load page chậm. Mà nên đặt ở thư mục của WSL luôn, VD: /home/[username]
- Copy file `docker-compose.yml`, thư mục `docker_config` ở repo này vào source code Laravel, cùng cấp với folder app
- Chỉnh sửa file `docker-compose.yml` dòng `traefik.http.routers.webserver.rule=Host(`v7.laravel.test`)` bằng tên hostname mà bạn muốn.
- Add host máy Windows `C:\Windows\System32\drivers\etc\hosts` bằng nội dung sau
```
# Please change to you hostname
127.0.0.1 v7.laravel.test
```
- Quay lại Ubuntu tạo docker network bằng lệnh `docker network create docker-net`. Tên `docker-net` tùy chọn, bạn có thể thay đổi theo ý thích, miễn bạn tạo tên gì thì trong file `docker-compose.yml` phần `network` phải cùng tên.
- Gõ lệnh `docker-compose up` để start các service.
- Gõ lệnh `docker ps` để kiểm tra các container đã chạy hết chưa

![docker ps](/img/04.png)

>- Từ máy Windows truy cập hostname mà đã add host phía trên (http://v7.laravel.test/) xem kết quả.
>- Thông tin database `host: db | user: root | password: Secret@12345`
>- Truy cập Adminer http://localhost:9091
>- Truy cập phpMyAdmin http://localhost:9090/index.php

## 5. Thao tác với container

- Truy cập vào container Apache + PHP
```
docker exec -ti -u 1000:1000 apache-v7.laravel.test /bin/bash
cd /app
```
- Sau khi đã vào được container thì bạn có thể chạy các lệnh artisan của Laravel bình thường
- Truy cập container NodeJS
```
docker exec -ti -u 1000:1000 node /bin/bash
```

## 6. Visual Studio Code

- Để thao tác chỉnh sửa code thì chúng ta cần cài đặt VSCode trên máy Windows.
- Sau khi cài xong thì truy cập thư mục chứa source ở Ubuntu và đơn giản gõ `code .`, khi đó VSCode trên Windows tự bật lên.
- Cuối cùng Let's Start Coding✌
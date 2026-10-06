# Cuong_HN-KS24C-CNTT4_IT209_Session06_Bai01
## 1. Mục tiêu
- Tạo cấu trúc thư mục `/var/www/my-app` theo chuẩn FHS (`/var/www` là nơi chứa dữ liệu dịch vụ web).
- Phân quyền bằng `chmod` (dạng octal và symbolic).
- Đổi chủ sở hữu và nhóm sở hữu bằng `chown`.

## 2. Môi trường
- Hệ điều hành: Ubuntu (GitHub Codespaces)
- Tài khoản non-root: codespace
- Nhóm web: `www-data`

## 3. Nhật ký các lệnh đã thực hiện

### 3.1. Kiểm tra user và nhóm
```bash
whoami
getent group www-data
```

### 3.2. Tạo cấu trúc thư mục
```bash
sudo mkdir -p /var/www/my-app/public /var/www/my-app/logs
```

### 3.3. Đổi chủ sở hữu và nhóm sở hữu
```bash
sudo chown -R $USER:www-data /var/www/my-app
```

### 3.4. Phân quyền
```bash
# Cách octal
sudo chmod 750 /var/www/my-app/public
sudo chmod 770 /var/www/my-app/logs

# Cách symbolic (kết quả tương đương)
sudo chmod u=rwx,g=rx,o= /var/www/my-app/public
sudo chmod u=rwx,g=rwx,o= /var/www/my-app/logs
```

## 4. Kết quả kiểm tra
```bash
ls -la /var/www/my-app
```

Đầu ra:
total 16
drwxr-xr-x 4 codespace www-data 4096 Oct  6 12:28 .
drwxr-xr-x 3 root      root     4096 Oct  6 12:28 ..
drwxrwx--- 2 codespace www-data 4096 Oct  6 12:28 logs
drwxr-x--- 2 codespace www-data 4096 Oct  6 12:28 public

## 5. Giải thích

| Thư mục | Octal | Symbolic | Owner | Group | Others |
|---|---|---|---|---|---|
| public | 750 | drwxr-x--- | rwx | r-x | --- |
| logs | 770 | drwxrwx--- | rwx | rwx | --- |

- **Cách tính octal:** r = 4, w = 2, x = 1. Vậy 7 = rwx, 5 = r-x, 0 = ---.
- **public (750):** Owner có toàn quyền để quản lý mã nguồn tĩnh. Group (`www-data`) chỉ đọc và truy cập (`r-x`) để web server phục vụ file mà không sửa được. Others không có quyền gì.
- **logs (770):** Owner và Group đều đọc, ghi và truy cập, để ứng dụng và web server ghi nhật ký. Others không có quyền, đảm bảo bảo mật.
- **Vì sao thư mục cần quyền `x`:** với thư mục, `x` nghĩa là được phép đi vào (`cd`) và truy cập file bên trong. Thiếu `x` thì dù có `r` cũng không dùng được thư mục một cách bình thường.
- **chown:** `$USER:www-data` đặt chủ sở hữu là tài khoản non-root hiện tại và nhóm sở hữu là `www-data`; `-R` áp dụng cho cả thư mục con.

## 6. Kết luận
`public` đạt `drwxr-x---` (750) và `logs` đạt `drwxrwx---` (770), đúng yêu cầu đề bài.
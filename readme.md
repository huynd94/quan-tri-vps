# Tài Liệu Tổng Hợp Quản Trị VPS & Linux

---

## Mục Lục
1. [Cấu Hình Firewall & Tối Ưu VPS Yếu](#cấu-hình-firewall--tối-ưu-vps-yếu)
2. [Cài Đặt aaPanel và Squid Proxy](#cài-đặt-aapanel-và-squid-proxy)
3. [Tổng Hợp Lệnh Cơ Bản Trên Linux](#tổng-hợp-lệnh-cơ-bản-trên-linux)
4. [Trình Soạn Thảo Nano và Vi](#trình-soạn-thảo-nano-và-vi)
5. [Script Quản Lý Squid Proxy Tự Động](#script-quản-lý-squid-proxy-tự-động)

---

## Cấu Hình Firewall & Tối Ưu VPS Yếu

> Chi tiết cấu hình firewall và tối ưu VPS cho các hệ điều hành Oracle Linux 9, CentOS 7 và Ubuntu 20.04.  
(*Nội dung đầy đủ từ file `firewall_vps_optimization.md`*)

---

## Cài Đặt aaPanel và Squid Proxy

### 1. Cài đặt aaPanel

#### CentOS / Oracle Linux
```bash
yum install -y wget
wget -O install.sh http://www.aapanel.com/script/install_6.0_en.sh
bash install.sh
```

#### Ubuntu / Debian
```bash
apt install -y wget
wget -O install.sh http://www.aapanel.com/script/install-ubuntu_6.0_en.sh
bash install.sh
```

> Sau khi cài xong sẽ có địa chỉ IP:port và tài khoản đăng nhập aaPanel.

---

### 2. Cài Squid Proxy

```bash
# Ubuntu
sudo apt install squid -y

# CentOS / Oracle
sudo yum install squid -y
```

#### Cấu hình proxy cơ bản

```bash
sudo nano /etc/squid/squid.conf
```

Thêm các dòng:
```
http_port 3128
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
acl auth_users proxy_auth REQUIRED
http_access allow auth_users
```

Tạo user:
```bash
sudo apt install apache2-utils   # Ubuntu
sudo yum install httpd-tools     # CentOS/Oracle

htpasswd -c /etc/squid/passwd proxyuser
```

Khởi động Squid:
```bash
sudo systemctl restart squid
sudo systemctl enable squid
```

Test với curl:
```bash
curl -x http://proxyuser:matkhau@IP:3128 http://google.com
```

---

### 3. Quản lý user proxy

```bash
# Thêm user
htpasswd /etc/squid/passwd newuser

# Xoá user
htpasswd -D /etc/squid/passwd olduser

# Restart Squid sau thay đổi
sudo systemctl restart squid
```

---

## Tổng Hợp Lệnh Cơ Bản Trên Linux

### 1. Kiểm tra hệ điều hành
```bash
cat /etc/os-release
hostnamectl
lsb_release -a   # Ubuntu
uname -a         # Kernel
```

### 2. Quản lý người dùng
```bash
sudo adduser tenuser
sudo usermod -aG sudo tenuser
passwd tenuser
whoami
```

### 3. Quản lý hệ thống
```bash
sudo poweroff
sudo reboot
uptime
top
htop
df -h
free -m
```

### 4. Quản lý dịch vụ (SystemD)
```bash
sudo systemctl start <service>
sudo systemctl stop <service>
sudo systemctl status <service>
```

### 5. Cập nhật hệ thống
```bash
sudo apt update && sudo apt upgrade -y       # Ubuntu
sudo yum update -y                           # CentOS 7 / Oracle
sudo dnf update -y                           # Oracle Linux 9
```

### 6. Cài phần mềm cơ bản
```bash
sudo apt install curl wget nano net-tools unzip git -y       # Ubuntu
sudo yum install curl wget nano net-tools unzip git -y       # CentOS/Oracle
```

### 7. Kiểm tra cổng & kết nối
```bash
netstat -tulnp
ss -tulnp
lsof -i :80
ping 8.8.8.8
traceroute google.com
```

### 8. Nén & giải nén
```bash
zip -r tenfile.zip folder
unzip tenfile.zip
tar -czvf file.tar.gz folder
tar -xzvf file.tar.gz
```

### 9. Quản lý tiến trình
```bash
ps aux | grep <tên>
kill -9 <PID>
```

### 10. Quản lý tệp tin & thư mục
```bash
ls -l
cd /path
mkdir newfolder
rm -rf folder
cp file1 file2
mv old new
```

### 11. Cấp quyền & sở hữu
```bash
chmod +x script.sh
chown user:user file
```

### 12. Ghi log & xem log
```bash
journalctl -xe
tail -f /var/log/syslog
tail -f /var/log/messages
```

---

## Trình Soạn Thảo Nano và Vi

### Sử dụng `nano`
- Mở file:
```bash
nano ten_file
```
- Lưu file: `Ctrl + O`, nhấn `Enter`
- Thoát: `Ctrl + X`

### Sử dụng `vi` hoặc `vim`
- Mở file:
```bash
vi ten_file
```
- Vào chế độ soạn thảo: nhấn `i`
- Lưu và thoát: nhấn `Esc`, gõ `:wq`, rồi Enter
- Thoát không lưu: `Esc`, gõ `:q!`, rồi Enter

---

## Script Quản Lý Squid Proxy Tự Động

### Đường dẫn gợi ý: `/usr/local/bin/squid_manager.sh`

### Cách sử dụng:
1. Cấp quyền chạy:
```bash
chmod +x squid_manager.sh
```

2. Chạy script:
```bash
sudo ./squid_manager.sh
```

3. Menu chức năng:
- 1. Tạo User
- 2. Xoá User
- 3. Liệt kê tất cả user
- 4. Liệt kê chi tiết user (user:pass@IP:Port)
- 5. Kiểm tra trạng thái Squid
- 6. Khởi động lại Squid
- 7. Đổi mật khẩu
- 8. Sao lưu danh sách user
- 9. Thay đổi port proxy
- 10. Xem log truy cập
- 11. Tạo user tự động (random user và pass)
- 0. Thoát

> Script tự động tạo file `user_data.txt` chứa danh sách `user:pass` để tiện xuất thông tin.

---
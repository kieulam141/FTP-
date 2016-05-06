# Cài đặt FTP Server qua vsftpd trên Centos 6.7
## Mục lục

[1.Mô hình]	(#1)

[2.Giới thiệu]	(#2)

[3.Các bước triển khai]	(#3)

[4.Triển khai chi tiết]	(#4)

[4.1.Cài đặt vsftpd trên CentOS 6.7] (#41)

[4.2.Cấu hình FTP] (#42)

[4.3.Tạo user và kiểm tra] (#43)

[5.Tham Khảo]	(#5)

<a name="1"></a>
### 1.Mô hình	
<img src="http://i.imgur.com/vMdAKlS.png" />

<a name="2"></a>
### 2.Giới thiệu
- VSFTPD (Very Secure FTP Deamon) là 1 trong những FTP server được sử dụng nhiều nhất trong linux vì nó được gần như là bảo mật và nhanh nhất.
- 1 số đặc tính riêng biệt của VSFTPD:
<ul>
	<li>Cấu hình users ảo.</li>
	<li>Cấu hình IP ảo.</li>
	<li>Hoạt động độc lập.</li>
	<li>Cấu hình theo từng user.</li>
	<li>Có thể giới hạn băng thông.</li>
	<li>Cấu hình theo IP nguồn.</li>
	<li>Hỗ trợ IPv6.</li>
	<li>Hỗ trợ mã hóa bằng cách tích hợp SSL.</li>
</ul>
- Trong bài lab này tôi sử dụng 3 máy ảo:1 FTP server, 2 FTP client
```sh
-------------------------------------------------
VSFTPD Server
-------------------------------------------------
IP adress		:		192.168.10.4
Hostname		:		FTPServer
OS				:		CentOS 6.7
Cài đặt gói		:		vsftpd
```
```sh
-------------------------------------------------
FTP Client1
-------------------------------------------------
IP adress		:		192.168.10.2
Hostname		:		Centos
OS				:		CentOS 6.7
Cài đặt gói		:		ftp
```
```sh
-------------------------------------------------
FTP Client2
-------------------------------------------------
IP adress		:		192.168.10.101
Hostname		:		win7
OS				:		windows 7
```

<a name="3"></a>
### 3.Các bước triển khai
- Cài đặt vsftpd trên CentOS 6.7
- Cấu hình FTP
- Tạo user và kiểm tra

<a name="4"></a>
### 4.Triển khai chi tiết

<a name="41"></a>
#### 4.1.Cài đặt vsftpd trên CentOS 6.7
- Bước 1 : Cài đặt EPEL Repository cho YUM
```sh
sudo yum install epel-release
```
- Bước 2 : Cài đặt vsftpd bằng YUM trên CentOS 6.7
```sh
sudo yum install -y vsftpd
```
- Sau khi cài đặt vsftpd thành công, bạn sẽ thấy file “/etc/vsftpd/vsftpd.conf” là nơi chứa thông tin cấu hình cho vsftpd.
- Các lệnh của vsftpd :
```sh
service vsftpd start #khởi động vsftpd
service vsftpd restart #khởi động lại vsftpd
service vsftpd stop #stop vsftpd
```

<a name="42"></a>
#### 4.2.Cấu hình vsftpd
- Sửa file cấu hình vsftpd
```sh
vi /etc/vsftpd/vsftpd.conf
```
```sh
# line 12: no anonymous
anonymous_enable=NO

# line 81,82: bo comment ( allow ascii mode )
ascii_upload_enable=YES
ascii_download_enable=YES

# line 96,97: bo comment ( enable chroot )
chroot_local_user=YES
chroot_list_enable=YES

# line 99: bo comment ( specify chroot list )
chroot_list_file=/etc/vsftpd/chroot_list

# line 105: bo comment
ls_recurse_enable=YES

## You may fully customise the login banner string:			#them banner
ftpd_banner=Welcome to LamTLU FTP service.

# Thêm vào cuối file
# use localtime
use_localtime=YES
```
- Lưu lại và thoát, xong thì khởi động dịch vụ và cho phép khởi động cùng CentOS
```sh
 service vsftpd start
 chkconfig vsftpd on
``` 

<a name="4.3"></a>
#### 4.3.Tạo user và kiểm tra 
- Tạo folder nơi bạn muốn lưu trữ dữ liệu FTP.
`mkdir /ftp`
- Tạo user với thư mục đường dẫn /ftp
```sh
useradd -d /ftp/CentOS CentOS
passwd CentOS
useradd -d /ftp/win7 win7	
passwd win7
```
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/wQqJ1Ns.png" />

- Tạo 1 số file cho cả 2 users
```sh
touch /ftp/CentOS/test
touch /ftp/CentOS/test1
touch /ftp/win7/test
touch /ftp/win7/test1
```
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/YoWbCM4.png" />

- Tắt Selinux và firewall
```sh
setenforce 0
service iptables stop
```
- Kiểm tra trên 2 client

##### a.CentOS
- Trước khi kiểm tra, ta tiến hành cài đặt gói ftp cho client.
`yum -y install ftp`
- Kết nối tới ftp server
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/aUUrekP.png" />

- Test 1 số câu lệnh
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/wsPLPkL.png" />

##### b.win7
- Bạn truy cập địa chỉ cùa ftp server: `ftp://192.168.10.4`. Vào bằng web thì bạn chỉ có thêm xem hoặc download file mà không thể thêm hoặc chỉnh sửa file
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/wfwzBEQ.png" />

- Giải quyết 2 vấn đề trên bạn có thể dùng tool có sẵn như Filezella, Winscp.
- Ở đây tôi sẽ ví dụ tool winscp
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/lg6XYqx.png" />

- Sau khi truy cập được vào ftp server, bạn có thể thêm hoặc chỉnh sửa file nếu có quyền.
<img src="http://img.prntscr.com/img?url=http://i.imgur.com/ufwpmW4.png" />

### 5.Tham Khảo
- http://www.krizna.com/centos/how-to-configure-ftp-server-on-centos-6/
- http://www.server-world.info/en/note?os=CentOS_6&p=ftp&f=1

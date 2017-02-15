#LDAP - Lightweight Directory Access Protocol

##1. Tổng quan
LDAP là giao thức truy cập nhanh các dịch vụ thư mục
port mặc định 389
LDAP nằm ở tầng ứng dụng trong mô hình OSI, chạy trên TCP/IP
LDAP gồm 3 phiên bản: LDAPv1, LDAPv2, LDAPv3. LDAPv3 được thiết kế để khắc phục các hạn chế trước đó và có thêm các tính năng mới:
- Xác thực và dịch vụ dữ liệu bảo mật mạnh mẽ thông qua SASL
- Chứng thực bảo mật dữ liệu thông qua TSL (SSL)
- Quốc tế hóa thông qua sử dụng Unicode
- Referrals and Continuations (chuyển tiếp)
- Schema Discovery (khám phá sơ đồ).
- Mở rộng (điều khiển, và nhiều hoạt động mở rộng khác)
LDAP là giao thức truy cập vì vậy nó theo mô hình dạng cây(Directory Information Tree).
<img = https://github.com/trangnth/LDAP/blob/master/img/1.png >

##2. Phương thức hoạt động của LDAP
LDAP hoạt động theo mô hình client-server. Một hoặc nhiều LDAP server chứa thông tin về cây thư mục (Directory Information Tree – DIT). Client kết nối đến server và gửi yêu cầu. Server phản hồi bằng chính nó hoặc trỏ tới LDAP server khác để client lấy thông tin. Trình tự khi có kết nối với LDAP:

- Connect (kết nối với LDAP): client mở kết nối tới LDAP server
- Bind (kiểu kết nối: nặc danh hoặc đăng nhập xác thực): client gửi thông tin xác thực
- Search (tìm kiếm): client gửi yêu cầu tìm kiếm
- Interpret search (xử lý tìm kiếm): server thực hiện xử lý tìm kiếm
- Result (kết quả): server trả lại kết quả cho client
- Unbind: client gửi yêu cầu đóng kết nối tới server
- Close connection (đóng kết nối): đóng kết nối từ server

##3. Database backend của LDAP
Slapd là một “LDAP directory server” có thể chạy trên nhiều platform khác nhau. Bạn có thể sử dụng nó để cung cấp những dịch vụ của riêng mình. Những tính năng mà slapd cung cấp:

- LDAPv3: slapd hỗ trợ LDAP cả IPv4, IPv6 và Unix IPC.
- Simple Authentication and Security Layer: slapd hỗ trợ mạnh mẽ chứng thực và bảo mật dữ liệu dịch vụ bằng SASL
- Transport Layer Security: slapd hỗ trợ sử dụng TLS hay SSL.

Hai database mà SLAPD sử dụng để lưu trữ dữ liệu hiện tại là bdb và hdb. 

BDB sử dụng Oracle Berkeley DB để lưu trữ dữ liệu. Nó được đề nghị sử dụng làm database backend chính cho SLAPD thông thường. 

HDB là cũng tương tự như BDB nhưng nó sử dụng database phân cấp nên hỗ trợ cơ sỡ dữ liệu dạng cây. HDB thường được mặc định cấu hình trong SLAPD hiện nay.

##4. Lưu trữ thông tin của LDAP

Ldif (LDAP Data Interchange Format) là một chuẩn định dang file text lưu trữ thông tin cấu hình LDAP và nội dung thư mục. File LDIF thường dùng để import dữ liệu mới vào trong directory hoặc thay đổi dữ liệu đã có. Dữ liệu trong file LDIF phải tuân theo quy luật có trong schema của LDAP.

<img = 2>

Schema là loại dữ liệu được định nghĩa từ trước. Mọi thành phần được thêm vào hoặc thay đổi trong directory của bạn sẽ được kiểm tra lại trong schema để đảm bảo chính xác.

Một số các thuộc tính cơ bản trong Ldif:

|STT|Tên    |Mô tả   |
|---|-------|--------|
|1|dn|Distinguished Name : tên gọi phân biệt|
|2|c|country – 2 kí tự viết tắt tên của một nước|
|3|o|organization – tổ chức|
|4|ou|organization unit – đơn vị tổ chức|
|5|objectClass|Mỗi giá trị objectClass hoạt động như một khuôn mẫu cho các dữ liệu được lưu giữ trong một entry. Nó định nghĩa một bộ các thuộc tính phải được trình bày trong entry (Ví dụ: entry này có giá trị của thuộc tính objectClass là eperson, mà trong eperson có quy định cần có các thuộc tính là tên, email, uid ,…thì entry này sẽ có các thuộc tính đó)|
|6|givenName|Tên|
|7|uid|id người dùng|
|8|cn|common name – tên thường gọi|
|9|telephoneNumber|số điện thoại|
|10|sn|surname – họ|
|11|userPassword|mật khẩu người dùng|
|12|mail|địa chỉ email|
|13|facsimileTelephoneNumber|số phách|
|14|createTimestamp|thời gian tạo ra entry này|
|15|creatorsName|tên người tạo ra entry này|
|16|pwdChangedTime|thời gian thay đổi mật khẩu|
|17|entryUUID|id của entry|

##5. Cài đặt LDAP
###5.1 Trên server
####Cài đặt LDAP
```
sudo apt-get update
sudo apt-get install slapd ldap-utils
```

Chọn yes

<img = 3>

Sau đó chạy lệnh sau để cấu hình lại:
```
sudo dpkg-reconfigure slapd
```
Trả lời một số câu hỏi sau:
- Omit OpenLDAP server configuration? **No**
- DNS domain name? Ví dụ domain name là **ved**
<img = 4 >
- Organization name?
<img = 5>
- Administrator password?
- Database backend? **HDB**
- Remove the database when slapd is purged? **No**
- Move old database? **Yes**
- Allow LDAPv2 protocol? **No**

#### Cài phpLDAPadmin để quản lý LDAP với giao diện web
```
sudo apt-get install phpldapadmin
```

#### Cấu hình phpLDAPadmin
Mở file `/etc/phpldapadmin/config.php`

Trong file này, tìm và sửa tham số sau dùng để truy cập vào web interface
```
$ servers-> setValue ( 'server', 'host', ' server_domain_name_or_IP ');
```

Tiếp theo, cấu hình domain đã chọn cho LDAP server, trong ví dụ trên là ved (nếu domain là ved.com thì sửa là `dc=ved,dc=com`)
```
$ servers-> setValue ( 'server', 'base', array ( 'dc = ved '));
```
```
$ servers-> setValue ( 'login', 'bind_id', 'cn = admin, dc = ved ');
```
Tìm, uncommenting và sửa `false` thành `true` ở dòng dưới đây
```
$ Configure> custom-> xuất hiện [ 'hide_template_warning'] = true ;
```
Cuối cùng, save file và đăng nhập vào web interface theo link: `domain_name_or_IP_address/phpldapadmin` 

Trong trường hợp gặp lỗi **Error trying to get a non-existent value (appearance, password_hash)**, mở tập tin  
```
nano /usr/share/phpldapadmin/lib/TemplateRender.php
```  
dòng 2469 sửa `password_hash` thành `password_hash_custom`

###5.2 Trên client
####Install Client Packages
Chạy lệnh
```
sudo apt-get update
sudo apt-get install libpam-ldap nscd      *
```
Quá trình cài đặt bắt đầu, bạn phải hoàn thành một số câu sau:
- LDAP server Uniform Resource Identifier: **ldap://LDAP-server-IP-Address/** *(Thay "ldapi:///" bằng "ldap://" )*
- Tiếp theo, cần cấu hình đúng với file `/etc/phpldapadmin/config.php file` trên LDAP server. Ở đây là `dc=ved`

<img = 0>

- LDAP version to use: **3**
- Make local root Database admin: **Yes**
- Does the LDAP database require login? **No**
- LDAP account for root: **cn=admin,dc=test,dc=com**
- LDAP root account password: **Your-LDAP-root-password**

Nếu muốn thay đổi hoặc sai bất cứ bước nào thì chạy lệnh sau để làm lại:
```
sudo dpkg-reconfigure ldap-auth-config
```

####Configure Client Software

Sửa file `/etc/nsswitch.conf` 
```
passwd:          compat ldap
group:           compat ldap
shadow:          compat ldap
```

Mở tập tin /etc/pam.d/common-password sửa dòng 26, bỏ `use_authtok` đi
```
password [success=1 user_unknown=ignore default=die] pam_ldap.so try_first_pass
```

Mở tập tin /etc/pam.d/common-session. Thêm vào dòng cuối cùng nếu muốn mỗi user khi đăng nhập sẽ tự động tạo một thư mục user trong home
`session required pam_mkhomedir.so skel=/etc/skel umask=0022`   *

Restart lại service `sudo /etc/init.d/nscd restart`   *

Bây giờ có thể login vào LDAP client theo những user đã được tạo trên LDAP server


##Tham Khảo: 

https://viblo.asia/tran.van.cuong/posts/zoZVRgdZMmg5

https://www.digitalocean.com/community/tutorials/how-to-authenticate-client-computers-using-ldap-on-an-ubuntu-12-04-vps

https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-a-basic-ldap-server-on-an-ubuntu-12-04-vps



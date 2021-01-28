# Install LEMP Stack CentOS7 Wordpress

#### Các Service và version yêu cầu 


- Nginx v1.16.1 ++ [repo nginx](http://nginx.org/packages/centos/7/x86_64/)

- Mariadb-server v10.1 ++ [repo mariadb sv](https://mariadb.org/download/#mariadb-repositories)

- php-fpm v7.3 ++ [repo php-fpm](http://rpms.remirepo.net/enterprise/remi-release-7.rpm)

- wordpress newversion [down wordpress](http://wordpress.org/latest.tar.gz)

## I. Install Nginx

- Cài Nginx v1.16.1 với repo có sẵn epel-release

```sudo yum install nginx```

```nginx -v ```  ## kiểm tra version vừa cài

- Bật service và cho phép cổng firewall

```systemctl start nginx```

```sudo firewall-cmd --permanent --add-service=http```

```sudo firewall-cmd --permanent --add-service=https```

```sudo firewall-cmd --reload```

### 1. Tạo VitualHost chạy HTTP

Tạo file .conf trong `/etc/nginx/conf.d/`

`vim /etc/nginx/conf.d/test.com.conf`

File cấu hình có các tham số sau:

```
server{
        listen 80;
        server_name test.com;
        root /home/www/test.com;
        error_log /var/log/nginx/test.com_error.log error;
        access_log /var/log/nginx/test.com_access.log main
    location /{
        index index.html index.php;
     }
}
```
Tạo thư mục để file .html theo đường dẫn `/home/www/test.com`

`mkdir -p /home/www/test.com`

Tạo file .html để thử chạy

`vim /home/www/test.com/index.html`




## II. Install Mariadb

- Cài Mariadb v10.5 với repo [link](https://mariadb.org/download/#mariadb-repositories) có hướng dẫn

```sudo yum install MariaDB-server MariaDB-client```


```mariadb -v ``` ## kiểm tra version vừa cài

- Bật service và install bảo mật cho database

```systemctl start mariadb```

```mysql_secure_installation  #set passwd root cho database```

### 1. Tạo DataBase

Truy cập vào database với quyền root, với password vừa tạo ở bước trên 

`mariadb -u root -p`

Tạo database và user:

```
# Tạo database
create database wordpress;

# Tạo user
create user 'wordpress'@localhost identified by 'password';

# Cấp quyền cho user vào database
grant all privileges on *.* to 'wordpress'@localhost;

# Xác nhận
flush privileges;
```

Kiểm tra database đã tạo:

```
SELECT host, user, password FROM mysql.user;

SHOW GRANTS FOR 'user'@localhost;
```

## III. Install PHP

- Cài php-fpm v7.4 với repo có các version đầy đủ

```sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm```

*Enabled version mà mình muốn cài (Xem repo version tại ```cd /etc/yum.repos.d/```)*

```yum-config-manager --enable remi-php74 ```

```sudo yum install php php-mysqlnd php-fpm```

```php-fpm -v ```   ## kiểm tra version vừa cài

### 1. Cấu hình

Sửa file cấu hình php-fpm `vim /etc/php-fpm.d/www.conf` 

```
[www]
listen = 127.0.0.1:9000
listen.allowed_clients = 127.0.0.1
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
user = nginx
group = nginx
pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
slowlog = /var/log/php-fpm/www-slow.log
php_admin_value[error_log] = /var/log/php-fpm/www-error.log
php_admin_flag[log_errors] = on
php_value[session.save_handler] = files
php_value[session.save_path] = /var/lib/php/session
```

Sửa file cấu hình VitualHost chạy được file /php qua php-fpm

Thêm cấu hình sau: `vim /etc/nginx/conf.d/test.com.conf`

```
location ~ \.php {
#   fastcgi_pass unix:/var/run/php_fpm.sock;
    fastcgi_pass 127.0.0.1:9000;
    include        /etc/nginx/fastcgi_params;
    fastcgi_param   SCRIPT_FILENAME $document_root/$fastcgi_script_name;
}
```

## IV. Install Wordpress

### 1. Cài Wordpress

Cài Wordpress version mới nhất

`cd /tmp && wget http://wordpress.org/latest.tar.gz`

Giải nén file vừa cài vào thư mục chứa

`tar -xvzf latest.tar.gz -C /home/www/test.com`

Phân quyền cho wordpress

`chown -R nginx /home/www/test.com/wordpress`

Copy file conf mẫu

`cp /home/www/test.com/wordpress/wp-config-sample.php /home/www/test.com/wordpress/wp-config.php`

Sửa file conf vừa copy thêm Database

`vim /home/www/test.com/wordpress/wp-config.php`

Thêm các dòng:

```
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );
/** MySQL database username */
define( 'DB_USER', 'wordpress' );
/** MySQL database password */
define( 'DB_PASSWORD', 'password' );
/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
```

Kết quả khi nhập domain VitualHost


<img src=https://kinsta.com/wp-content/uploads/2018/12/wordpress-install-language.png>











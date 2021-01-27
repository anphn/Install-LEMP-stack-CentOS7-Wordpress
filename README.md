# Install LEMP Stack CentOS7 Wordpress

### Các Service và version yêu cầu 


- Nginx v1.16.1 ++ [repo nginx](http://nginx.org/packages/centos/7/x86_64/)

- Mariadb-server v10.1 ++ [repo mariadb sv](https://mariadb.org/download/#mariadb-repositories)

- php-fpm v7.3 ++ [repo php-fpm](http://rpms.remirepo.net/enterprise/remi-release-7.rpm)

- wordpress newversion [down wordpress](http://wordpress.org/latest.tar.gz)

## 1. Install Nginx

- Cài Nginx v1.16.1 với repo có sẵn epel-release

```sudo yum install nginx```

```nginx -v ```  ## kiểm tra version vừa cài

- Bật service và cho phép cổng firewall

```systemctl start nginx```

```sudo firewall-cmd --permanent --add-service=http```

```sudo firewall-cmd --permanent --add-service=https```

```sudo firewall-cmd --reload```


## 2. Install Mariadb

- Cài Mariadb v10.5 với repo [link](https://mariadb.org/download/#mariadb-repositories) có hướng dẫn

```sudo yum install MariaDB-server MariaDB-client```


```mariadb -v ``` ## kiểm tra version vừa cài

- Bật service và install bảo mật cho database

```systemctl start mariadb```

```mysql_secure_installation  #set passwd root cho database```


## 3. Install PHP

- Cài php-fpm v7.4 với repo có các version đầy đủ

```sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm```

*Enabled version mà mình muốn cài (Xem repo version tại ```cd /etc/yum.repos.d/```)*

```yum-config-manager --enable remi-php74 ```

```sudo yum install php php-mysqlnd php-fpm```

```php-fpm -v ```   ## kiểm tra version vừa cài


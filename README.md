Menggunakan SSL Cloudflare untuk Mengamankan WordPress
===============

Repositori untuk melakukan instalasi dan konfigurasi wordpress dengan menggunakan keamanan SSL dari Cloudflare

## Task
Instalasi dan Konfigurasi:
* Web Server NGINX
* Database Server MariaDB 10.1.44
* PHP 7.2

Ketentuan pengerjaan:
* Menggunakan VPS dengan OS centos7
* Menggunakan subdomain dari domain utama (wp.domain.tld) 
* Menggunakan mangement DNS di Cloudflare
* SSL yang digunakan ialah SSL dari Cloudflare

## Langkah 1 Instalasi dan Konfigurasi LEMP
#### Step 1: Instalasi Web Server Nginx

- Untuk melakukan instalasi Nginx yang pertama adalah melakukan remote ke IP VPS dengan menggunakan SSH


```$ ssh root@ipaddress```

- Setelah berhasil login ke VPS, lakukan pembaharuan paket/repository dari system operasi Centos7 dengan perintah sebagai berikut:


```#yum -y update```

- Lakukan instalasi paket epel-release sebelum melakukan instalasi paket pada OS centos7 dengan memasukan perintah berikut:

```#  yum install epel-release -y```

- Setelah paket epel-release selesai diinstall selanjutnya ialah instalasi webserver Nginx dan biasanya instalasi ini memakan waktu 10 sd 20 second, untuk melakukan instalasi Nginx masukan perintah berikut:

```# yum install nginx -y```

- Selanjutnya lakukan enable direktori dan file webserver Nginx, berikut perintahnya:

```# systemctl enable nginx```

- Setelah direktori dan file webserver Nginx dienable, jalankan service Nginx dengan perintah berikut:

```# systemctl start nginx```

- Apabila paket webserver Nginx telah selesai diinstal pastikan service Nginx berjalan dengan status Running.

```
# systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2020-03-08 16:33:22 WIB; 1h 29min ago
  Process: 10899 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 10896 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 10895 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 10901 (nginx)
   CGroup: /system.slice/nginx.service
           ├─10901 nginx: master process /usr/sbin/nginx
           └─10902 nginx: worker process
```

#### Step 2: Instalasi Database Server MariaDB 10.1.44

- Setelah webserver Nginx telah diinstall selanjutnya lakukan instalasi database server mariaDB 10.1.44 dan langkah pertama ialah menambahkan repository untuk mariaDB.

```# vi /etc/yum.repos.d/mariadb.repo```

- Masukan perintah berikut:

```
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

- Setelah menambahkan repo mariaDB, lakukan instalasi mariaDB dengan perintah berikut:

```# yum install mariadb-server```

- Selanjutnya lakukan enable direktori dan file database mariaDB, berikut perintahnya:

```# systemctl enable mariadb```

- Setelah direktori dan file database mariaDB dienable, jalankan service mariaDB dengan perintah berikut:

```# systemctl start mariadb```

- Apabila paket database server telah selesai diinstall pastikan service mariaDB berjalan dengan status Running.

```
# systemctl status mariadb
● mariadb.service - MariaDB 10.1.44 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/mariadb.service.d
           └─migrated-from-my.cnf-settings.conf
   Active: active (running) since Sat 2020-03-07 20:09:38 WIB; 22h ago
     Docs: man:mysqld(8)
           https://mariadb.com/kb/en/library/systemd/
  Process: 29925 ExecStartPost=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)
  Process: 29885 ExecStartPre=/bin/sh -c [ ! -e /usr/bin/galera_recovery ] && VAR= ||   VAR=`/usr/bin/galera_recovery`; [ $? -eq 0 ]   && systemctl set-environment _WSREP_START_POSITION=$VAR || exit 1 (code=exited, status=0/SUCCESS)
  Process: 29883 ExecStartPre=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)
 Main PID: 29897 (mysqld)
   Status: "Taking your SQL requests now..."
   CGroup: /system.slice/mariadb.service
           └─29897 /usr/sbin/mysqld
```

#### Step 3: Instalasi PHP 7.2

- Setelah melakukan instalasi database server selanjutnya ialah install PHP versi 7.2 dan langkah pertama adalah mengambil dan menginstall repository untuk PHP versi 7 keatas.

```# yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm```

- Setelah repository untuk PHP versi 7 diinstall, langkah selanjutnya adalah enable remi/repository untuk PHP 7.2

```# yum-config-manager --enable remi-php72```

- Selanjutnya lakukan instalasi PHP 7.2 beserta paket php yang akan digunakan
 
```# yum install php php-common php-opcache php-mcrypt php-cli php-gd php-curl php-mysqlnd```
 

- Lihat versi dan pastikan service PHP berjalan

```
# php -v 
PHP 7.2.28 (cli) (built: Feb 18 2020 11:07:04) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.2.28, Copyright (c) 1999-2018, by Zend Technologies
```

## Langkah 2 Konfigurasi CMS wordpress
#### Step 1: Membuat folder untuk meletakan root folder CMS wordpress

- Buat dan pilih folder untuk mendownload dan menyimpan folder dan file CMS Wordperss 

```
# cd /home
home# mkdir A
home# cd A/
home/A# 
```

#### Step 2: Download dan Extract File CMS Wordpress

- Setelah folder untuk CMS wordpress selesai dibuat, selanjutnya adalah melakukan instalasi wget dan unzip .

```# yum install wget unzip -y```

- Unduh File CMS wordpress

```# wget http://wordpress.org/latest.tar.gz```

- Extract File CMS wordpress

```# tar xf latest.tar.gz```

- Beri hak akses dan hak kepemilikan untuk folder A

```
# chown -R nginx:nginx /home/A/
# chmod -R 755 /home/A/
```

#### Step 3: Konfigurasi File PHP dan menetukan PHP socket

```# vi /etc/php-fpm.d/www.conf```

- Rubah user dan grub menjadi nginx dan buat php socket

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/user.png" width="500">


#### Step 4: Konfigurasi Server Blok untuk CMS Wordpress

- Buat file baru dengan nama _wp.conf_

```# vi /etc/nginx/conf.d/wp.conf```

- Di dalam file _wp.conf_ masukan kode berikut

```
server {
    listen 80;
    listen [::]:80;
    server_name wp.padiakse.my.id www.wp.padiakse.my.id;
    return 302 https://$server_name$request_uri;
}
server {
    # SSL configuration
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    ssl        on;
    ssl_certificate         /etc/ssl/certs/cert.pem;
    ssl_certificate_key     /etc/ssl/private/key.pem;
    server_name wp.padiakse.my.id www.wp.padiakse.my.id;
    root /home/A/wordpress/;
    index index.html index.php ;
    error_log /var/log/nginx/wppadiakse-error.log;
    access_log /var/log/nginx/wppadiakse-access.log;
    location / {
           try_files $uri $uri/ /index.php?$args;
    }
location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
        # With php5-cgi alone:
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_script_name;
        include fastcgi_params;
}
}
```

- Save dan close

#### Step 5: Buat database baru dan edit wp-config

- Pertama buat database baru pada mariaDB

```# mysql -u root -p```

- Setelah masuk tambahkan database baru dengan nama _wp_ / **isikan bebas sesuai kebutuhan**

```MariaDB [(none)]> create database nama_database;```

- Masukan user dan password untuk database _wp_

```MariaDB [(none)]> GRANT ALL ON nama_database.* TO 'isikan_user'@'localhost' IDENTIFIED BY 'isikan_password';```

- Beri hak akses untuk database yang dipilih

```MariaDB [(none)]> FLUSH PRIVILEGES;```

- Exit untuk keluar dari MariaDB

```MariaDB [(none)]> exit;```

- Kemudian masuk ke folder wordpress

```# cd /home/A/wordpress```

- Salin wp-config-sample.php dangan nama baru wp-config.php

```# cp wp-config-sample.php wp-config.php```

- Edit *wp-config.php*

```# vi wp-config.php```

- Rubah isi file wp-config.php berikut

```
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'isikan_nama_database' );
/** MySQL database username */
define( 'DB_USER', 'isikan_user_database' );
/** MySQL database password */
define( 'DB_PASSWORD', 'isikan_password_database' );
```

- Simpan dan keluar.


## Langkah 3 Konfigurasi SSL di Cloudflare
#### Step 1: login, management DNS dan SSL dari Cloudflare

- Login akun Cloudflare

```https://dash.cloudflare.com/login```

- Masukan email dan password

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/login%20cf.png" width="500">

- Setelah login silakan klik new site dan masukan root domain (domain.tld) nantinya setelah memasukan domain untuk site, kita diminta untuk merubah nameserver untuk domain yang dimasukan dan untuk merubah nameserver silakan menghubungi pihak penyedia domain dan apabila site telah aktif maka akan ada tampilan berikut:

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/pilih%20site%20aktif.png" width="500">

- Setelah itu klik site yang aktif kemudian pilih submenu DNS > add record 

```
  Masukan type: A
  Name:domain.tld/sub.domain.tld, 
  IPv4 address: (IP VPS), 
  TTL: (isi bebas) 
  Terakhir klik save.
```

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/dns%20add.png" width="500">

- Apabila domain telah resolve selanjutnya konfigurasi SSL dengan memilih sub menu SSL/TLS dan kemudian Klik menu Origin Server > pilih Create Sertificate.

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/pilihos.png" width="500">

- Pilih tipe sertifikat kemudian masukan hostname dan pilih durasi SSL yang terakhir klik next

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/tes1.png" width="500">

- Setelah klik next akan disajikan isi file _Origin Certificate_ dan isi file _Private key_ salin terlebih dahulu isi file tersebut kemudian klik Ok

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/klikok.png" width="500">

- Apabila isi file _Origin Certificate_ dan isi file _Private key_ telah disalin, selanjutnya buat file berikut.

```
# vi /etc/ssl/certs/cert.pem (Isikan isi file _Origin Certificate_)
# vi /etc/ssl/private/key.pem (Isikan isi file _Private key_)
```

- Apabila sudah keluar dan simpan kedua file tersebut dan selanjutnya pastikan SSL sudah ditambahkan untuk mengeceknya silakan masuk pada menu SSL > Origin Server.

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/ssl%20.png" width="500">

- Kunjungi Website yang telah terbuat dan pastikan telah menggunakan SSL dari Cloudflare.

<img src="https://manan.s3-id-jkt-1.kilatstorage.id/gambar/ck1.png" width="500">

*Sekian dan Terima Kasih*

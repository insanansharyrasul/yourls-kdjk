# Project Hosting Yourls [Kelompok /Paralel 1]

# Yourls "The de facto standard self-hosted URL shortener"

## Deskripsi Aplikasi

[Tulis]

## Instalasi

1. Siapkan VPS / VM (Bisa dari Azure, AWS, GCP, Hetzner, Hostinger, dan sejenisnya) dan Domain (pointing ke IP VM)
2. Connect ke server  (dalam case ini VM menggunakan Azure dan OS Ubuntu, dan SSH)
```
ssh kdjkroot@20.78.34.223
```
3. Update
```
apt-get update && apt-get upgrade -y
```
4. Install Git dan Nano (jika belum ada)
```
apt-get install git nano -y
```
5. Install NGINX (untuk webserver dan reverse proxy)
```
apt-get install nginx -y
systemctl start nginx
systemctl enable nginx
```
6. Install MariaDB
```
apt-get install mariadb-server -y
systemctl start mariadb
systemctl enable mariadb
```
7. Install PHP and modul - modulnya (Yourls menggunakan PHP)
```
apt-get install php php-fpm php-xml php-pear php-cli php-zip php-json php-mysql  php-gd php-mbstring php-curl php-bcmath -y
```
8. SetUp MariaDB dan buat database untuk Yourls
```
mysql_secure_installation

mysql -u root -p

CREATE DATABASE yourlsdb DEFAULT CHARACTER SET UTF8 COLLATE utf8_unicode_ci;
CREATE USER 'yourlsuser'@'localhost' IDENTIFIED BY 'SomePasswordKuat';
GRANT ALL PRIVILEGES ON yourlsdb.* TO 'yourlsuser'@'localhost' IDENTIFIED BY 'SomePasswordKuat' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```
9. Clone Yourls ke direktori 
```
cd /var/www/html

rm index.html
rm index.nginx-debian.html

git clone https://github.com/YOURLS/YOURLS.git .
```
10. Buat, dan ubah config.php
```
cp user/config-sample.php user/config.php

nano user/config.php
```
11. Edit user/config.php
```
define( 'YOURLS_DB_USER', 'yourlsuser' );

define( 'YOURLS_DB_PASS', 'StrongPassword' );

define( 'YOURLS_DB_NAME', 'yourlsdb' );

define( 'YOURLS_SITE', 'https://akupendekkin.my.id' );

define( 'YOURLS_COOKIEKEY', 'kdjkkelompok4randomaje' );

define('YOURLS_ADMIN_SSL', true);

$yourls_user_passwords = array(
'admin' => 'PasswordKuat',
);
```
12. Update permission folder
```
chown -R www-data:www-data /var/www/html
chmod -R 775 /var/www/html
```
13. Aktifkan Firewall, buka untuk HTTP, HTTPS dan SSH saja
```
ufw allow http
ufw allow https
ufw allow ssh
```
14. Setting NGINX
```
nano /etc/nginx/sites-available/yourls.conf
```
Isi dengan ini, sesuaikan lokasi projek, server_name dan versi php:
```
server {

  listen 80 default_server;
  listen [::]:80 default_server;

  server_name akupendekkin.my.id;

  root /var/www/html;

  location / {
  try_files $uri $uri/ /yourls-loader.php$is_args$args;
  }

  location ~ \.php$ {
    include fastcgi.conf;
    fastcgi_index index.php;
    fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
  }

}
```
Ubah isi dari default nginx, hilangkan default_server
```
nano /etc/nginx/sites-available/default

server {
        listen 80; #hilangkan default_server
        listen [::]:80; #hilangkan default_server
        .
        .
        .
```

Lakukan symlink dari sites-available ke sites-enabled, lalu coba restart nginx
```
nginx -t
ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/yourls.conf /etc/nginx/sites-enabled/
systemctl restart nginx
```
15. Setting SSL untuk domain
```
apt-get install certbot python3-certbot-nginx -y
certbot --nginx -d akupendekkin.my.id
```
maka file conf NGINX yang berasosiasi dengan domain akan berubah otomatis untuk mengakomodir cert dari SSL

16. Uji Coba
```
https://akupendekkin.my.id/admin/install #ikuti installasi

https://example.com/admin/index.php #login dengan username dan password admin
```

## Cara Pemakaian

1. Akses Halaman Web  
   [Instruksi akses aplikasi]

2. Proses Setup Awal  
   [Instruksi setup]

3. Login Sebagai Admin  
   [Instruksi login]

4. Mengunggah Data / Konten  
   [Instruksi upload / penggunaan fitur]

5. Manajemen Konten  
   [Instruksi manajemen galeri/konten]

**Tips Penggunaaan**:

1. [Tips 1]
2. [Tips 2]
3. [Tips 3]
4. [Tips 4]
5. [Tips 5]
6. [Tips 6]

## Pembahasan

## Kelebihan [Nama Aplikasi]

## Kekurangan [Nama Aplikasi]

## Perbandingan [Nama Aplikasi] dengan Aplikasi Web Sejenis

## Kesimpulan

## Referensi

[Daftar referensi]

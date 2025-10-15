# Project Hosting YOURLS [Kelompok /Paralel 1]

# Yourls "The de facto standard self-hosted URL shortener"

## Deskripsi Aplikasi

YOURLS adalah kumpulan skrip PHP yang kuat yang memungkinkan Anda untuk mengelola Pemendek URL Sendiri di server Anda. 

Dengan YOURLS, Anda memiliki kendali penuh atas tautan Anda, statistik terperinci, dukungan plugin, dan fitur lainnya, semuanya dikemas dalam paket gratis dan sumber terbuka.

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

https://akupendekkin.my.id/admin/index.php #login dengan username dan password admin
```

## Cara Pemakaian

1. Akses Halaman Web  

Kunjungi https://akupendekkin.my.id/admin/ 

Masukkan password dan username yang sudah ditentukan saat instalasi untuk menggunakannya

![](assets/ss1.png)

2. Membuat Link Pendek

Setelah login, akan tampilan seperti pada gambar. Cukup masukkan link yang ingin dipendekkan ke dalam "**Enter the URL**" dan ubah menjadi link yang bebas pada bagian **Custom Short URL** setelah anda yakin, baru klik **Shorten The URL**.

![](assets/ss2.png)

3. Mengunjungi Link Pendek

Setelah link berhasil dibuat, link akan bisa langsung dikunjungi dengan format yang ada pada bagian **Short URL**. Cukup kunjungi 

```
https://akupendekkin.my.id/{short_url_anda}
```

Untuk mengunjungi link nya. YOURLS akan langsung *redirect* ke address yang sebenarnya.

**Tips Penggunaaan**:

1. Manfaatkan plugin

Banyak plugin berguna untuk menambah fungsi YOURLS, misalnya:

- Plugin analitik lebih mendetail
- Plugin anti-spam
- Plugin untuk auto-hash URL

Simpan plugin hanya dari sumber tepercaya (resmi atau GitHub YOURLS).

Cek plugin resmi: https://yourls.org/plugins

2. Gunakan API untuk otomatisasi

YOURLS punya REST API, contohnya:
```
curl -d "url=https://contoh.com&keyword=mykeyword&format=simple&signature=APIKEY&action=shorturl" https://linkku.id/yourls-api.php
```

Cocok untuk integrasi dengan skrip, bot Telegram, form, atau CMS.

Gunakan signature (API key) untuk autentikasi agar tidak sembarang orang bisa membuat shortlink.

3. Kelola link dengan rapi

- Gunakan format penamaan keyword yang konsisten (misalnya event-2025 atau promo-okt25).
- Hapus link rusak atau tidak aktif secara berkala.
- Gunakan kategori/tags lewat plugin jika butuh manajemen lebih lanjut.

## Pembahasan

## Kelebihan [Nama Aplikasi]

## Kekurangan [Nama Aplikasi]

## Perbandingan [Nama Aplikasi] dengan Aplikasi Web Sejenis

## Kesimpulan

## Referensi

[Daftar referensi]

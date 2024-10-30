# cara install phpmyadmin ubuntu24.04
Berikut adalah cara install dan Konfigurasi Server Block Nginx untuk phpMyAdmin dengan domain `mydatabase.unisan.ac.id`:

1. **Install phpMyAdmin**
   Jika belum terinstal, instal phpMyAdmin dengan perintah berikut:
   ```bash
   sudo apt update
   sudo apt install phpmyadmin
   ```

2. **Buat Server Block untuk phpMyAdmin**

   Buat file konfigurasi Nginx baru untuk domain `mydatabase.unisan.ac.id`:
   ```bash
   sudo nano /etc/nginx/sites-available/mydatabase.unisan.ac.id
   ```

   Tambahkan konfigurasi berikut ke dalam file tersebut:
   ```nginx
   server {
       listen 80;
       server_name mydatabase.unisan.ac.id;

       root /usr/share/phpmyadmin;
       index index.php index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/var/run/php/php8.3-fpm.sock; # Sesuaikan dengan versi PHP yang digunakan
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.ht {
           deny all;
       }

       access_log /var/log/nginx/mydatabase_access.log;
       error_log /var/log/nginx/mydatabase_error.log;
   }
   ```

3. **Aktifkan Konfigurasi Server Block**

   Buat symlink dari file konfigurasi yang baru saja dibuat ke dalam direktori `sites-enabled`:
   ```bash
   sudo ln -s /etc/nginx/sites-available/mydatabase.unisan.ac.id /etc/nginx/sites-enabled/
   ```

4. **Periksa Konfigurasi Nginx**

   Pastikan tidak ada kesalahan pada konfigurasi Nginx dengan menjalankan:
   ```bash
   sudo nginx -t
   ```

5. **Restart Nginx**

   Jika tidak ada error, restart Nginx untuk menerapkan perubahan:
   ```bash
   sudo systemctl restart nginx
   ```

6. **Uji Akses phpMyAdmin**

   Buka browser dan akses `http://mydatabase.unisan.ac.id`. Anda harus melihat halaman login phpMyAdmin.

### Catatan:
- Pastikan domain `mydatabase.unisan.ac.id` sudah mengarah ke server Anda dengan mengonfigurasi DNS.
- Jika Anda menggunakan SSL, tambahkan sertifikat SSL dengan Let's Encrypt atau lainnya.
#
#### Reconfigure phpmyadmin
Jika kamu belum dapat login di halaman-web phpmyadmin atau kamu lupa password phpmyadmin, jangan kuatir..., karena ini bisa di reconfigure dengan perintah ini:
```code
dpkg-reconfigure phpmyadmin
```
ubah metode koneksi dari unix sock ke tcp/ip dan atur ulang database phpmyadmin | user | password-nya

### Agar user phpmyadmin bisa create database
Untuk mengubah user `phpmyadmin` agar bisa diakses dari semua IP address dan memiliki hak untuk membuat database, ikuti langkah-langkah berikut:

### 1. **Login ke MySQL/MariaDB sebagai Root**
   Jalankan perintah berikut untuk login ke MySQL/MariaDB sebagai user `root`:
   ```bash
   mysql -u root -p
   ```

   Masukkan password root MySQL/MariaDB.

### 2. **Ubah User phpmyadmin Agar Bisa Diakses dari Semua IP Address**
   Jalankan perintah berikut untuk mengubah user `phpmyadmin` agar bisa diakses dari semua IP (`%`):
   ```sql
   UPDATE mysql.user SET Host='%' WHERE User='phpmyadmin' AND Host='localhost';
   ```

### 3. **Memberikan Hak untuk Membuat Database**
   Berikan hak akses kepada user `phpmyadmin` untuk membuat database:
   ```sql
   GRANT CREATE, ALL PRIVILEGES ON *.* TO 'phpmyadmin'@'%';
   ```

### 4. **Flush Privileges**
   Setelah mengubah hak akses, jalankan perintah berikut untuk memperbarui privilege di MySQL/MariaDB:
   ```sql
   FLUSH PRIVILEGES;
   ```

### 5. **Verifikasi Hak Akses**
   Untuk memastikan perubahan sudah diterapkan, jalankan perintah berikut untuk melihat hak akses user `phpmyadmin`:
   ```sql
   SHOW GRANTS FOR 'phpmyadmin'@'%';
   ```

### 6. **Keluar dari MySQL/MariaDB**
   Setelah selesai, keluar dari MySQL/MariaDB:
   ```sql
   EXIT;
   ```

Dengan langkah-langkah di atas, user `phpmyadmin` kini bisa diakses dari semua IP address dan memiliki hak untuk membuat database.

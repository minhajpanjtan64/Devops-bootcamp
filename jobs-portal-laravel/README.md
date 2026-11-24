# Jobs Portal - Laravel

A job listings portal built with Laravel.

---

## Requirements

- PHP 8.3+
- Composer
- MySQL
- Nginx + PHP-FPM
- Node.js & NPM (optional, for frontend assets)

---

## Server Setup (Ubuntu 24.04)

### 1. Install PHP 8.3 and extensions

```bash
sudo apt update
sudo apt install -y php8.3-cli php8.3-fpm php8.3-mbstring php8.3-xml php8.3-bcmath php8.3-curl php8.3-zip php8.3-mysql php8.3-tokenizer
```

### 2. Install Composer

```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

### 3. Install MySQL

```bash
sudo apt install -y mysql-server
sudo systemctl start mysql
sudo systemctl enable mysql
```

### 4. Install Nginx

```bash
sudo apt install -y nginx
```

---

## Application Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/jobs-portal-laravel.git
cd jobs-portal-laravel
```

### 2. Install dependencies

```bash
composer install
```

### 3. Set up the environment file

```bash
cp .env.example .env
nano .env
```

Update the following values in `.env`:

```env
APP_URL=http://your-server-ip

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=jobs_portal
DB_USERNAME=jobs_user
DB_PASSWORD=yourpassword
```

### 4. Generate application key

```bash
php artisan key:generate
```

---

## Database Setup

### 1. Create the database and user

```bash
sudo mysql
```

Inside the MySQL shell:

```sql
CREATE DATABASE jobs_portal;
CREATE USER 'jobs_user'@'localhost' IDENTIFIED BY 'yourpassword';
GRANT ALL PRIVILEGES ON jobs_portal.* TO 'jobs_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 2. Run migrations

```bash
php artisan migrate
```

### 3. Seed the database

To populate the database with a default user and dummy listings:

```bash
php artisan db:seed
```

---

## File Storage

When uploading listing images, files are stored in `storage/app/public`. Run the following to make them publicly accessible:

```bash
php artisan storage:link
```

---

## Nginx Configuration

Create a new Nginx site config:

```bash
sudo nano /etc/nginx/sites-available/jobs-portal
```

```nginx
server {
    listen 80;
    server_name _;

    root /var/www/jobs-portal-laravel/public;
    index index.php index.html;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

Enable the site and restart services:

```bash
sudo ln -s /etc/nginx/sites-available/jobs-portal /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl restart php8.3-fpm
```

If your app lives in your home directory, create a symlink to `/var/www/`:

```bash
sudo ln -s /home/www-data/jobs-portal-laravel /var/www/jobs-portal-laravel
```

---

## Permissions

```bash
sudo chown -R www-data:www-data storage bootstrap/cache
sudo chmod -R 775 storage bootstrap/cache
```

---

## Accessing the App

Visit the app in your browser:

```
http://your-server-ip
```

## Troubleshooting
 
### `composer install` fails: package "does not satisfy" PHP 8.3
 
**Symptoms**
 
```
nette/schema v1.2.2 requires php >=7.1 <8.2 -> your php version (8.3.x) does not satisfy that requirement.
```
 
The same error can appear for `nette/utils`, `phpspec/prophecy`, and `league/config`.
 
**Cause:** `composer.lock` pins old package versions that cap PHP below 8.2.
 
**Fix:** update the offending packages:
 
```bash
composer update nette/schema nette/utils phpspec/prophecy league/config --with-dependencies
```
 
If other packages still conflict, run a full update and test the app afterwards:
 
```bash
composer update
```
 
Also check `composer.json` for exact version pins or a `config.platform.php` override that forces an older PHP version.
 
> Avoid `composer install --ignore-platform-reqs` as a permanent fix. The old versions were not tested on PHP 8.2+ and may break at runtime.
 
Once everything works, commit the updated `composer.lock` so that fresh clones install without this problem.
 
### `Carbon::setLastErrors(): Argument #1 ($lastErrors) must be of type array, false given`
 
**Cause:** an old `nesbot/carbon` version is incompatible with PHP 8.2+. It usually appears when seeding, because factories call `now()`.
 
**Fix:**
 
```bash
composer update nesbot/carbon --with-dependencies
php artisan config:clear
php artisan migrate:fresh --seed
```
 
### `SQLSTATE[HY000] [1698] Access denied for user 'root'@'localhost'`
 
**Cause:** on Ubuntu, MySQL's `root` user uses `auth_socket` authentication, so Laravel cannot log in as `root` with a password.
 
**Fix:** use a dedicated database user as described in [Database Setup](#database-setup), then update `.env` and run:
 
```bash
php artisan config:clear
php artisan migrate
```
 
If you must use `root` on a local dev machine, set a password for it first:
 
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
FLUSH PRIVILEGES;
```
 
(On MySQL 8.4+, drop `WITH mysql_native_password`. On MariaDB use `IDENTIFIED VIA mysql_native_password USING PASSWORD('your_password')`.) This is not recommended on a server, because `root` has access to every database.
 
### `SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry '...' for key 'users.users_email_unique'`
 
**Cause:** the seeder ran before (or partially ran), and the default user already exists.
 
**Fix:** reset the database and reseed (**deletes all data**):
 
```bash
php artisan migrate:fresh --seed
```
 
If it still fails on a fresh database, the seeder or factory is creating the same email twice. Check `database/seeders/DatabaseSeeder.php` and make sure `UserFactory` uses `fake()->unique()->safeEmail()`.
 
---

---

<img width="1900" height="892" alt="image" src="https://github.com/user-attachments/assets/3289a7e4-c1ee-4054-9bc6-2bf7e5f33904" />
<img width="1894" height="890" alt="image" src="https://github.com/user-attachments/assets/71638afc-9485-48a3-ae24-0e56d3fb27dd" />
<img width="1920" height="892" alt="image" src="https://github.com/user-attachments/assets/df7ce447-3f11-43ac-8d0c-65cf40adb645" />
<img width="1897" height="843" alt="image" src="https://github.com/user-attachments/assets/6e7b134a-fb2a-48af-9939-36df9de0a608" />




## License

This project is open-sourced under the [MIT license](https://opensource.org/licenses/MIT).

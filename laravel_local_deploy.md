# Laravel Local Server Deployment on Ubuntu (Apache + MySQL)

## Objective:

Deploy a Laravel application **locally** on an Ubuntu machine using:

- Apache as the web server
- MySQL as the database
- Laravel (moved from SQLite to MySQL)

---

## System Requirements:

- Ubuntu OS
- Laravel project ready (was in `~/Desktop/laravel-app/hands-on-lab-nishat`)
- Internet connection for package installations

---

## Step-by-Step Process

### Step 1: Install Apache Web Server

```bash
sudo apt update
sudo apt install apache2 -y
```

Enable and start Apache:

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```

Test by visiting: [http://localhost](http://localhost)

---

### Step 2: Install MySQL Server

```bash
sudo apt install mysql-server -y
```

Secure the installation:

```bash
sudo mysql_secure_installation
```

Remove anonymous users, disable remote root login, remove test DB, and reload privileges.

---

### Step 3: Create Laravel MySQL Database and User

```bash
sudo mysql -u root -p
```

Inside MySQL prompt:

```sql
CREATE DATABASE laravel_db;
CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'StrongPasswordHere';
GRANT ALL PRIVILEGES ON laravel_db.* TO 'laravel_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

### Step 4: Install PHP & Required Extensions

```bash
sudo apt install php libapache2-mod-php php-mysql php-xml php-mbstring php-curl php-zip php-bcmath php-cli php-common php-gd unzip curl -y
```

---

### Step 5: Move Laravel Project to Apache Web Directory

```bash
sudo mv ~/Desktop/laravel-app/hands-on-lab-nishat /var/www/laravel-app
```

---

### Step 6: Set File Permissions

```bash
sudo chown -R www-data:www-data /var/www/laravel-app
sudo find /var/www/laravel-app -type f -exec chmod 664 {} \;
sudo find /var/www/laravel-app -type d -exec chmod 775 {} \;
sudo chmod -R 775 /var/www/laravel-app/storage
sudo chmod -R 775 /var/www/laravel-app/bootstrap/cache
```

Create log file and fix permissions:

```bash
sudo touch /var/www/laravel-app/storage/logs/laravel.log
sudo chown www-data:www-data /var/www/laravel-app/storage/logs/laravel.log
sudo chmod 664 /var/www/laravel-app/storage/logs/laravel.log

sudo chown www-data:www-data /var/www/laravel-app/.env
sudo chmod 664 /var/www/laravel-app/.env
```

---

### Step 7: Configure Apache for Laravel

Create config file:

```bash
sudo nano /etc/apache2/sites-available/laravel.conf
```

Paste this:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/laravel-app/public
    ServerName localhost

    <Directory /var/www/laravel-app/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/laravel_error.log
    CustomLog ${APACHE_LOG_DIR}/laravel_access.log combined
</VirtualHost>
```

Enable config and rewrite module:

```bash
sudo a2ensite laravel.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

### Step 8: Update Laravel Environment for MySQL

Edit `.env`:

```bash
sudo nano /var/www/laravel-app/.env
```

Update DB section:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=StrongPasswordHere
```

---

### Step 9: Install Laravel Dependencies

```bash
cd /var/www/laravel-app
sudo -u www-data composer install
```

Generate app key:

```bash
sudo -u www-data php artisan key:generate
```

Run migrations:

```bash
sudo -u www-data php artisan migrate
```

---

### ✅ Final Result

Visit in browser:

```
http://localhost
```

You should see your Laravel app running successfully using MySQL instead of SQLite.

---

## Outcome

we now have a **fully working Laravel development environment** on local machine using Apache + MySQL. This simulates real-world deployment practices.


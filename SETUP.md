# 🛠 Setup Working Environment

## To set up a Laravel 11 environment on Ubuntu follow these steps

### Step 1: Install Required Packages

1. **Update system packages**:

   ```bash
   sudo apt update && sudo apt upgrade
   ```

2. **Install PHP 8.3** and required extensions:

   ```bash
   sudo add-apt-repository ppa:ondrej/php
   sudo apt update
   sudo apt install php8.3 php8.3-cli php8.3-fpm php8.3-mbstring php8.3-xml php8.3-bcmath php8.3-zip php8.3-curl php8.3-gd php8.3-mysql php8.3-sqlite3
   ```

3. **Install Composer** (Dependency Manager for PHP):

   ```bash
   curl -sS https://getcomposer.org/installer | php
   sudo mv composer.phar /usr/local/bin/composer
   sudo chmod +x /usr/local/bin/composer
   ```

4. **Install Node.js and npm** (for Laravel frontend scaffolding):

   ```bash
   curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install nodejs
   ```

### Step 2: Install Laravel 11

1. **Navigate to your desired directory**:

   ```bash
   cd /var/wwww/html/
   ```

2. **Create a new Laravel project**:

   ```bash
   composer create-project --prefer-dist laravel/laravel:^11.0 laravel-11-workshop
   ```

3. **Set permissions** for the `storage` and `bootstrap/cache` directories:

   ```bash
   sudo chmod -R 775 laravel-11-workshop/storage laravel-11-workshop/bootstrap/cache
   sudo chown -R www-data:www-data laravel-11-workshop/storage laravel-11-workshop/bootstrap/cache
   ```

### Step 3: Configure the `.env` File

1. Inside the `laravel-11-workshop` folder, copy the `.env.example` file:

   ```bash
   cp .env.example .env
   ```

2. **Generate the application key**:

   ```bash
   php artisan key:generate
   ```

3. **Configure the database** in the `.env` file:

   ```bash
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=your_database
   DB_USERNAME=your_username
   DB_PASSWORD=your_password
   ```

### Step 4: Setup Virtual Host (Optional)

If you want to serve the app through a domain or local development URL, create an Apache or Nginx virtual host configuration:

1. **Apache**:
   Create a new virtual host file for your Laravel app:

   ```bash
   sudo nano /etc/apache2/sites-available/laravel-11-workshop.conf
   ```

   Add the following content:

   ```apache
   <VirtualHost *:80>
       ServerName laravel-11-workshop.local
       DocumentRoot /var/www/html/laravel-11-workshop/public
       
       <Directory /var/www/html/laravel-11-workshop/public>
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

   Enable the site and mod_rewrite:

   ```bash
   sudo a2ensite laravel-11-workshop.conf
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   ```

2. **Nginx**:
   Create a new server block for your app:

   ```bash
   sudo nano /etc/nginx/sites-available/laravel-11-workshop
   ```

   Add the following content:

   ```nginx
   server {
       listen 80;
       server_name laravel-11-workshop.local;
       root /var/www/html/laravel-11-workshop/public;

       index index.php index.html index.htm;

       location / {
           try_files $uri $uri/ /index.php?$query_string;
       }

       location ~ \.php$ {
           fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
           fastcgi_index index.php;
           include fastcgi_params;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
       }
   }
   ```

   Enable the configuration and restart Nginx:

   ```bash
   sudo ln -s /etc/nginx/sites-available/laravel-11-workshop /etc/nginx/sites-enabled/
   sudo systemctl restart nginx
   ```

### Step 5: Final Steps

1. **Run migrations**:

   ```bash
   php artisan migrate
   ```

2. **Start the development server** (if not using a virtual host):

   ```bash
   php artisan serve
   ```

Your Laravel 11 application should now be up and running. Let me know if you need further assistance!

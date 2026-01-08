# TableTrack POS

<p align="center">
  <img src="public/img/tabletrack-logo.png" alt="TableTrack Logo" width="200">
</p>

<p align="center">
  <strong>Complete SaaS Restaurant Management Solution</strong>
</p>

<p align="center">
  <a href="#features">Features</a> •
  <a href="#requirements">Requirements</a> •
  <a href="#installation">Installation</a> •
  <a href="#configuration">Configuration</a> •
  <a href="#troubleshooting">Troubleshooting</a>
</p>

---

## 🍽️ About TableTrack

TableTrack is a comprehensive restaurant management POS (Point of Sale) system built with Laravel. It provides everything you need to manage your restaurant operations including order management, table reservations, inventory tracking, staff management, and detailed reporting.

## ✨ Features

- 📋 **Order Management** - Complete POS system for dine-in, takeaway, and delivery
- 🪑 **Table Management** - Real-time table status and reservation system
- 📦 **Inventory Tracking** - Stock management with batch production support
- 👥 **Multi-role System** - Admin, Manager, Chef, Waiter, Cashier roles
- 📊 **Reports & Analytics** - Sales, inventory, and performance reports
- 🌐 **Multi-language** - Support for multiple languages
- 💳 **Payment Gateways** - Stripe, PayPal, Razorpay, and more
- 📱 **PWA Support** - Progressive Web App for mobile access

## 📋 Requirements

### System Requirements
- PHP >= 8.2
- MySQL >= 5.7 or MariaDB >= 10.3
- Node.js >= 18.x
- Composer >= 2.x
- NPM or PNPM

### PHP Extensions
- BCMath
- Ctype
- cURL
- DOM
- Fileinfo
- GD or Imagick
- JSON
- Mbstring
- OpenSSL
- PDO MySQL
- Tokenizer
- XML
- Zip

---

## 🚀 Installation

### Local Development Installation

#### Step 1: Clone Repository
```bash
git clone https://github.com/mbuzzz/tabletrackPOS.git
cd tabletrackPOS
```

#### Step 2: Install PHP Dependencies
```bash
composer install
```

#### Step 3: Install Node Dependencies
```bash
npm install
# or using pnpm
pnpm install
```

#### Step 4: Environment Setup
```bash
# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate
```

#### Step 5: Configure Database
Edit `.env` file with your database credentials:
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=tabletrack_db
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

#### Step 6: Run Migrations & Seeders
```bash
# Run migrations
php artisan migrate

# Seed initial data (if available)
php artisan db:seed
```

#### Step 7: Build Assets
```bash
npm run dev
# or for watching changes
npm run dev -- --watch
```

#### Step 8: Start Development Server
```bash
php artisan serve
```

Visit `http://localhost:8000` in your browser.

---

### Production Installation

#### Step 1: Server Preparation

Ensure your server has:
- PHP 8.2+ with required extensions
- MySQL/MariaDB database
- Nginx or Apache web server
- SSL certificate (recommended)

#### Step 2: Clone & Upload Files
```bash
git clone https://github.com/mbuzzz/tabletrackPOS.git /var/www/tabletrack
cd /var/www/tabletrack
```

#### Step 3: Install Dependencies
```bash
composer install --no-dev --optimize-autoloader
npm install
npm run build
```

#### Step 4: Environment Configuration
```bash
cp .env.example .env
php artisan key:generate
```

Edit `.env` for production:
```env
APP_NAME=TableTrack
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com

REDIRECT_HTTPS=true

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=tabletrack_production
DB_USERNAME=your_db_user
DB_PASSWORD=your_secure_password

# Mail Configuration
MAIL_MAILER=smtp
MAIL_HOST=your-smtp-host
MAIL_PORT=587
MAIL_USERNAME=your-email
MAIL_PASSWORD=your-password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@yourdomain.com
```

#### Step 5: Set Permissions
```bash
# Set ownership
sudo chown -R www-data:www-data /var/www/tabletrack

# Set directory permissions
sudo chmod -R 755 /var/www/tabletrack
sudo chmod -R 775 /var/www/tabletrack/storage
sudo chmod -R 775 /var/www/tabletrack/bootstrap/cache
```

#### Step 6: Run Migrations
```bash
php artisan migrate --force
php artisan db:seed --force  # If needed
```

#### Step 7: Optimize Application
```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan storage:link
```

#### Step 8: Configure Web Server

**Nginx Configuration:**
```nginx
server {
    listen 80;
    listen 443 ssl;
    server_name yourdomain.com;
    root /var/www/tabletrack/public;

    ssl_certificate /path/to/ssl/certificate.crt;
    ssl_certificate_key /path/to/ssl/private.key;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

**Apache Configuration (.htaccess is included):**
Ensure `mod_rewrite` is enabled:
```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

#### Step 9: Setup Cron Job
```bash
crontab -e
```
Add this line:
```
* * * * * cd /var/www/tabletrack && php artisan schedule:run >> /dev/null 2>&1
```

#### Step 10: Setup Queue Worker (Optional but Recommended)
```bash
# Using Supervisor
sudo apt install supervisor

# Create supervisor config
sudo nano /etc/supervisor/conf.d/tabletrack-worker.conf
```

Add:
```ini
[program:tabletrack-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/tabletrack/artisan queue:work --sleep=3 --tries=3
autostart=true
autorestart=true
user=www-data
numprocs=2
redirect_stderr=true
stdout_logfile=/var/www/tabletrack/storage/logs/worker.log
```

Start supervisor:
```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start tabletrack-worker:*
```

---

## ⚙️ Configuration

### Payment Gateways

Configure payment providers in `.env`:

```env
# Stripe
STRIPE_KEY=pk_live_...
STRIPE_SECRET=sk_live_...

# PayPal
PAYPAL_CLIENT_ID=...
PAYPAL_CLIENT_SECRET=...
PAYPAL_MODE=live

# Razorpay
RAZORPAY_KEY=...
RAZORPAY_SECRET=...
```

### Pusher (Real-time Features)

```env
PUSHER_APP_ID=your_app_id
PUSHER_APP_KEY=your_app_key
PUSHER_APP_SECRET=your_app_secret
PUSHER_APP_CLUSTER=your_cluster
```

---

## 🔧 Troubleshooting

### Common Issues

**1. 500 Internal Server Error**
```bash
# Check storage permissions
sudo chmod -R 775 storage bootstrap/cache

# Clear caches
php artisan cache:clear
php artisan config:clear
php artisan view:clear
```

**2. Assets Not Loading**
```bash
# Rebuild assets
npm run build

# Re-link storage
php artisan storage:link
```

**3. Database Connection Error**
- Verify `.env` database credentials
- Check if MySQL service is running
- Ensure database exists

**4. Composer Memory Error**
```bash
COMPOSER_MEMORY_LIMIT=-1 composer install
```

### Logs Location
- Application logs: `storage/logs/laravel.log`
- Web server logs: `/var/log/nginx/` or `/var/log/apache2/`

---

## 📄 License

This project is proprietary software. All rights reserved.

---

## 🤝 Support

For support and inquiries, please contact the development team.

---

<p align="center">
  Made with ❤️ for Restaurant Owners
</p>

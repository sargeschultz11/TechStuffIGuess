---
layout: post
title: "Comprehensive Guide to Installing Snipe-IT on Ubuntu 22.04"
date: 2025-04-09 09:30:00 -0500
categories: [general-it]
tags: [asset-management, ubuntu, installation-guide, snipe-it]
---

# Comprehensive Guide to Installing Snipe-IT on Ubuntu 22.04

¯\\\_(ツ)\_/¯ You know how it goes - one day you're manually tracking IT assets in a spreadsheet, and the next day your CEO is asking for a detailed report of every laptop, server, and software license in the organization... by tomorrow morning.

If you've reached the point where spreadsheets just aren't cutting it anymore, Snipe-IT is one of the best open-source asset management solutions available. This guide will walk you through installing it on Ubuntu 22.04 LTS.

## What is Snipe-IT?

Snipe-IT is a free, open-source IT asset management system built on the Laravel PHP framework. It provides a comprehensive platform for tracking:

- Hardware assets (laptops, servers, mobile devices)
- Software licenses
- Accessories
- Consumables
- User assignments and check-outs

The web-based interface makes it accessible from anywhere, and it's designed to be both powerful and user-friendly.

## Prerequisites

Before we begin, make sure you have:

- A fresh Ubuntu 22.04 LTS server
- Root or sudo access
- Basic familiarity with command line operations
- A domain or subdomain pointed to your server (optional but recommended)

## Step 1: System Preparation

First, let's make sure our system is up-to-date:

```bash
sudo apt update
sudo apt upgrade -y
```

## Step 2: Installing Required PHP Extensions

Snipe-IT requires PHP 8.1 with several extensions. Install them all at once with:

```bash
sudo apt install php8.1-{bcmath,common,ctype,curl,fileinfo,fpm,gd,iconv,intl,mbstring,mysql,soap,xml,xsl,zip,cli}
```

## Step 3: Installing Composer

Composer is the PHP dependency manager that Snipe-IT uses. Let's install it:

```bash
sudo curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

Verify the installation:

```bash
composer -V
```

## Step 4: Setting up MariaDB

Now we'll install and secure the database server:

```bash
sudo apt install mariadb-server mariadb-client -y
sudo mysql_secure_installation
```

Follow the prompts to set a root password and secure your installation.

### Creating the Database

Access the MySQL prompt:

```bash
sudo mysql
```

Create a database and user for Snipe-IT:

```sql
CREATE DATABASE snipeit;
CREATE USER 'snipeit'@'localhost' IDENTIFIED BY 'YOUR_SECURE_PASSWORD';
GRANT ALL ON snipeit.* TO snipeit@localhost identified by 'YOUR_SECURE_PASSWORD';
FLUSH PRIVILEGES;
exit;
```

Make sure to replace `YOUR_SECURE_PASSWORD` with a strong password.

## Step 5: Installing and Configuring Nginx

Install Nginx as the web server:

```bash
sudo apt install nginx -y
```

Create a new Nginx configuration file:

```bash
sudo nano /etc/nginx/conf.d/snipeit.conf
```

Add the following configuration (adjust as needed):

```nginx
server {
    listen 80;
    server_name YOUR_SERVER_IP_OR_DOMAIN;
    root /var/www/html/snipe-it/public;
    
    index index.php;
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    location ~ \.php$ {
        include fastcgi.conf;
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

Replace `YOUR_SERVER_IP_OR_DOMAIN` with your actual server IP or domain name.

Test the configuration and restart Nginx:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

## Step 6: Installing Snipe-IT

First, install Git:

```bash
sudo apt install git
```

Clone the Snipe-IT repository:

```bash
cd /var/www/html
sudo git clone https://github.com/snipe/snipe-it snipe-it
cd snipe-it
```

Copy the example environment file:

```bash
sudo cp .env.example .env
sudo nano .env
```

Edit the following values in the `.env` file:

```
APP_URL=http://YOUR_DOMAIN_OR_IP
APP_TIMEZONE='YOUR_TIMEZONE'  # e.g., 'America/New_York' or 'UTC'
DB_DATABASE=snipeit
DB_USERNAME=snipeit
DB_PASSWORD=YOUR_SECURE_PASSWORD
```

Set proper permissions:

```bash
sudo chown -R www-data:www-data /var/www/html/snipe-it
sudo chmod -R 755 /var/www/html/snipe-it
```

## Step 7: Installing Dependencies

Install the PHP dependencies using Composer:

```bash
cd /var/www/html/snipe-it
sudo composer update --no-plugins --no-scripts
sudo composer install --no-dev --prefer-source --no-plugins --no-scripts
```

Generate the application key:

```bash
sudo php artisan key:generate
```

## Step 8: Final Configuration and Initial Setup

Set up the database tables:

```bash
sudo php artisan migrate
```

If prompted to continue, type `yes`.

Now you can access Snipe-IT through your web browser at `http://YOUR_SERVER_IP_OR_DOMAIN`. Follow the web-based setup wizard to:

1. Create an admin user
2. Configure your company information
3. Set up asset categories and statuses

## Step 9: Setting Up Scheduled Tasks (Cron Jobs)

Snipe-IT uses Laravel's scheduling system for maintenance tasks. Add a cron job to handle these:

```bash
sudo crontab -e
```

Add this line:

```
* * * * * cd /var/www/html/snipe-it && php artisan schedule:run >> /dev/null 2>&1
```

This ensures that all scheduled tasks run at their appropriate times.

### Customizing Task Schedules

If you need to adjust when specific tasks run, you can modify the `app/Console/Kernel.php` file. Here are some useful scheduled tasks in Snipe-IT:

1. **Expiring Alerts**: Notifications for assets and licenses that will expire soon
2. **Expired Alerts**: Notifications for items that have already expired
3. **Upcoming Audits**: Reminders for scheduled asset audits
4. **Inventory Alerts**: Notifications about inventory levels

For example, to change when expiring alerts run:

```php
$schedule->command('snipeit:expiring-alerts')->weekdays()->at('8:30');
```

Common schedule options include:
- `->daily()`: Once per day at midnight
- `->dailyAt('13:00')`: Daily at 1:00 PM
- `->weekly()`: Once per week
- `->monthly()`: Once per month
- `->weeklyOn(1, '8:00')`: Every Monday at 8:00 AM

## Step 10: Securing Your Installation (Optional but Recommended)

For production use, consider these additional security steps:

### Setting Up SSL with Let's Encrypt

Install Certbot:

```bash
sudo apt install certbot python3-certbot-nginx
```

Obtain and configure an SSL certificate:

```bash
sudo certbot --nginx -d YOUR_DOMAIN
```

### Configuring Firewall

Allow only necessary ports:

```bash
sudo apt install ufw
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

## Updating Snipe-IT

When new versions are released, update with these steps:

```bash
cd /var/www/html/snipe-it
sudo git pull
sudo composer update --no-dev --prefer-source
sudo php artisan migrate
sudo php artisan config:clear
sudo php artisan cache:clear
sudo php artisan view:clear
```

For versions 4.1.3 and higher, you can use the one-step upgrade script:

```bash
cd /var/www/html/snipe-it
sudo git pull
sudo php upgrade.php
```

## Troubleshooting Common Issues

### 500 Internal Server Error

Check the Laravel logs:

```bash
sudo tail -f /var/www/html/snipe-it/storage/logs/laravel.log
```

Common fixes include:
- Correcting permissions: `sudo chown -R www-data:www-data /var/www/html/snipe-it`
- Clearing caches: `sudo php artisan cache:clear`

### Database Connection Issues

Verify your database credentials in the `.env` file and ensure the MariaDB service is running:

```bash
sudo systemctl status mariadb
```

### "Your app key is missing" Error

Generate a new application key:

```bash
sudo php artisan key:generate
```

## Conclusion

You now have a fully functional Snipe-IT installation for managing your IT assets. While the setup might seem complex, the payoff is worth it - a robust, searchable database of all your assets that makes tracking, auditing, and reporting a breeze.

Snipe-IT is actively maintained, with regular updates and a supportive community. If you find it valuable, consider supporting the project through their website.

Have you encountered other issues during your Snipe-IT installation? Drop a comment below, and I'll update this guide with solutions!

## Related Articles


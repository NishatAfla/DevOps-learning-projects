FROM php:8.2-apache

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libonig-dev \
    libzip-dev \
    unzip \
    zip \
    curl \
    git \
    && docker-php-ext-install pdo pdo_mysql zip \
    && a2enmod rewrite\
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* 

# Set working directory
WORKDIR /var/www/html

COPY composer.json composer.lock ./

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
RUN composer install --no-autoloader --no-scripts --no-interaction

# Copy application files
COPY . .

# Update Apache to serve Laravel's public directory
RUN sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/html/public|g' /etc/apache2/sites-available/000-default.conf

# Set proper permissions
RUN chown -R www-data:www-data /var/www/html/storage \
    && chown -R www-data:www-data /var/www/html/bootstrap/cache \
    && chmod -R 755 /var/www/html/storage \
    && chmod -R 755 /var/www/html/bootstrap/cache


# Install Composer autoload and scripts after copying the app
RUN composer dump-autoload --optimize

# Expose Apache port
EXPOSE 80

# Ensure Apache stays in the foreground
CMD ["apache2-foreground"]

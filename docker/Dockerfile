FROM php:8.2-fpm

# Install dependencies, Nginx, and PHP extensions
RUN apt-get update && apt-get install -y \
    git unzip zip nginx libpng-dev libjpeg-dev libfreetype6-dev \
    libonig-dev libxml2-dev libzip-dev libicu-dev libicu-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip intl calendar \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Copy Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www/html

# Copy app source code
COPY . .

# Install PHP dependencies
RUN composer install --no-interaction --optimize-autoloader

# Copy Nginx configuration
COPY docker/nginx.conf /etc/nginx/conf.d/default.conf

# Fix Laravel permissions
RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache \
    && chmod -R 775 /var/www/html/storage /var/www/html/bootstrap/cache

# Expose port 80 for web
EXPOSE 80

# Start both Nginx & PHP-FPM
CMD service nginx start && php-fpm


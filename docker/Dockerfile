# build stage
FROM php:8.2-fpm

# Install system dependencies and PHP extensions
RUN apt-get update && apt-get install -y \

# Stage 1: Build Bagisto app with PHP-FPM and Nginx
FROM php:8.2-fpm

# Install necessary dependencies
RUN apt-get update && apt-get install -y \
    nginx \
    git \
    unzip \
    zip \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    libicu-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install \
        pdo_mysql \
        mbstring \
        exif \
        pcntl \
        bcmath \
        gd \
        zip \
        intl \
        calendar

# Fix Git “dubious ownership” warning inside Jenkins
RUN git config --global --add safe.directory /var/www/html
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip intl \
    && apt-get clean && rm -rf /var/lib/apt/lists/*


# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www/html


# Copy project files
COPY . .

# Install PHP dependencies
RUN composer install --no-interaction --optimize-autoloader

# Expose port 9000 and start PHP-FPM
EXPOSE 9000
CMD ["php-fpm"]


# Copy all files
COPY . .

# Fix permissions
RUN chown -R www-data:www-data /var/www/html && chmod -R 755 /var/www/html

# Install dependencies
RUN composer install --no-interaction --optimize-autoloader

# Copy Nginx config
COPY ./docker/nginx.conf /etc/nginx/conf.d/default.conf

# Expose web port
EXPOSE 80

# Start both Nginx and PHP-FPM
CMD service nginx start && php-fpm


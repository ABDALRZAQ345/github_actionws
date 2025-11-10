FROM php:8.3-fpm

# ------------------------
# Install system dependencies
# ------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libzip-dev \
    libonig-dev \
    zip \
    curl \
    bash \
    git \
    unzip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ------------------------
# PHP extensions
# ------------------------
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip \
    && pecl install redis \
    && docker-php-ext-enable redis

# ------------------------
# Composer
# ------------------------
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# ------------------------
# Set working directory
# ------------------------
WORKDIR /var/www/html

# ------------------------
# Copy only composer files first for caching
# ------------------------
COPY composer.json composer.lock ./

# ------------------------
# Install Laravel dependencies
# ------------------------
RUN composer install --optimize-autoloader --no-interaction --prefer-dist --no-scripts

# ------------------------
# Copy the rest of the application
# ------------------------
COPY . .

# ------------------------
# Create necessary directories & permissions
# ------------------------
RUN mkdir -p storage bootstrap/cache \
    && chown -R www-data:www-data storage bootstrap/cache \
    && chmod -R 755 storage bootstrap/cache

# Laravel subdirectories (framework caches, logs, sessions, views)
RUN mkdir -p storage/framework/cache storage/framework/sessions storage/framework/views storage/logs \
    && chown -R www-data:www-data storage \
    && chmod -R 755 storage

# Bootstrap cache permissions
RUN mkdir -p bootstrap/cache \
    && chown -R www-data:www-data bootstrap/cache \
    && chmod -R 755 bootstrap/cache

# ------------------------
# Set default command
# ------------------------
CMD ["php-fpm"]

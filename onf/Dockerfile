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
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ------------------------
#  PHP extensions
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
# Copy application files
# ------------------------
COPY . .
RUN mkdir -p bootstrap/cache

# ------------------------
# Create necessary directories & permissions
# ------------------------
RUN mkdir -p storage bootstrap/cache \
    && chown -R www-data:www-data storage bootstrap/cache \
    && chmod -R 755 storage bootstrap/cache
# Create necessary directories
RUN mkdir -p /var/www/html/storage/logs
RUN mkdir -p /var/www/html/storage/framework/cache
RUN mkdir -p /var/www/html/storage/framework/sessions
RUN mkdir -p /var/www/html/storage/framework/views
RUN mkdir -p /var/www/html/bootstrap/cache

# Set permissions
RUN chown -R www-data:www-data /var/www/html
RUN chmod -R 755 /var/www/html/storage
RUN chmod -R 755 /var/www/html/bootstrap/cache

COPY docker/start.sh /usr/local/bin/start.sh
RUN chmod +x /usr/local/bin/start.sh
ENTRYPOINT ["/usr/local/bin/start.sh"]

# Install Laravel dependencies (prod)
RUN composer install  --optimize-autoloader --no-interaction


CMD ["php-fpm"]

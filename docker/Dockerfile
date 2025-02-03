# Usamos la imagen base de PHP 8.3 FPM en Alpine
FROM php:8.3-fpm-alpine AS php

# 1. Instalación de dependencias y herramientas necesarias
RUN apk add --no-cache \
    git \
    zip \
    curl \
    sudo \
    unzip \
    icu-dev \
    bzip2-dev \
    readline-dev \
    g++ \
    libzip-dev \
    libpng-dev \
    libjpeg-turbo-dev \
    freetype-dev \
    oniguruma-dev \
    libxml2-dev \
    jpegoptim \
    optipng \
    pngquant \
    gifsicle \
    nodejs \
    npm \
    shadow

# 2. Instalación de extensiones de PHP
RUN docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-install opcache && \
    docker-php-ext-install -j$(nproc) \
    bz2 \
    intl \
    # iconv \
    bcmath \
    calendar \
    exif \
    pdo_mysql \
    zip \
    gd \
    mbstring \
    xml \
    pcntl

# 3. Configuración de GD
RUN docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd

# 4. Configuración personalizada de php.ini
COPY docker/php/php.ini /usr/local/etc/php/conf.d/custom.ini

# 5. Instalación de Composer
COPY --from=composer:2.7 /usr/bin/composer /usr/bin/composer

# 6. Creación de un usuario con el mismo UID/GID que el host
ARG uid
RUN useradd -G www-data,root -u $uid -d /home/devuser devuser && \
    mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser

# 7. Cambiar al usuario devuser
USER devuser

# 8. Comandos para ejecutar al iniciar el contenedor
CMD \
    # npm ci -d && npm run build && \
    composer install --prefer-dist --no-progress --no-suggest && \
    composer clear-cache && \
    rm -f public/storage && \
    php artisan storage:link && \
    php-fpm
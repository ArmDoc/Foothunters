FROM php:8.0.2-fpm@sha256:b9adc59273171d12e243e4185d83cbdb78eb7a826b9f7f6c1ce82403fd656b97 as base

LABEL maintainer="Aparg"

EXPOSE 9000

# Set working directory
WORKDIR /var/www

# Add php ext repo
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# Install php extensions
RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions mbstring pdo_mysql zip exif pcntl gd memcached

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    unzip \
    git \
    curl \
    lua-zlib-dev \
    libmemcached-dev \
    net-tools \
    telnet \
    ffmpeg

RUN curl -sL https://deb.nodesource.com/setup_16.x > /tmp/setup.sh && \
    chmod +x /tmp/setup.sh && /tmp/setup.sh && \
    apt -y install nodejs && \
    npm install -g yarn @quasar/cli

# Install Redis
RUN pecl install redis && docker-php-ext-enable redis

# Install supervisor
#RUN apt-get install -y supervisor

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

RUN mkdir /opt/app && \
    chown -R www:www /opt/app

COPY /services/app/php.ini /usr/local/etc/php/conf.d/zzz-php.ini
COPY /services/app/fpm.conf /usr/local/etc/php-fpm.d/zzz-fpm.conf

# PHP Error Log Files
RUN mkdir /var/log/php
RUN touch /var/log/php/errors.log && chmod 777 /var/log/php/errors.log


RUN echo "Aparg Forever ♥"

RUN	docker-php-ext-install bcmath

# Copy code to /var/www
RUN  chown -R www:www /var/www
COPY --chown=www:www ./src /var/www

# add root to www group
RUN chmod -R ug+w /var/www/storage

FROM base AS quasar

USER www

WORKDIR /var/www/resources/js/quasar

COPY src/resources/js/quasar/package.json ./package.json
RUN yarn install && \
    quasar build && \
    cp -r dist/spa/* /var/www/public/quasar && \
    cp dist/spa/index.html /var/www/resources/views/index.blade.php

FROM quasar AS prod

# Change current user to www

USER www

RUN mv /var/www/* /opt/app && \
    rm -rf /opt/app/storage && \
    cd /opt/app && composer install --dev

WORKDIR /var/www

CMD cp -R /opt/app/* /var/www && \
    cp -R /opt/app/* /tmp/shared && \
    ln -nsf /var/www/storage/app/public /var/www/public/storage && \
    php artisan storage:link && \
    php artisan cache:clear && \
    php artisan config:cache && \
    php artisan view:cache && \
    php artisan migrate --force && \
    php artisan auth:clear-resets && \
    php artisan route:cache && \
    php-fpm
    
FROM base AS dev

# Change current user to www
USER www

WORKDIR /var/www

CMD composer install --dev && \
	php artisan key:generate && \
    php artisan storage:link && \
    php artisan migrate --force && \
    #php artisan route:clear && \
    #php artisan cache:clear && \
    php-fpm

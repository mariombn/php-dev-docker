FROM php:8.2-fpm-alpine

ARG WWWGROUP

WORKDIR /var/www

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN addgroup -S 1000 && adduser -S laravel --uid 1000 -G 1000

RUN apk update --no-cache \
&& apk add \
bash \
curl \
icu-dev \
oniguruma-dev \
tzdata \
supervisor \
unzip \
zip \
libzip-dev \
htop \
mysql-client \
postgresql-dev \
less \
gmp-dev \
ghostscript \
nano


RUN docker-php-ext-install intl

RUN docker-php-ext-install pcntl

RUN docker-php-ext-install pdo_mysql

RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql
RUN docker-php-ext-install pdo pdo_pgsql pgsql

RUN docker-php-ext-install mbstring

RUN docker-php-ext-install bcmath

RUN docker-php-ext-install gmp

RUN docker-php-ext-install exif

RUN apk add --update linux-headers \
    && apk add --no-cache $PHPIZE_DEPS \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug

RUN docker-php-ext-install zip

RUN apk add --no-cache libjpeg-turbo libjpeg-turbo-dev jpeg-dev freetype-dev libpng libpng-dev && docker-php-ext-configure gd --with-freetype --with-jpeg && docker-php-ext-install gd && apk del libpng-dev


ADD https://raw.githubusercontent.com/mlocati/docker-php-extension-installer/master/install-php-extensions /usr/local/bin/

RUN chmod uga+x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions imagick

RUN pecl install -o -f redis \
&&  rm -rf /tmp/pear \
&&  docker-php-ext-enable redis

RUN rm -rf /var/cache/apk/*

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini

COPY www.conf /usr/local/etc/php-fpm.d/www.conf
COPY start-container /usr/local/bin/start-container
COPY php.ini /usr/local/etc/php/conf.d/99-sail.ini
COPY docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN chmod +x /usr/local/bin/start-container

RUN chown -R laravel:1000 /var/www
RUN chmod -R 777 /var/www
USER laravel
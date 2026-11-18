FROM php:8.4-fpm-alpine

RUN apk add --no-cache \
        sqlite \
        sqlite-dev \
        icu-dev \
        oniguruma-dev \
        libzip-dev \
        $PHPIZE_DEPS \
    && docker-php-ext-install pdo pdo_sqlite intl mbstring zip bcmath opcache \
    && apk del $PHPIZE_DEPS

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

COPY docker/php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

RUN addgroup -g 1000 www && adduser -G www -u 1000 -D www

USER www

EXPOSE 9000

CMD ["php-fpm"]

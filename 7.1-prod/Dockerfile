FROM php:7.1-fpm-alpine

RUN apk --no-cache add nginx supervisor curl unzip sudo bash wget vim

# Configure nginx
COPY config/nginx.conf /etc/nginx/nginx.conf
COPY config/site.conf /etc/nginx/conf.d/site.conf
RUN chown -R www-data:www-data /var/lib/nginx

# Configure PHP-FPM
COPY config/fpm-pool.conf /etc/php7/php-fpm.d/fpm-pool.conf
COPY config/php.ini /usr/local/etc/php/conf.d/zzz_custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Add web root
RUN mkdir -p /var/www && chown -R www-data:www-data /var/www && rm -R /var/www/*
WORKDIR /var/www

# Add PHP extensions
RUN apk --no-cache add zlib-dev libxml2-dev libpng-dev icu icu-libs icu-dev libmcrypt-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install pdo_mysql mysqli zip xml gd mbstring mcrypt iconv exif intl

# Add redis PHP extension
RUN apk add --no-cache autoconf gcc g++ imagemagick-dev libtool make openssl-dev \
    && pecl install redis-3.1.2 mongodb-1.2.8 imagick-3.4.3 \
    && docker-php-ext-enable redis mongodb imagick \
    && apk del autoconf gcc g++ libtool make openssl-dev \
    && rm -rf /tmp/* /var/cache/apk/*
    
# Forward nginx errors to stderr
RUN ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
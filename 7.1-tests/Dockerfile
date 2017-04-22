FROM doriangray/nginx-php-symfony:7.1-prod

# Composer / PhpUnit
RUN apk --no-cache add git --repository http://dl-cdn.alpinelinux.org/alpine/edge/main/ \
    && mkdir /var/opt/composer \
    && cd /var/opt/composer \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php --install-dir=/usr/bin --filename=composer \
    && rm composer-setup.php \
    && composer require "phpunit/phpunit:~6.1" --no-interaction \
    && ln -s /var/opt/composer/vendor/bin/phpunit /usr/local/bin/phpunit
    
# Xdebug
RUN apk add --no-cache autoconf gcc g++ libtool make \
    && pecl install xdebug-2.5.1 \
    && docker-php-ext-enable xdebug \
    && apk del autoconf gcc g++ libtool make \
    && rm -rf /tmp/* /var/cache/apk/*
    
# Codesniffer
RUN apk --no-cache add patch --repository http://dl-cdn.alpinelinux.org/alpine/edge/main/ \
    && pear install PHP_CodeSniffer-3.0.0RC4

# PHP MD
RUN apk add --no-cache bzip2-dev \
    && docker-php-ext-install bz2 \
    && curl -L http://static.phpmd.org/php/latest/phpmd.phar -o /usr/local/bin/phpmd \
    && chmod +x /usr/local/bin/phpmd
    
# PHPCPD
RUN curl -L https://phar.phpunit.de/phpcpd.phar -o /usr/local/bin/phpcpd \
    && chmod +x /usr/local/bin/phpcpd

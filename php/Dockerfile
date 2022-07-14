FROM php:7.1-fpm

#ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
ADD install-php-extensions /usr/local/bin/

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions bcmath bz2 calendar exif gd gettext gmp igbinary imagick memcached mongodb mysqli pcntl pdo_mysql pdo_pgsql pgsql redis shmop sockets swoole sysvmsg sysvsem sysvshm wddx xhprof xsl zip

RUN install-php-extensions @composer
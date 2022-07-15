FROM php:7.1-fpm

# 如果有网络问题，就连接到宿主机的代理地址
ENV http_proxy=http://172.16.17.245:7890
ENV https_proxy=http://172.16.17.245:7890

ENV DEBIAN_FRONTEND noninteractive

# 设置时区
#ENV TZ=Asia/Shanghai
#ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
#ADD install-php-extensions /usr/local/bin/

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

#RUN install-php-extensions @fix_letsencrypt
#
#RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
#    install-php-extensions bcmath bz2 calendar exif gd gettext gmp igbinary imagick memcached mongodb mysqli pcntl pdo_mysql pdo_pgsql pgsql redis shmop sockets swoole sysvmsg sysvsem sysvshm wddx xhprof xsl zip

#RUN chmod +x /usr/local/bin/install-php-extensions && \
#    install-php-extensions bcmath redis gd

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# Locales
RUN apt-get update \
	&& apt-get install -y locales

RUN dpkg-reconfigure locales \
	&& locale-gen C.UTF-8 \
	&& /usr/sbin/update-locale LANG=C.UTF-8

RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen \
	&& locale-gen

ENV LC_ALL C.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Common
RUN apt-get update \
	&& apt-get install -y \
		openssl \
		git \
		gnupg2

# compression
RUN apt-get update \
	&& apt-get install -y \
	libbz2-dev \
	zlib1g-dev \
	libzip-dev \
	&& docker-php-ext-install -j$(nproc) \
		zip \
		bz2

# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libyaml-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

# 安装 PECL 拓展，安装Redis，swoole
RUN pecl install redis \
#    && pecl install swoole \
    && pecl install psr \
    && pecl install mongodb \
    && pecl install grpc \
    && pecl install protobuf \
    && docker-php-ext-enable redis  psr mongodb grpc protobuf #swoole

# 增加 pgsql, pdo_pgsql 扩展
#RUN apt-get update && \
#apt-get install -y --no-install-recommends libpq-dev && \
#rm -r /var/lib/apt/lists/* && \
#docker-php-ext-install -j$(nproc) pgsql pdo_pgsql

# 安装 pdo_mysql 拓展
RUN docker-php-ext-install pdo_mysql \
    && docker-php-ext-enable pdo_mysql


# 安装 phalcon4.x 拓展
#RUN pecl install phalcon \
#    && docker-php-ext-enable phalcon

#这里版本号根据自己的需要更改
# 内存不够的话 在./configure 添加 --disable-fileinfo
RUN git clone https://github.com/phalcon/cphalcon.git \
    && cd cphalcon \
    && git checkout v3.1.2 \
    && cd build/php7/64bits \
    && phpize \
    && ./configure --disable-fileinfo --with-php-config=/usr/local/bin/php-config --enable-phalcon \
    && make \
    && make install \
    && docker-php-ext-enable phalcon

# 安装 yaml 拓展
#RUN pecl install yaml \
#    && docker-php-ext-enable yaml

# 安装 Composer
#RUN install-php-extensions @composer
# Install composer and put binary into $PATH
RUN curl -sS https://getcomposer.org/installer | php \
	&& mv composer.phar /usr/local/bin/ \
	&& ln -s /usr/local/bin/composer.phar /usr/local/bin/composer

WORKDIR /data

RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /var/cache/*
FROM php:7.4-fpm

MAINTAINER PGMAN <13538462645@163.com>

ENV DEBIAN_FRONTEND noninteractive

#COPY temp/bin /usr/local/bin/
#RUN chmod -R 700 /usr/local/bin/

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
    && pecl install swoole \
    && pecl install psr \
    && pecl install mongodb \
    && pecl install grpc \
    && pecl install protobuf \
    && docker-php-ext-enable redis swoole psr mongodb grpc protobuf

# 安装 phalcon4.x 拓展
RUN pecl install phalcon \
    && docker-php-ext-enable phalcon

# 安装 phalcon3.1.2 拓展 php7.4版本不确定能不能支持旧版本
#RUN git clone https://github.com/phalcon/cphalcon.git \
#    && cd cphalcon
#    && checkout v3.1.2
#    && cd build
#    && ./install --php-config /usr/bin/php-config
#    && docker-php-ext-enable phalcon

# 安装 yaml 拓展
RUN pecl install yaml \
    && docker-php-ext-enable yaml

# 安装 Composer
# Install composer and put binary into $PATH
RUN curl -sS https://getcomposer.org/installer | php \
	&& mv composer.phar /usr/local/bin/ \
	&& ln -s /usr/local/bin/composer.phar /usr/local/bin/composer

WORKDIR /data

# Clean
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /var/cache/*
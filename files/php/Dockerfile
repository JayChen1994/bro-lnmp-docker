FROM php:7.2.14-fpm

#  设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#  更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        git \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install zip \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install opcache \
        && docker-php-ext-install mysqli \
        && rm -r /var/lib/apt/lists/*

#  将预先下载好的拓展包从宿主机拷贝进去
COPY ./pkg/redis-5.0.1.tgz /usr/local/redis.tgz

#  安装 PECL 拓展，这里我们安装的是Redis
RUN pecl install /usr/local/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

COPY ./pkg/swoole-4.4.5.tgz /usr/local/swoole.tgz
RUN pecl install /usr/local/swoole.tgz && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini

#  安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH


WORKDIR /data


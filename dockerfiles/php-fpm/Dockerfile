FROM php:7.1.1-fpm-alpine

MAINTAINER isuzuki <ikkuntech@gmail.com>

RUN apk update && apk upgrade \
    && apk add \
        # for memcached
        autoconf \
        gcc \
        make \
        cyrus-sasl-dev \
        libc-dev \
        libmemcached-dev \
        zlib-dev

RUN docker-php-ext-install pdo_mysql mysqli

# Install the php memcached extension
RUN curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/php7.tar.gz" \
    && mkdir -p memcached \
    && tar -C memcached -zxvf /tmp/memcached.tar.gz --strip 1 \
    && ( \
        cd memcached \
        && phpize \
        && ./configure \
        && make -j$(nproc) \
        && make install \
    ) \
    && rm -r memcached \
    && rm /tmp/memcached.tar.gz \
    && docker-php-ext-enable memcached

RUN mkdir /var/run/php-fpm

ADD ./zz-app.conf /usr/local/etc/php-fpm.d/
ADD ./zz-docker.conf /usr/local/etc/php-fpm.d/

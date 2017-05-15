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

# Install composer and prestissimo
RUN curl -s http://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    composer global require hirak/prestissimo

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

# Install Blackfire Probe
# @see https://blackfire.io
RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire.so -D - -L -s https://packages.blackfire.io/binaries/blackfire-php/1.16.2/blackfire-php-alpine_amd64-php-$version.so \
    && mv /tmp/blackfire.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > $PHP_INI_DIR/conf.d/blackfire.ini

RUN mkdir /var/run/php-fpm

ADD ./zz-app.conf /usr/local/etc/php-fpm.d/
ADD ./zz-docker.conf /usr/local/etc/php-fpm.d/

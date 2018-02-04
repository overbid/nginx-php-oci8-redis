FROM php:fpm

LABEL maintainer="Eakkapat Pattarathamrong (overbid@gmail.com)"

ENV LD_LIBRARY_PATH /opt/oracle/instantclient_12_2
ENV ORACLE_HOME /opt/oracle/instantclient_12_2

RUN groupmod -g 500 www-data \
        && usermod -u 500 www-data

RUN set -x \
        && apt-get update \
        && apt-get -y upgrade \
        && apt-get install --no-install-recommends -y \
                libaio1 \
                libicu-dev \
                libjpeg62-turbo-dev \
                libfreetype6-dev \
                libmcrypt-dev \
                libpng-dev \
                git \
        && apt-get clean \
        && rm -rf /var/lib/apt/lists/*

ADD instantclient_12_2.tar.gz /opt/oracle/
RUN  set -x \
        && chmod +x /opt/oracle/instantclient_12_2/sqlplus \
        && ln -s /opt/oracle/instantclient_12_2/libclntsh.so.12.1 /opt/oracle/instantclient_12_2/libclntsh.so \
        && ln -s /opt/oracle/instantclient_12_2/libclntshcore.so.12.1 /opt/oracle/instantclient_12_2/libclntshcore.so \
        && ln -s /opt/oracle/instantclient_12_2/libocci.so.12.1 /opt/oracle/instantclient_12_2/libocci.so

RUN set -x \
        && docker-php-ext-configure \
                gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-configure \
                pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_2/,12.2 \
        && docker-php-ext-configure \
                intl \
        && docker-php-ext-install -j$(nproc) \
                bcmath \
                intl \
                gd \
                pdo_oci \
                # sockets \
                zip

RUN set -x \
        && echo 'instantclient,/opt/oracle/instantclient_12_2/' | pecl install \
                oci8 \
        && yes '' | pecl install -f \
                mcrypt

RUN set -x \
        && docker-php-ext-enable \
                mcrypt \
                oci8 \
                opcache

RUN set -x \
        && echo 'date.timezone = "Asia/Bangkok"' | tee /usr/local/etc/php/conf.d/locale.ini \
        && echo 'default_charset = "utf-8"' | tee -a /usr/local/etc/php/conf.d/locale.ini \
        && echo "cgi.fix_pathinfo = 1" | tee -a /usr/local/etc/php/conf.d/file.ini \
        && echo "upload_max_filesize = 8M" | tee -a /usr/local/etc/php/conf.d/file.ini

# COPY ./zz-socket.conf /usr/local/etc/php-fpm.d/zz-socket.conf

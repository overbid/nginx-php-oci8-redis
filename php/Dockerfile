FROM php:fpm

RUN apt-get update && apt-get -y upgrade
RUN apt-get install -y \
	libaio1 \
	wget \
	unzip

RUN mkdir /opt/oracle \
	&& cd /opt/oracle \
	&& wget https://s3.amazonaws.com/merofile/instantclient-basic-linux.x64-12.1.0.2.0.zip \
	&& wget https://s3.amazonaws.com/merofile/instantclient-sdk-linux.x64-12.1.0.2.0.zip \
	&& unzip /opt/oracle/instantclient-basic-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
	&& unzip /opt/oracle/instantclient-sdk-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
	&& ln -s /opt/oracle/instantclient_12_1/libclntsh.so.12.1 /opt/oracle/instantclient_12_1/libclntsh.so \
	&& ln -s /opt/oracle/instantclient_12_1/libclntshcore.so.12.1 /opt/oracle/instantclient_12_1/libclntshcore.so \
	&& ln -s /opt/oracle/instantclient_12_1/libocci.so.12.1 /opt/oracle/instantclient_12_1/libocci.so \
	&& rm -rf /opt/oracle/*.zip

RUN docker-php-ext-install pdo_mysql \
	&& pecl install redis \
    && pecl install xdebug \
 	&& echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8 \
    && docker-php-ext-enable \
    	redis \
    	xdebug \
    	oci8 \
    	opcache

RUN curl -sS https://getcomposer.org/installer | php -- \
	--install-dir=/usr/local/bin \
	--filename=composer

RUN apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

FROM php:7.3-fpm

LABEL maintainer="José Francisco Chacon <jose.fcts@gmail.com>"

RUN unlink /etc/localtime
RUN ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

# always run apt update when start and after add new source list, then clean up at end.
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    curl \
    procps \
    libmemcached-dev \
    libz-dev \
    libpq-dev \
    libjpeg-dev \
    libpng-dev \
    libzip-dev \
    libfreetype6-dev \
    libssl-dev \
    libmcrypt-dev \
    bash \
    git \
    unzip \
    wget \
    vim \
    zip \
    nano \
    ghostscript \
    supervisor \
  && rm -rf /var/lib/apt/lists/*

# RUN apt-get install bash

RUN docker-php-ext-install pdo_pgsql \
  && docker-php-ext-configure zip --with-libzip \
  && docker-php-ext-install zip \
  && docker-php-ext-install pdo_mysql \
  && docker-php-ext-configure gd \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 && \
    docker-php-ext-install gd

RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -

RUN apt update -yqq && \
    apt-get install -y nodejs

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# Optional Software's will only be installed if you set them to `true`
# in the `docker-compose.yml` before the build.
# Example:
#   - INSTALL_ZIP_ARCHIVE=true
#

###########################################################################
# SOAP:
###########################################################################

ARG INSTALL_SOAP=false

RUN if [ ${INSTALL_SOAP} = true ]; then \
    # Install the soap extension
    rm /etc/apt/preferences.d/no-debian-php && \
    apt-get -y install libxml2-dev php-soap && \
    docker-php-ext-install soap \
;fi

###########################################################################
# pgsql
###########################################################################

ARG INSTALL_PGSQL=false

RUN if [ ${INSTALL_PGSQL} = true ]; then \
    # Install the pgsql extension
    docker-php-ext-install pgsql \
;fi

###########################################################################
# xdebug:
###########################################################################

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    # Install the xdebug
	  pecl install xdebug; \
    docker-php-ext-enable xdebug \
;fi

# Copy xdebug configration
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

RUN sed -i "s/xdebug.remote_autostart=0/xdebug.remote_autostart=1/" /usr/local/etc/php/conf.d/xdebug.ini && \
    sed -i "s/xdebug.remote_enable=0/xdebug.remote_enable=1/" /usr/local/etc/php/conf.d/xdebug.ini && \
    sed -i "s/xdebug.cli_color=0/xdebug.cli_color=1/" /usr/local/etc/php/conf.d/xdebug.ini

###########################################################################
# PHP REDIS EXTENSION
###########################################################################

ARG INSTALL_PHPREDIS=false

RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    # Install Php Redis Extension
    printf "\n" | pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis \
;fi

###########################################################################
# MongoDB:
###########################################################################

ARG INSTALL_MONGO=false

RUN if [ ${INSTALL_MONGO} = true ]; then \
    # Install the mongodb extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl install mongo && \
      docker-php-ext-enable mongo \
    ;fi && \
    pecl install mongodb && \
    docker-php-ext-enable mongodb \
;fi

###########################################################################
# ZipArchive:
###########################################################################

ARG INSTALL_ZIP_ARCHIVE=false

RUN if [ ${INSTALL_ZIP_ARCHIVE} = true ]; then \
    apt-get install libzip-dev -y && \
    docker-php-ext-configure zip --with-libzip && \
    # Install the zip extension
    docker-php-ext-install zip \
;fi

###########################################################################
# pcntl
###########################################################################

ARG INSTALL_PCNTL=false
RUN if [ ${INSTALL_PCNTL} = true ]; then \
    # Installs pcntl, helpful for running Horizon
    docker-php-ext-install pcntl \
;fi

###########################################################################
# bcmath:
###########################################################################

ARG INSTALL_BCMATH=false

RUN if [ ${INSTALL_BCMATH} = true ]; then \
    # Install the bcmath extension
    docker-php-ext-install bcmath \
;fi

###########################################################################
# GMP (GNU Multiple Precision):
###########################################################################

ARG INSTALL_GMP=false

RUN if [ ${INSTALL_GMP} = true ]; then \
    # Install the GMP extension
    apt-get update && \
	  apt-get install -y libgmp-dev && \
    docker-php-ext-install gmp \
;fi

###########################################################################
# PHP Memcached:
###########################################################################Fintl

ARG INSTALL_MEMCACHED=false

RUN if [ ${INSTALL_MEMCACHED} = true ]; then \
    # Install the php memcached extension
    curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/2.2.0.tar.gz" \
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
    && docker-php-ext-enable memcached \
;fi

###########################################################################
# Exif:
###########################################################################

ARG INSTALL_EXIF=false

RUN if [ ${INSTALL_EXIF} = true ]; then \
    # Enable Exif PHP extentions requirements
    docker-php-ext-install exif \
;fi

###########################################################################
# Opcache:
###########################################################################

ARG INSTALL_OPCACHE=false

RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache \
;fi

# Copy opcache configration
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

###########################################################################
# Human Language and Character Encoding Support:
###########################################################################

ARG INSTALL_INTL=false

RUN if [ ${INSTALL_INTL} = true ]; then \
    # Install intl and requirements
    apt-get install -y zlib1g-dev libicu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl \
;fi

###########################################################################
# Mysqli Modifications:
###########################################################################

ARG INSTALL_MYSQLI=false

RUN if [ ${INSTALL_MYSQLI} = true ]; then \
    docker-php-ext-install mysqli \
;fi


###########################################################################
# LDAP:
###########################################################################

ARG INSTALL_LDAP=false

RUN if [ ${INSTALL_LDAP} = true ]; then \
    apt-get install -y libldap2-dev && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap \
;fi

###########################################################################
# Image optimizers:
###########################################################################

USER root

ARG INSTALL_IMAGE_OPTIMIZERS=false

RUN if [ ${INSTALL_IMAGE_OPTIMIZERS} = true ]; then \
    apt-get install -y jpegoptim optipng pngquant gifsicle \
;fi

###########################################################################
# ImageMagick:
###########################################################################

USER root

ARG INSTALL_IMAGEMAGICK=false

RUN if [ ${INSTALL_IMAGEMAGICK} = true ]; then \
    apt-get install -y libmagickwand-dev imagemagick && \
    pecl install imagick && \
    docker-php-ext-enable imagick \
;fi

###########################################################################
# IMAP:
###########################################################################

ARG INSTALL_IMAP=false

RUN if [ ${INSTALL_IMAP} = true ]; then \
    apt-get install -y libc-client-dev libkrb5-dev && \
    rm -r /var/lib/apt/lists/* && \
    docker-php-ext-configure imap --with-kerberos --with-imap-ssl && \
    docker-php-ext-install imap \
;fi

###########################################################################
# Calendar:
###########################################################################

USER root

ARG INSTALL_CALENDAR=false

RUN if [ ${INSTALL_CALENDAR} = true ]; then \
    docker-php-ext-configure calendar && \
    docker-php-ext-install calendar \
;fi

###########################################################################
# YAML:
###########################################################################

USER root

ARG INSTALL_YAML=false

RUN if [ ${INSTALL_YAML} = true ]; then \
    apt-get install libyaml-dev -y ; \
    pecl install yaml ; \
    docker-php-ext-enable yaml \
;fi

###########################################################################
# GRPC:
###########################################################################

ARG INSTALL_GRPC=false

RUN if [ ${INSTALL_GRPC} = true ]; then \
     pecl install grpc \
     docker-php-ext-enable grpc \
;fi

###########################################################################
# COMPOSER:
###########################################################################

USER root

ARG INSTALL_COMPOSER=false

RUN if [ ${INSTALL_COMPOSER} = true ]; then \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    printf "\nPATH=\"~/.composer/vendor/bin:\$PATH\"\n" | tee -a ~/.bashrc \
;fi

RUN composer global require "squizlabs/php_codesniffer=*"

###########################################################################
# Check PHP version:
###########################################################################

RUN php -v | head -n 1 | grep -q "PHP 7.3"

###########################################################################
# Configure supervisor:
###########################################################################

COPY ./laravel-worker.conf /etc/supervisor/conf.d
RUN mkdir /var/log/supervisor/gateway
RUN echo 'alias startsupervisor="supervisord -c /etc/supervisor/supervisord.conf"' >> ~/.bashrc

###########################################################################
# Configure npm modules for laravel queues:
###########################################################################

RUN npm install -g uuid
RUN npm install -g node-rsa
RUN npm install -g randomstring
RUN npm install -g minimist

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#
COPY ./laravel.ini /usr/local/etc/php/conf.d

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

WORKDIR /var/www
EXPOSE 9000

ARG PHP_VERSION=${PHP_VERSION}

FROM php:${PHP_VERSION}-fpm

LABEL maintainer="Loki <xiezhaofei@locker.top>"

# 通用配置
RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's|security.debian.org/debian-security|mirrors.aliyun.com/debian-security|g' /etc/apt/sources.list && \
    cat /etc/apt/sources.list && \
    rm /etc/apt/sources.list.d/buster.list && \
    apt-get update -yqq && \
    apt-get install -y cron procps && \
    pecl channel-update pecl.php.net

###########################################################################
# SOAP:
###########################################################################

ARG INSTALL_SOAP=false

RUN if [ ${INSTALL_SOAP} = true ]; then \
    # Install the soap extension
    apt-get -y install libxml2-dev php-soap && \
    docker-php-ext-install soap \
    ;fi

###########################################################################
# bcmath:
###########################################################################

ARG INSTALL_BCMATH=false

RUN if [ ${INSTALL_BCMATH} = true ]; then \
    docker-php-ext-install bcmath \
    ;fi

###########################################################################
# Exif:
###########################################################################

ARG INSTALL_EXIF=false

RUN if [ ${INSTALL_EXIF} = true ]; then \
    docker-php-ext-install exif \
    ;fi

###########################################################################
# MCrypt:
###########################################################################

ARG INSTALL_MCRYPT=false

RUN if [ ${INSTALL_MCRYPT} = true ]; then \
    apt-get install -y libmcrypt-dev && \
    pecl install mcrypt-1.0.1 && \
    docker-php-ext-enable mcrypt \
    ;fi

###########################################################################
# GHOSTSCRIPT:
###########################################################################

ARG INSTALL_GHOSTSCRIPT=false

RUN if [ ${INSTALL_GHOSTSCRIPT} = true ]; then \
    # Install the ghostscript extension
    # for PDF editing
    apt-get install -y \
    poppler-utils \
    zlib1g-dev \
    libpng-dev libxpm-dev\
    libwebp-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    ghostscript && \
    docker-php-ext-configure gd \
    --with-gd \
    --with-webp-dir \
    --with-jpeg-dir \
    --with-png-dir \
    --with-zlib-dir \
    --with-xpm-dir \
    --with-freetype-dir \
    --enable-gd-native-ttf && \
    docker-php-ext-install gd \
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
# Human Language and Character Encoding Support:
###########################################################################

ARG INSTALL_INTL=false

RUN if [ ${INSTALL_INTL} = true ]; then \
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
# ZipArchive:
###########################################################################

ARG INSTALL_ZIP_ARCHIVE=false

RUN if [ ${INSTALL_ZIP_ARCHIVE} = true ]; then \
    # Install the zip extension
    docker-php-ext-install zip \
    ;fi

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
# Opcache:
###########################################################################

ARG INSTALL_OPCACHE=false

RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache \
    ;fi

# Copy opcache configration
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

###########################################################################
# Check PHP version:
###########################################################################

RUN docker-php-ext-install pdo_mysql

ARG PHP_VERSION=${PHP_VERSION}

RUN php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

COPY ./tp.ini /usr/local/etc/php/conf.d
RUN rm /usr/local/etc/php-fpm.d/www.conf
COPY ./www.conf /usr/local/etc/php-fpm.d/www.conf

USER root

RUN chown -R www-data:www-data /var/www

VOLUME [ "/var/www" ]

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
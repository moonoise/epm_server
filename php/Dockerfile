FROM php:7.4-apache

ENV APACHE_DOCUMENT_ROOT=/var/www/html/

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf # stack overflow thinks this is comment */
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf # stack overflow thinks this is comment */

RUN apt-get update && apt-get install -qqy git unzip libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libaio1 wget && apt-get clean autoclean && apt-get autoremove --yes &&  rm -rf /var/lib/{apt,dpkg,cache,log}/

# composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir /opt/oracle \
    && cd /opt/oracle

ADD oracle/instantclient-basic-linux.x64-19.6.0.0.0dbru.zip /opt/oracle
ADD oracle/instantclient-sdk-linux.x64-19.6.0.0.0dbru.zip /opt/oracle

# Install Oracle Instantclient
RUN  unzip /opt/oracle/instantclient-basic-linux.x64-19.6.0.0.0dbru.zip -d /opt/oracle \
    && unzip /opt/oracle/instantclient-sdk-linux.x64-19.6.0.0.0dbru.zip -d /opt/oracle \
    && rm -rf /opt/oracle/*.zip # stack overflow thinks this is comment */

ENV LD_LIBRARY_PATH  /opt/oracle/instantclient_19_6:${LD_LIBRARY_PATH}

# Install Oracle extensions
RUN echo 'instantclient,/opt/oracle/instantclient_19_6/' | pecl install oci8 \
      && docker-php-ext-enable \
               oci8 \
       && docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_19_6,19.6 \
       && docker-php-ext-install \
               pdo_oci 

# git
RUN apt-get update && apt-get install -y git
RUN apt-get install -y \
        freetds-bin \
        freetds-dev \
        freetds-common \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        zlib1g-dev \
        libxml2-dev \
        libzip-dev \
        libonig-dev \
        sendmail \
        graphviz

RUN ln -s /usr/lib/x86_64-linux-gnu/libsybdb.a /usr/lib/
# RUN ln -s /usr/lib/ x86_64-pc-linux-gnu/libsybdb.a /usr/lib/

RUN  docker-php-ext-install \
                iconv \
                mbstring \
                intl \
                gd \
                mysqli \
                pdo_mysql \
                pdo_dblib \
                sockets \
                zip \
                pcntl \
        && docker-php-ext-enable \
                oci8 \
                pdo_oci

RUN a2enmod rewrite

# xdebug
#RUN pecl install xdebug
#RUN docker-php-ext-enable xdebug

# Install Node.js
RUN apt-get install --yes curl
RUN curl -sL https://deb.nodesource.com/setup_13.x | bash -
RUN apt-get install -y nodejs
RUN apt-get install --yes build-essential


# addrs
RUN mkdir -p /var/www/html/application/cache
RUN chown www-data:www-data /var/www/html/application/cache

RUN mkdir -p /var/www/html/application/logs
RUN chown www-data:www-data /var/www/html/application/logs


EXPOSE 80
EXPOSE 3306

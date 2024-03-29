FROM php:7.2.8-fpm

COPY ./sources.list.stretch /etc/apt/sources.list

RUN apt-get update \
    && apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && :\
    && apt-get install -y libicu-dev \
    && docker-php-ext-install intl \
    && :\
    && apt-get install -y libxml2-dev \
    && apt-get install -y libxslt-dev \
    && docker-php-ext-install soap \
    && docker-php-ext-install xsl \
    && docker-php-ext-install xmlrpc \
    && docker-php-ext-install wddx \
    && :\
    && apt-get install -y libbz2-dev \
    && docker-php-ext-install bz2 \
    && :\
    #&& apt-get install -y ruby zlib1g zlib1g.dev \
    && docker-php-ext-install zip \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install exif \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install calendar \
    && docker-php-ext-install sockets \
    && docker-php-ext-install gettext \
    && docker-php-ext-install shmop \
    && docker-php-ext-install sysvmsg \
    && docker-php-ext-install sysvsem \
    && docker-php-ext-install sysvshm \
    && docker-php-ext-install opcache \
    # && pecl install xdebug \
    # && docker-php-ext-enable xdebug \
    # imagick
    && apt-get install -y libmagickwand-dev imagemagick \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    #&& docker-php-ext-install pdo_firebird \
    #&& docker-php-ext-install pdo_dblib \
    #&& docker-php-ext-install pdo_oci \
    #&& docker-php-ext-install pdo_odbc \
    #&& docker-php-ext-install pdo_pgsql \
    #&& docker-php-ext-install pgsql \
    #&& docker-php-ext-install oci8 \
    #&& docker-php-ext-install odbc \
    #&& docker-php-ext-install dba \
    #&& docker-php-ext-install interbase \
    #&& :\
    #&& apt-get install -y libmcrypt-dev \
    #&& docker-php-ext-install mcrypt \
    #&& :\
    #&& apt-get install -y curl \
    #&& apt-get install -y libcurl3 \
    #&& apt-get install -y libcurl4-openssl-dev \
    #&& docker-php-ext-install curl \
    #&& :\
    #&& apt-get install -y libreadline-dev \
    #&& docker-php-ext-install readline \
    && :\
    && apt-get install -y libsnmp-dev \
    && apt-get install -y snmp \
    && docker-php-ext-install snmp \
    #&& :\
    #&& apt-get install -y libpspell-dev \
    #&& apt-get install -y aspell-en \
    #&& docker-php-ext-install pspell \
    #&& :\
    #&& apt-get install -y librecode0 \
    #&& apt-get install -y librecode-dev \
    #&& docker-php-ext-install recode \
    #&& :\
    #&& apt-get install -y libtidy-dev \
    #&& docker-php-ext-install tidy \
    #&& :\
    #&& apt-get install -y libgmp-dev \
    #&& ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
    #&& docker-php-ext-install gmp \
    #&& :\
    #&& apt-get install -y postgresql-client \
    #&& apt-get install -y mysql-client \
    #&& :\
    #&& apt-get install -y libc-client-dev \
    #&& docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    #&& docker-php-ext-install imap \
    #&& :\
    #&& apt-get install -y libldb-dev \
    #&& apt-get install -y libldap2-dev \
    #&& docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu \
    #&& docker-php-ext-install ldap \
    #&& pecl install xdebug-2.6.1 \
    #&& docker-php-ext-enable xdebug \
    # clean
    && apt-get clean \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Composer
RUN php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /bin/composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com

# Install Redis extension from source
COPY ./redis-4.1.0.tgz /tmp/redis.tgz
RUN ls /tmp
RUN mkdir -p /tmp/redis \
    && tar -xf /tmp/redis.tgz -C /tmp/redis --strip-components=1 \
    && rm /tmp/redis.tgz \
    && docker-php-ext-configure /tmp/redis --enable-redis \
    && docker-php-ext-install /tmp/redis \
    && rm -r /tmp/redis

# Permission
RUN mkdir -p /usr/share/nginx/html \
    && chown -R www-data.www-data /usr/share/nginx/html \
    && chown www-data.www-data /var/www \
    && chmod 755 /var/www/

# Cron Supervisor
RUN apt-get update && apt-get install -y supervisor && apt-get install -y cron && apt-get clean
RUN mkdir -p /var/log/supervisor && chmod -R 755 /var/log/supervisor && cat /dev/null > /var/log/cron.log

COPY ./cron/gaea /var/spool/cron/crontabs/
# ADD crontab /var/spool/cron/crontabs/gaea
# RUN chmod 0644 /var/spool/cron/crontabs/gaea
# ENTRYPOINT [ "/usr/sbin/cron", "-f", "/var/spool/crontabs/gaea" ]
RUN cat /var/spool/cron/crontabs/gaea >> /etc/crontab
RUN crontab /var/spool/cron/crontabs/gaea

COPY ./supervisor/supervisord.conf /etc/supervisor/supervisord.conf
ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c",  "/etc/supervisor/supervisord.conf"]

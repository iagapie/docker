FROM php:7.2-fpm as base

MAINTAINER Igor Agapie <igoragapie@gmail.com>

RUN set -ex \
	&& chown -Rf www-data:www-data /var/www \
	\
	&& mkdir -p /usr/share/man/man1 \
	&& mkdir -p /usr/share/man/man7 \
	\
	&& apt-get update \
	&& apt-get upgrade -y --no-install-recommends \
	&& apt-get install -y --no-install-recommends \
		bash-completion \
		vim \
		nano \
		unzip \
		mysql-client \
		postgresql-client \
		openssh-client \
		rsync \
		procps \
		psmisc \
		net-tools \
		iputils-ping \
		netcat \
		snmp \
		git \
		dirmngr \
		gnupg \
		\
		libc-client-dev \
		libicu-dev \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libpng-dev \
		libxslt1-dev \
		libmemcached-dev \
		zlib1g-dev \
		libmcrypt-dev \
		unixodbc-dev \
		libssh2-1-dev \
		libzip-dev \
		libenchant-dev \
		libgmp-dev \
		libkrb5-dev \
		libpq-dev \
		libsnmp-dev \
		libldb-dev \
		libldap2-dev \
		libsqlite3-dev \
		libbz2-dev \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
	&& rm -r /var/lib/apt/lists/* \
	\
	&& echo 'if [[ -f "/etc/bash_completion" ]]; then' >> /etc/profile \
	&& echo '    . /etc/bash_completion' >> /etc/profile \
	&& echo 'fi' >> /etc/profile

# Install PHP Extensions

RUN set -ex \
	&& docker-php-source extract \
	&& { \
        echo '# https://github.com/docker-library/php/issues/103#issuecomment-271413933'; \
        echo 'AC_DEFUN([PHP_ALWAYS_SHARED],[])dnl'; \
        echo; \
        cat /usr/src/php/ext/odbc/config.m4; \
    } > temp.m4 \
    && mv temp.m4 /usr/src/php/ext/odbc/config.m4 \
	&& docker-php-ext-install -j$(nproc) \
		iconv \
		bcmath \
		opcache \
		snmp \
		intl \
		soap \
		xsl \
		zip \
		bz2 \
		calendar \
		dba \
		enchant \
		exif \
		gettext \
		gmp \
		mysqli \
		pgsql \
		pdo_mysql \
		pdo_pgsql \
		pcntl \
		sockets \
		wddx \
		xmlrpc \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd \
	&& docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
	&& docker-php-ext-install -j$(nproc) imap \
	&& docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
	&& docker-php-ext-install -j$(nproc) ldap \
	&& docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr \
	&& docker-php-ext-install -j$(nproc) pdo_odbc \
	&& docker-php-ext-configure odbc --with-unixODBC=shared,/usr \
	&& docker-php-ext-install -j$(nproc) odbc

RUN set -ex \
	&& pecl update-channels \
	&& pecl install \
		mcrypt-1.0.2 \
		memcached \
		apcu \
		redis \
		oauth \
		xdebug \
		ssh2-1.1.2 \
	&& docker-php-ext-enable \
		mcrypt \
		memcached \
		apcu \
		redis \
		oauth \
		ssh2 \
	&& echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.iniOLD \
	&& rm -rf /tmp/pear ~/.pearrc \
	&& docker-php-source delete

# Xdebug Config

ENV PHP_XDEBUG_REMOTE_HOST=localhost \
	PHP_XDEBUG_REMOTE_PORT=9000 \
	PHP_XDEBUG_IDEKEY=IDEKEY

RUN set -ex \
	&& echo '#!/bin/sh' >> /usr/local/bin/xdebug \
	&& echo 'set -e' >> /usr/local/bin/xdebug \
	&& echo '' >> /usr/local/bin/xdebug \
	&& echo 'if [ -s "/usr/local/etc/php/conf.d/xdebug.ini" ]; then' >> /usr/local/bin/xdebug \
	&& echo '    mv /usr/local/etc/php/conf.d/xdebug.ini /usr/local/etc/php/conf.d/xdebug.iniOLD \' >> /usr/local/bin/xdebug \
	&& echo '    && kill -USR2 1 \' >> /usr/local/bin/xdebug \
	&& echo '    && echo "========= XDebug was disabled ========="' >> /usr/local/bin/xdebug \
	&& echo 'else' >> /usr/local/bin/xdebug \
	&& echo '    mv /usr/local/etc/php/conf.d/xdebug.iniOLD /usr/local/etc/php/conf.d/xdebug.ini \' >> /usr/local/bin/xdebug \
	&& echo '    && kill -USR2 1 \' >> /usr/local/bin/xdebug \
	&& echo '    && echo "========= XDebug was enabled ========="' >> /usr/local/bin/xdebug \
	&& echo 'fi' >> /usr/local/bin/xdebug \
	&& chmod +x /usr/local/bin/xdebug \
	&& echo 'xdebug.default_enable=1' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.remote_autostart=1' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.remote_enable=1' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.remote_handler=dbgp' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.profiler_enable=0' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.profiler_output_dir="/var/www/html"' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.remote_connect_back=1' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.cli_color=1' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.var_display_max_depth=10' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.remote_host=${PHP_XDEBUG_REMOTE_HOST}' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.remote_port=${PHP_XDEBUG_REMOTE_PORT}' >> /usr/local/etc/php/conf.d/custom-xdebug.ini \
	&& echo 'xdebug.idekey=${PHP_XDEBUG_IDEKEY}' >> /usr/local/etc/php/conf.d/custom-xdebug.ini

# Install Composer

RUN set -ex \
	&& curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer

USER www-data

RUN set -ex \
	&& mkdir -p /var/www/.composer \
	&& composer g require \
		hirak/prestissimo \
		psy/psysh:@stable \
	&& rm -rf /var/www/.composer/cache/*

ENV PATH="/var/www/.composer/vendor/bin:${PATH}"

USER root

# Install Node, NPM, Grunt and Yarn

RUN set -ex \
	&& curl -sL https://deb.nodesource.com/setup_10.x | bash - \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends nodejs \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
	&& rm -r /var/lib/apt/lists/* \
    && npm i -g grunt-cli yarn

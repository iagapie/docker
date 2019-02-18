FROM iagapie/php:7.3-fpm

MAINTAINER Igor Agapie <igoragapie@gmail.com>

RUN set -ex \
	&& groupmod -g 1000 www-data \
	&& usermod -u 1000 www-data \
	&& chown -Rf www-data:www-data /var/www

FROM iagapie/httpd:2.4.35

MAINTAINER Igor Agapie <igoragapie@gmail.com>

RUN set -ex \
	&& groupmod -g 1000 www-data \
	&& usermod -u 1000 www-data \
	&& chown www-data:www-data "$HTTPD_PREFIX" \
	&& chown -Rf www-data:www-data /var/www

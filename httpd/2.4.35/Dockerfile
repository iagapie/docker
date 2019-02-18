FROM httpd:2.4.35

MAINTAINER Igor Agapie <igoragapie@gmail.com>

ENV FPM_HOST=php-fpm
ENV FPM_PORT=9000
ENV DOCROOT=/var/www/html

RUN set -ex \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends \
		openssl \
		vim \
		nano \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
	&& rm -r /var/lib/apt/lists/*

RUN set -ex \
	&& mkdir -p ${DOCROOT} \
	&& cp $HTTPD_PREFIX/htdocs/* ${DOCROOT}/ \
	&& chown -R www-data:www-data /var/www \
	&& chmod -R 777 /var/www

RUN set -ex \
	&& sed -i \
		-e 's/^#\(Include .*httpd-ssl.conf\)/\1/' \
        	-e 's/^#\(Include .*httpd-default.conf\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_ssl.so\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_socache_shmcb.so\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_rewrite.so\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_proxy.so\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_proxy_fcgi.so\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_deflate.so\)/\1/' \
        	-e 's/^#\(LoadModule .*mod_mime_magic.so\)/\1/' \
        	-e 's/User daemon/User www-data/' \
        	-e 's/Group daemon/Group www-data/' \
        	-e 's/DirectoryIndex index.html/DirectoryIndex index.php index.html/' \
        	-e 's#/usr/local/apache2/htdocs#${DOCROOT}#g' \
        	-e '/<Directory "${DOCROOT}">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' \
        	conf/httpd.conf

RUN set -ex \
	&& sed -i \
		-e 's#/usr/local/apache2/htdocs#${DOCROOT}#g' \
		-e 's/ServerName/#ServerName/' \
		-e 's/ServerAdmin/#ServerAdmin/' \
		conf/extra/httpd-ssl.conf

RUN set -ex \
	&& echo '<IfModule proxy_fcgi_module>' >> conf/httpd.conf \ 
	&& echo '	ProxyPassMatch ^/(.*\.php(/.*)?)$ fcgi://${FPM_HOST}:${FPM_PORT}${DOCROOT}/$1' >> conf/httpd.conf \
	&& echo '	<Directory "${DOCROOT}">' >> conf/httpd.conf \
	&& echo '		<FilesMatch ".+(\.php)$">' >> conf/httpd.conf \
	&& echo '			SetHandler "proxy:fcgi://${FPM_HOST}:${FPM_PORT}"' >> conf/httpd.conf \
	&& echo '		</FilesMatch>' >> conf/httpd.conf \
	&& echo '	</Directory>' >> conf/httpd.conf \
	&& echo '	<Proxy "fcgi://${FPM_HOST}:${FPM_PORT}">' >> conf/httpd.conf \
	&& echo '		ProxySet connectiontimeout=5 timeout=7200' >> conf/httpd.conf \
	&& echo '	</Proxy>' >> conf/httpd.conf \
	&& echo '</IfModule>' >> conf/httpd.conf

RUN set -ex \
	&& touch conf/extra/httpd-extra.conf \
	&& echo 'Include conf/extra/httpd-extra.conf' >> conf/httpd.conf

RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
	-keyout conf/server.key \
	-out conf/server.crt \
	-subj "/C=IT/ST=Rome/L=Rome/O=It OrgName/OU=IT Department/CN=localhost"

RUN chown www-data:www-data conf/server.*

WORKDIR ${DOCROOT}

EXPOSE 80 443

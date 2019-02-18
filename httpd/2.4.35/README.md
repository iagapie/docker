# Apache 2.4.35

### Docker image
```
# docker pull iagapie/httpd:2.4.35
```

### Environment variables
    - FPM_HOST [Default: php-fpm]
    - FPM_PORT [Default: 9000]
    - DOCROOT [Default: /var/www/html]

### SSL Path
```
# /usr/local/apache2/conf/server.{crt,key}
```

### Generate self signed certificate
```
# openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout [CRT PATH]/server.key \
    -out [CRT PATH]/server.crt \
    -subj "/C=IT/ST=Rome/L=Rome/O=It OrgName/OU=IT Department/CN=[DOMAIN NAME]"
```

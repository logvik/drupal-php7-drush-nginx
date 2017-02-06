FROM hyperknot/baseimage16:latest

MAINTAINER Viktorov Konstantin "e@logvik.com"
ENV DEBIAN_FRONTEND noninteractive

# Ensure UTF-8
RUN locale-gen en_US.UTF-8
ENV LANG       en_US.UTF-8
ENV LC_ALL     en_US.UTF-8

# Update system
RUN apt-get update && apt-get dist-upgrade -y
RUN apt-get install -y nano

# Prevent restarts when installing
RUN echo '#!/bin/sh\nexit 101' > /usr/sbin/policy-rc.d && chmod +x /usr/sbin/policy-rc.d

# Basic packages
RUN apt-get -y install apt-utils php7.0-dev php7.0-fpm php7.0-mysql php7.0-imap php7.0-mcrypt php7.0-curl php7.0-cli php7.0-gd php7.0-pgsql php7.0-sqlite php7.0-common php7.0-json php7.0-zip
RUN pecl channel-update pecl.php.net
RUN pecl install apcu imagick pear redis memcache
RUN apt-get -y install nginx-extras git curl supervisor
RUN apt-get -y install mailutils

RUN /usr/bin/curl -sS https://getcomposer.org/installer | /usr/bin/php
RUN /bin/mv composer.phar /usr/local/bin/composer
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Composer and Drush
RUN /usr/local/bin/composer self-update
RUN /usr/local/bin/composer global require drush/drush:8.*
RUN ln -s /root/.composer/vendor/drush/drush/drush /usr/local/bin/drush

# Prepare directory
RUN mkdir -p /var/www
RUN chown -R www-data:www-data /var/www

EXPOSE 80
EXPOSE 443
WORKDIR /var/www
CMD ["/usr/bin/supervisord", "-n"]

ADD ./cron.sh /opt/cron.sh
RUN chmod +x /opt/cron.sh

RUN mkdir -p /var/cache/nginx/microcache

#Create init directories
RUN service php7.0-fpm start

# Define mountable directories.
VOLUME ["/opt/app", "/opt/nginx", "/opt/ssl", "/opt/logs"]

### Add configuration files
# Supervisor
ADD ./config/supervisor/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# PHP
ADD ./config/php/www.conf /etc/php7.0/fpm/pool.d/www.conf
ADD ./config/php/php.ini /etc/php7.0/fpm/php.ini
ADD ./config/php/php-fpm.conf /etc/php7.0/fpm/php-fpm.conf

# NGINX
ADD ./config/nginx/blacklist.conf /etc/nginx/blacklist.conf
ADD ./config/nginx/cron_allowed_hosts.conf /etc/nginx/cron_allowed_hosts.conf
RUN mkdir -p /etc/nginx/sites-enabled
ADD ./config/nginx/default /etc/nginx/sites-enabled/default
ADD ./config/nginx/drupal.conf /etc/nginx/drupal.conf
ADD ./config/nginx/drupal_upload_progress.conf /etc/nginx/drupal_upload_progress.conf
ADD ./config/nginx/fastcgi.conf /etc/nginx/fastcgi.conf
ADD ./config/nginx/fastcgi_drupal.conf /etc/nginx/fastcgi_drupal.conf
ADD ./config/nginx/fastcgi_microcache_zone.conf /etc/nginx/fastcgi_microcache_zone.conf
ADD ./config/nginx/fastcgi_no_args_drupal.conf /etc/nginx/fastcgi_no_args_drupal.conf
ADD ./config/nginx/map_block_http_methods.conf /etc/nginx/map_block_http_methods.conf
ADD ./config/nginx/map_cache.conf /etc/nginx/map_cache.conf
ADD ./config/nginx/map_https_fcgi.conf /etc/nginx/map_https_fcgi.conf
ADD ./config/nginx/microcache_fcgi.conf /etc/nginx/microcache_fcgi.conf
ADD ./config/nginx/microcache_fcgi_auth.conf /etc/nginx/microcache_fcgi_auth.conf
ADD ./config/nginx/mime.types /etc/nginx/mime.types
ADD ./config/nginx/nginx.conf /etc/nginx/nginx.conf
ADD ./config/nginx/nginx_status_allowed_hosts.conf /etc/nginx/nginx_status_allowed_hosts.conf
ADD ./config/nginx/php_fpm_status_allowed_hosts.conf /etc/nginx/php_fpm_status_allowed_hosts.conf
ADD ./config/nginx/upstream_phpcgi_unix.conf /etc/nginx/upstream_phpcgi_unix.conf
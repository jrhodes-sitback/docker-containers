# PHP 5.6 container, optimised for Continuous Integration.

FROM chinthakagodawita/soe:php5.6
MAINTAINER Chinthaka Godawita <chin.godawita@me.com>

# Install all packages.
RUN apt-get update

## MySQL.
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y mysql-server

# Configure MySQL (installed with user:root and no password).
RUN mysql_install_db
ADD ./conf/mysql/ci_overrides.cnf /etc/mysql/conf.d/ci_overrides.cnf

# Log to stdout/stderr.
RUN ln -sfv /dev/stdout /var/log/mysql/mysql.log
RUN ln -sfv /dev/stderr /var/log/mysql/error.log

# PHPCS & Coder.
RUN composer global require drupal/coder
RUN phpcs --config-set installed_paths \
  ~/.composer/vendor/drupal/coder/coder_sniffer

# Startup settings.
ADD ./conf/supervisor/ci.conf /etc/supervisor/conf.d/ci.conf

# Prevent socat from taking over the MySQL socket.
RUN perl -pi -e 'BEGIN{undef $/;} s/(\[program:socat.*?autostart=)true/$1false/sgm' /etc/supervisor/conf.d/soe.conf

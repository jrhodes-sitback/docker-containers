# Standard Operating Environment running off Ubuntu 14.04 & PHP 5.6.

FROM chinthakagodawita/base:ubuntu-14.04
MAINTAINER Chinthaka Godawita <chin.godawita@me.com>

# Install all packages.
RUN apt-get update

## Socat
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install socat

## Apache.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install apache2

## Memcached.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install memcached

## MySQL client.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install mysql-client

## SSMTP.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install ssmtp

# Configure socat.
ADD ./conf/socat/start.sh /root/socat-start.sh
RUN chmod +x /root/socat-start.sh

# Configure Apache.
COPY ./conf/apache2/httpd-vhosts.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod vhost_alias
RUN a2enmod rewrite
RUN a2enmod ssl
RUN a2enmod headers

# @TODO: Fix to be .docker.
# *.dev certificates
RUN mkdir /root/certs
COPY ./conf/certs/dev-self-signed-ssl.crt /root/certs/dev-self-signed-ssl.crt
COPY ./conf/certs/dev-self-signed-ssl.key /root/certs/dev-self-signed-ssl.key

# Fix issue with SSLMutex.
RUN mkdir -p /var/run/apache2

# PHP.
RUN add-apt-repository ppa:ondrej/php5-5.6
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install \
  php5 \
  php5-gd \
  php5-dev \
  php5-curl \
  php5-mcrypt \
  php5-mysql \
  php5-xdebug \
  php5-memcached \
  php-soap \
  php-pear

# Configure PHP.
ADD ./conf/php/30-overrides.ini /etc/php5/apache2/conf.d/30-overrides.ini

# Enable PHP modules.
RUN php5enmod opcache

# Setup Xdebug.
ADD ./conf/php/30-xdebug-conf.ini /etc/php5/apache2/conf.d/30-xdebug-conf.ini

# Install Composer.
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install drush.
RUN composer global require drush/drush:7.*

# Install PimpMyLog.
RUN mkdir -p /server/pimpmylog
RUN composer require -d /server/pimpmylog "potsky/pimp-my-log"
RUN chmod -R 777 /server/pimpmylog/vendor/potsky/pimp-my-log
ADD ./conf/pimpmylog/config.user.json /server/pimpmylog/config.user.json
ADD ./conf/apache2/pimpmylog.conf /etc/apache2/sites-available/pimpmylog.conf
RUN a2ensite pimpmylog

# Add apache user to the adm group so they can read logs.
RUN usermod -a -G adm www-data

# Update PATH to include Composer's bin (also add into /etc/profile so that our
# tests pick it up too).
ENV COMPOSER_BIN /root/.composer/vendor/bin
ENV PATH "$COMPOSER_BIN:$PATH"
RUN echo "" >> /etc/profile
RUN echo "PATH=$COMPOSER_BIN:\$PATH" >> /etc/profile

# Install MailHog & MailHog's sendmail replacement.
RUN curl -s -L https://github.com/mailhog/MailHog/releases/latest | egrep -o '/mailhog/MailHog/releases/download/[v.0-9]*/MailHog_linux_amd64' | wget --base=http://github.com/ -i - -O /usr/local/bin/mailhog
RUN chmod +x /usr/local/bin/mailhog
# Disabled until https://github.com/mailhog/mhsendmail/issues/3 is fixed.
# RUN curl -s -L https://github.com/mailhog/mhsendmail/releases/latest | egrep -o '/mailhog/mhsendmail/releases/download/[v.0-9]*/mhsendmail_linux_amd64' | wget --base=http://github.com/ -i - -O /usr/local/bin/mhsendmail
# RUN chmod +x /usr/local/bin/mhsendmail
# RUN update-alternatives --install /usr/sbin/sendmail mhsendmail /usr/local/bin/mhsendmail 1
# Configure PHP to use mhsendmail.
# ADD ./conf/php/40-mhsendmail.ini /etc/php5/cli/conf.d/40-mhsendmail.ini
# ADD ./conf/php/40-mhsendmail.ini /etc/php5/apache2/conf.d/40-mhsendmail.ini

# Configure SSMTP to relay to MailHog.
ADD ./conf/ssmtp/ssmtp.conf /etc/ssmtp/ssmtp.conf

# Startup settings.
ADD ./conf/supervisor/soe.conf /etc/supervisor/conf.d/soe.conf

# HTTP, HTTPS, MailHog SMTP, MailHog WebUI, PimpMyLog & Xdebug.
EXPOSE 80 443 1025 8000 8025 9000

WORKDIR /var/www

CMD ["/usr/bin/supervisord", "-n"]

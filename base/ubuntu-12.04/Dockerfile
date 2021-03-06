# Base for most of the Docker containers here.

FROM chinthakagodawita/ubuntu:12.04
MAINTAINER Chinthaka Godawita <chin.godawita@me.com>

# Increment this to trigger a full rebuild.
ENV DC_BASE_VERSION 'ubuntu-12.04-3.0.0'

# Update repositories and make sure we're on the latest 14.04 release.
RUN apt-get -y update
RUN DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade

# Set timezone to be AEST/AEDT.
RUN echo "Australia/Sydney" > /etc/timezone
RUN dpkg-reconfigure --frontend noninteractive tzdata

# Supervisord (should be configured in any containers that inherit this base).
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install supervisor

# APT management tools.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install python-software-properties

# Python easy_install.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install python-setuptools

# Essential build tools, required for some software.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install \
  build-essential \
  autoconf \
  automake

# Curl.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install curl

# Telnet.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install telnet

# Rsync.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install rsync

# Wget.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install wget

# Nano & Vim (just in case).
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install nano vim

# Netstat et al.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install net-tools

# Git.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install git

# stdout helper for supervisord.
RUN easy_install supervisor-stdout

# Startup settings.
ADD ./conf/supervisor/base.conf /etc/supervisor/conf.d/base.conf

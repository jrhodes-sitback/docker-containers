# Base nodejs container.

FROM chinthakagodawita/base:ubuntu-14.04
MAINTAINER Chinthaka Godawita <chin.godawita@me.com>

# Add node 0.12 via nodesource.
RUN curl -sL https://deb.nodesource.com/setup_4.x | bash -
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs

# Add PM2 for process management.
RUN npm install pm2 -g

# Setup application base directory.
RUN mkdir -p /server
WORKDIR /server

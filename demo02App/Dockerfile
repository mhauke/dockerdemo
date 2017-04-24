FROM node:7.4.0-alpine
MAINTAINER Clemens Siebler <clemens.siebler@gmail.com>

# Build container
ADD . /srv/app
WORKDIR /srv/app
RUN npm install

# Startup command
EXPOSE 80
ENTRYPOINT ["node", "index.js"]

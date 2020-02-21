FROM node:12.13.0-alpine as node
  
FROM php:7.3-fpm-alpine

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

RUN docker-php-ext-install mysqli && \
    docker-php-ext-install pdo_mysql

RUN apk add --update nodejs nodejs-npm

RUN apk add --no-cache \
    build-base \
    libpng-dev \
    lcms2-dev \
    bash

WORKDIR /var/www/html

COPY --from=node /usr/local/bin/node /usr/local/bin/
COPY --from=node /usr/local/lib/node_modules/ /usr/local/lib/node_modules/
RUN ln -s /usr/local/bin/node /usr/local/bin/nodejs \
    && ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm \
    && ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npx

RUN npm install -g npx @vue/cli


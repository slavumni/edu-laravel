FROM php:8.2-fpm-alpine

# default host user
ARG HOST_UID=1000
# default host group
ARG HOST_GID=1000
# username for php-fpm
ENV USERNAME=www-data
# app path
ENV APP_HOME /var/www

# for install usermod & usergroup
#RUN echo https://dl-2.alpinelinux.org/alpine/edge/community/ >> /etc/apk/repositories
# Install dependencies
RUN apk update && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS \
    libzip-dev zip

# Install extensions
RUN docker-php-ext-install pdo_mysql zip

# install usermod and usergroup, fix permissions for www-data user
RUN echo https://dl-2.alpinelinux.org/alpine/edge/community/ >> /etc/apk/repositories
RUN apk --no-cache add shadow && usermod -u $HOST_UID $USERNAME && groupmod -g $HOST_GID $USERNAME
# change owner to www-data
RUN chown -R ${USERNAME}:${USERNAME} $APP_HOME

# put php config for php-fpm
COPY ./docker/laravel/php.ini /usr/local/etc/php/php.ini

# install Xdebug in case dev/test environment

# install composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
RUN chmod +x /usr/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER 1

# set working directory
WORKDIR $APP_HOME

USER ${USERNAME}

# copy source files and config file
COPY --chown=${USERNAME}:${USERNAME} ./laravel/ $APP_HOME/
#COPY --chown=${USERNAME}:${USERNAME} .env.$ENV $APP_HOME/.env

# install all PHP dependencies
RUN COMPOSER_MEMORY_LIMIT=-1 composer install --optimize-autoloader --no-interaction --no-progress

#USER root

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]

ARG FRANKENPHP_VERSION=1.10.1
ARG PHP_VERSION=8.3.28
FROM dunglas/frankenphp:${FRANKENPHP_VERSION}-php${PHP_VERSION}-alpine as build

# Install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

# Create a non-root user
ARG USER_ID=1000
ARG USER_NAME=www
ARG GROUP_ID=1000
ARG GROUP_NAME=www
RUN \
    addgroup -g ${GROUP_ID} -S ${GROUP_NAME} && \
    adduser -D -u ${USER_ID} -G ${GROUP_NAME} -S ${USER_NAME} && \
    # Add additional capability to bind to port 80 and 443
    setcap CAP_NET_BIND_SERVICE=+eip /usr/local/bin/frankenphp && \
    # Give write access to /config/caddy and /data/caddy
    chown -R ${USER_NAME}:${GROUP_NAME} /config/caddy /data/caddy \
;

# Disable https
ENV SERVER_NAME=:80

# Install php extensions
RUN install-php-extensions \
    pdo_pgsql \
;

# ----------------------------------------------------------------------
# Development
# ----------------------------------------------------------------------
FROM build as development
# Install Xdebug extension
ARG WITH_XDEBUG=false
RUN if [ "${WITH_XDEBUG}" = "true" ]; then install-php-extensions xdebug; fi
COPY ./docker/backend/conf/ext/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Change user
USER ${USER_NAME}

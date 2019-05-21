FROM php:7.3

ARG BUILD_DATE
ARG VCS_REF
ENV COMPOSER_ALLOW_SUPERUSER 1

LABEL Maintainer="Zaher Ghaibeh <z@zah.me>" \
      Description="A simple PHP 7.3 image which contain just the minimum required to run Dusk on bitbucket pipelines." \
      org.label-schema.name="zaherg/laravel-dusk:7.3" \
      org.label-schema.description="A simple PHP 7.3 image which contain just the minimum required to run Dusk on bitbucket pipelines." \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.vcs-url="https://github.com/linuxjuggler/laravel-dusk" \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.schema-version="1.0.0"

ENV COMPOSER_ALLOW_SUPERUSER 1 \
    PHP_XDEBUG_DEFAULT_ENABLE ${PHP_XDEBUG_DEFAULT_ENABLE:-1} \
    PHP_XDEBUG_REMOTE_ENABLE ${PHP_XDEBUG_REMOTE_ENABLE:-1} \
    PHP_XDEBUG_REMOTE_HOST ${PHP_XDEBUG_REMOTE_HOST:-"127.0.0.1"} \
    PHP_XDEBUG_REMOTE_PORT ${PHP_XDEBUG_REMOTE_PORT:-9000} \
    PHP_XDEBUG_REMOTE_AUTO_START ${PHP_XDEBUG_REMOTE_AUTO_START:-1} \
    PHP_XDEBUG_REMOTE_CONNECT_BACK ${PHP_XDEBUG_REMOTE_CONNECT_BACK:-1} \
    PHP_XDEBUG_IDEKEY ${PHP_XDEBUG_IDEKEY:-docker} \
    PHP_XDEBUG_PROFILER_ENABLE ${PHP_XDEBUG_PROFILER_ENABLE:-0} \
    PHP_XDEBUG_PROFILER_OUTPUT_DIR ${PHP_XDEBUG_PROFILER_OUTPUT_DIR:-"/tmp"}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN apt-get update && apt-get install -y -q --no-install-recommends git libsodium-dev unzip zlib1g-dev \
    libxpm4 libxrender1 libgtk2.0-0 libnss3 libgconf-2-4 chromium xvfb gtk2-engines-pixbuf xfonts-cyrillic \
    xfonts-100dpi xfonts-75dpi xfonts-base xfonts-scalable imagemagick x11-apps libicu-dev libzip-dev \
    && mkdir /src && cd /src && git clone https://github.com/xdebug/xdebug.git \
    && cd xdebug \
    && sh ./rebuild.sh \
    && docker-php-source extract \
    && pecl install redis libsodium \
    && docker-php-ext-enable xdebug redis \
    && docker-php-source delete \
    && docker-php-ext-install -j"$(nproc)" pdo pdo_mysql intl zip \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*

COPY conf.d/xdebug.ini /usr/local/etc/php/conf.d/xdebug-dev.ini

RUN Xvfb -ac :0 -screen 0 1280x1024x16 &

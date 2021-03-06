FROM php:fpm-alpine
ENV WORKDIR "/var/www/app"

RUN apk upgrade --update && apk --no-cache add \
    gcc g++ make git autoconf tzdata openntpd libcurl curl-dev coreutils \
    libmcrypt-dev freetype-dev libxpm-dev libjpeg-turbo-dev libvpx-dev \
    libpng-dev openssl libxml2-dev postgresql-dev icu-dev

RUN docker-php-ext-configure intl \
    && docker-php-ext-configure opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/ \
    --with-xpm-dir=/usr/include/
RUN docker-php-ext-install -j$(nproc) gd iconv pdo pdo_pgsql pdo_mysql curl \
    mbstring json xml xmlrpc zip bcmath intl opcache
RUN docker-php-ext-install pcntl

# Install xDebug and Redis
RUN docker-php-source extract \
    && pecl install xdebug redis \
    && docker-php-ext-enable xdebug redis \
    && docker-php-source delete

# Install Composer
RUN curl -sS https://getcomposer.org/installer | \
    php -- --install-dir=/usr/local/bin --filename=composer
# Cleanup
RUN rm -rf /var/cache/apk/* \
    && find / -type f -iname \*.apk-new -delete \
    && rm -rf /var/cache/apk/*

RUN mkdir -p ${WORKDIR}
RUN chown www-data:www-data -R ${WORKDIR}
WORKDIR ${WORKDIR}
EXPOSE 9000
CMD ["php-fpm"]

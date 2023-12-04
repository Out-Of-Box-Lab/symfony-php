FROM --platform=linux/amd64 php:8.2-fpm-alpine

MAINTAINER Dieter Blomme <dieterblomme@gmail.com>

ENV WORKDIR "/var/www/app"

RUN cd /usr/local/etc/php/conf.d/ && \
  echo 'memory_limit = -1' >> /usr/local/etc/php/conf.d/docker-php-ram-limit.ini

RUN apk upgrade --update && apk --no-cache add \
    gcc g++ make autoconf tzdata openntpd libcurl curl-dev coreutils \
    freetype-dev libxpm-dev libjpeg-turbo-dev libvpx-dev \
    libpng-dev libressl libxml2-dev icu-dev libzip-dev \
    postgresql-dev yarn openldap-dev

RUN apk add --no-cache --virtual build-dependencies icu-dev libxml2-dev \
    freetype-dev libpng-dev libjpeg-turbo-dev g++ make autoconf patch

RUN docker-php-ext-configure intl \
    && docker-php-ext-configure opcache \
    && docker-php-ext-configure ldap \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd zip xml intl pdo pdo_mysql pdo_pgsql curl bcmath opcache exif ldap gettext

# Install Redis
RUN docker-php-source extract \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && docker-php-source delete

# Add timezone
RUN ln -s /usr/share/zoneinfo/UTC /etc/localtime && \
    "date"

# Install composer
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

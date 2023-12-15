FROM --platform=linux/amd64 php:8.2-fpm-alpine

ENV WORKDIR "/var/www/app"

RUN cd /usr/local/etc/php/conf.d/ && \
  echo 'memory_limit = -1' >> /usr/local/etc/php/conf.d/docker-php-ram-limit.ini

RUN apk upgrade --update  \
    && apk --no-cache add --virtual build-dependencies \
        autoconf \
        coreutils \
        curl-dev \
        freetype-dev \
        g++ \
        gcc \
        gettext-dev \
        icu-dev \
        libcurl \
        libjpeg-turbo-dev \
        libmemcached-dev \
        libpng-dev \
        libressl \
        libvpx-dev \
        libxml2-dev \
        libxpm-dev \
        libzip-dev \
        make \
        openldap-dev \
        openntpd \
        patch \
        postgresql-dev \
        tzdata \
        yarn \
        ansible \
    && rm -rf /var/cache/apk/* \
    && find / -type f -iname \*.apk-new -delete \
    && rm -rf /var/cache/apk/*

RUN docker-php-ext-configure intl \
    && docker-php-ext-configure opcache \
    && docker-php-ext-configure ldap \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc)  \
      gd  \
      zip  \
      xml  \
      intl  \
      pdo  \
      pdo_mysql  \
      pdo_pgsql  \
      curl  \
      bcmath  \
      opcache  \
      exif  \
      ldap  \
      gettext

# Install PECL extensions
RUN docker-php-source extract \
    && pecl install redis memcached \
    && docker-php-ext-enable redis memcached \
    && docker-php-source delete

# Add timezone
RUN ln -s /usr/share/zoneinfo/UTC /etc/localtime && \
    "date"

# Install composer
RUN curl -sS https://getcomposer.org/installer | \
    php -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir -p ${WORKDIR}
RUN chown www-data:www-data -R ${WORKDIR}

WORKDIR ${WORKDIR}

EXPOSE 9000

CMD ["php-fpm"]

FROM php:7-fpm

RUN apt-get update && apt-get install -y \
            libfreetype6-dev \
            libjpeg62-turbo-dev \
            libmcrypt-dev \
            libpng-dev \
            libicu-dev \
            libpq-dev \
            libxpm-dev \
            libvpx-dev \
            libzip-dev \
            libxml2-dev \
            unzip \
        && pecl install xdebug mcrypt-1.0.2 \
        && docker-php-ext-enable xdebug mcrypt \
        && docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install -j$(nproc) intl \
        && docker-php-ext-install -j$(nproc) zip \
        && docker-php-ext-install -j$(nproc) pgsql \
        && docker-php-ext-install -j$(nproc) pdo_pgsql \
        && docker-php-ext-install -j$(nproc) pdo \
        && docker-php-ext-install -j$(nproc) pdo_mysql \
        && docker-php-ext-install -j$(nproc) xml \
        && docker-php-ext-install -j$(nproc) exif \
        && docker-php-ext-configure gd \
            --with-freetype-dir=/usr/include/ \
            --with-jpeg-dir=/usr/include/ \
            --with-xpm-dir=/usr/lib/x86_64-linux-gnu/ \
            --with-vpx-dir=/usr/lib/x86_64-linux-gnu/

RUN echo 'date.timezone="Europe/Madrid"' >> /usr/local/etc/php/conf.d/date.ini
RUN echo 'session.save_path = "/tmp"' >> /usr/local/etc/php/conf.d/session.ini

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer
RUN curl -sSL https://phar.phpunit.de/phpunit.phar -o /usr/bin/phpunit && chmod +x /usr/bin/phpunit

ENV CONSUL_TEMPLATE_VERSION 1.5.3
ENV CONSUL_TEMPLATE_SHA1 b402e1a0db26adb9638a9e85c6c672acd137df233e8c69f26180f2e2fd6f4cbc

RUN curl --retry 7 -kLso /tmp/consul-template.zip \
    "https://releases.hashicorp.com/consul/${CONSUL_TEMPLATE_VERSION}/consul_${CONSUL_TEMPLATE_VERSION}_linux_amd64.zip" \
    && echo "${CONSUL_TEMPLATE_SHA1} /tmp/consul-template.zip" | sha256sum -c \
    && unzip /tmp/consul-template.zip -d /usr/local/bin \
    && rm /tmp/consul-template.zip

ENV CONTAINERPILOT_VERSION 3.8.0
ENV CONTAINERPILOT_SHA1 84642c13683ddae6ccb63386e6160e8cb2439c26
ENV CONTAINERPILOT file:///etc/containerpilot.json

RUN curl --retry 7 -kLso /tmp/containerpilot.tar.gz \
    "https://github.com/joyent/containerpilot/releases/download/${CONTAINERPILOT_VERSION}/containerpilot-${CONTAINERPILOT_VERSION}.tar.gz" \
    && echo "${CONTAINERPILOT_SHA1} /tmp/containerpilot.tar.gz" | sha1sum -c \
    && tar zxf /tmp/containerpilot.tar.gz -C /usr/local/bin \
    && rm /tmp/containerpilot.tar.gz

COPY config/ /etc
COPY scripts/ /usr/local/bin

RUN chmod +x /usr/local/bin/reload.sh

#CMD /usr/local/bin/containerpilot -config /etc/containerpilot.json
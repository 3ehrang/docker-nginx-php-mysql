##################################
### Base image
##################################
FROM debian:latest as base_image

LABEL maintainer="Behrang Nourmohamadi <<behrangno@gmail.com>>"
LABEL version="1.0"
LABEL description="Base image"

ARG user=skynet

# Add a service user.
RUN adduser --system --no-create-home --group --uid 77777 $user

# Base installation
RUN apt-get update && \
    apt-get install -y unzip curl supervisor apt-transport-https lsb-release ca-certificates && \
    curl https://packages.sury.org/php/apt.gpg --output /etc/apt/trusted.gpg.d/php.gpg && \
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list && \
    apt-get clean && \
    apt-get update && \
    apt-get install -y nginx php7.4 php7.4-fpm php7.4-cli php7.4-bcmath php7.4-ctype php7.4-json php7.4-mbstring php7.4-xml php7.4-mysql php-memcached && \
    apt-get autoremove -y && \
    apt-get autoclean -y && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# supervisor config
COPY supervisor/* /etc/supervisor/conf.d/
COPY nginx/sites-available/* /etc/nginx/sites-available/
COPY nginx/logging.conf /etc/nginx/conf.d/logging.conf
COPY php/www.conf /etc/php/7.4/fpm/pool.d/www.conf

RUN mkdir -p /run/php

## Nginx
RUN sed -i "s/user.*/user $user;/g" /etc/nginx/nginx.conf && \
    sed -i "s/access_log/# access_log/" /etc/nginx/nginx.conf && \
    sed -i "s/error_log/# error_log/" /etc/nginx/nginx.conf

ENTRYPOINT ["/usr/bin/supervisord"]

FROM base_image as development

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

WORKDIR /var/www/html
    


ARG NGINX_VERSION=1.14.2

FROM nginx:${NGINX_VERSION}

LABEL maintainer="Mahmoud Zalt <mahmoud@zalt.me>"

COPY nginx.conf /etc/nginx/

ARG PHP_UPSTREAM_CONTAINER=php-fpm
ARG PHP_UPSTREAM_PORT=9000

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf

CMD ["nginx"]

EXPOSE 80 443

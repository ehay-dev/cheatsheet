FROM alpine:3.18

LABEL maintainer "Ehay DEV <github.com/ehay-dev>"

ENV NGINX_VERSION 1.25.2

WORKDIR /tmp

RUN apk update && \
    apk add       \
      alpine-sdk  \
      openssl-dev \
      pcre-dev    \
      zlib-dev

RUN curl -LSs http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz -O                                             && \
    tar xf nginx-${NGINX_VERSION}.tar.gz                                                                             && \
    cd     nginx-${NGINX_VERSION}                                                                                    && \
    git clone https://github.com/chobits/ngx_http_proxy_connect_module                                               && \
    patch -p1 < ./ngx_http_proxy_connect_module/patch/proxy_connect_rewrite_102101.patch                             && \
    ./configure                                                                                                         \
      --add-module=./ngx_http_proxy_connect_module                                                                      \
      --sbin-path=/usr/sbin/nginx                                                                                       \
      --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-threads                    \
      --with-cc-opt='-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' && \
    make -j $(nproc)                                                                                                 && \
    make install                                                                                                     && \
    rm -rf /tmp/*

WORKDIR /

COPY ./nginx.conf /usr/local/nginx/conf/nginx.conf

EXPOSE 3128

STOPSIGNAL SIGTERM

CMD [ "nginx", "-g", "daemon off;" ]
FROM registry.cn-shenzhen.aliyuncs.com/51fx/alpine:3.10

MAINTAINER graham_liu <graham_liu@aliyun.com>

ENV LANG zh_CN.UTF-8
ENV OPENRESTY_VERSION 1.13.6.2

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && apk add -U tzdata zlib zlib-dev pcre pcre-dev make gcc libc-dev openssl openssl-dev curl perl-dev zip unzip patch && rm -rf /var/cache/apk/* \
        && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
        && adduser -h /var/www/ -g "OpenResty Server User" -D -s /sbin/nologin nginx \
        && curl -fSL https://openresty.org/download/openresty-$OPENRESTY_VERSION.tar.gz -o /tmp/openresty-$OPENRESTY_VERSION.tar.gz \
        && curl -fSL https://github.com/xiaokai-wang/nginx_upstream_check_module/archive/master.zip -o /tmp/nginx_upstream_check_module.zip \
        && unzip /tmp/nginx_upstream_check_module.zip -d /tmp \
        && tar xzf /tmp/openresty-$OPENRESTY_VERSION.tar.gz -C /tmp && cd /tmp/openresty-$OPENRESTY_VERSION/bundle/nginx-1.13.6 && patch -p1 < /tmp/nginx_upstream_check_module-master/check_1.12.1+.patch \
        && cd /tmp/openresty-$OPENRESTY_VERSION && ./configure \
                --prefix=/usr/local/openresty \
                --user=nginx \
                --group=nginx \
                --with-http_ssl_module \
                --with-http_realip_module \
                --with-http_gzip_static_module \
                --with-http_stub_status_module \
                --with-stream \
                --with-stream_ssl_module \
                --with-stream_ssl_preread_module \
                --with-stream_realip_module \
                --add-module=/tmp/nginx_upstream_check_module-master/ \
        && make && make install \
        && ln -s /usr/local/openresty/nginx/sbin/* /usr/local/sbin/ && rm -rf /tmp/nginx* /tmp/openresty*

EXPOSE 80 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]

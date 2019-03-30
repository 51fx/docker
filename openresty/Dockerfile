FROM centos:6.10

MAINTAINER graham_liu <graham_liu@aliyun.com>

ENV OPENRESTY_VERSION 1.13.6.2

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
	&& echo 'Asia/Shanghai' >/etc/timezone \
	&& rm -rf /etc/yum.repos.d/* \
	&& curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo \
	&& yum clean all && yum makecache && yum update -y \
	&& yum install iputils vim zlib-devel pcre-devel make gcc libc-devel openssl openssl-devel curl perl-devel zip unzip patch -y 

RUN useradd -d /var/www/ -c "OpenResty Server User" -s /sbin/nologin www 

RUN curl -fSL https://openresty.org/download/openresty-$OPENRESTY_VERSION.tar.gz -o /tmp/openresty-$OPENRESTY_VERSION.tar.gz \
	&& curl -fSL https://github.com/weibocom/nginx-upsync-module/archive/v2.1.0.zip -o /tmp/nginx-upsync-module.zip \
	&& curl -fSL https://github.com/xiaokai-wang/nginx_upstream_check_module/archive/master.zip -o /tmp/nginx_upstream_check_module.zip \
	&& unzip /tmp/nginx-upsync-module.zip -d /tmp && unzip /tmp/nginx_upstream_check_module.zip -d /tmp

RUN tar xzf /tmp/openresty-$OPENRESTY_VERSION.tar.gz -C /tmp && cd /tmp/openresty-$OPENRESTY_VERSION/bundle/nginx-1.13.6 && patch -p1 < /tmp/nginx_upstream_check_module-master/check_1.12.1+.patch \
	&& cd /tmp/openresty-$OPENRESTY_VERSION && ./configure \
		--prefix=/usr/local/openresty \
		--user=www \
		--group=www \
		--with-http_ssl_module \
		--with-http_realip_module \
		--with-http_gzip_static_module \
		--with-http_stub_status_module \
		--with-stream \
		--with-stream_ssl_module \
		--with-stream_ssl_preread_module \
		--with-stream_realip_module \
		--add-module=/tmp/nginx-upsync-module-2.1.0/ \
		--add-module=/tmp/nginx_upstream_check_module-master/ \
	&& make && make install

RUN ln -s /usr/local/openresty/nginx/sbin/* /usr/local/sbin/ && rm -rf /tmp/nginx* /tmp/openresty*

EXPOSE 80 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
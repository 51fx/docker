FROM centos:6.10

MAINTAINER graham_liu <graham_liu@aliyun.com>

ENV NGINX_VERSION 1.12.2

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
	&& echo 'Asia/Shanghai' >/etc/timezone \
	&& rm -rf /etc/yum.repos.d/* \
	&& curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo \
	&& yum clean all && yum makecache && yum update -y \
	&& yum install iputils vim zlib-devel pcre-devel make gcc libc-devel openssl openssl-devel curl perl-devel geoip-devel gd-devel libxslt-devel gnupg linux-headers zip unzip patch -y 

RUN useradd -d /var/www/ -c "Nginx Server User" -s /sbin/nologin nginx 

RUN curl -fSL https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz -o /tmp/nginx-$NGINX_VERSION.tar.gz \
	&& curl -fSL https://github.com/weibocom/nginx-upsync-module/archive/v2.1.0.zip -o /tmp/nginx-upsync-module.zip \
	&& curl -fSL https://github.com/xiaokai-wang/nginx_upstream_check_module/archive/master.zip -o /tmp/nginx_upstream_check_module.zip \
	&& unzip /tmp/nginx-upsync-module.zip -d /tmp && unzip /tmp/nginx_upstream_check_module.zip -d /tmp

RUN tar xzf /tmp/nginx-$NGINX_VERSION.tar.gz -C /tmp && cd /tmp/nginx-$NGINX_VERSION && patch -p1 < /tmp/nginx_upstream_check_module-master/check_1.12.1+.patch \
	&& ./configure \
		--prefix=/usr/local/nginx \
		--user=nginx \
		--group=nginx \
		--with-http_ssl_module \
		--with-http_realip_module \
		--with-http_addition_module \
		--with-http_sub_module \
		--with-http_dav_module \
		--with-http_flv_module \
		--with-http_mp4_module \
		--with-http_gunzip_module \
		--with-http_gzip_static_module \
		--with-http_random_index_module \
		--with-http_secure_link_module \
		--with-http_stub_status_module \
		--with-http_auth_request_module \
		--with-http_xslt_module=dynamic \
		--with-http_image_filter_module=dynamic \
		--with-stream \
		--with-stream_ssl_module \
		--with-stream_ssl_preread_module \
		--with-stream_realip_module \
		--with-http_slice_module \
		--with-mail \
		--with-mail_ssl_module \
		--with-compat \
		--with-file-aio \
		--with-http_v2_module \
		--add-module=/tmp/nginx-upsync-module-2.1.0/ \
		--add-module=/tmp/nginx_upstream_check_module-master/ \
	&& make && make install

RUN ln -s /usr/local/nginx/sbin/* /usr/local/sbin/ 

EXPOSE 80 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]

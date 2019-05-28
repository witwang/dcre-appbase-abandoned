FROM php:7.3-fpm

RUN echo 'deb http://mirrors.163.com/debian/ stretch main non-free contrib \n\
deb http://mirrors.163.com/debian/ stretch-updates main non-free contrib \n\
deb http://mirrors.163.com/debian/ stretch-backports main non-free contrib \n\
deb-src http://mirrors.163.com/debian/ stretch main non-free contrib \n\
deb-src http://mirrors.163.com/debian/ stretch-updates main non-free contrib \n\
deb-src http://mirrors.163.com/debian/ stretch-backports main non-free contrib \n\
deb http://mirrors.163.com/debian-security/ stretch/updates main non-free contrib \n\
deb-src http://mirrors.163.com/debian-security/ stretch/updates main non-free contrib' > /etc/apt/sources.list


# 安装php扩展
WORKDIR /tmp
COPY ./redis.tar.gz /tmp/redis.tar.gz
COPY ./memcached.tar.gz /tmp/memcached.tar.gz
COPY ./mongo.tgz /tmp/mongo.tgz
COPY ./phalcon.tar.gz /tmp/phalcon.tar.gz
COPY ./psr.tar.gz /tmp/psr.tar.gz
RUN tar xf /usr/src/php.tar.xz \
  && mv php-7.3.5 /usr/src/php \
  && mkdir -p /usr/src/php/ext/memcached \
  && tar -C /usr/src/php/ext/memcached -zxf /tmp/memcached.tar.gz --strip 1 \
  && mkdir -p /usr/src/php/ext/mongodb \
  && tar -C /usr/src/php/ext/mongodb -zxf /tmp/mongo.tgz --strip 1 \
  && mkdir -p /usr/src/php/ext/redis \
  && tar -C /usr/src/php/ext/redis -zxf /tmp/redis.tar.gz --strip 1 \
  && mkdir -p /usr/src/php/ext/psr \
  && tar -C /usr/src/php/ext/psr -zxf /tmp/psr.tar.gz --strip 1 \
  && mkdir -p /tmp/phalcon \
  && tar -C /tmp/phalcon -zxf /tmp/phalcon.tar.gz --strip 1
  
# 安装php
RUN apt-get clean \
  && apt-get update \
  && apt-get -y remove libssl1.1 \
  && apt-get install -y libpng-dev libpng++-dev libjpeg-dev libmemcached-dev libssl-dev libcurl4-openssl-dev libzip-dev libzip4 \
  && rm -rf /var/lib/apt/lists/* \
  && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
  && docker-php-ext-install gd mysqli pdo_mysql zip opcache redis \
  && docker-php-ext-configure memcached \
  && docker-php-ext-install memcached \
  && docker-php-ext-configure mongodb \
  && docker-php-ext-install mongodb \
  && docker-php-ext-configure bcmath \
  && docker-php-ext-install bcmath \
  && docker-php-ext-configure psr \
  && docker-php-ext-install psr

# 安装phalcon
RUN cd /tmp/phalcon/build \
  && ./install \
  && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini

# 生成配置文件
COPY ./php.ini /usr/local/etc/php/conf.d/
COPY ./opcache.ini /usr/local/etc/php/conf.d/

FROM php:7.1-fpm

ADD https://raw.githubusercontent.com/mlocati/docker-php-extension-installer/master/install-php-extensions /usr/local/bin/

RUN chmod uga+x /usr/local/bin/install-php-extensions && sync

RUN DEBIAN_FRONTEND=noninteractive apt-get update -q \
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq -y \
      curl \
      git \
      zip unzip \
    && install-php-extensions \
      bcmath \
      bz2 \
      calendar \
      exif \
      gd \
      intl \
      ldap \
      mcrypt \
      mysqli \
      opcache \
      pdo_mysql \
      zip \
      sockets
RUN \
  curl 'http://pecl.php.net/get/redis-3.1.5.tgz' -o /tmp/redis-3.1.5.tgz  \
  && cd /tmp \
  && pecl install redis-3.1.5.tgz \
  && rm -rf redis-3.1.5.tgz \
  && docker-php-ext-enable redis

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && ln -s $(composer config --global home) /root/composer
ENV PATH=$PATH:/root/composer/vendor/bin COMPOSER_ALLOW_SUPERUSER=1

RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

RUN apt-get update && apt-get install wget -y

RUN apt-get -y --allow-unauthenticated install \
    libgearman-dev

RUN cd /tmp \
    && git clone https://github.com/wcgallego/pecl-gearman.git \
    && cd pecl-gearman \
    && git checkout gearman-2.0.3 \
    && phpize \
    && ./configure \
    && make -j$(nproc) \
    && make install \
    && rm -r /tmp/pecl-gearman \
    && docker-php-ext-enable gearman

RUN apt-get install supervisor gearman-tools -y -f

RUN apt-get install gearman-job-server -y

RUN docker-php-ext-install fileinfo 

RUN docker-php-ext-install sockets

COPY ./www.conf /usr/local/etc/php-fpm.d/www.conf

COPY ./openssl.cnf /etc/ssl/openssl.cnf

COPY ./start.sh start.sh

ENV TERM xterm

RUN apt-get install -y nnn

RUN apt-get install -y zsh

RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true

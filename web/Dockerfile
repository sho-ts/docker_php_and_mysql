FROM php:7.3-apache

# パッケージリストの更新
RUN apt-get update

COPY ./php/php.ini /usr/local/etc/php/
COPY ./apache/*.conf /etc/apache2/sites-enabled/

# composerのハッシュ値
ARG composer_setup_hash=756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3

# composerのインストールディレクトリ
ARG composer_dir=/usr/local/bin
ARG composer_filename=composer

# mod_rewriteの有効化
RUN  a2enmod rewrite

# libxml2 / unzip
RUN apt-get -y install libxml2-dev unzip

# php拡張機能
RUN docker-php-ext-install \
  pdo_mysql \
  mysqli \
  mbstring \
  json \
  simplexml \
  dom \
  fileinfo \
  hash \
  xml \
  iconv

RUN apt-get install -y \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libmcrypt-dev \
  libpng-dev \
  mailutils \
  memcached \
  msmtp \
  msmtp-mta \
  openssl \
  && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
  && docker-php-ext-install -j$(nproc) gd exif

# Composer インストール
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('sha384', 'composer-setup.php') !== '$composer_setup_hash') { \
  echo '!!! Failed to install Composer !!!'; \
  unlink('composer-setup.php'); \
  } \
  echo PHP_EOL;" \
  && php composer-setup.php --install-dir=$composer_dir --filename=$composer_filename \
  && php -r "unlink('composer-setup.php');" \
  # インストールした Composer のバージョン表示（確認用、省略可）
  && composer -v | grep "Composer version" \
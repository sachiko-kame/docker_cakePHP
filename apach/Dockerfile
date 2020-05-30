From php:7.4-apache
# cakePHP4はphp7.2以上必須

# unzipとかはcakePHPインストール時に使われるものをインストール
RUN apt-get update \
&& apt-get install -y \
zip \
nano \
libicu-dev \
unzip

# could not find driver』の対応 mysql使いたい
RUN docker-php-ext-install mysqli pdo pdo_mysql intl

# 入ってここからが比較的行いやすいかと ymlに記述
WORKDIR /var/www/html

# vimコマンド使いたくて入れているのみ 入れなくても良い
RUN ["apt-get", "update"]
RUN ["apt-get", "install", "-y", "vim"]
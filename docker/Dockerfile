# Amazon Linux 2ベースイメージを使用
FROM amazonlinux:2

# イメージの更新と必要なパッケージのインストール
RUN amazon-linux-extras enable php7.4 && \
    yum -y update && \
    yum -y install httpd systemd php php-mysqlnd php-pdo php-xml php-zip php-intl php-opcache php-cli php-common php-json php-mbstring php-pdo_mysql php-gd php-fpm git vim man && \
    yum clean all

# Composerのインストール
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
    php -r "unlink('composer-setup.php');"

RUN yum update -y && yum install -y tar gzip iputils mysql wget xz && yum clean all

RUN curl -sL https://rpm.nodesource.com/setup_16.x | bash -
RUN yum install -y nodejs
RUN npm install -g yarn

# ApacheとPHPのカスタム設定ファイルをコンテナにコピー
COPY ./httpd.conf /etc/httpd/conf/httpd.conf
COPY ./conf.d/my-app.conf /etc/httpd/conf.d/my-app.conf

# COPY ./php.ini /etc/php.ini

# MySQLクライアントのカスタム設定ファイルをコンテナにコピー
COPY ./my.cnf /root/.my.cnf

# 読み取り専用に設定ファイルを変更
RUN chmod 444 /root/.my.cnf

RUN yum -y install sudo &&\
    groupadd sudo && \
    useradd -r -u 1000 -g apache -d /var/www/html -s /bin/bash wwwuser &&\
    usermod -aG apache wwwuser


# ドキュメントルートの設定
WORKDIR /var/www/html

# httpd
RUN echo "Mutex posixsem" >> /etc/httpd/conf/httpd.conf

# 80ポートを公開
EXPOSE 80

# Apacheをフォアグラウンドで実行するためのCMDを指定
CMD ["/usr/sbin/httpd","-D","FOREGROUND"]

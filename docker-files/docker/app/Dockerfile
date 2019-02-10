FROM centos:7

# CentOS port by Jim O'Halloran.  Based on work by Chris Fidao in the
# Shipping Docker Vessel project.
LABEL maintainer="Jim O'Halloran <jim@jimohalloran.com>"

RUN useradd -ms /bin/bash -u 1337 vessel
WORKDIR /var/www/html

ENV GOSU_VERSION 1.11
RUN set -x \
    && yum install -y wget && yum clean all \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-amd64" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-amd64.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true

ADD epel-release-latest-7.noarch.rpm /tmp/epel-release-latest-7.noarch.rpm
ADD ius-release.rpm /tmp/ius-release.rpm

RUN rpm -Uvh /tmp/epel-release-latest-7.noarch.rpm /tmp/ius-release.rpm \
    && rm /tmp/epel-release-latest-7.noarch.rpm /tmp/ius-release.rpm \
    && yum install -y php72u-cli php72u-fpm php72u-fpm-httpd php72u-gd php72u-pdo \
        php72u-mbstring php72u-opcache php72u-pecl-xdebug php72u-process php72u-bcmath \
        php72u-xml php72u-json php72u-mysqlnd supervisor mariadb \
    && yum clean all \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer


RUN ln -sf /dev/stdout /var/log/httpd/access_log \
    && ln -sf /dev/stderr /var/log/httpd/error_log

COPY h5bp-defaults.conf /etc/httpd/conf.d/h5bp-defaults.conf
COPY default-vhost.conf /etc/httpd/conf.d/default-vhost.conf
COPY php-fpm.conf /etc/php-fpm.conf
COPY xdebug.ini /etc/php.d/xdebug.ini
COPY vessel.ini /etc/php.d/99-vessel.ini

EXPOSE 80

COPY supervisord.conf /etc/supervisord.conf
COPY start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container

ENTRYPOINT ["start-container"]

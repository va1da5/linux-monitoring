FROM debian:11-slim

ENV DEBIAN_FRONTEND noninteractive

ARG DVWA_VERSION=2.2.2

# Download and install dependencies
RUN set -xe \
    && apt-get update \
    && apt-get install -y wget \
    && apt-get -y install apache2 default-mysql-client php7.4 php7.4-mysql php7.4-gd \
    && wget https://github.com/digininja/DVWA/archive/refs/tags/$DVWA_VERSION.tar.gz -O /tmp/dvwa.tar.gz \
    && tar xfz /tmp/dvwa.tar.gz --strip-components=1 -C /var/www/html \
    && rm -rf /var/www/html/index.html \
    && rm -rf /tmp/dvwa.tar.gz \
    && apt-get purge -y wget \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Configure application
RUN set -xe \
    && chmod -R 777 /var/www/html \
    && sed -i 's/allow_url_include = Off/allow_url_include = On/g' /etc/php/7.4/apache2/php.ini

EXPOSE 80
COPY entrypoint.sh /usr/local/bin
RUN chmod +x /usr/local/bin/entrypoint.sh

CMD ["/usr/local/bin/entrypoint.sh"]

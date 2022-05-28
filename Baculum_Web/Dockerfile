FROM bacula-common-rpi:11.0.6 AS common

FROM debian:bullseye

ENV DEBIAN_FRONTEND=noninteractive

#RUN wget -qO - http://www.bacula.org/downloads/baculum/baculum.pub | apt-key add -

#COPY files/baculum.list /etc/apt/sources.list.d/baculum.list


RUN apt-get update && apt-get --no-install-recommends --yes install \
                make \
                wget \
                ca-certificates \
                apache2 \
                libapache2-mod-php \
                php-bcmath \
                php-cgi \
                php-mysql \
                php-pgsql \
                php-json \
                php-xml \
                php-curl \
                php-ldap \
		php-fpm \
		sudo \
		supervisor

RUN cd /tmp && wget -qO - https://sourceforge.net/projects/bacula/files/bacula/11.0.6/bacula-gui-11.0.6.tar.gz | tar xvfz - \
        && cd bacula-gui-11.0.6/baculum/
WORKDIR /tmp/bacula-gui-11.0.6/baculum
RUN make build DESTDIR=/tmp/baculum-files SAMPLETYPE=deb-template HTTPDNAME=apache2 HTTPDSITECONF=sites-available WWWDIR=/usr/share/baculum/htdocs


RUN cp -R /tmp/baculum-files/usr/share/baculum/ /usr/share \
          && cp /tmp/baculum-files/etc/apache2/sites-available/baculum-*conf /etc/apache2/sites-available/ \
          && cp /tmp/baculum-files/etc/baculum/Config-web-apache/baculum.users /usr/share/baculum/htdocs/protected/Web/Config \
          && cp --remove-destination /tmp/baculum-files/usr/share/locale/en/LC_MESSAGES/baculum-web.mo /usr/share/baculum/htdocs/protected/Web/Lang/en/messages.mo

#RUN mkdir -p /opt/bacula/bin

WORKDIR /

COPY --from=common /opt/bacula /opt/bacula

RUN for i in `ls /opt/bacula/bin`; do ln -s /opt/bacula/bin/$i /usr/sbin/$i; done

COPY files/sudoers-baculum /etc/sudoers.d/sudoers-baculum 

COPY files/supervisord.conf /etc/supervisor/supervisord.conf

RUN chown -R www-data:www-data /usr/share/baculum/

RUN mkdir -p /run/php

RUN a2enmod rewrite

RUN a2ensite baculum-web

RUN apt-get clean

EXPOSE 9095/tcp

ENTRYPOINT ["/usr/bin/supervisord"]

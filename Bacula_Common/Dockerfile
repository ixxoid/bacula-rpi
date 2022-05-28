FROM debian:bullseye as builder

ENV DEBIAN_FRONTEND=noninteractive
ENV PREFIX=/opt/bacula
RUN apt-get update && apt-get --no-install-recommends --yes install \
		apt-utils \
		apt-transport-https \
                build-essential \
		gnupg \
		ca-certificates \
		wget \
                libreadline-dev \
                libncurses-dev \
                postgresql-server-dev-13 \
                libs3-dev 


RUN cd /tmp && wget https://sourceforge.net/projects/bacula/files/bacula/11.0.6/bacula-11.0.6.tar.gz \
    && tar xvf bacula-11.0.6.tar.gz \
    && cd bacula-11.0.6 && CFLAGS="-g -O2 -Wall" \
  ./configure \
    --sbindir=${PREFIX}/bin \
    --sysconfdir=${PREFIX}/etc \
    --docdir=${PREFIX}/html \
    --htmldir=${PREFIX}/html \
    --with-working-dir=${PREFIX}/working \
    --with-pid-dir=${PREFIX}/working \
    --with-scriptdir=${PREFIX}/scripts \
    --with-plugindir=${PREFIX}/plugins \
    --with-archivedir=/var/lib/bacula \
    --libdir=${PREFIX}/lib \
    --enable-smartalloc \
    --disable-docker-plugin \
    --enable-conio \
    --disable-s3 \
    --with-postgresql \
    --with-dump-email=email@example.com \
    --with-job-email=email@example.com \
    --with-smtp-host=mail.example.com \
    --with-baseport=9101 \
    && make && make install


FROM debian:bullseye

RUN apt-get update && apt-get --no-install-recommends --yes install \
                libpq5 \
                postgresql-client-common \
                postgresql-client-13

WORKDIR /opt/bacula
RUN pwd
RUN ls /opt
COPY --from=builder /opt/bacula .


RUN mkdir /var/lib/bacula
RUN for i in `ls /opt/bacula/bin`; do ln -s /opt/bacula/bin/$i /usr/sbin/$i; done 


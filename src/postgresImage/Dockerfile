#FROM ubuntu
#RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8
#RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list
#RUN apt-get update && apt-get install -y python-software-properties software-properties-common postgresql-9.3 postgresql-client-9.3 postgresql-contrib-9.3
#USER postgres
#RUN    /etc/init.d/postgresql start &&\
#    psql --command "CREATE USER docker WITH SUPERUSER PASSWORD 'docker';" &&\
#    createdb -O docker docker
#RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/9.3/main/pg_hba.conf
#RUN echo "listen_addresses='*'" >> /etc/postgresql/9.3/main/postgresql.conf
#EXPOSE 5432
#VOLUME  ["/etc/postgresql", "/var/lib/postgresql"]
#CMD ["/usr/lib/postgresql/9.3/bin/postgres", "-D", "/var/lib/postgresql/9.3/main", "-c", "config_file=/etc/postgresql/9.3/main/postgresql.conf"]

#FROM ubuntu
#
#RUN apt-get update && apt-get install gcc zlib1g-dev libreadline6-dev apt-utils make -y
#
#RUN mkdir  /tmp/postgresarc
#
#ADD https://ftp.postgresql.org/pub/source/v9.5.8/postgresql-9.5.8.tar.gz /tmp/postgresarc
#
#RUN cd /tmp/postgresarc && tar -zxf postgresql-9.5.8.tar.gz
#
#RUN cd /tmp/postgresarc/postgresql-9.5.8 &&\
#    make configure &&\
#    ./configure &&\
#    make &&\
#    su &&\
#    make install
#
#RUN cd /tmp/postgresarc/postgresql-9.5.8 &&\
#    useradd postgres &&\
#    mkdir /usr/local/pgsql/data &&\
#    chown postgres /usr/local/pgsql/data &&\
#    su - postgres &&\
#    /usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data &&\
#    /usr/local/pgsql/bin/postmaster -i -D /usr/local/pgsql/data >logfile 2>&1 &


FROM ubuntu

RUN groupadd -r postgres --gid=999 && useradd -r -g postgres --uid=999 postgres

RUN apt-get update \
	&& apt-get install -y git build-essential libreadline-dev zlib1g-dev \
	&& mkdir -p /temp/postgres_9.5.8/sources

ADD https://ftp.postgresql.org/pub/source/v9.5.8/postgresql-9.5.8.tar.gz /temp/postgres_9.5.8/sources
RUN cd /temp/postgres_9.5.8/sources && gunzip postgresql-9.5.8.tar.gz && tar xf postgresql-9.5.8.tar \
	&& cd /temp/postgres_9.5.8/sources/postgresql-9.5.8 \
	&& ./configure \
	&& make \
	&& make install \
	&& mkdir /usr/local/pgsql/data \
	&& chown postgres /usr/local/pgsql/data

ADD init.sh /init.sh
RUN chmod +x /init.sh && chown postgres /init.sh

RUN apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* \
	&& localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

USER postgres

ENV PGDATA /usr/local/pgsql/data
ENV PGHOME /usr/local/pgsql
ENV PATH $PATH:/usr/local/pgsql/bin

RUN $PGHOME/bin/initdb -D $PGDATA
RUN echo "host all  all    0.0.0.0/0  md5" >> /usr/local/pgsql/data/pg_hba.conf
RUN echo "listen_addresses='*'" >> /usr/local/pgsql/data/postgresql.conf

VOLUME /usr/local/pgsql/data/volume
EXPOSE 5432

RUN /init.sh

CMD ["-D $PGDATA >logfile 2>&1 &"]
ENTRYPOINT postgres
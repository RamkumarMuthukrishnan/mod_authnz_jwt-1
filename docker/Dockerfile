FROM debian:buster-slim as build

WORKDIR /build

RUN apt-get update && \
  apt-get install -y ca-certificates make automake git g++ libtool pkg-config autoconf libssl-dev check libjansson-dev libz-dev procps apache2 apache2-dev
  
ARG LIBJWT_VERSION=1.12.1
ARG MOD_AUTHNZ_JWT_VERSION=1.2.0

RUN git clone https://github.com/benmcollins/libjwt.git && \
    cd libjwt && \
    git checkout tags/v$LIBJWT_VERSION && \
    autoreconf -i && \
    ./configure && \
    make && \
    make install

RUN git clone https://github.com/RamkumarMuthukrishnan/mod_authnz_jwt-1.git && \
    cd mod_authnz_jwt-1 && \
    git checkout tags/v$MOD_AUTHNZ_JWT_VERSION && \
    autoreconf -ivf && \
    PKG_CONFIG_PATH=/usr/local ./configure && \
    make && \
    make install
    
FROM httpd:2.4

COPY --from=build /usr/local/lib/libjwt.so /usr/lib/x86_64-linux-gnu/libjwt.so.1
COPY --from=build /usr/lib/apache2/modules/mod_authnz_jwt.so /usr/local/apache2/modules/mod_authnz_jwt.so

RUN echo "LoadModule auth_jwt_module modules/mod_authnz_jwt.so" >> /usr/local/apache2/conf/httpd.conf

FROM ubuntu:18.04
 
# create the mssql user
RUN useradd -u 10001 mssql
 
# installing SQL Server
RUN apt-get update && apt-get install -y wget software-properties-common apt-transport-https
RUN wget -qO- https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/18.04/mssql-server-2019.list)"
RUN apt-get update && apt-get install -y mssql-server
 
# creating directories
RUN mkdir /var/opt/sqlserver
RUN mkdir /var/opt/sqlserver/data
RUN mkdir /var/opt/sqlserver/log
RUN mkdir /var/opt/sqlserver/backup
 
# set permissions on directories
RUN chown -R mssql:mssql /var/opt/sqlserver
RUN chown -R mssql:mssql /var/opt/mssql
 
# switching to the mssql user
USER mssql
 
# starting SQL Server
CMD /opt/mssql/bin/sqlservr

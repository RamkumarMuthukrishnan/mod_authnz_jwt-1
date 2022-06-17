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

FROM mcr.microsoft.com/mssql/server:2017-latest
ENV ACCEPT_EULA=Y
ENV SA_PASSWORD=abcDEF123#
ENV MSSQL_PID=Developer
ENV MSSQL_TCP_PORT=1433
WORKDIR /src
COPY filldata.sql ./filldata.sql
RUN (/opt/mssql/bin/sqlservr --accept-eula & ) | grep -q "Service Broker manager has started" &&  /opt/mssql-tools/bin/sqlcmd -S127.0.0.1 -Usa -PabcDEF123# -i filldata.sql


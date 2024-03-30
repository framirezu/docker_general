FROM php:8.2-apache

#ZONA HORARIA
RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/America/Lima /etc/localtime
RUN echo "date.timezone = America/Lima" > /usr/local/etc/php/conf.d/timezone.ini


#soporte ldap
RUN apt-get update && apt-get install libldap2-dev -y && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap


#soporte postgres
RUN apt-get update -y && apt-get install postgresql-client libpq-dev -y && docker-php-ext-install pdo pdo_pgsql


#soporte mysql
RUN apt-get update -y && apt-get install default-mysql-client -y && docker-php-ext-install mysqli pdo pdo_mysql 


# Install prerequisites required for tools and extensions installed later on.
ENV ACCEPT_EULA=Y
RUN apt-get update \
    && apt-get install -y apt-transport-https gnupg2 libpng-dev libzip-dev nano unzip \
    && rm -rf /var/lib/apt/lists/*

# Install prerequisites for the sqlsrv and pdo_sqlsrv PHP extensions.
# Some packages are pinned with lower priority to prevent build issues due to package conflicts.
# Link: https://github.com/microsoft/linux-package-repositories/issues/39
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/11/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    && echo "Package: unixodbc\nPin: origin \"packages.microsoft.com\"\nPin-Priority: 100\n" >> /etc/apt/preferences.d/microsoft \
    && echo "Package: unixodbc-dev\nPin: origin \"packages.microsoft.com\"\nPin-Priority: 100\n" >> /etc/apt/preferences.d/microsoft \
    && echo "Package: libodbc1:amd64\nPin: origin \"packages.microsoft.com\"\nPin-Priority: 100\n" >> /etc/apt/preferences.d/microsoft \
    && echo "Package: odbcinst\nPin: origin \"packages.microsoft.com\"\nPin-Priority: 100\n" >> /etc/apt/preferences.d/microsoft \
    && echo "Package: odbcinst1debian2:amd64\nPin: origin \"packages.microsoft.com\"\nPin-Priority: 100\n" >> /etc/apt/preferences.d/microsoft \
    && apt-get update \
    && apt-get install -y msodbcsql18 mssql-tools18 unixodbc-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pecl install sqlsrv pdo_sqlsrv
RUN docker-php-ext-enable sqlsrv pdo_sqlsrv 

# Habilita Soporte para SQLSERVER-2012 (TLS v1.0)
RUN echo "[system_default_sect] \nMinProtocol = TLSv1 \nCipherString = DEFAULT@SECLEVEL=1" >> /etc/ssl/openssl.cnf


# Reiniciar Apache.
RUN apachectl restart



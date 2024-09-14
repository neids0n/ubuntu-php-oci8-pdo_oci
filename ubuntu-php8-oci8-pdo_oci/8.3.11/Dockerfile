FROM ubuntu/apache2:2.4-22.04_beta

LABEL maintainer="neids0n"

# Atualizando o TimeZone
ENV TZ=America/Bahia
RUN apt-get update && apt-get install -y tzdata && \
ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Atualizar o repositório e instalar os pacotes necessários
RUN apt-get install -y software-properties-common && add-apt-repository ppa:ondrej/php && apt-get update && \
apt-get install -y vim less zip libaio1 alien wget php8.3 php8.3-xml php-xml php-common php8.3-cli php-pear \
php8.3-fpm php-json php8.3-opcache php8.3-readline libapache2-mod-php8.3 php8.3-common php8.3-dev

# Removendo erro AH00558 do Apache
RUN sed -i '$s/$/\n/' /etc/apache2/apache2.conf && sed -i '$i ServerName localhost' /etc/apache2/apache2.conf

# Baixando e instalando os pacotes do Oracle Instantclient
RUN cd ~ &&  wget https://download.oracle.com/otn_software/linux/instantclient/2111000/oracle-instantclient-basic-21.11.0.0.0-1.x86_64.rpm && \
wget https://download.oracle.com/otn_software/linux/instantclient/2111000/oracle-instantclient-sqlplus-21.11.0.0.0-1.x86_64.rpm && \
wget https://download.oracle.com/otn_software/linux/instantclient/2111000/oracle-instantclient-devel-21.11.0.0.0-1.x86_64.rpm && \
alien -i oracle-instantclient-basic-21.11.0.0.0-1.x86_64.rpm && alien -i oracle-instantclient-sqlplus-21.11.0.0.0-1.x86_64.rpm && \
alien -i oracle-instantclient-devel-21.11.0.0.0-1.x86_64.rpm && export ORACLE_HOME=/usr/lib/oracle/21/client64

# Baixando e instalando o OCI8
RUN cd ~ && pecl download oci8 && tar xzf oci8-3.3.0.tgz && cd oci8-3.3.0/ && phpize && \
./configure --with-oci8=instantclient,/usr/lib/oracle/21/client64/lib && make install && \
echo "extension=oci8.so" > /etc/php/8.3/mods-available/oci8.ini && \
ln -s /etc/php/8.3/mods-available/oci8.ini /etc/php/8.3/apache2/conf.d/oci8.ini && \
ln -s /etc/php/8.3/mods-available/oci8.ini /etc/php/8.3/cli/conf.d/oci8.ini

# Baixando e instalando o pdo_oci
# Site para localizar a versão do php: https://github.com/php/php-src/tree/PHP-8.3.11
RUN cd ~ && wget https://github.com/php/php-src/archive/refs/heads/PHP-8.3.11.zip && unzip PHP-8.3.11.zip && \
cd php-src-PHP-8.3.11/ext/pdo_oci && phpize && ./configure --with-pdo-oci=instantclient,/usr/lib/oracle/21/client64/lib,21 && \
make install && echo "extension=pdo_oci.so" > /etc/php/8.3/mods-available/pdo_oci.ini && \
ln -s /etc/php/8.3/mods-available/pdo_oci.ini /etc/php/8.3/apache2/conf.d/pdo_oci.ini && \
ln -s /etc/php/8.3/mods-available/pdo_oci.ini /etc/php/8.3/cli/conf.d/pdo_oci.ini && \
service apache2 restart && echo "<?php\nphpinfo();\n?>" > /var/www/html/phpinfo.php && \
cd ~ && rm -rf oracle-instantclient-* oci8-3.3.0* *PHP* *.xml

WORKDIR /var/www/html
EXPOSE 80
CMD apachectl -D FOREGROUND

# Laboratorio-LibreNMS
Laboratorio de SNMPv3 con LibreNMS

## 1 Actualizamos nuestro servidor 

```bash
sudo apt update
sudo apt upgrade
```

## 2 Ingresamos a modo root para realizar la mayoria de configuraciones 

```bash
sudo -i
```
## 3 Instalamos las NGINX, Dependencias y añadimos el usuario Librens

```bash
apt install acl curl fping git graphviz imagemagick mariadb-client mariadb-server mtr-tiny nginx-full nmap php-cli php-curl php-fpm php-gd php-gmp php-json php-mbstring php-mysql php-snmp php-xml php-zip rrdtool snmp snmpd whois unzip python3-pymysql python3-dotenv python3-redis python3-setuptools python3-systemd python3-pip
```
```bash
sudo apt update
```
```bash
useradd librenms -d /opt/librenms -M -r -s "$(which bash)"
```

## 4 Descargamos e Instalamos LibreNMS

```bash
cd /opt
git clone https://github.com/librenms/librenms.git
```

## 4 Asignamos los permisos.

```bash
chown -R librenms:librenms /opt/librenms
chmod 771 /opt/librenms
setfacl -d -m g::rwx /opt/librenms/rrd /opt/librenms/logs /opt/librenms/bootstrap/cache/ /opt/librenms/storage/
setfacl -R -m g::rwx /opt/librenms/rrd /opt/librenms/logs /opt/librenms/bootstrap/cache/ /opt/librenms/storage/
```

## 5 Inatalamos dependencias de PHP

```bash
su - librenms
```
```bash
./scripts/composer_wrapper.php install --no-dev
```
```bash
exit
```

## 6 Colocamos el Time Zone a nuestro servidor (Guatemala)

Buscamos con cntrl + W date.time y sustituimos por nuestro timedatectl set-timezone "America/Guatemala" y descomentamos.

```bash
nano /etc/php/8.1/fpm/php.ini
```
```bash
nano /etc/php/8.1/cli/php.ini
```
```bash
timedatectl set-timezone America/Guatemala
```

## 7 Configuramos MariaDB, añadimos las siguintes configuraciones y reiniciamos MariaDB

```bash
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
```bash
innodb_file_per_table=1
lower_case_table_names=0
```
```bash
systemctl enable mariadb
systemctl restart mariadb
systemctl status mariadb
```

Iniciamos Maria DB

En la seccion de password colocamos nuestra contraseña

```bash
mysql -u root
```
```bash
CREATE DATABASE librenms CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'librenms'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON librenms.* TO 'librenms'@'localhost';
exit
```

## 8 COnfiguramos PHP-FPM

```bash
cp /etc/php/8.1/fpm/pool.d/www.conf /etc/php/8.1/fpm/pool.d/librenms.conf
```
```bash
nano /etc/php/8.1/fpm/pool.d/librenms.conf
```

Cambiamos [www] a [librenms] y cambiamos user, group, listen:

```bash
user = librenms
group = librenms
listen = /run/php-fpm-librenms.sock
```

Como sugerencia si no tienen ninguna otra aplicaion WEB borrar el archivo www.conf

```bash
cd /etc/php/8.1/fpm/pool.d/
rm www.conf
```

## 9 Configuramos la pagina WEB:

```bash
nano /etc/nginx/conf.d/librenms.conf
```

En el siguiente comando cambiar IP del servidor por la suya.

```bash
server {
 listen      80;
 server_name (colocan su IP del servidor);
 root        /opt/librenms/html;
 index       index.php;

 charset utf-8;
 gzip on;
 gzip_types text/css application/javascript text/javascript application/x-javascript image/svg+xml text/plain text/xsd text/xsl text/xml image/x-icon;
 location / {
  try_files $uri $uri/ /index.php?$query_string;
 }
 location ~ [^/]\.php(/|$) {
  fastcgi_pass unix:/run/php-fpm-librenms.sock;
  fastcgi_split_path_info ^(.+\.php)(/.+)$;
  include fastcgi.conf;
 }
 location ~ /\.(?!well-known).* {
  deny all;
 }
}
```

Corremos los siguientes comandos para terminar con la configuracion.

```bash
rm /etc/nginx/sites-enabled/default
systemctl restart nginx
systemctl restart php8.1-fpm
```

## 10 Habilitar lnms para complementacion de comandos con tab.

```bash
ln -s /opt/librenms/lnms /usr/bin/lnms
cp /opt/librenms/misc/lnms-completion.bash /etc/bash_completion.d/
```

## 11 Configuramos SNMPD

Cambiamos RANDOMSTRINGGOESHERE por PUBLIC

```bash
cp /opt/librenms/snmpd.conf.example /etc/snmp/snmpd.conf
nano /etc/snmp/snmpd.conf
```
```bash
curl -o /usr/bin/distro https://raw.githubusercontent.com/librenms/librenms-agent/master/snmp/distro
chmod +x /usr/bin/distro
systemctl enable snmpd
systemctl restart snmpd
```
```bash
cp /opt/librenms/misc/librenms.logrotate /etc/logrotate.d/librenms
```

## 12 Habilitamos scheduler

```bash
cp /opt/librenms/dist/librenms-scheduler.service /opt/librenms/dist/librenms-scheduler.timer /etc/systemd/system/
```
```bash
systemctl enable librenms-scheduler.timer
systemctl start librenms-scheduler.timer
```

### 13 Accesemos por nuestro WEB Browser a nuestra IP del servido.

```bash
ip del servidor/install/checks
```
Seguimos los pasos para finalizar la configuracion. 

## 14 Por ultimo, en nuestro server habilitamos Dispatcher Service para corregir el error que nos aparece.

```bash
su - liberns
pip3 install -r requirements.txt
exit
```
```bash
cp /opt/librenms/misc/librenms.service /etc/systemd/system/librenms.service && systemctl enable --now librenms.service
```

## Esperamos unos momentos mientras el servidor active lo que hemos agregado y volvemos a chequear.

## 15 Agregamos un dispositivo para analizarlo con libreNMS. Todo dentro del WEB Browser. 




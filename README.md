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












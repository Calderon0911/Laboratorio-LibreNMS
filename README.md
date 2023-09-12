# Laboratorio-OpenNMS
Laboratorio de SNMPv3 con OpenNMS

## 1 Actualizamos SNMP y actializamos nuestro servidor.

```bash
sudo apt update
sudo apt install snmpd
```
```bash
sudo apt install snmp
```

## 2  Configurar SNMPv3 en el archivo snmpd.conf

```bash
sudo nano /etc/snmp/snmpd.conf
```
## 3 Añade o modifica la sección para SNMPv3 (remplaza miUsuario, miClaveAut y miClavePriv con tus propios valores):

```bash
createUser miUsuario SHA miClaveAut AES miClavePriv
rwuser miUsuario
```
cambiar mi usuario,miclaveaut y miclavePriv por los datos que deseen usar.

Para corroborar y confirmar las configuraciones corremos los siguientes comandos:

```bash
sudo systemctl restart snmpd
sudo systemctl status snmpd
```

## 4 Ahora procedemos a instalar LibreNMS y primero instalaremos las dependencias
```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update
sudo apt install -y acl curl composer fping git graphviz imagemagick mariadb-client mariadb-server mtr-tiny nginx-full nmap php7.4-cli php7.4-curl php7.4-fpm php7.4-gd php7.4-json php7.4-mbstring php7.4-mysql php7.4-snmp php7.4-xml php7.4-zip snmp snmpd whois python3-pymysql python3-dotenv python3-redis python3-setuptools
```



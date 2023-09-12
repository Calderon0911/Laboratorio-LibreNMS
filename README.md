# Laboratorio-OpenNMS
Laboratorio de SNMPv3 con OpenNMS

## 1 Actualizamos SNMP y actializamos nuestro servidor.

```bash
sudo apt update
sudo apt install snmpd
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

## 4 Ahora procedemos a instalar OpenNMS

```bash
sudo apt install openjdk-11-jdk
```

## 5 Correr los siguientes comandos para descargar los repositorios de OpenNMS

```bash
wget -O - https://debian.opennms.org/OPENNMS-GPG-KEY | sudo apt-key add -
sudo apt install software-properties-common
sudo add-apt-repository "deb [arch=amd64] https://debian.opennms.org stable main"
sudo apt update
sudo apt install opennms
```

## 6 Actualizar e instalar OpenNMS:

```bash

#Autor: Fagner Geraldes Braga  
#Data de criação: 12/12/2021  
#Data de atualização: 18/12/2021  
#Versão: 0.02
***
Dia 4 - 11/12/2021
***
## Montando as partições

### Sob demanda
```bash
# /srv/www/express
/dev/sdb1
# /var/www/asf
/dev/sdb2
# /var/log/apache2
/dev/sdc1
```
```bash
mkdir -p /srv/www/express
mkdir -p /var/www/asf
mkdir -p /var/log/apache2
```
```bash
mount -t xfs /dev/sdb1 /srv/www/express/
mount -t xfs /dev/sdb2 /var/www/asf/
mount -t xfs /dev/sdc1 /var/log/apache2/
# mount - comando para montagem de dispositivos, partições
# -t - tipo do sistema de arquivos
```
```bash
df -vhT
lsblk
```
```bash
# Desmontando partições
# Sair do diretório no momento da desmontagem

umount /srv/www/express
umount /var/www/asf
umount /var/log/apache2
```

```bash
# Montagem das partições de forma automática ao inicializar
vim /etc/fstab
/dev/sdb1 /srv/www/express xfs defaults 0 0
/dev/sdb2 /var/www/asf xfs defaults 0 0
/dev/sdc1 /var/log/apache2 xfs defaults 0 0
:x!

# Verifica o arquivo fstab e monta as partições que faltam
mount -av
```

Desligar a máquina Intranet  
Ligar a máquina cliente interno

```bash
# Particionando o disco sdb com 2 partições de 5GB
fdisk /dev/sdb
n
p
1
enter
+5G
n
p
2
enter
enter
w
lsblk

# Formatando as partições como ext4 e ext3
mkfs -t ext4 /dev/sdb1
mkfs -t ext3 /dev/sdb2
lsblk -f

# Definindo label para as partições
tune2fs -L git /dev/sdb1
tune2fs -L backup /dev/sdb2

# Montando as partições
mkdir -p /var/git
mkdir -p /srv/backup
mount -t ext4 /dev/sdb1 /var/git
mount -t ext3 /dev/sdb2 /srv/backup
df -vhT

# Instalando o Git e clonando um repositório
apt install git -y
cd /var/git
git clone https://github.com/fagnerfgb/infralabs.git
du -sh
```
# Empacotamento de arquivos

```bash
# CPIO
find /var/git | cpio -ov > /srv/backup/gitfagner.cpio
# -o - cria o Empacotamento
# -v - verbose

ls -lh /srv/backup

cpio -iv --list < /srv/backup/gitfagner.cpio
# -i - extrai o Empacotamento
# -v - verbose
# --list para listar e não extrair

# TAR
tar -cvf /srv/backup/gitfagner.tar /var/git
# -c - cria o empacotamento
# -v - verbose
# -f - caminho onde o empacotamento será salvo
```

# Compactação de arquivos e pacotes

gzip - rápido  
bzip2 = comprime mais  
xz = comprime mais ainda  

```bash
# Compactando pacotes
gzip /srv/backup/gitfagner.cpio
bzip2 /srv/backup/gitfagner.tar
xz /srv/backup/gitfagner.cpio

# Descompactando pacotes
gunzip /srv/backup/gitfagner.cpio.gzip
bunzip2 /srv/backup/gitfagner.tar.bzip2
unxz /srv/backup/gitfagner.cpio.xz
```

# Empacotar e Comprimir arquivos

 
| Comando | App de compactação  |
| :--:    | :--:                |
| -j      | bzip2               |  
| -J      | xz                  | 
| -z      | gzip                | 

```bash
tar -cvJf /srv/backup/conf.tar.xz /etc/

# Verificar integridade do arquivo
cd /srv/backup
sha1sum * 2> /dev/null > /opt/sum.txt

# Converter sistema de arquivos ext3 em ext4
# Sair do diretório da partição que terá o sistema de arquivos convertido
cd /

# Desmontar a partição
umount /srv/backup

tune2fs -O extents,uninit_bg,dir_index /dev/sdb2
# -O - cria ou limpa as funcionalidades do sistema de arquivos

lsblk -f

vim /etc/fstab
/dev/sdb1 /var/git ext4 defaults 0 0
/dev/sdb2 /srv/backup ext4 defaults 0 0
:x!

mount -av
cd /srv/backup
sha1sum * > /opt/sum2.txt 2> /dev/null
diff -s /opt/sum.txt /opt/sum2.txt

# Apagar os arquivos do diretório
rm -rf /var/git/*

#Restaurando o backup
cpio -iv < srv/backup/gitfagner.cpio  

tar -xvf /srv/backup/gitfagner.tar -C /tmp/
#-x = extrair
#-C = local de destino
```
# Redes

## Máquina Intranet  
server  
dns  
webserver  
enp0s3 - rede interna - 192.168.1.10  
enp0s8 - rede interna - 192.168.1.11

```bash
vim /etc/network/interfaces

# Configurando placa de rede com ip estático 
auto enp0s3 
iface enp0s3 inet static 
    address 192.168.1.10 
    netmask 255.255.255.0 
    gateway 192.168.1.1

# Configurando placa de rede com ip estático 
auto enp0s8 
iface enp0s8 inet static 
    address 192.168.1.11 
    netmask 255.255.255.0 
:x!
```
```bash
vim /etc/resolv.conf

# Configurando o DNS
search asf.com
domain asf.com
nameserver 8.8.8.8
nameserver 192.168.1.10
nameserver 192.168.1.20
:x!
```
```bash
# Alterando o atributo do arquivo para que ele não possa ser alterado
chattr +i /etc/resolv.conf
```
```bash
# Alterando o nome da máquina
hostnamectl set-hostname intranet.asf.com
```
```bash
systemctl restart networking
ip a
ip route
shutdown -r now
```

## Máquina Datacenter  
server  
dns  
e-mail  
enp0s3 - rede interna - 192.168.1.20  

```bash
vim /etc/network/interfaces

# Configurando placa de rede com ip estático 
auto enp0s3 
iface enp0s3 inet static 
    address 192.168.1.20 
    netmask 255.255.255.0 
    gateway 192.168.1.1
:x!
```
```bash
vim /etc/resolv.conf

# Configurando o DNS
search asf.com
domain asf.com
nameserver 8.8.8.8
nameserver 192.168.1.10
nameserver 192.168.1.20
:x!
```
```bash
# Alterando o atributo do arquivo para que ele não possa ser alterado
chattr +i /etc/resolv.conf
```
```bash
# Alterando o nome da máquina
hostnamectl set-hostname datacenter.asf.com
```
```
systemctl restart networking
ip a
ip route
shutdown -r now
```

## Máquina Storage
server  
dhcp  
nfs  
enp0s3 - rede interna - 192.168.1.30  

```bash
vim etc/sysconfig/network-scripts/ifcfg-enp0s3

# Configurando placa de rede com ip estático 
TYPE="Ethernet"
BOOTPROTO"static"
NAME="enp0s3"
DEVICE="enp0s3"
ONBOOT="yes"
IPADDR=192.168.1.30
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
:x!
```
```bash
vim /etc/resolv.conf

# Configurando o DNS
search asf.com
domain asf.com
nameserver 8.8.8.8
nameserver 192.168.1.10
nameserver 192.168.1.20
:x!
```
```bash
# Alterando o atributo do arquivo para que ele não possa ser alterado
chattr +i /etc/resolv.conf
```
```bash
# Alterando o nome da máquina
hostnamectl set-hostname storage.asf.com
```
```bash
ifdown enp0s3
ifup enp0s3
ip a
ip route
shutdown -r now
```

## Máquina Gateway  
server  
proxy  
firewall  
vpn  
roteador  
enp0s3 - nat (pra sair pra internet) - 10.0.2.15  
enp0s8 - rede interna - 192.168.1.1  

ensp0s9 - host only  
No Gerenciador de Redes do Hospedeiro do VirtualBox  
VirtualBox Host-Only Ethernet Adapter  
IP: 200.50.100.1  
Máscara: 255.255.255.0  
Não Habilitar DHCP  

```bash
vim etc/sysconfig/network-scripts/ifcfg-enp0s8

# Configurando placa de rede com ip estático 
TYPE="Ethernet"
BOOTPROTO"static"
NAME="enp0s8"
DEVICE="enp0s8"
ONBOOT="yes"
IPADDR=192.168.1.1
NETMASK=255.255.255.0
:x!
```
```bash
vim etc/sysconfig/network-scripts/ifcfg-enp0s9

# Configurando placa de rede com ip estático 
TYPE="Ethernet"
BOOTPROTO"static"
NAME="enp0s9"
DEVICE="enp0s9"
ONBOOT="yes"
IPADDR1=200.50.100.10
IPADDR1=200.50.100.20
NETMASK=255.255.255.0
:x!
```
```bash
vim /etc/resolv.conf

# Configurando o DNS
search asf.com
domain asf.com
nameserver 8.8.8.8
nameserver 192.168.1.10
nameserver 192.168.1.20
:x!
```
```bash
# Alterando o atributo do arquivo para que ele não possa ser alterado
chattr +i /etc/resolv.conf
```
```bash
# Alterando o nome da máquina
hostnamectl set-hostname storage.asf.com
```
```bash
ifdown enp0s8
ifdown enp0s9
ifup enp0s8
ifup enp0s9
ip a
ip route
shutdown -r now
```

## Máquina Cliente Interno
enp0s3 - rede interna - 192.168.1.100  
cd /etc/network  
vim interfaces  

```bash
# Configurando placa de rede com ip estático 
auto enp0s3 
iface enp0s3 inet static 
    address 192.168.1.100 
    netmask 255.255.255.0 
    gateway 192.168.1.1
:x!
```
```bash
vim /etc/resolv.conf

# Configurando o DNS
search asf.com
domain asf.com
nameserver 8.8.8.8
nameserver 192.168.1.10
nameserver 192.168.1.20
:x!
```
```bash
# Alterando o atributo do arquivo para que ele não possa ser alterado
chattr +i /etc/resolv.conf
```
```bash
# Alterando o nome da máquina
hostnamectl set-hostname interno.asf.com
```
```bash
vim /etc/hosts

192.168.1.100 	interno.asf.com 	interno
192.168.1.10 	intranet.asf.com 	intranet
192.168.1.11 	express.asf.com		express
192.168.1.20	datacenter.asf.com	datacenter
192.168.1.30 	storage.asf.com		storage
192.168.1.1	    gateway.asf.com		gateway
:x!
```
```bash
systemctl restart networking
ip a
ip route
shutdown -r now
```

## Máquina Cliente Externo
enp0s3 - host only  

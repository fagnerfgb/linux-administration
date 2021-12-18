#Autor: Fagner Geraldes Braga
#Data de criação: 12/12/2021
#Data de atualização: 18/12/2021
#Versão: 0.02
***
Dia 3 - 06/12/2021
***
```bash
ls -l /dev/sda*
file /dev/sda*
stat /dev/sda
```
```bash
cd /var/
mkdir audit
cd audit/
# Grava tudo o que é digitado no terminal num arquivo
script block.txt 

# Para a gravação
exit
```
```bash
# Lista os blocos
lsblk 

# Manipula tabela de partição de disco
fdisk -l

# -l lista as tabelas de partição

# Relata o espaço de disco usado pelo sistema de arquivo
df -vhT 
# -h = humano
# -T = imprime o tipo do sistema de arquivos
# -v = pode ignorar. Para compatibilidade com versões anteriores

df -i
# -i = inodes

lsblk  -f
# -f  = informações sobre os sistemas de arquivos
```

* device  
* label  
* UUID  


```bash
# monta um sistema de arquivos
mount 

/dev/sda1 on /boot type ext2 (rw,relatime)
/dev/mapper/interno--vg-root on / type ext4 (rw,relatime,errors=remount-ro)
# mostra informações dos blocos
cat /proc/partitions 

# mostra os ids dos blocos
blkid 

# uso do disco
du 
du -sh * 2> /dev/null

# mostra quanto tempo o sistema está ligado e quantos usuários estão # logados e a média de carga do sistema
uptime 

# mostra processos do Linux
top 

# visualizador de processos interativo - htop 
apt install htop -y

# free = mostra a quantidade de memória livre e utilizada no sistema
free -mh
#-m = mebibytes
#-h = humano

cat /proc/meminfo
cat /proc/swaps
swapon -s
cat /proc/cpuinfo

# lscpu = mostra informações sobre a arquitetura do processador
lscpu

# lsusb = lista dispositivos USB
lsusb
# -v = verbose


# lspci = lista todos os dispositivos PCI
lspci
# -k = mostra drivers do kernel
# -v = verbose
# -vv =  muito verbose
# -vvv = super verbose

# lshw - lista hardware
apt install lshw -y
lshw

#Command line system information script for console and IRC
apt install inxi -y
inxi -Fxz
``` 
### Máquina Intranet 
Criar 2 discos de 10GB cada no VirtualBox

### Máquina Cliente Interno
Criar 2 discos de 10GB cada no VirtualBox

### Máquina Storage
Criar 7 discos de 10GB cada no VirtualBox

# Particionamento de discos

1. particionar  
2. formatar  
3. montar 


## Particionar o disco sdb com 2 partições de 5GB
```bash 
# partição 1
fdisk /dev/sdb
n # = adiciona uma nova partição
p # = primária
1
enter
+5G
```
```bash 
# partição 2
n
p
enter
enter
enter
w # = grava e sai
```
## Particionar o disco sdc com 1 partição de 10GB
```bash
fdisk /dev/sdc
n # = adiciona uma nova partição
p # = primária
enter
enter
enter
w
```
```bash
lsblk
```
## Formatar as partições dos discos
```bash
apt install vim  xfsprogs parted -y
mkfs -t xfs /dev/sdb1
mkfs -t xfs /dev/sdb2
mkfs -t xfs /dev/sdc1
```
```bash
lsblk -f
```
## Colocando o Label nas partições
```bash
xfs_admin -L express /dev/sdb1
xfs_admin -L asf  /dev/sdb2
xfs_admin -L logs /dev/sdc1
```

#Autor: Fagner Geraldes Braga 
#Data de criação: 12/12/2021  
#Data de atualização: 18/12/2021  
#Versão:0.03
***
Dia 5 - 12/12/2021
***
# Configuração do Firewall IPTABLES
```bash
#Máquina Gateway
# Verifica o status do firewalld no CentOS
systemctl status firewalld

# Parar e desabilitar o firewalld 
systemctl stop firewalld
systemctl disable firewalld

# Instalação do serviço do IPTABLES
yum install iptables-services -y

# Habilitar o serviço do IPTABLES
systemctl enable iptables

# Configurar o roteamento
vim /etc/sysctl.conf
    net.ipv4.ip_forward=1
:x!

# Mostra as configurações do sysctl
sysctl -p

# Instalação do Git
yum install git -y

# Clonagem de repositório
cd /root/
git clone https://github.com/fagnerfgb/linux-administration

# Copiar arquivo firewall.sh para o diretório /sbin
cp /root/linux-administration/gateway/sbin/firewall.sh  /sbin/

# Habilitar IPTABLES
firewall.sh

# Mostra as configurações do Firewall
iptables -L
```


```bash
# Ligar máquina Storage
# Testar se computador possui internet
ping www.terra.com.br

# Parar e desabilitar o firewalld 
systemctl stop firewalld
systemctl disable firewalld

# Instalação do Git e do DHCP Server
yum install git dhcp-server -y

# Clonagem de repositório
cd /root/
git clone https://github.com/fagnerfgb/linux-administration

# Copiar arquivo dhcpd.conf para o diretório /etc/dhcp
cp /root/linux-administration/storage/dhcp/dhcpd.conf /etc/dhcp
```
Etapas para entrega de endereço DHCP  

Discovery  
Offer  
Request  
Ack  

```bash
# Acessar máquina Storage através da máquina Cliente Interno usando o SSH
ssh -p 22 analista@192.168.1.30

vim /etc/dhcp/dhcpd.conf
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp*/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#

ddns-update-style none;
#deny unknown-clients;

# Criando um escopo DHCP
subnet 192.168.1.0 netmask 255.255.255.0 {
	range 192.168.1.111 192.168.1.222;
	authoritative;
	interface enp0s3;
	option domain-name "asf.com";
	option domain-name-servers 192.168.1.10,192.168.1.20,8.8.8.8;
	option netbios-name-servers 192.168.1.40;
	option broadcast-address 192.168.1.255;
	option routers 192.168.1.1;
	default-lease-time 600;
	max-lease-time 7200;
	min-lease-time 120;
}
:x!

# Reiniciar o serviço do DHCP
systemctl restart dhcpd
systemctl enable dhcpd

# Verificar logs para acompanhar funcionamento do DHCP
tail -f /var/log/messages

# máquina Cliente Interno vai pegar ip do dhcp na interface enp0s8
```
```bash
# máquina Storage
# Criando reserva de endereços IP
vim /etc/dhcp/dhcpd.conf

host cliente-interno {
	# mac da placa de rede enp0s8 da máquina cliente interno
    hardware ethernet 08:00:27:FA:A2:97;
	# aloca o endereço ip abaixo para a interface enp0s8 da máquina cliente interno
    fixed-address 192.168.1.150;
}
:x!
```
```bash
máquina Cliente Interno
# Forçar o cliente a trocar informações com o DHCP
dhclient enp0s8
```

# Gerenciamento de Pacotes

## Debian

### Gerenciamento em Alto Nível

```bash
# Verificando o arquivo com a lista de repositórios
cd /etc/apt
cat sources.list

# Atualiza a base de índices
apt update

# Atualiza os pacotes instalados para a versão mais recente
apt upgrade

# Pesquisa pacotes de tenha alguma relação com a palavra-chave pesquisada
apt search tcpdump
apt-cache search tcpdump

# Mostra informações do pacote
apt show tcpdump
apt-cache show tcpdump

# Instala o pacote
apt install tcpdump -y

# Mostra a lista de todos os pacotes
dpkg -la
## ii - pacote instalado

# Mostra se o pacote pesquisado está instalado
dpkg -l tcpdump

# Mostra os arquivos que pertencem ao pacote e onde eles estão
dpkg -S tcpdump

cd /tmp

# Remove um pacote
apt remove htop

apt install -d htop
# -d = download only

cp /var/cache/apt/archives/htop_2.2.0-1+b1_amd64.deb /tmp/

# Baixando código fonte 
cd /opt
apt source htop

# Preparando sistema para compilação
apt build-dep htop

# Limpeza de pacotes depreciados
apt autoclean

# Limpeza do instalador
apt clean

# Limpeza de pacotes órfãos
apt autoremove
```

### Gerenciamento em Baixo Nível
```bash
cd /tmp/
# Informações do pacote 
dpkg -I htop_2.2.0-1+b1_amd64.deb

# Quais são os arquivos do pacote e onde serão instalados (Antes da Instalação)
dpkg -c htop_2.2.0-1+b1_amd64.deb

# Instalar o pacote
dpkg -i htop_2.2.0-1+b1_amd64.deb

# Quais são os arquivos do pacote e onde serão instalados (Depois de Instalado)
dpkg -L htop

# Remove o programa e preserva os arquivos
dpkg -r htop

# Remove tudo
dpkg -P htop

# Alien - Converte programa para outro formato
# Ex: .deb para .rpm
apt install alien build-essential -y

# Converte pacote .deb para .rpm (-r)
alien -r htop_2.2.0-1+b1_amd64.deb

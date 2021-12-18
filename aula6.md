#Autor: Fagner Geraldes Braga  
#Data de criação: 13/12/2021  
#Data de atualização: 18/12/2021  
#Versão: 0.02
***
Dia 6 - 13/12/2021
***
máquina Gateway

```bash
iptables -t filter -L INPUT
```
```bash
vim /sbin/firewall.sh
# linha 107 - Habilita conexão SSH da máquina Cliente Interno
# 4 - Habilita o SSH do node Cliente Interno e Externo
iptables -A INPUT -p tcp -i enp0s8 -s $CLI -d $GTW --dport 22 -j ACCEPT
```
```bash
firewall.sh
```
Gerenciamento de Pacotes de Alto Nível (CentOS)
***
```bash
# Mostra a lista de repositórios habilitados no computador
yum repolist

# Dentro deste diretórios estão os arquivos dos repositórios do CentOS
cd /etc/yum.repos.d/

# Visualizando um arquivo com informações do repositório
cat CentOS-Linux-BaseOS.repo
[baseos] #id do pacote
name=CentOS Linux $releasever - BaseOS
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=BaseOS&infra=$infra
#baseurl=http://mirror.centos.org/$contentdir/$releasever/BaseOS/$basearch/os/
gpgcheck=1 # verifica se o pacote é confiável
enabled=1 = #1 = repositório ativo
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial # assinatura do pacote
```
```bash
# Verifica se tem pacotes a serem atualizados
yum check-update

# Atualiza os pacotes desatualizados
yum update

# Atualiza um pacote específico
yum upgrade pacote

# Procura um pacote
yum search htop

# Mostra informações do pacote
yum info htop

# Instala um pacote 
yum install htop -y

# Verifica se o pacote foi instalado
rpm -q htop

# Informa no nome completo do pacote instalado
rpm -qf /bin/vim

# Remove um pacote
yum remove htop -y

# Instala o pacote yum-utils
yum install yum-utils -y

cd /tmp
# Baixa pacote, mas não instala
yumdownloader htop
```
Grupos de Pacotes
```bash
yum group list
yum groupinfo "Ferramentas de Desenvolvimento"
yum groupinstall "Ferramentas de Desenvolvimento"
yum groupremove "Ferramentas de Desenvolvimento"
```
```bash
# Limpeza do cache
yum clean all

# Reinstala o pacote
yum reinstall nano
```
Gerenciamento de Baixo Nível (CentOS)
***
```bash
# Lista todos os pacotes instalados
rpm -qa
***
# Informações do pacote instalado informado
rpm -qi hwdata
***
# Mostra o que tem dentro do pacote
rpm -ql nano
***
# Informações do pacote que foi baixado
rpm -qpi htop-3.0.5-1.el8.x86_64.rpm
***
# Informa as dependências do pacote (-R)
rpm -qpR htop-3.0.5-1.el8.x86_64.rpm
***
# Verifica se o pacote está OK (--test)
rpm -ivh --percent --test htop-3.0.5-1.el8.x86_64.rpm
***
# Instalar pacote
rpm -ivh --percent htop-3.0.5-1.el8.x86_64.rpm
***
# Testar o pacote antes de removê-lo
rpm -evh --test htop
-e = remove
--test = testar
***
# Remover o pacote
rpm -evh htop
***
# Upgrade do pacote
rpm -Uvh pacote
***
# Verifica os arquivos que possuem integridade alterada
rpm -Va
```
| Caracter | Descrição | 
| -------- | --------- |
|S         |file Size differs |
|M         |Mode differs (includes permissions and file type)
|5         |digest (formerly MD5 sum) differs
|D         |Device major/minor number mismatch
|L         |readLink(2) path mismatch
|U         |User ownership differs
|G         |Group ownership differs
|T         |mTime differs
|P         |caPabilities differ



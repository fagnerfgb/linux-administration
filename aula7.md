#Autor: Fagner Geraldes Braga  
#Data de criação: 14/12/2021  
#Data de atualização: 18/12/2021  
#Versão: 0.02
***
Dia 7 - 14/12/2021
***
# Compilação de Pacotes

## Máquina Cliente Interno

```bash
cd /opt/
ls -lh

# Verifica se o htop está instalado
dpkg -l htop

cd htop-2.2.0/
# ./configure - prepara o ambiente e gera o arquivo Makefile
# make - faz a compilação
# make clean - limpeza de resíduos quando houver problema na compilação
# make install - instala os dados compilados e coloca os arquivos nos diretórios específicos
# make uninstall - remove o programa compilado e instalado 
./configure && make && make install

#Por padrão o programa fica instalado em /usr/local
```

# Gerenciamento de Bibliotecas
```bash
# Mostra as bibliotecas utilizadas pelo programa
ldd /bin/pwd

/lib/x86_64-linux-gnu/ = local da maioria das bibliotecas compartilhadas

# Visualiza todas as bibliotecas
ldconfig -p

# Movendo uma biblioteca do diretório conhecido
mkdir /bibliotecas
mv /lib/x86_64-linux-gnu/libacl.so.1 /bibliotecas/
ldd /bin/mv
libacl.so.1 => not found

cd /etc/ld.so.conf.d/
mv /lib/x86_64-linux-gnu/libselinux.so.1 /bibliotecas/
export LD_LIBRARY_PATH=/bibliotecas

vim /etc/profile
export LD_LIBRARY_PATH=/bibliotecas

vim bibliotecas.conf
/bibliotecas
:x!

ldconfig
ldd /bin/ls
libselinux.so.1 => /bibliotecas/libselinux.so.1 (0x00007f407fa52000)
```
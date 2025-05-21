# Acessando o cluster

O cluster está em uma rede interna da UFMG sem acesso direto à internet, portanto seu acesso é feito **somente** via ssh.

## Pré-requisitos:
  - Conta gerada pelo CRC vinculada ao grupo *speed*
  Isso pode ser feito enviando um email do seu orientador para eles, solicitando acesso.
  - **Obs:** Recomenda-se usar o sistema Linux para acessar o mesmo por facilidade de uso e tendo em vista que toda documentação foi testada apenas em ambiente Linux. Mas para um appendice usando windows [clique aqui](#passos-para-windows-puro---como-fazer-conexão-com-o-sonic-windows)


## Passo-a-passo (resumo):
  [1) Acessando o login.dcc.ufmg.br](#1-Acessando-o-login.dcc.ufmg.br)<br>
      - Para ter acesso ao cluster é necessário primeiro logar na máquina externa [login.dcc.ufmg.br]() (antiga mica), conforme as instruções abaixo.<br>
  [2) Acessando a phocus4](#3-Acessando-a-phocus4)<br>
      -  Existem DNSs nas máquinas que reconhecem os hostnames mencionados.<br>
      - O cluster só é acessível do nó de login phocus4, sendo necessário logar-se nele para realizar qualquer tarefa.<br>
<br>

**Bônus**<br>
    [Login por chave pública](#Login-por-chave-pública)<br>
    [Login por chave pública e tunneling](#Login-por-chave-pública-e-tunneling)<br>
    [Too Long Didn't Read (TLDR)](#tldr)<br>
___

## Usando ssh pela primeira vez

### 1)  Acessando o login.dcc.ufmg.br

Por meio do protocolo ssh é possível abrir uma conexão segura com uma máquina externa, retornando um terminal de comandos. Para usuários de Linux, a grande maioria de distribuições já são disponibilizadas com um cliente ssh, porém se não for o seu caso, basta instalar qualquer cliente ssh (recomenda-se openssh-client). Para acessar uma máquina remota basta o comando:

```console
user@host:~$ ssh username@login.dcc.ufmg.br
(username@login.dcc.ufmg.br) Password:
FreeBSD 11.2-RELEASE-p14 (GENERIC) #0: Mon Aug 19 22:38:50 UTC 2019

Welcome to LOGIN.dcc.ufmg.br!

################################################  
#                                              #
#    UNIVERSIDADE  FEDERAL  DE  MINAS  GERAIS  #
#     Departamento de Ciencia da Computacao    #
#                                              #
################################################

### OBS: Utilizar o diretorio /var/tmp para  ###
###      armazenamento temporario.           ###

Bem vindo!

[username@login ~]$
```

Ao fazer isso será pedida a senha do seu usuário CRC (username). Na primeira vez que você loga em uma maquina nova, o ssh poderá te perguntar se a máquina que está logando realmente é a que você quer (ip e chave ECDSA não aceitas ainda):

```console
The authenticity of host 'node (192.164.99.99)' can't be established.
ECDSA key fingerprint is 00:11:22:33:44:55:66:77:88:99:aa:bb:cc:dd:ee:ff.
Are you sure you want to continue connecting (yes/no)? 
```

###  2) Acessando a phocus4
A phocus4 é a máquina de login do cluster Sonic. Somente a partir dela é possível acessar as máquinas de computação (e.g., gorgonas). O CRC provê um servidor LDAP para a sua rede interna, que é usado pelo Sonic. Isso significa que o seu username e senha mantidos pelo CRC (o mesmo usado para acessar login.dcc.ufmg.br) são disponíveis em todas as máquinas do Sonic. 

Para logar na phocus4 basta:

```console
username@login:~$ ssh phocus4
username@phocus4's password: 
username@phocus4:~$ 
```

Na phocus4 você poderá usar o cluster normalmente. Para fechar a conexão remota basta usar o comando exit, ou pelo atalho Ctrl+D.

___


## Login por chave pública

Como você pode ter percebido, precisar passar por 2 logins vai ficar cansativo rapidamente. Porém é possível fazer o login direto com apenas 1 comando ssh e sem precisar colocar senha. Existem duas formas principais de se aferir acesso a um usuário no login via ssh: (i) verificação de senha, ou (ii) verificação de chaves. O ssh usa um modelo de comunicação criptografada asimétrica. Nele existem duas chaves, uma publica e uma privada. Entregando sua chave publica para alguma máquina que se deseja acessar (por um canal seguro, como a própia conexão feita com senha), é possivel que essa máquina verifique que você realmente é quem diz ao submeter sua chave privada em seguida (feito automaticamente pelo seu cliente ssh). Em termos práticos, se dermos nossa chave pública para um nó como o login.dcc.ufmg.br, ela poderá saber que você realmente é quem diz ser. 

Caso seja sua primeira vez usando uma chave pública ssh é necessário antes a geração dessas de sua máquina privada. Não é preciso usar configurações especiais, apenas apertando enter nos pedidos de input:

```console
user@host:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user/.ssh/id_rsa
Your public key has been saved in /home/user/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:80ifYzToRzC+gjJ7%zgtHPT3!uaCUILn3hjUmtm5ffU user@host
The key's randomart image is:
+---[RSA 3072]----+
|  .              |
| . =  .          |
|..=              |
|o+0o. o.         |
| *.+o..oS        |
|. *.a.o*.E .     |
| o.O oo== +      |
|o   =.+.=.       |
|.=   ..*.        |
+----[SHA256]-----+
```

Agora foram gerados 2 aqruivos na sua máquina pessoal em /home/username/.ssh: id_rsa e id_rsa.pub, sendo o segundo a sua chave pública (.pub). Para fazer o acesso via chave ssh, basta colocar o conteudo inteiro de id_rsa.pub em um arquivo authorized_keys na máquina remota (estamos assumindo que você está na sua máquina pessoal $host$ com o seu username $user$):

```console
user@host:~$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABADABagQCdubTA8bEGNHaP4zPevSPHbjkYnCMX5dOfxYvd44yr8e8/O26VEJIuMNzpsVkxW+A4sSEBvSz2wbezT7YcZ5zRy/jEBRrnC4D2qUBCsJ5nyKeaIAE9dqwJoemgfu+1P0Ddv2qYZTECGsu0aA8I1TqFcAqFWbktWqV872/62vncO5VWxLulzGgFg16qNxuUKTGJpD3BMi5DN/7Mxgcgpu9BAOUksxVEbY9KcAvSWVmzGmySKno3USRwXmJQNi9dSo1zHaoPTSLgtzYEh9cm87VdZNVMvLDGoGXT7Jq9gj0thIrYR2gLEY19Xrvdy9aDHsX6JiNSDsWikcyt5XNLYH55BMubNn/+2Kjsl6/PY/VPJa71++FTWnQyvXE6QKXcuPoRywDpBJ8qDNAowTngfV1hkVi+axQBTxRfB7b7jH78FQnu42qjRejgl6ot0H+mriktu13çmRMJC7BGek1QJlaUzxKGVcg++VPgbnBT4CBA9bsWE6+gEcztk= user@host
user@host:~$ ssh username@login.dcc.ufmg.br
(username@login.dcc.ufmg.br) Password:
FreeBSD 11.2-RELEASE-p14 (GENERIC) #0: Mon Aug 19 22:38:50 UTC 2019 ....
[username@mica ~]$ mkdir ~/.ssh
[username@mica ~]$ vim ~/.ssh/authorized_keys
... colar a chave pública, digitar ":wq" + Enter para sair
[username@mica ~]$ chmod 644 ~/.ssh/authorized_keys
[username@mica ~]$
```

Note o 'chmod 644' ao fim da configuração. Se não alterar a permissão de 'authorized_keys' o cliente ssh da máquina remota não conseguirá ler o seu 'authorized_keys', pedindo sempre a senha.

Para testar se o login com chave pública está funcionado basta desconectar da máquina remota e tentar a conexão novamente. Desta vez, não será pedida senha. Isso deve ser feito em todas as máquinas (login.dcc.ufmg.br e phocus4) usando a mesma chave pública de seu computador pessoal.

___


## Login por chave pública e tunneling

Porém, o acima só resolve a parte de pedir senha. Ainda são necessários 2 comandos de ssh para acessar o cluster (via phocus4). Esse processo pode ser automatizado por um tunneling ssh. Existe a possibilidade de criar um arquivo de configuração na sua máquina pessoal que descreve como deve ser feita a conexão com uma máquina remota via ssh. Coisas como o ip ou hostname, usuário de login, local da chave pública, porta a ser usada, entre outras. Esse arquivo se encontra em ~/.ssh/config, podendo não existir inicialmente. Se for o caso, basta criá-lo para o usar, não sendo necessário nenhum outro tipo de configuração. Abaixo vem um exemplo de um arquivo config de ssh para acessar todas as máquinas remotas já mencionadas (basta copiar isso no arquivo config de sua máquina pessoal substituindo o username):

```
Host dcc
  Hostname login.dcc.ufmg.br
  User username

Host phocus4
  ProxyCommand ssh dcc -W %h:%p
  Hostname phocus4.speed.dcc.ufmg.br
  User username
```

No script acima está descrito que: para se conectar à phocus4 com um usuário username é necessário fazer um tunelamento via o host dcc. Este host dcc é acessível pelo hostname login.dcc.ufmg.br assim como a phocus4 é conhecida pelo host dcc pelo nome phocus4.speed.dcc.ufmg.br. Dessa forma, ao rodar o comando 'ssh phocus4', todo esse processo será automatizado. O cliente ssh também assume que pode existir um par de chaves em '~/.ssh' e tentará enviar essas chaves para o host remoto, evitando o pedido de senha. O script acima deve estar presente na sua máquina pessoal, não sendo necessário colocá-lo em outra máquinas. Abaixo o resultado após a criação do arquivo de configuração:

```console
user@host:~$ ssh phocus4
Last login: Sat Mar  2 06:37:21 2024 from 192.168.99.100
user@phocus4:~$
```

Dado que são feitas 2 conexões ssh, é possível que o tempo de login seja um pouco maior (alguns segundos). Quando for usar scp para copiar arquivos para o storage do cluster, isso pode ser feito diretamente via os hostnames que configuramos (e.g., phocus4). Por fim, é possível fazer esse processo de configuração para várias máquinas (caso você tenha mais de um computador), bastando apenas colocar todas as chaves públicas em authorized_keys. Porém é importante ter cuidado ao permitir várias máquinas fazerem login pela sua conta via chave pública. Qualquer um com acesso a uma chave privada poderá se logar pelo seu usuário. Como acessos e comandos executados são armazenados, estes podem ser auditados para encontrar responsaveis de possíveis abusos ou mal usos do cluster.

___


## Passos para Windows (Puro) - Como fazer conexão com o Sonic Windows

### Usando ssh pela primeira vez - Mesmos passos para Linux
  Vide: [Usando ssh pela primeira vez](#usando-ssh-pela-primeira-vez)

### Login por chave pública no Windows

Abrir .config do ssh ->  explorer.exe . no WSL ou Windows.

1) Gere uma chave
	1.1) Acesse o prompt de comando como **administrador**
	1.2) Vá para o diretório: C:\Users\<user>\.ssh
	1.3) ssh-keygen -t rsa -b 4096 -f C:\Users\<user>\.ssh\id_rsa

2) Abra outro terminal
	2.1) type C:\Users\<user>\.ssh\id_rsa.pub
	2.2) Copie todo o texto, incluindo "ssh-rsa"

	2.3) A parte a baixo é igual
```console
[username@mica ~]$ mkdir ~/.ssh
[username@mica ~]$ vim ~/.ssh/authorized_keys
... colar a chave pública, digitar ":wq" + Enter para sair
[username@mica ~]$ chmod 644 ~/.ssh/authorized_keys
[username@mica ~]$
```
### Login por chave pública e tunneling - Mesmos passos para Linux
  Vide: [Login por chave pública e tunneling](#Login-por-chave-pública-e-tunneling)

___

## TLDR
 - Acesso ao cluster somente via ssh
 - Mais fácil em sistemas Linux
 - O que você deve fazer, em ordem, um passo de cada vez, para acessar pela primeira vez o cluster:
   1. Criar uma conta no CRC especificando que precisa estar no grupo 'speed'
   2. Pedir a criação de uma conta na cerberus no tópico 'Novos Acessos' do grupo do telegram
   3. Criar um par de chaves ssh na sua máquina via 'ssh-keygen', caso você não tenha chaves ssh no seu pc. Não precisa colocar senha na sua chave ssh
   4. Criar o arquivo ~/.ssh/config descrito acima
   5. Rodar o comando 'ssh phocus4'
   6. Resolvido
 - Configure chave pública para acesso mais fácil ao cluster, assim não será necessário colocar a sua senha 2 vezes.

___

[Anterior tópico: Como funciona?](como-funciona.md)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[Próximo tópico: Como submeter jobs](submissao-slurm.md)

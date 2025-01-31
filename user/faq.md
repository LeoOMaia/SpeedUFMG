# FAQ

Perguntas respondidas mais frequentes. Já sabendo usar mais ou menos o cluster e tendo problemas, a primeira coisa que deve ser feita é ler esse FAQ. Como uma "diversão", cada pergunta tem um contador de quantas vezes alguem teve essa dúvida e perguntou no grupo antes de ler esse FAQ. Eu tenho plena confiança que vocês, eximios usuários, farão o possível para deixar esses contadores zerados.

## [2] Meus códigos/dados estão em uma gorgona e não consigo mais acessá-los. O que eu faço para acessá-los?
Basta submeter um job interativo. Ao receber a alocação você terá uma sessão interativa com acesso à gorgona de sua escolha. Nessa sessão você poderá acessar os dados locais do nó alocado.

## [1] Eu não consigo acessar meus arquivos em /home_cerberus/speed/username, não tendo permissão. O diretório, assim como os arquivos aparecem com outro owner. O que devo fazer?
 - Isso está sendo um problema recorrente da forma como a home_cerberus é montada em todos nós (sshfs). Isso é causado pela interação do sshfs com LDAP.
 - Para resolver, basta alguém com acesso root usar o chown -R para corrigir a propriedade do diretório, assim como dos arquivos.
 - Essa solução é dependente de nó. Isso significa que ao fazer isso em uma gorgona1, por exemplo, **você não terá acesso em uma gorgona2!**
 - Estamos abertos a sugestões de soluções paliativas, porém, isso será resolvido permanentemente com o sistema Lustre.
 - Outra solução paliativa é usar o storage local para manter os seus dados quando houver instabilidades nesse acesso.

## [2] O module python3.12.1 não está carregando, mostrando apenas o python3.10.
 - Tente carregar, descarregar e carregar o python3.12.1 algumas vezes até funcionar:

```command
username@phocus4:~$ python3 --version
Python 3.10.12
username@phocus4:~$ module load python3.12.1
Python 3.10.12
username@phocus4:~$ module unload python3.12.1
username@phocus4:~$ module load python3.12.1
username@phocus4:~$ python3 --version
Python 3.12.1
```

## [3] Não estou conseguindo mais criar um venv na home_cerberus.
 - A máquina cerberus está instável, falhando ocasionalmente.
 - Esse problema normalmente aparece como o seguinte erro: "Error: [Errno 5] Input/output error: 'lib' -> '/home_cerberus/speed/usename/venv/lib64'"
 - Arquivos ainda estão acessíveis na home_cerberus, porém foram identificados problemas ao gerar (ou mover) venv’s para ela.
 - Quando você tiver problemas relacionados à cerberus, busque soluções para usar ela o mínimo possível.
 - Todas gorgonas tem um diretório no path /home/all_home, onde você deve criar um diretório /home/all_home/username na primeira vez.
 - Exemplo, crie seu venv na gorgona que você quiser usar:

```command
username@phocus4:/root$ srun -w gorgona7 --time 10:00:00 --pty bash
username@gorgona7:/root$ cd /home/all_home/username
username@gorgona7:/home/all_home# pwd
/home/all_home/username
username@gorgona7:/home/all_home/username# module avail
---------------------- /opt/Modules/modulefiles ----------------------
anaconda3.2023.09-0  module-git   modules  python3.12.1  
dot                  module-info  null     use.own       

Key:
modulepath  
username@gorgona7:/home/all_home/username# module load python3.12.1
username@gorgona7:/home/all_home/username# python3 --version
Python 3.12.1
username@gorgona7:/home/all_home/username# python3 -m venv g7venv
username@gorgona7:/home/all_home/username# source g7venv/bin/activate
(g7venv) username@gorgona7:/home/all_home/username# python3 --version
Python 3.12.1
```
 - Uma desvantágem dessa solução é que você deverá usar apenas uma gorgona para seus testes por venv. Alternativamente, você pode criar um venv local em cada gorgona que usar.

## [0] ERROR: Could not install packedges due to an OSError: [Errorno 28] No space left on device
Use o comando 

```comand 
$ df -h
```
Para verificar se realmente há ou não espaço no diretório que está sendo operado. Caso realmente não haja espaço, retorne para a home da máquina, execute novamente o comando acima e avalie qual partição tem espaço disponível e mude os arquivos para ela usando o comando:

```comand 
$ mv [options(s)] [source_file_name(s)] [Destination_file_name]
```

Exemplo: No meu caso eu tive problemas ao criar um venv e instalar minhas dependências - o espaço acabava antes de instalar tudo o que precisava. Estava em um nó de computação (vulgo gorgona) no diretorio /home/all_home/user e o que eu fiz foi mudar os meus arquivos para a pasta /scratch/username.


## [0] ERROR: Could not install packedges due to an OSError: [Errorno 13] Permission denied: '/home/pos/user' check the permissions

Aparentemente o venv precisa de um home. O que você deve fazer nesse caso é criar um dir na sua pasta. (Ex. /scratch/user/home) e "fingir" que essa é a sua home através do comando:

```comand 
$ export HOME=/scratch/user/home
```
Assim ele deve baixar as coisas do venv nesse other home.

De todo modo indico usar o comando abaixo para forçar o sistema a usar apenas os dados dentro do venv com o comando:

```comand 
pip install --no-cache-dir -r <dir_onde_ta_o_arquivo>/requirements.txt
```

## [1] A minha home não existe em algumas gorgonas.
As gorgonas foram formatadas e preparadas em momentos diferentes, então por mais que a gente tente mantê-las consistentes entre si, não tem como fazer isso de forma mais definitiva até uma formatação completa delas. Essa formatação é algo a ser feito mais a frente.

Uma dessas diferenças encontradas foi o path da home '~'. As vezes este é localizado em /home/vip/username e às vezes /home/pos/username. Todas as gorgonas tem um diretório no path /home/all_home, onde você deve criar um diretório /home/all_home/username na primeira vez.

## [3] Submeti um job mas não aconteceu nada, nem saiu um arquivo slurm-id.out.
Você não rodou da home_cerberus...
Apenas arquivos na home_cerberus são globalmente acessíveis pelo cluster. Se você submete um job de phocus4:/home/username/meu_exp o nó de computação alocado para execução (e.g., uma gorgona) não tem como escrever um arquivo diretamente na phocus4. Porém, ao fazer isso da home_cerberus um arquivo phocus4:/home_cerberus/speed/username/slurm-id.out é o mesmo de gorgona1:/home_cerberus/speed/username/slurm-id.out. Isso significa que a gorgona1 está escrevendo em um "disco global", o qual pode ser lido da phocus4.

## [1] Não estou conseguindo pullar do git.
A phocus4 tem um problema recorrente de gateway, no qual alguns sites (IPs, e não DNS name resolution) não são acessíveis. Para ferificar que este é o caso basta usar o comando 'ping github.com'. Se ele tiver sendo pingado, então o problema provavvelmente é relacionado à sua conta git. Caso github.com esteja inacessível da phocus4 é necessário uma reconfiguração do gateway da phocus4. Isso só pode ser feito com sudo, então reporte o problema no grupo do telegram para ser resolvido.

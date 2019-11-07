# Configurando um Cluster Local  usando o RavenDB e Docker
Para essa demonstração usaremos a versão gratuita do RavenDB que permite criar um cluster com até 3 nós. Para fazer essa etapa, será necessário ter obtido uma licença grátis, disponível neste link: https://ravendb.net/buy

## Subindo uma instância Líder (Leader)
Usando o Docker, criaremos um container com o servidor do RavenDB, com o seguinte comando no terminal:
 
 ```
docker run --name "nodeA" -d -p 8080:8080 -p 38888:38888 ravendb/ravendb
 ```

Onde cada parâmetro significa:
- `docker`: software container onde criaremos nossos servidores.
- `run`: comando run do Docker que cria um container isolado com processo.
-  `--name "nodeA"`: nome (ou apelido) dado ao container para conseguirmos acessar em outros comandos.
- `-d`: container em modo *detatched*, para que possamos usar o mesmo terminal para os próximos comandos.
- `-p 8080:8080`: mapeamento das portas IP da máquina hospedeira e do container, no formato \<portaHospedeira>:\<portaContainer>.
- `-p 38888:38888`: mapeamento das portas TCP da máquina hospedeira e do container.
- `ravendb/ravendb`: imagem do RavenDB a ser usada no container.

Mais informações na documentação do comando [run](https://docs.docker.com/engine/reference/run). 

## Subindo instâncias Membro (Member)
Criaremos agora mais dois containers com os seguintes comandos no terminal:
 
 ```
docker run --name "nodeB" -d -p 8081:8080 ravendb/ravendb
 ```
 
 ```
docker run --name "nodeC" -d -p 8082:8080 ravendb/ravendb
 ```

## Encontrando o IP e porta de cada instância
Podemos encontrar o IP e a porta de cada container usando o comando:
 ```
 sudo docker network inspect bridge
```
Buscando o endereço de cada nó, similar a seguinte saída:
```
"Containers": {
            {
                "Name": "nodeA",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        }
```

## Definindo os nós do Cluster

Definiremos o nó A como líder e os nós B e C como membros, para construir o cluster.

### Membros
Nos nós B e C a configuração é bem simples. Acessando pelo navegador endereço correspondente ao IP encontrado anteriormente na porta 8080, podemos fazer a configuração inicial do nó:

Aceite os termos de uso:

![termos de uso](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224010_localhost.png)

Selecione o modo não seguro (para fins de demonstração):

![escolher modo de configuração](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-40-44.png)
 
Digite o IP correspondente no campo indicado:

![definir ip e porta](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224324_172.17.0.3.png)

Clique em Next para continuar. 

Reinicie o servidor:

![reiniciar servidor](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-41-10.png)

Faça o mesmo processo para o outro nó membro.

### Líder
O processo é bem parecido, seguindo os mesmo passos, com o detalhe de selecionar apenas a opção *Create new Cluster* e definir o nome do nó, na tela de configuração de endereços:

![opçao cluster](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224426_172.17.0.3.png)

Clique em Next e ao final, reinicie o nó:

![reiniciar servidor](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-41-10.png)

### Definindo a topologia
Com o navegador aberto no endereço do nó Líder, no nosso caso o nó A, usaremos o RavenStudio para definir a topologia do *cluster*:

![tela inicial](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-45-30.png)

> Antes de começar, certifique-se que você possuí uma licença gratuíta, que permite a criação de um cluster com até 3 nós. Você pode obtê-la em: https://ravendb.net/license/request?&build=42020


No menu lateral, clique na opção About: 

![about](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_225216_172.17.0.3.png)

Nessa tela, clique em Register License:

![configurar licença](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-45-40.png)

Insira a licença obtida anteriormente ou clique em "*Obtain a new license*". Depois, clique em "Submit":

![submit](https://github.com/AndersonGarrote/PMD/blob/master/Configura%C3%A7%C3%A3o%20do%20Cluster/Captura%20de%20tela%20de%202019-11-07%2008-37-00.png)

Agora seguiremos com a configuração.

No menu lateral, clique na opção "Manage Server": 

![manage server](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_225216_172.17.0.3.png)

No sub-menu, clique na opção "Cluster":

![cluster](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_225252_172.17.0.3.png)

Vamos agora definir o número máximo de núcleos do processador no nó.

> Na licença grátis, é permitido no máximo o uso de 3 *cores*  (núcleos) ao todo no cluster. Por isso, é necessário limitar o uso de núcleos por nó. Caso apareça uma mensagem aviando que o número máximo de *cores* foi atingido, retorne para esta etapa antes de continuar.

No nó A clique em "*Operations*"e em seguida em "*Reassign Cores*":

![cores](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224803_172.17.0.3.png)

Defina o numero de *cores* para 1 e salve:

![max_cores](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224835_172.17.0.3.png)

Voltando para a tela de configuração do cluster:

![cluster](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_225252_172.17.0.3.png)

Clique em "*Add node to Cluster*"

Insira o IP e porta e a Tag do nó Membro, por exemplo B. Tome cuidado para não esquecer de desmarcar a opção "*Use Available Cores*" e definir o número máximo de *cores* para 1:

![config_node](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224728_172.17.0.3.png)


Clique em "*Test Connection*"

![teste](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224657_172.17.0.3.png):

Clique em "*Assign Cores*"

Clique em "*Add node to Cluster*"

Insira o IP e porta e a Tag do nó Membro, por exemplo B. Tome cuidado para não esquecer de desmarcar a opção "*Use Available Cores*" e definir o número máximo de *cores* para 1:

Clique em "*Test Connection*"

Se a conexão foi realizada, clique em "*Add*". Se não funcionar, revise os passos anteriores.

O Studio deve exibir a seguinte tela com a topologia do cluster:

![topologia](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-51-23.png)

Repita o processo para o outro membro.

> Essa configuração pode mudar com o passar do tempo, pois devido a questões como problemas com a rede ou sobrecarga de recursos pode haver uma votação e um novo líder ser escolhido.

## Criando um banco com replicação
Vamos agora criar um banco com replicação, mantendo os nós replicados em todos os nós.

No menu lateral, clique na opção "Databases": 

![manage server](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_225216_172.17.0.3.png)

Clique em "*Add database*".:

Defina o nome do banco e o fator de replicação. No nosso caso, o fator será 3 para que cada nó tenha uma réplica do banco:

![new database](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-07_071059_172.17.0.2.png)

Agora, basta clicar no banco para acessar os documentos, manipulá-los e fazer consultas, na opção Query.

> É possível criar um banco de dados de exemplo pelo menu lateral esquerdo, na opção Settings -> Create Sample Data. Clique em Create e explore os dados!

Fonte: https://ravendb.net
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkwNzM5NjEsLTgxMjA2NjExOSwtMTU4OD
k3MTM0NiwxNTg1MDE4MTQzLC0xNzcwNjk1MjAyLC0yMDA1MTcx
OTM2LC0xMDE1OTQ4Mzk3LC00NzU4NDUzMzUsNjQzODM1NDk2LC
0xMjYxMzYzNjMwLDMxNDEwNjAyXX0=
-->

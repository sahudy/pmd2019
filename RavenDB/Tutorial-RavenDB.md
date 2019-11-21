# Tutorial RavenDB

## Tabela de Conteúdos

   * [Tutorial RavenDB](#tutorial-ravendb)
   * [Visão Geral](#visão-geral)
      * [Instalando o RavenDB](#instalando-o-ravendb)
      * [Usando o Docker](#usando-o-docker)
      * [Versão Windows](#versão-windows)
      * [Versão Linux](#versão-linux)
      * [Verificando estado do servidor](#verificando-estado-do-servidor)
   * [Configurando um Cluster Local usando o RavenDB e Diferentes Máquinas](#configurando-um-cluster-local-usando-o-ravendb-e-diferentes-máquinas)
      * [Subindo uma instância Líder (Leader) e duas instâncias Membro (Member)](#subindo-uma-instância-líder-leader-e-duas-instâncias-membro-member)
      * [Encontrando o IP e porta de cada instância](#encontrando-o-ip-e-porta-de-cada-instância)
      * [Definindo os nós do Cluster](#definindo-os-nós-do-cluster)
         * [Membros](#membros)
         * [Líder](#líder)
         * [Definindo a topologia](#definindo-a-topologia)
      * [Criando um banco com replicação](#criando-um-banco-com-replicação)
   * [Configurando um Cluster Local  usando o RavenDB e Docker](#configurando-um-cluster-local--usando-o-ravendb-e-docker)
      * [Subindo uma instância Líder (Leader)](#subindo-uma-instância-líder-leader)
      * [Subindo instâncias Membro (Member)](#subindo-instâncias-membro-member)
      * [Encontrando o IP e porta de cada instância](#encontrando-o-ip-e-porta-de-cada-instância-1)
      * [Definindo os nós do Cluster](#definindo-os-nós-do-cluster-1)
         * [Membros](#membros-1)
         * [Líder](#líder-1)
         * [Definindo a topologia](#definindo-a-topologia-1)
      * [Criando um banco com replicação](#criando-um-banco-com-replicação-1)
   * [Conceitos e comandos Básicos](#conceitos-e-comandos-básicos)
      * [Exemplos de consultas](#exemplos-de-consultas)
         * [Cláusula from](#cláusula-from)
         * [Cláusula where](#cláusula-where)
         * [Cláusula select](#cláusula-select)
         * [Cláusula order by](#cláusula-order-by)
         * [Cláusula group by](#cláusula-group-by)
   * [Arquitetura de Distribuição](#arquitetura-de-distribuição)
      * [Camada de Cluster](#camada-de-cluster)
      * [Camada de Banco de Dados](#camada-de-banco-de-dados)
      * [RavenDB e o Teorema CAP](#ravendb-e-o-teorema-cap)
   * [Replicação de Dados](#replicação-de-dados)
      * [Consistência](#consistência)
   * [Disponibilidade](#disponibilidade)
      * [Escalabilidade](#escalabilidade)
   * [Transações](#transações)
   * [Segurança](#segurança)
   * [Glossário](#glossário)
   * [Exercícios](#exercícios)
   * [Exercícios - Soluções](#exercícios---soluções)

> Script of TOC created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)


# Visão Geral

O RavenDB é um banco de dados NoSQL orientado a documentos com operações ACID em todo o *cluster*.

![Funcionalidades](https://raw.githubusercontent.com/AndersonGarrote/PMD/master/Vis%C3%A3o%20Geral/characteristics_new.png)<!-- imagem -->

A disponibilidade é facilmente escalável usando replicação de dados e seu desempenho pode chegar a 1 milhão de requisições de leitura e 150 mil de escrita por segundo, mesmo em computadores de baixo custo.

![Plataformas Disponíveis](https://raw.githubusercontent.com/AndersonGarrote/PMD/master/Vis%C3%A3o%20Geral/platforms.png)<!-- imagem -->

Além disso, ele é multiplataforma, tendo versões para Windows, Linux, Mac, Docker e Raspberry Pi. Pode ser acessado usando o cliente RavenStudio ou pelos clientes de C#, Node.js, Java Phyton, Ruby e Go.

Fonte: https://ravendb.net

## Instalando o RavenDB
>Observação: Este tutorial foi contruído baseado na versão estável 4.2.4 do RavenDB

O banco de dados de documentos NoSQL **RavenDB** pode ser baixado na seção de downloads do site: [ravendb.net/download](https://ravendb.net/download).

![página de downloads](https://github.com/AndersonGarrote/PMD/blob/master/Instala%C3%A7%C3%A3o/downloads_page.png?raw=true)



## Usando o Docker
Caso deseje usar apenas uma máquina para seguir este tutorial, utilizaremos a versão para Docker, por ser multiplataforma e permitir realizar os testes de instâncias de nós no cluster usando containers.

>Para isso, é necessário instalar o docker, pelo instalador de programas do sistema operacional ou direto do site [www.docker.com](https://www.docker.com). Nesse endereço também é possível encontrar, mais informações úteis sobre o Docker.

Com o Docker instalado, escolheremos a respectiva versão no site do RavenDB, após aceitar os termos e condições. Seremos redirecionados para outra página, com diversas informações de configuração.
Estamos interessados no seguinte comando em um terminal:

```
	docker pull ravendb/ravendb
```

Assim, baixaremos a imagem com RavenDB para Docker.
> Pode ser necessário estar com um usuário administrador para realizar os comandos. No Linux, basta usar `sudo`  antes dos comandos do Docker.

Feito isso, podemos subir um container usando o seguinte comando:

```
	docker run -d -p 8080:8080 -p 38888:38888  ravendb/ravendb
```

## Versão Windows
Depois de baixar o arquivo .zip, extraia o conteúdo e execute o arquivo `run.psi`, clicando com o botão direito em em seguida em Executar com o PowerShell.

## Versão Linux
Depois de baixar o arquivo .zip, extraia o conteúdo e execute o arquivo `run.sh` via linha de comando no prompt;

```
	./run.sh
```
## Verificando estado do servidor

Podemos conferir o estado usando o cliente do browser, no endereço http://localhost:8080. A seguinte tela deve aparecer:

![cliente RavenDB](https://github.com/AndersonGarrote/PMD/blob/master/Instala%C3%A7%C3%A3o/telaraven.png?raw=true)


Neste ponto, podemos seguir para a **Configuração do Cluster**.


Fonte: https://ravendb.net


# Configurando um Cluster Local usando o RavenDB e Diferentes Máquinas
Para essa demonstração usaremos a versão gratuita do RavenDB que permite criar um cluster com até 3 nós. Para fazer essa etapa, será necessário ter obtido uma licença grátis, disponível neste link: https://ravendb.net/buy

## Subindo uma instância Líder (Leader) e duas instâncias Membro (Member)

> Este tutorial foi feito usando a versão de Linux do RavenDB, mas o procedimento é o mesmo para a versão Windows.

Iniciaremos o executável da versão de Linux em cada uma das 3 máquinas:
 
 ```
./run.sh
 ```

## Encontrando o IP e porta de cada instância

Podemos encontrar o IP e a porta de cada máquina no próprio terminal do servidor do RavenDB.

## Definindo os nós do Cluster

Definiremos o nó A como líder e os nós B e C como membros, para construir o cluster.

### Membros
Nos nós B e C a configuração é bem simples. Após alguns segundos o navegador abrirá o assistente de configuração do nó, onde, podemos fazer a configuração inicial do nó:

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

![teste](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_224657_172.17.0.3.png)

Se a conexão foi realizada, clique em "*Add*". Se não funcionar, revise os passos anteriores.

O Studio deve exibir a seguinte tela com a topologia do cluster:

![topologia](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Captura%20de%20tela%20de%202019-11-06%2022-51-23.png)

Repita o processo para o outro membro.

> Essa configuração pode mudar com o passar do tempo, pois devido a questões como problemas com a rede ou sobrecarga de recursos pode haver uma votação e um novo líder ser escolhido.

## Criando um banco com replicação
Vamos agora criar um banco com replicação, mantendo os nós replicados em todos os nós.

No menu lateral, clique na opção "Databases": 

![manage server](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-06_225216_172.17.0.3.png)

Clique em "*Add database*".

Defina o nome do banco e o fator de replicação. No nosso caso, o fator será 3 para que cada nó tenha uma réplica do banco:

![new database](https://github.com/AndersonGarrote/PMD/blob/master/Configuração%20do%20Cluster/Opera%20Instantâneo_2019-11-07_071059_172.17.0.2.png)

Agora, basta clicar no banco para acessar os documentos, manipulá-los e fazer consultas, na opção Query.

> É possível criar um banco de dados de exemplo pelo menu lateral esquerdo, na opção Settings -> Create Sample Data. Clique em Create e explore os dados!

Fonte: https://ravendb.net

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

# Conceitos e comandos Básicos

Por ser um banco de dados NoSQL, alguns conceitos são diferentes se comparados com um RDBMS comum, mas podemos fazer um paralelo entra a nomenclatura de cada um deles: 

|  RavenDB    | RDBMS  |
|-------------|--------|
| collection  | table  |
| document    | row    |
| field       | column |
| index       | index  |

O RavenDB possí uma linguagem de consulta própria chamada [RQL](https://ravendb.net/docs/article-page/4.2/csharp/indexes/querying/what-is-rql), similar à SQL, mas com consultas escritas em uma ordem um pouco diferente.

## Exemplos de consultas 

### Cláusula `from`
RQL: `from <collection>`

- Exemplo:
Retornar todos os documentos da coleção Employees

RQL: `from Employees`

SQL: `select * from Employees`

### Cláusula `where`
RQL: `where <operation>`

- Exemplo:
Retornar todos os Produtos com preço por unidade = 15

RQL: `from Products where PricePerUnit = 15`

SQL: `select * from Products where PricePerUnit = 15`

### Cláusula `select`
RQL: `select <column>`

- Exemplo:
Retornar os nomes dos Produtos com preço por unidade = 15

RQL: `from Products where PricePerUnit = 15 se a`

SQL: `select  from Products where PricePerUnit = 15`

### Cláusula `order by`
RQL: `order by <column> as <DataType> <OrderingType>`

- Exemplo:
Retornar Produtos com preço por unidade = 15 ordenados pelas unidades em pedidos

RQL: `from Products where PricePerUnit = 15 order by UnitsOnOrder as long `

SQL: `select * from Products where PricePerUnit = 15 order by UnitsOnOrder`

### Cláusula `group by`
RQL: `group by <column>`

- Exemplo:


RQL: `from Products group by Supplier where count() > 15`

SQL: `select * from Products where count(Supplier) = 15 group by Supplier`

`

Saiba mais sobre RQL em: https://ravendb.net/docs/article-page/4.2/csharp/indexes/querying/what-is-rql

# Arquitetura de Distribuição
O RavenDB opera sobre *clusters* que providenciam redundância e disponibilidade dos dados. No contexto do RavenDB, *clusters* são um grupo de servidores chamados de *nós* e podem estar em mais de uma máquina. O RavenDB possui uma divisão de duas camadas de funcionamento: **Camada de Cluster** e **Camada de Banco de Dados**.

## Camada de Cluster

A **Camada de Cluster** é composta por nós (3 ou mais, é recomendado um número ímpar) que realizam operações de acordo com o consenso de todos os nós. Esse protocolo é um algoritmo de consenso desenvolvido para o RavenDB (chamado de Consenso *Rachis*, uma implementação derivada do protocolo [Raft](https://raft.github.io/)), e ele define um nó líder responsável por escolher o melhor nó para um determinado cliente, dando preferência a consistência das operações. 

## Camada de Banco de Dados

A **Camada de Banco de Dados** representa bancos de dados de um *cluster* que formam um grupo e trabalham em união e cooperativamente. A quantidade de membros nos grupos é definido pelo Fator de Replicação. Cada nó possui uma cópia completa da topologia do *cluster* (informações dos nós que compõe o cluster), no qual especifica qual nó possui um determinado banco de dados. Esse processo de replicação ocorre sobre uma conexão TCP entre os nós.

> Essa distinção entre duas camadas é importante para manter várias propriedades do RavenDB. A eleição de um nó líder pelo consenso *Rachis* oferece a consistência forte, permitindo a continuidade da operação com a garantia do líder de que a maioria dos nós estão funcionando. Isso também significa que cada operação do *cluster* será futuramente aceita, ou não, pelo *cluster* inteiro.

## RavenDB e o Teorema CAP

De acordo com o **Teorema CAP**, o *RavenDB* consegue ser **tolerante a partições**, **consistente** e **disponível**. Mesmo contradizendo o teorema CAP, isso é possível pois ele aborda cada propriedade em uma camada diferente. A camada de *cluster* é  CP, ou seja, consistente mas pode não estar disponível. A camada de *banco de dados* é AP, ou seja, sempre disponível mas pode não ser consistente.

Fontes:
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview)
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/distribution/distributed-database](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/distribution/distributed-databaserainsen-oerations)

# Replicação de Dados

Um banco de dados pode ser replicado em vários nós dentro de um *cluster*, dependendo do Fator de Replicação. Quando um nó possui um banco de dados ele é chamado de *nó de banco de dados*. Cada um desses *nós* possuem uma cópia completa do banco de dados localmente, incluindo os documentos e índices, e assim podem realizar qualquer operação de busca e escrita de dados. Além dessas operações locais em cada *nó*, há também operações que impactam o *cluster* inteiro, sendo no caso operações de, por exemplo, criação de documento. Um *grupo de banco de dados* pode ser formado por vários desses nós, e quando um banco de dados novo é criado, pode-se escolher em quais nós ele existe.

Veja alguns exemplos de bancos de dados replicados no cluster, com diferentes fatores de replicação:
![Tela de replicação](https://github.com/AndersonGarrote/PMD/blob/master/Arquitetura%20e%20Replica%C3%A7%C3%A3o/replicas1.png)
![Diagrama de replicação](https://github.com/AndersonGarrote/PMD/blob/master/Arquitetura%20e%20Replica%C3%A7%C3%A3o/RavenDB%20replica%C3%A7%C3%A3o.png)

>Na camada de *clusters* é feita a replicação entre dois grupos de bancos de dados (Denominada de Replicação Externa) de forma semelhante a replicação tradicional. 

 Cada nó se mantém sincronizado a partir de replicação *master-master*. Neste tipo de replicação, qualquer nó pode realizar escritas ao banco de dados e essa mudança será replicada aos outros nós. Isso significa que todo nó possui uma cópia do banco de dados de seu grupo e cada mudança de escrita é replicada automaticamente à outras instâncias do banco de dados. Por exemplo, se tivermos um *cluster* com os nós A, B e C e criarmos um banco de dados com 3 de fator de replicação no nó A, esse banco também estará nos nós B e C. E se o nó C realizar uma escrita ao banco de dados, os nós A e B serão atualizados com a mudança.
 
> Atualmente o RavenDB não dá suporte para *sharding*, sendo previsto para ser implementado em uma versão futura. 

[https://ravendb.net/learn/inside-ravendb-book/reader/4.0/6-ravendb-clusters](https://ravendb.net/learn/inside-ravendb-book/reader/4.0/6-ravendb-clusters)


## Consistência
A consistência é mantida na camada de *cluster* pois toda operação feita nesta camada precisa de um consenso, significando que a operação deve ser aceita de acordo com um quórum mínimo dos nós (n / 2  + 1 nós) ou ela não será registrada. Para isso, foi implementado o algoritmo de consenso *Rachis* [Raft](https://raft.github.io/).  Na camada de banco de dados,  cada banco de dados é replicado para vários nós de um *cluster*, e os nós com o mesmo banco de dados forma um grupo. Todas as mudanças em documentos de um banco de dados são mantidas de acordo com o *Change Vector*, que é a implementação no RavenDB do conceito de *vector clock* (Vetor de Relógio).

Fontes: [https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/replication/change-vector](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/replication/change-vector)
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/rachis/consensus-operations](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/rachis/consensus-operations)


# Disponibilidade

Algumas ações, como criar banco de dados ou índices requerem um consenso do _cluster_, para isto o RavenDB utiliza o  _Rachis_, uma implementação proprietária de algoritmo de consenso para sistemas distribuídos. Requerendo um quórum de N/2 + 1 processos para que seja possível a eleição de líder.

No RavenDB temos o banco de dados replicado entre vários nós, um grupo de nós que mantém o mesmo banco de dados replicado é chamado de *Group Database* e, o fator de replicação pode ser definido, lembrando que a versão gratuita do RavenDB suporta fator de replicação de até 3 nós. Documentos são mantidos em sincronia em nós pertencentes à um _Database Group_ com uma replicação _master to master_

Tendo um _cluster_ com vários nós é possível realizar requisições espalhadas em vários nós, com a finalidade de maior desempenho, porém a replicação nos permite mesmo com a maioria do _cluster_ inoperante, desde que tenhamos um único nó vivo, temos acesso à leituras e escritas no banco.

A seguir teremos a demonstração de uma topologia com possíveis falhas junto com o passo a passo utilizando _Docker_.

Para exemplificar iremos criar uma topologia de 3 nós sendo o nó **A** definido como *Master* e os nós **B** e **C** como *Members*.

- Para subirmos as máquinas:
	> $ sudo docker run --name <nome_do_nó> -d -p 8080:8081 -p 38888:38888 ravendb/ravendb
	- Onde o primeiro parâmetro de porta (8080) refere-se a porta da máquina física e o segundo (8081) à porta do serviço _Docker_. Para criarmos várias máquinas basta variar o seu nome e porta de serviço.
	
![Todos os nós ativos](https://github.com/AndersonGarrote/PMD/blob/master/Disponibilidade/all.png?raw=true)

Existem quatro tipos de nó:

 ![Tipos de nó](https://github.com/AndersonGarrote/PMD/blob/master/Disponibilidade/Tipos.png?raw=true)

- *Leader*: Nó líder do *Group Database*, é responsável por manter a saúde do _cluster_ e assegurar a consistência das decisões.
- *Member*: Nó membro do *Group Database*, pode se tornar um Líder em caso de eleição.
- *Watcher*: Nó membro com menos privilégios; não pode ser eleito como Líder em caso de votação, não participando do Quórum.
- *Promotable*: Estado de nó aplicável apenas em caso de eleição; um nó elegível deve ser membro e estar em pleno funcionamento.

Ao derrubarmos o nó **A** teremos uma nova eleição que será realizada pelo quórum restante de Membros:

- Para vermos quais máquinas estão funcionando:
	>  $ sudo docker container ls
- Para derrubarmos um nó, removendo a máquina:
	>  $ sudo docker stop <nome_do_nó>

![Nó A inoperante](https://github.com/AndersonGarrote/PMD/blob/master/Disponibilidade/A_down.png?raw=true)

Em um outro cenário onde **C** foi rebaixado, ficando como um _Watcher_. Ao derrubarmos o atual _Leader_ **B** o quórum de _Members_ não é suficiente para eleger outro nó, desta forma a eleição fica esperando um quórum suficiente (N/2 + 1):

![Votação](https://github.com/AndersonGarrote/PMD/blob/master/Disponibilidade/Voting.png?raw=true)

- Para voltarmos um nó ao funcionamento podemos excluir o nó que foi parado:
	> $ sudo docker rm <nome_do_nó>
	- Em seguida subi-lo novamente.
	
Fonte:[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview)

## Escalabilidade

Se tratando de escalabilidade, quando um novo nó é inserido no Grup do Banco de Dados, um Mentor decidido pelo sistema de gerenciamento fica responsável por atualizá-lo, inserindo-o no contexto do _Group Database_. O novo nó terá o estado _Promotable_  até receber e indexar todos os documentos vindos do Mentor.

Temos uma replicação _master-master_ onde os dados são enviados de maneira assíncrona toda vez que um novo nó entrar no _cluster_ ou algum dado mudar no _Master_.

Assim fica possível escalar sistemas a nível de _clusters_ com simplicidade.

Fonte: https://ravendb.net/docs/article-page/4.2/Csharp/server/clustering/distribution/distributed-database

# Transações
As operações no RavenDB sobre documentos e em armazenamentos feitas são todas ACID:
 - Atômico: Toda operação é executada completamente, sem ser interrompido por outra operação.
 - Consistência: O estado de um documento se mantêm em toda a execução da operação.
 - Isolamento: Todas as operações em uma transação não interferem em outra transação.
 - Visibilidade: As transações em mais de um documento são disponíveis imediatamente após o registro. 
 - Durabilidade - Toda transação será executada no nó de banco de dados inteiro.

Na camada de *cluster*, as transações também são ACID:

 - Atômico: Garantia de execução da transação se for aprovado pelo quórum e pelo teste de simultaneidade, caso contrário a transação é desfeita.
 - Consistência: Se a transação for completa e os documentos estão sincronizados ao nós, então a requisição feita pelo nó será feita.
 - Isolamento: Não há interferência por outra transação durante as operações.
 - Durabilidade - Com a transação aceita, ela será executada em todos os bancos de dados do nó.


Porém quando o banco de dados possui índices, então as operações de buscas seguem o modelo BASE devido ao funcionamento dos índices no banco de dados: 


 - *Basically Available* (Disponível de forma básica): buscas por índices sempre estão disponíveis, mas podem estar desatualizadas.
 - *Soft State* (Estado simples): O estado do sistema pode mudar com o tempo dependendo da demora da indexação.
 - Eventual Consistency (Consistência Futura): o banco de dados será consistênte quando a indexação acabar e não receber novos documentos.

Fonte: [https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/cluster-transactions](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/cluster-transactions)
[https://ravendb.net/docs/article-page/4.1/csharp/client-api/faq/transaction-support](https://ravendb.net/docs/article-page/4.1/csharp/client-api/faq/transaction-support)

# Segurança
No modo de desenvolvimento, o RavenDB roda no modo inseguro, porém em estágio de desenvolvimento é interessante a utilização do modo seguro, onde, por padrão somente o `localhost` está autenticado, desta forma, outros usuários terão que ser previamente autenticados para poderem acessar remotamente. O RavenDB  usa certificados de cliente `X509` para autenticação.

RavenDB oferece criptografia completa do banco e dados usando a biblioteca _libsodium_.

Fonte: https://ravendb.net/docs/article-page/4.2/csharp/server/security/overview


# Glossário
- ***Document* (documento)**: O equivalente a uma tupla de dados em SQL.
 - ***Collection* (coleção)**: Metadado de um documento que agrupa docunos eeerem ao mesmo tipo de dado, análogo às tabelas em SQL.
 - ***Cluster***: Grupo de servidores que podem ou não estar na mesma máquina.
 - **Fator de Replicação**: Número de réplicas de banco de dados em um *cluster*.
 - **Nó do *Cluster***: Membro de um cluster erações de *clusters* controlados pelo consenso *Rachis*.
 - **Nó *Leader* (Líder)**: Nó responsável por coordenar o *cluster*, adicionar novos nós e monitorar seus estados. É sempre um Membro.
 - **Nó *Member* (Membro)**: Nó capaz de votar em caso de eleição de Líder. Responde a requisições normalmente.
 - **Nó *Watcher* (ObservadorMembro)**: Nó incapaz de votar em caso de eleição de Líder. Responde a requisições normalmente.
 - **Grupo de Banco de Dados**: Nós que possuem o mesmo banco de dados.
 - **Teorema CAP**: Teorema que diz que não é possível ter mesmo tempo,  forma completa, a garantia de *Consistency* (consistência), *Availability* (disponibilidade) e *Partition Tolerance* (tolerância a partição, escalabilidade).
 
 Fonte: https://ravendb.net/
 
# Exercícios
> Para fazer os exercícios, crie um banco de dados de exemplo pelo menu lateral esquerdo, na opção Settings -> Create Sample Data. Clique em Create. 

- 1 - Retorne o primeiro nome de todos os empregados.
- 2  - Retorne o nome das empresas localizadas no Brasil.
- 3 - Retorne o nome e o endereço das empresas localizadas na cidade de Campinas ou São Paulo.
- 4 - Retorne os pedidos com o preço do Frete (Freight) entre 50 e 100.
- 5 - Retorne o nome dos produtos de um pedido e o endereço em que o pedido foi enviado. (Dica: Algumas colunas não são visíveis por padrão, selecione em Display as colunas para serem exibidas)
- 6 - Retorne os produtos e o preço total dos pedidos. (Dica: Declare função)
- 7 - Retorne os pedidos feitos pela empresa de ID 'companies/88-A'. Exiba o nome da empresa e o nome do funcionário. (Dica: Use `load`)
- 8 - Retorne o nome das empresas em ordem alfabética.
- 9 - Retorne os produtos em ordem decrescente do preço por unidade.
- 10 - Retorne as empresas localizadas a 500Km do Rio de Janeiro (Dica: Use as coordenadas Latitude -22 e Longitude -43)
- 11 - Retorne os fornecedores que fornecem 3 ou mais produtos. Exiba o ID do fornecedor e o número de produtos.

# Exercícios - Soluções
- 1 -  `from Employees select FirstName`
- 2 -  `from Companies where Address.Cselect Name`
- 3 - `from Companies 
where (Address.City = "Campinas" or Address.City = "Sao Paulo")
Country = "Brazil" select Name, Address`
- 4 - `from Orders
where Freight BETWEEN 50 and 100`
- 5 - `from Orders
select ShipTo, Lines[].ProductName as Products`
- 6 - `declare function lineItemPrice(l) {
    return l.PricePerUnit * l.Quantity * (1 - l.Discount);
}
from Orders as o
select {
    TopProducts: o.Lines
    Total: o.Lines.reduce((acc, l) => acc + lineItemPrice(l), 0)
}`
- 7 -  `from Orders as o
where Company = 'companies/88-A'
load o.Employee as e, o.Company as c, o.Lines[].Product as products[]
select { 
    CompanyName: c.Name,
    EmployeeName: e.FirstName + " " + e.LastName,
    Products: products
}`
- 8 - `from Companies
order by Name as asc`
- 9 - `from Products 
order by PricePerUnit as double desc`
- 10 - `from Companies
where spatial.within(
    spatial.point(Address.Location.Latitude, Address.Location.Longitude), 
    spatial.circle(500, -22.9306303, -43.358808, 'kilometers'))`
- 11 - `from Products
group by Supplier
where count() >= 3
select Supplier, count()`
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0MjQ5MTIyMzcsMTA5NjYzNDk3MCwtMj
A0NzA5MzQwOV19
-->

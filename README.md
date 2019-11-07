# AEROSPIKE

O Aerospike é um banco de dados NoSQL  Key-Value distribuído, desenhado para aplicações que devem estar sempre disponíveis e lidar com big data de maneira confiável.

Desenvolvida em C, opera em três camadas: Uma camada de dados, uma camada de distribuição autogerenciada (o *"Aerospike Smart Cluster"*) e uma camada de cliente (o *"Aerospike Smart Client"*).

A camada de distribuição é replicada através de diferentes nós, para garantir consistência. Permitindo também que o banco de dados se mantenha operacional frente a falhas em nós individuais, ou a remoção de algum deles.

A camada de cliente é utilizada para manter controle sobre as configurações do *cluster* e a localização dos dados, e gerenciar o servidor.

A camada de dados é otimizada para armazenar os dados em discos rígidos e memórias flash, sendo os índices do banco armazenados em RAM para rápida disponibilidade, e os dados escritos escritos em blocos, para diminuir a latência.

| ![Arquitetura](https://www.aerospike.com/docs/architecture/assets/as-architecture.png) | 
|:--:| 
| *Arquitetura do Aerospike* |

Para o desenvolvimento deste documento e exemplos, utilizamos versão ```4.7.0.2``` do Aerospike.

### Instalação e configuração no Ubuntu 18.04+
Nesta parte do tutorial ensinaremos como fazer a instalação do Aerospike e configuração para Ubuntu 18.04 ou mais recente.

#### Aerospike Server
O primeiro passo deste tutorial é instalar o Aerospike Server e para isso começaremos com o *download*:

* No terminal do linux, cole o seguinte comando: 
```wget -O aerospike.tgz 'https://www.aerospike.com/download/server/latest/artifact/ubuntu18'```

* Para extrair o conteúdo da pasta compactada, entre na pasta onde realizou o download pelo terminal e cole o seguinte comando: 
```tar -xvf aerospike.tgz```

* Para extrair o conteúdo da pasta compactada, entre na pasta onde realizou o download pelo terminal e cole o seguinte comando: ```tar -xvf aerospike.tgz```

* O conteúdo será extraído para uma pasta de nome: ```aerospike-server-community-4.7.0.2-ubuntu18.04```


Agora, para realizar a instalação:

* Entre na pasta criada com o seguinte comando: 
```cd aerospike-server-community-4.7.0.2-ubuntu18.04```

* Para continuar a instalação é necessário possuir o Python instalado, com um número de versão maior que 2.7 e menor que 3. Com o Python instalado, execute com o comando: 
```sudo ./asinstall```


Agora tudo deve estar instalado corretamente, para conferir isso utilize os seguintes comandos:

* Para inicializar o banco utilize: 
```sudo service aerospike start```

* Para se certificar que o banco está rodando utilize: 
```sudo service aerospike status```

#### Instalação do AMC
O AMC (*Aerospike Management Console*) é uma ferramenta para manipular e monitorar *cluster* de maneira rápida, com updates automáticos dos status dos *clusters*.

Para a instalação é necessário se certificar de que possui os seguintes pacotes:
* python (2.6+)
* gcc
* python-dev

Assim que todas as dependências forem instaladas, para baixar o AMC você deverá entrar no seguinte link: ```https://www.aerospike.com/download/amc/4.0.27/``` e selecionar o pacote para Ubuntu 12.04+.

Quando o *download* do arquivo terminar é necessário fazer a instalação com o seguinte comando no terminal: 
```sudo dpkg -i aerospike-amc-community-4.0.27\_amd64.deb``` 

Assim que o ACM for instalado os comandos básicos para utilizá-lo são:
*  Inicialização: 
```sudo /etc/init.d/amc start```
*  Parada: 
```sudo /etc/init.d/amc stop```
*  Verificar status: 
```sudo systemctl status amc```

### Instalação e configuração no MAC OS X
Nesta parte do tutorial ensinaremos como fazer a instalação do Aerospike e configuração para MAC OS X.

#### Aerospike Server e AMC  
No MAC OS, o Aerospike funciona dentro de um ambiente virtual, por isso, é necessária a instalação do Vagrant (ambiente de desenvolvimento virtual) e do VMWare (máquina virtual).

* Faça o download e instale o Vagrant : 
```https://www.vagrantup.com/downloads.html```

* Faça o download e instale o VMWare: 
```https://www.virtualbox.org/wiki/Downloads```

 Agora, já podemos começar a instalação do Aerospike:
 
* Crie e acesse o diretório de trabalho do Aerospike: 
```mkdir ~/aerospike-vm && cd ~/aerospike-vm```

* Inicialize a máquina virtual do Aerospike: 
```vagrant init aerospike/aerospike-ce```

* Inicialize o Vagrant:
    ``` vagrant up ```
    ``` vagrant ssh ``` 
    ``` sudo service aerospike start ``` 
    ``` sudo service amc start ```

### Modelo de Dados
O modelo de dados do Aerospike é *schema-less*, com registros podendo ser adicionados com campos novos sem que o esquema dos dados inseridos anteriormente tenha que ser alterado. Abaixo, são descritos os conceitos de *namespaces*, *sets*, *records* *bins* e *keys*, utilizados pelo Aerospike.

Aerospike   | RDBMS
--------- | ------
namespace | tablespace
set       | table
record    | row
bin       | column
index     | index

##### Namespaces
Um *namespace* é uma coleção de dados que segue uma "política" comum, como o número de réplicas, a maneira como o dado é armazenado, e quando ele expira. É o equivalente deste sistema aos *databases* dos sistemas relacionais. Um *namespace* contém *records*, *indexes*, e políticas, que determinam alguns comportamentos, conforme descrito acima. 

##### Sets
Em *namespaces*, *records* pertecem a *containers* lógicos chamados *sets*. *Sets* permitem que aplicações agrupem dados logicamente em coleções. Eles herdam as políticas definidas por seu *namespace* e podem definir regras adicionais, específicas para aqueles dados. Por exemplo, índices secundários podem ser especificados apenas para um *set* em particular.

##### Records
Um *record* (registro) é a unidade básica de armazenamento em um banco de dados Aerospike. Registros podem pertencem a um *namespace* ou a um *set* dentro de um *namespace*. *Records* usam uma *Key* (chave) como seu identificador único.

Cada registro contém os seguintes componentes:

* Identificador único (Key): Registros são identificados e armazenados utilizando um *hash* de sua *Key*.
* Metadata: informações sobre versão (geração), expiração, mestre, partição onde está armazenada. Gerado automáticamente.
* *Bins*: equivalente aos campos em SGBDs relacionais.

##### Bins
Em um registro, dados são armazenados em um ou mais *bins* (campos). Campos consistem em um nome e um valor e não especificam um tipo de dados (estes são definidos pelo valor armazenado, ou seja, de maneira dinâmica), o que agrega mais flexibilidade ao modelo de dados.

##### Keys e Digests
Chaves (*Keys*) são utilizadas para ler e escrever registros (*records*) no banco. Quando uma chave é definida, ela e a informação de seu *set* (o nome dele) são transformados utilizado uma função *hash*, gerando um *digest* de 160 bits, utilizado para endereçar aquele registro no banco. Ou seja, o usuário utiliza a chave em suas operações, enquanto o *digest* é utilizado para armazenar o registro e recupera-lo.

| ![Modelo de dados](https://github.com/WilliamAlves/PMD-Aerospike/blob/master/images/data-model1.png) | 
|:--:| 
| *Ilustração do modelo de dados do Aerospike* |

### Como interagir com o sistema?
Conforme citado acima, uma das maneiras de se interagir com o Aerospike é através do [AMC](https://www.aerospike.com/docs/amc/) (*Aerospike Management Console*), que fornece uma interface gráfica onde é possível analizar os nós do cluster e algumas métrias.

| ![AMC](https://github.com/WilliamAlves/PMD-Aerospike/blob/master/images/amc1.png) | 
|:--:| 
| *Imagem do Aerospike Management Console (AMC), de onde é possível visualizar e gerenciar os nós e registros do Aerospike* |

Outra maneira, é através do (*Aerospike Query Client*), utilizando a AQL (*Aerospike Query Language*). Com o Aerospike inicializado, basta digitar ```aql``` no terminal que o *Shell* AQC será inicializado, onde é possível utilizar a linguagem *SQL-like* do Aerospike para inserir e/ou manipular os *namespaces*, *sets*, *rows* e *bins* do banco.  

| ![AQC](https://miro.medium.com/max/1320/1*OU2M3uR78TV-lPKy5_PHOg.png) | 
|:--:| 
| *Imagem de um terminal rodando o AQC* |

### Comandos Básicos: Aerospike Query Language (AQL)
O Aerospike possui sua própria linguagem para realizar manipulações no banco e efetuar pesquisas, a Aerospike Query Language (AQL), baseada no SQL. Ela é utilizada no terminal, a partir do Aerospike Query Client (AQC).

##### Inicializando o AQC 
1. No terminal, digite: 
sudo service aerospike start (Linux) ou
 vagrant up 
 vagrant ssh  
 sudo service aerospike start  
 sudo service amc start   (Mac OS)
para iniciar o Aerospike.
2. Digite então aql para iniciar o AQC.

##### Cria um novo namespace
1. Para criar um novo namespace, você deve alterar as configurações no arquivo de configuração do namespace.
###### Aerospike database configuration file.
    service {
        user root
        group root
        paxos-single-replica-limit 1 # Number of nodes where the replica
        pidfile /var/run/aerospike/asd.pid
        service-threads 4
        transaction-queues 4
        transaction-threads-per-queue 4
        proto-fd-max 15000
    }

    logging {
        file /var/log/aerospike/aerospike.log {
        context any info
        }
    }

    network {
        service {
        address any
        port 3000
        }

         heartbeat {
         mode multicast
         address 239.1.99.222
         port 9918
         interval 150
         timeout 10
         }
        
         fabric {
         port 3001
        }

        info {
        port 3003
        }
    }

    namespace test {
        replication-factor 2
        memory-size 4G
        default-ttl 30d # 30 days, use 0 to never expire/evict.
        storage-engine memory
    }

    namespace bar {
    replication-factor 2
    memory-size 4G
    default-ttl 30d # 30 days, use 0 to never expire/evict.
    storage-engine memory
    }
2. Copie o bloco de namespace de teste e cole-o logo em seguida, alterando o nome para o que você deseja.
3. Salve o arquivo e pronto. Seu novo namespace está criado.

##### Lista os namespaces
Vá para o prompt do aql e digite o seguinte comando
#
    aql> show namespaces   
            
##### Lista os sets
Vá para o prompt do aql e digite o seguinte comando
#
    aql> show sets 

##### Insere registros
    aql> INSERT INTO user_profile.west (PK,location,last_activity) VALUES ('cookie_100','MA',342) 
    aql> INSERT INTO user_profile.west (PK,location,last_activity) VALUES ('cookie_101','AZ',345)
    aql> INSERT INTO user_profile.west (PK,location,last_activity) VALUES ('cookie_102','CA',345)
    aql> INSERT INTO user_profile.west (PK,location,last_activity) VALUES ('cookie_103','AL',340)
    aql> INSERT INTO user_profile.west (PK,location,last_activity) VALUES ('cookie_104','TX',347)
    aql> INSERT INTO user_profile.west (PK,location,last_activity) VALUES ('cookie_105','MA',323)
#
    INSERT INTO <ns>[.<set>] (PK, <bins>) VALUES (<key>, <values>)
    DELETE FROM <ns>[.<set>] WHERE PK = <key>
    TRUNCATE <ns>[.<set>] [upto <LUT>] 
#
          <ns> é o namespace do record.
          <set> é o nome do set para o record.
          <key> é a chave-primária do record.
          <bins> é uma lista de nomes de bins.
          <values> é uma lista de valores de bins, pode conter expressão de tipo.
          <LUT> é o último momento de update, onde o set ou o namespace precisa ser truncado.
#
          INSERT INTO test.demo (PK, foo, bar) VALUES ('key1', 123, 'abc')
          INSERT INTO test.demo (PK, foo, bar) VALUES ('key1', CAST('123' AS INT), JSON('{"a": 1.2, "b": [1, 2, 3]}'))
          INSERT INTO test.demo (PK, foo, bar) VALUES ('key1', LIST('[1, 2, 3]'), MAP('{"a": 1, "b": 2}'))
          INSERT INTO test.demo (PK, gj) VALUES ('key1', GEOJSON('{"type": "Point", "coordinates": [123.4, -456.7]}'))
          DELETE FROM test.demo WHERE PK = 'key1'
          
                  Supported AQL Types:
      
            Tipo do valor do Bin              Nome do tipo equivalente
           ===============================================================
            Integer                           DECIMAL, INT, NUMERIC
            Floating Point                    FLOAT, REAL
            Aerospike CDT (List, Map, etc.)   JSON
            Aerospike List                    LIST
            Aerospike Map                     MAP
            GeoJSON                           GEOJSON
            String                            CHAR, STRING, TEXT, VARCHAR
           ===============================================================

##### Lista registros

##### Seleciona registros
#
    SELECT <bins> FROM <ns>[.<set>]
    SELECT <bins> FROM <ns>[.<set>] WHERE <bin> = <value>
    SELECT <bins> FROM <ns>[.<set>] WHERE <bin> BETWEEN <lower> AND <upper>
    SELECT <bins> FROM <ns>[.<set>] WHERE PK = <key>
    SELECT <bins> FROM <ns>[.<set>] IN <indextype> WHERE <bin> = <value>
    SELECT <bins> FROM <ns>[.<set>] IN <indextype> WHERE <bin> BETWEEN <lower> AND <upper>
    SELECT <bins> FROM <ns>[.<set>] IN <indextype> WHERE <bin> CONTAINS <GeoJSONPoint>
    SELECT <bins> FROM <ns>[.<set>] IN <indextype> WHERE <bin> WITHIN <GeoJSONPolygon>

          <ns> é o namespace do record.
          <set> é o nome do set para o record.
          <key> é a chave-primária do record.
          <bin> é o nome do bin.
          <value> é o valor do bin.
          <indextype> é o tipo do index que o usuário deseja procurar. (LIST/MAPKEYS/MAPVALUES)
          <bins> é uma lista de nomes de bins.
          <lower> é o menor valor numa busca.
          <upper> é o maior valor numa busca.
          
##### Seleciona registros por atributos (condicional)
#
    aql> SELECT * FROM user_profile.west WHERE last_activity BETWEEN 340 AND 345

##### Inspeciona uma busca
    EXPLAIN SELECT * FROM <ns>[.<set>] WHERE PK = <key>

          <ns> é o namespace do record.
          <set> é o nome do set para o record.
          <key> é a chave-primária do record.
      Exemplo:
          EXPLAIN SELECT * FROM test.demo WHERE PK = 'key1'

##### Índices
1. Criar índices:
#
    CREATE INDEX <index> ON <ns>[.<set>] (<bin>) NUMERIC|STRING|GEO2DSPHERE
    aql> CREATE INDEX idx_foo ON test.demo (foo) NUMERIC 

2. Deletar índices:
#
    DROP INDEX <ns>[.<set>] <index>]
    aql> DROP INDEX teste.demo idx_foo 
3. Mostrar índices
#
    ql > show indexes

##### Agregações e User Defined Functions (UDF)

      AGGREGATE <module>.<function>(<args>) ON <ns>[.<set>]
      AGGREGATE <module>.<function>(<args>) ON <ns>[.<set>] WHERE <bin> = <value>
      AGGREGATE <module>.<function>(<args>) ON <ns>[.<set>] WHERE <bin> BETWEEN <lower> AND <upper>

          <module> é o módulo UDF que possui a função que será chamada.
          <function> é o UDF que será chamada.
          <args> é uma lista que contém os argumentos de UDF.
          <ns> é o namespace do record.
          <set> é o nome do set para o record.
          <key> é a chave-primária do record.
          <bin> é o nome do bin.
          <value> é o valor do bin.
          <lower> é o menor valor numa busca.
          <upper> é o maior valor numa busca.

      Exemplos:

          AGGREGATE myudfs.udf2(2) ON test.demo WHERE foo = 123
          AGGREGATE myudfs.udf2(2) ON test.demo WHERE foo BETWEEN 0 AND 999


      EXECUTE <module>.<function>(<args>) ON <ns>[.<set>]
      EXECUTE <module>.<function>(<args>) ON <ns>[.<set>] WHERE PK = <key>
      EXECUTE <module>.<function>(<args>) ON <ns>[.<set>] WHERE <bin> = <value>
      EXECUTE <module>.<function>(<args>) ON <ns>[.<set>] WHERE <bin> BETWEEN <lower> AND <upper>
      EXECUTE myudfs.udf1(2) ON test.demo
      EXECUTE myudfs.udf1(2) ON test.demo WHERE PK = 'key1'


##### Remover Records
Este exemplo remove a chave mykey do namespace test no set myset.
# 
    Key key = new Key("test", "myset", "mykey");
    client.delete(policy, key);
##### info
      SHOW NAMESPACES | SETS | BINS | INDEXES
      SHOW SCANS | QUERIES
      STAT NAMESPACE <ns> | INDEX <ns> <indexname>
      STAT SYSTEM
      ASINFO <ASInfoCommand>
      
      SHOW USER [<user>]
      SHOW USERS
      SHOW ROLE <role>
      SHOW ROLES
#     
    JOB MANAGEMENT
      KILL_QUERY <transaction_id>
      KILL_SCAN <scan_id>
#      
    USER ADMINISTRATION
      SHOW USER [<user>]
      SHOW USERS
      SHOW ROLE <role>
      SHOW ROLES
#      
    MANAGE UDFS
      SHOW MODULES
      DESC MODULE <filename>
      
          <filepath> é o caminho para o modulo UDF.
          <filename> é o nome do arquivo do módulo UDF.
      Exemplos:
      
          SHOW MODULES
          DESC MODULE test.lua
#      
    RUN <filepath>
    SYSTEM <bash command>
    SETTINGS
        ECHO                          (true | false, default false)
        VERBOSE                       (true | false, default false)
        OUTPUT                        (TABLE | JSON | MUTE | RAW, default TABLE)
        OUTPUT_TYPES                  (true | false, default true)
        TIMEOUT                       (time in ms, default: 1000)
        SOCKET_TIMEOUT                (time in ms, default: -1)
        LUA_USERPATH                  <path>, default : /opt/aerospike/usr/udf/lua
        USE_SMD                       (true | false, default false)
        RECORD_TTL                    (time in sec, default: 0)
        RECORD_PRINT_METADATA         (true | false, default false, mostra na tela os metadados dos records)
        REPLICA_ANY                   (true | false, default false)
        KEY_SEND                      (true | false, default false)
        DURABLE_DELETE                (true | false, default false)
        SCAN_PRIORITY                 priority of scan (LOW, MEDIUM, HIGH, AUTO), default : AUTO
        
      Para ter o valor da configuração use:
          aql> GET <setting>
      	
      Para resetar o valor de uma configuração para o default, use:
          aql> RESET <setting>
      	
    OUTROS
        HELP
        QUIT|EXIT|Q

## Arquitetura
No Aerospike, cada requisição é satisfeita por um único nó, que armazena e é o responsável (mestre) de uma parte do total de dados. Essa arquitetura cria um sistema sem um ponto único de falha, e permite a escalabilidade horizontal. Para aumentar a disponibilidade e a confiabilidade, o Aerospike também replica os dados em diferentes nós.

### Distribuição
No Aerospike, cada *namespace* é dividido em 4096 partições lógicas, que são divididas igualmente entre os n nós do *cluster*. Ou seja, quando não há réplicas dos dados (fator de replicação igual a 1), cada nó armazena aproximadamente 
1/n dos dados.

Essa divisão se dá da seguinte maneira: a chave de cada registro (independente do tamanho) é transformado em uma *hashtag* de 20 *bytes*, utilizando o RIPEMD160 (uma função hash de distribuição aleatória). Utilizando os 12 primeiros *bits* da *hashtag*, o ID da partição deste registro é determinado. Graças às características desta função, as partições terão uma distribuição normal dentro dos diferentes nós do cluster, não havendo necessidade de *sharding* manual.

Quando nós são adicionados ou removidos do *cluster*, um novo *cluster* se formará e seus nós se coordenarão para dividir as partições entre eles. Então, o *cluster* automaticamente se rebalanceará.

Como os dados se distribuem igualmente (e aleatoriamente) nos nós, não existem *hot spots* ou gargalos onde aconteçam mais requisições de dados que em outros nós.

### Replicação
O Aerospike replica as partições em um ou mais nós. Um nó se torna o mestre, para escritas e leituras, para uma partição, enquanto outros nós guardam uma réplica dessa partição.

O fator de replicação é configurável; no entanto, ele não pode exceder o número de nós existentes. Mais réplicas significa maior confiabilidade dos dados, mas aumenta a demanda do cluster, já que as escritas tem de ser propagadas para todas as réplicas. Por padrão (e na maior parte dos casos), é utilizado fator de replicação 2 (o mestre e uma réplica).

Para o caso de fator de replicação 2, por exemplo, além das 1/n partições da qual é mestre, cada nó também terá réplica de 1/n dos dados do *cluster*.

| ![Replicação](https://aerospike.com/docs/architecture/assets/ARCH_shared_nothing_small.png) | 
|:--:| 
| *Replicação fator 2. Dois nós em um cluster que são master e réplica.* |


## Implementação de propriedades
Conforme o teorema CAP, o Aerospike, até a versão 3.0, é um banco de dados AP - isto é, oferece disponibilidade ao invés de consistência, em casos de partição de rede. O Aerospike 3.0 não fornece diversas funcionalidades que são necessárias para consistência de replicação durante uma transação. Em vez disso, permite que o dado esteja disponível e aceitando escritas - criando, eventualmente, conflitos de escrita. O processo de escolher uma versão do dado para ser persistida, em caso de versões conflitantes, pode resultar em perda de dado.

À partir da versão 4.0, o Aerospike suporta tanto o modo AP (Disponível e Tolerante à partição), quanto o modo CP (Consistente e Tolerante à partição). Sendo o modo configurado através das políticas do “namespace”.

### Transações
Como citado anteriormente, até sua versão 3.0, era suportado apenas o modo AP (disponibilidade sobre consistência). Com isso, em caso de partição de rede, qualquer partição se colocaria como dono de todos os dados (que estivessem disponíveis nos nós do "sub-cluster"). Quando o problema da partição fosse resolvido, conflitos aconteceriam, causando perda de dados.

A partir do Aerospike 4, a "consistência forte" foi introduzida. Com esse algoritmo, as partes do cluster e as partições gerenciam cuidadosamente quais partes do cluster ainda estão disponíveis, não permitindo qualquer potencial conflito de escrita.

Ele passou a manter, também, consistência com base em chave-primária, promovendo durabilidade ao *commitar* a escrita para múltiplos servidores físicos, com componentes de *hardware* independentes.

Para durabilidade ainda maior, pode ser necessário que uma escrita seja feita em memória persistente.

No entanto, ser *ACID* também implica em outras duas características: isolamento e consistência *Multi-record* e isolamento de consulta, para que uma consulta *multi-record* seja consistente em relação à escritas. O Aerospike não provê essas duas caracteristicas.

### Consistência
O Aerospike oferece consistência forte para transações *single-record*, e todas as operações podem ser executadas de modo linear. Em adição, o Aerospike permite configuração para relaxar a consistência, em favor de maior performance e disponibilidade, conforme necessário.

A consistência forte garante que todas as escritas para um determinado arquivo irão ser aplicadas em uma ordem específica (sequencialmente), e escritas não serão reordenadas ou puladas (ou seja, perdidas).

Em particular, escritas que são reconhecidas como enviadas (*committed*) foram aplicadas e existem na linha do tempo, em contraste com outras escritas ao mesmo arquivo. Essa garantia se mantém mesmo em face à falhas na rede e partições do cluster. Escritas marcadas como *"timeouts"* ou *"InDoubt"* podem não ser aplicadas.

A consistência forte do Aerospike é *per-record*, e não envolve transações *multi-record*. Cada escrita ou atualização dentro de um mesmo arquivo será atômica e isolada, e a ordenação é garantida ao se utilizar relógio híbrido.

São oferecidos, também, tanto o modo *Full Linearizable*, que provê uma única *view* dentre todos os clientes que podem ler dados, como o modo de *Consistência de Sessão*, que garante que um processo individual veja a sequencial de atualizações. Qual modo será utilizado pelo cluster é configurável.

Na replicação síncrona, o dado é escrito no mestre e nas réplicas “simultaneamente”. Desta maneira, todas as cópias dos dados estão sempre sincronizadas. Isto é alcançado ao se esperar que todos os nós com uma cópia de um dado X processem uma transação feita sobre este dado, enviando a confirmação da transação ao usuário apenas após a confirmação de todas as cópias. Na replicação assíncrona, as réplicas geralmente ficam algumas transações para trás do mestre.

Em um exemplo, onde um usuário efetua uma transação sobre um dado com o mestre no nó A e réplica no nó B, o que ocorre é:

* O nó A guarda a transação em cache e imediatamente envia a atualização à cópia do dado no nó B.
* A cópia em B envia uma mensagem de confirmação de volta ao nó A.
* O nó A envia a confirmação de escrita ao usuário.

Enquanto na replicação assíncrona, uma das formas de se relaxar a consistência do cluster em troca de disponibilidade. O que seria feito seria:

* O nó A escreve a atualização e envia a confirmação ao usuário.
* O nó A envia a atualização do dado à cópia no nó B.
* O nó B, apenas então, envia uma mensagem de confirmação de escrita ao nó A.

Quando o banco está se recuperando de um reparticionamento, podem haver escritas perdidas, na replicação assíncrona.


### Disponibilidade
Como o sistema é configurado como AP, do teorema CAP, a disponibilidade é um dos fatores mais importantes no banco de dados, perdendo um pouco da consistência. O Aerospike diz ter disponibilidade perfeita, sendo que sempre que um nó cai, outros vão estar disponíveis com a informação necessária para ser utilizada.

O sistema implementa isso utilizando o algoritmo de *Smart Partitions*, onde quando um nó de uma partição para de funcionar, as requisições que este nó aceitaria são distribuídas aleatoriamente entre os outros nós da partição. Isso é um diferencial do Aerospike entre os outros bancos de dados, que neste caso iriam alocar todas as requisições para um único ou poucos outros nós, causando um possível overflow de requisições.

Além disso, vale ressaltar algumas tecnologias utilizadas pelo Aerospike para garantir o máximo de tempo no ar, que são:

* O sistema de monitoramento de *cluster* dinâmico, que consiste em automaticamente detectar nós entrando e saindo do *cluster*, e assim que essa movimentação ocorre, os dados são re-replicados. O sistema distribui as responsabilidades entre os nós principais e os nós réplica igualmente, garantindo a disponibilidade mencionada acima.

* O *Aerospike Smart Client*, que serve para monitorar o *cluster* durante sua adaptação em uma nova rede. Ele guarda informações como a localização mais recente dos dados, além de reconfigurar as conexões do cliente automaticamente e inicializá-las, independentemente do hardware. 

*  A replicação geográfica, que se preocupa em encontrar *datacenters* mais adequados para hospedar os dados com uma distribuição favorável para a velocidade de conexão.

Além de tudo isso, os *updates* do *software* são realizados de maneira que não ocorram problemas de compatibilidade entre versões. Assim não é necessário um *downtime* para que ocorra a atualização.

### Escalabilidade
Devido a arquitetura *shared-nothing*, basta apenas adicionar novos nós ao cluster. Ela permite escalabilidade horizontal em todas as camadas da aplicação. Esse é um dos maiores diferenciais de Aerospike sobre outros bancos, que não se preocupam tanto com a escalabilidade, e quando chegam em seu limite causam danos aos clientes de seus serviços, e no fim a equipe desenvolvedora vai gastar mais tempo e dinheiro para atualizar a aplicação. E ainda assim é só uma questão de tempo até o mesmo problema acontecer e o processo se iniciar de novo.

### Quando usar?
O Aerospike foi desenhado para aplicações que devem estar sempre disponíveis e lidar com big data de maneira rápida e confiável. A seguir veremos alguns [casos de uso] e exemplos de empresas que o utilizam no dia a dia de suas operações.

* Substituição de cache
    Latência baixa e alto rendimento são características do Aerospike e fazem com que ele seja um ótimo candidato para substitui o cache. Quando existe um grande número de dados dinâmicos é necessária a decisão entre guardar dados em cache, e assim abrir espaço para eventuais inconsistências de dados, ou fazer muitas consultas lentas. O Aerospike funciona com o sistema chave-valor que possui grande foco na rapidez e simplicidade, com caminho mais direto para o disco ou memória na busca dos dados, e por isso o seu uso pode substituir o uso de cache. Empresas como a KAYAK, uma ferramenta de busca especializada em viagens, utilizam o Aerospike desta forma em suas buscas.

* Armazenamento de perfil de usuários
    O armazenamento de perfis de usuário é muito importante em alguns tipos de sistemas e muitas vezes os dados são variados e de tamanho pequeno. O Aerospike funciona muito bem nessas condições e por conta disso possui muitos clientes do ramo de publicidade, como as empresas AppNexus e Nielsen.

* Mecanismo de recomendação
    Mecanismos de recomendação exigem uma camada de dados rápida, para suportar muitas requisições sem grande impacto para o usuário, e flexível, já que a base tende a crescer rapidamente. O Aerospike funciona muito bem neste contexto e a utilização dele no mercado acontece em diversas empresas de publicidade e e-commerces. 0 Aerospike possui até mesmo um modelo open source de criação de um mecanismo de recomendação usando suas ferramentas, RESTful Web Service e Spring Boot.

* [Teste do link] 

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen)
[Teste do link]: <https://twitter.com/littleis13>
[casos de uso]: <https://www.aerospike.com/solutions/technology/use-cases/>

### Glossário de Termos

* *Schemaless*: Não segue uma estrutura pré-definida ou específica. Não estruturado.

* *Namespace*: Uma coleção de dados que segue uma "política" comum. É o equivalente deste sistema aos bancos de dados dos sistemas relacionais.

* *Set*: Um conjunto de registros. Equivalente a uma tabela em um banco de dados relacional.

* *Record*: Registro. Equivalente a um registro/linha de um banco de dados relacional.

* *Bin*: Campo. Equivalente a uma coluna em banco de dados relacional.

* *Index*: índice de um registro. Permite localizar/realizar buscas por uma característica dos registros.

* *Aerospike Management Console*: Interface visual fornecida pelo Aerospike para analisar e gerenciar os nós e seus *namespaces* e *records*.

* *Aerospike Query Client*: Interface em linha de comando do Aerospike para manipular dados e gerenciar o *cluster*.

* *SQL-like*: Baseada no SQL. Próxima ao SQL.

* *User Defined Functions* (UDF): Funções criadas pelo usuário para algum tipo de processamento ou transformação nos dados.

* *RIPEMD160*: Uma função hash de distribuição aleatória.

* *Sharding*: Arquitetura de banco de dados, onde os dados podem ser espalhado por várias máquinas. Equivalente a “Share-nothing”.

* *Hot spots*: Um ponto único de falha. Um ponto/local de onde muitos dados são processados ou requisitados.

* *Commitar*: Submeter. Se refere à reconhecer uma transação no banco de dados. Ter enviado uma mudança.

* *Multi-record*: no contexto utilizado, se refere a transações que utilizam dados de mais de um nó. “Multi-record transactions”, transações com dados de vários nós do *cluster*.

* *Single-record*: no contexto utilizado, se refere a transações que utilizam dados de um único nó. “Single-record transactions”, transações com dados de um mesmo nó do *cluster*.

* *Full Linearizable*: Que pode ser feito de maneira sequencial. Pode ser linearizado/ordenado.

* *Shared-nothing*: Arquitetura de computação distribuída onde cada requisição é satisfeita por um único nó, que armazena e é o responsável (mestre) de uma parte do total de dados. Equivalente a “Sharding”.

* *Big Data*: Um grande volume de dados.



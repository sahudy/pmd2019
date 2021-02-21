# ArangoDB Tutorial

#### Neste tutorial iremos cobrir alguns conceitos básicos sobre ArangoDB e algumas de suas arquiteturas, além de mostrar o passo a passo necessário para a criação de um sistema com um cluster e 3 nós na arquitetura Master/Slave.

## Índice

  * [Visão Geral](#visão-geral)
  * [Instalação](#instalação)
    + [Linux (Fedora)](#linux-fedora)
    + [MacOS](#macos)
  * [Configuração](#configuração)
  * [Comandos Básicos](#comandos-básicos)
  * [Replicação e arquitetura de distribuição de dados](#replicação-e-arquitetura-de-distribuição-de-dados)
    + [Replicação](#replicação)
      - [Replicação síncrona](#replicação-síncrona)
      - [Replicação assíncrona](#replicação-assíncrona)
    + [Single Instance](#single-instance)
    + [Master/Slave](#master-slave)
    + [Active Failover](#active-failover)
    + [Cluster](#cluster)
  * [Implementação de Propriedades](#implementação-de-propriedades)
    + [Consistência](#consistência)
    + [Transações](#transações)
    + [Disponibilidade](#disponibilidade)
    + [Escalabilidade](#escalabilidade)
    + [Quórum](#quórum)
  * [Quando utilizar ArangoDB](#quando-utilizar-arangodb)
  * [Quando não utilizar](#quando-não-utilizar)
  * [Parte Prática](#parte-prática)
  * [Configuração](#configuração-1)
    + [Criação do cluster](#criação-do-cluster)
    + [Configuração do Banco de Dados](#configuração-do-banco-de-dados)
  * [Inserção de dados na coleção Restaurants](#inserção-de-dados-na-coleção-restaurants)
  * [Consultas na base de dados](#consultas-na-base-de-dados)
  * [Testando a replicação dos dados](#testando-a-replicação-dos-dados)
  * [Exercícios para praticar](#exercícios-para-praticar)
  * [Referências](#referências)


## Visão Geral
  ArangoDB é um NoSQL, native multi-model, SGBD (Sistema de Gerenciamento de Banco de Dados), o que significa que ele une 3 tipos de modelos de dados dentro de seu núcleo, sendo eles: chave-valor, documentos e grafos. Isso permite que usuários possam armazenar seus dados em qualquer um destes modelos e utilizar apenas uma linguagem declarativa para consultas, inserções, remoções e alterações na base de dados.
        
  Como ArangoDB é native multi-model é possível realizar consultas combinando padrões de acesso a diferentes tipos de dados em uma única consulta, assim possibilitando consultas que relacionem os dados armazenados nos diferentes modelos.
  
  ## Instalação
  Nesta seção iremos explicar como foi feita a instalação da versão 3.5.1 do ArangoDB nos sistemas operacionais Linux e MacOS. 
  
   ### Linux Fedora
   Devido a falta de suporte da aplicação por meio do site oficial para versões mais recentes do SO, iremos instalar por meio do SnapCraft.
        
   Assumiremos que o SnapCraft já está instalado (caso não esteja, o link para instalação é este: 
                  https://snapcraft.io/docs/installing-snap-on-fedora).
   
   Abrimos um terminal e utilizamos o seguinte comando:
   ```
   sudo snap install arangodb3
   ```
   e esperamos o download e posterior instalação a serem executadas pelo comando.
   ### MacOS
   Já no sistema operacional MacOS, podemos utilizar o seguinte comando para instalar o ArangoDB, no terminal:
   ```
   brew install arangodb
   ```
   Caso não consiga instalar por causa que não reconhece o comando brew, instalar o homebrew primeiro através do link: https://brew.sh.
        
   Ao terminar a instalação, o servidor ArangoDB já estará rodando em sua máquina pronto para ser acessado.
   
## Configuração
  Após ter instalado o ArangoDB em sua máquina, no terminal execute o comando, a seguir, para inicializar e ter acesso ao ArangoDB.
  ```
  arangodb
  ```
  Para acessar o servidor entramos no browser e acessamos a seguinte url: http://127.0.0.1:8529, a qual aponta para a definição padrão de url e porta para o servidor ArangoDB. Nessa página, temos que fazer login, o qual faremos com o usuário "root" que é o já criado como padrão para o servidor.
  
## Comandos Básicos
  ### Nesta seção é exemplificado o uso da linguagem AQL, que é a usada no ArangoDB, para alguns comando básicos e vamos comparar suas estruturas com a SQL.
  
  #### Terminologia
  Na figura abaixo, há a comparação dos termos entre a linguagem SQL e AQL.
  
  ![](https://raw.githubusercontent.com/rabbit11/Massive-Data-Processing/master/Project/img/terminologia.png)</br>
  **Figura 1: Comparação SQL e AQL** 
  
  #### Estrutura básica da AQL
  Na tabela abaixo, podemos ver algumas cláusulas do AQL comparadas com a SQL e qual o significado de cada um.
  
  | Cláusula         | SQL         | Significado                                             |
|------------------|-------------|---------------------------------------------------------|
| Filter           | Where       | Define condições para os resultados de retorno          |
| For...return     | Select      | Define quais colunas serão selecionadas na consulta     |
| With <agregação> | <agregação> | Define quais função de agregação fará parte da consulta |
| Collect          | Group by    | Define quais campos serão agrupados                     |
| Sort             | Order by    | Define quais campos serão ordenados                     |
| Insert           | Insert      | Insere campo(s) na coleção                              |
| Update           | Update      | Atualiza campo(s) na coleção                            |
| Remove           | Delete      | Deleta campo(s) na coleção                              |

**Tabela 1: Compração entre cláusulas**
  
  #### Inserção de documento:
  No exemplo abaixo, vamos inserir dentro da tabela Users, o nome John Doe do gênero masculino (m).
 
                  AQL                                                                SQL
   
   ```                                                                 
   INSERT { name: "John Doe", gender: "m" }                             INSERT INTO users (name,gender)
        INTO users                                                      VALUES ("John Doe","m");
   ```
   
  #### Atualização de documento:
  No exemplo abaixo, vamos atualizar o campo name para John Smith onde este tem id(SQL) ou key(AQL) igual a 1 na tabela de users.
  
                AQL                                                                 SQL
  
  ```
   UPDATE { _key: "1" }                                                 UPDATE users
       WITH { name: "John Smith" }                                      SET name = "John Smith"
       IN users                                                         WHERE Id = 1
  ```
  
  #### Remoção de documento:
  No exemplo abaixo, vamos excluir os dados que tem no campo id(SQL) ou key(AQL) o valor igual a "value", da tabela users.
  
                AQL                                                                 SQL           
  
  ```
   REMOVE \{ \_key:"value" \}                                            DELETE FROM users
              IN users                                                   WHERE id = "value";
  ```
  
  #### Seleção de documentos:
  No exemplo abaixo, vamos selecionar todos os dados da tabela.
  
                AQL                                                                 SQL
  
  ```
   FOR user IN users                                                      SELECT * FROM users;                               
              RETURN user
  ```
  
  #### Filtro de documentos em seleção:
  No exemplo abaixo, vamos selecionar o primeiro nome e o sobrenome concatenados e o gênero, onde o campo active for igual a 1 da tabela users.
  
                AQL                                                                 SQL
  
  ```
  FOR user IN users                                        SELECT CONCAT(firstName, " ", lastName)
              FILTER user.active == 1                             AS name, gender
              RETURN {                                     FROM users
                name: CONCAT(user.firstName, " ",          WHERE active = 1;
                             user.lastName),
                gender: user.gender
              }
  ```
  
  #### Ordenação de documentos numa seleção:
  No exemplo baixo, vamos selecionar todos os dados onde tiverem o campo active igual a 1 e ordenados de forma crescente por nome e gênero.
  
                AQL                                                                 SQL
  
  ```
  FOR user IN users                                                         SELECT * FROM users                  
              FILTER user.active == 1                                       WHERE active = 1
              SORT user.name, user.gender                                   ORDER BY name, gender;
              RETURN user
 ```
 
 #### Contagem de documentos de uma coleção:
 No exemplo abaixo, vamos selecionar o agrupamgento do gênero e quantos dados existem com aquele gênero onde o campo active for igual a 1.
 
                AQL                                                                 SQL
  
  ```
 FOR user IN users                                              SELECT gender, COUNT(*) AS number 
              FILTER user.active == 1                           FROM users
              COLLECT gender = user.gender                      WHERE active = 1
                WITH COUNT INTO number                          GROUP BY gender;
              RETURN { 
                gender: gender, 
                number: number 
              }
 ```
 
 #### Agrupamento de documentos de uma coleção:
 
 
                AQL                                                                 SQL
  
  ```
  FOR user IN users                                           SELECT YEAR(dateRegistered) as year,
            FILTER user.active == 1                                  MONTH(dateRegistered) as month,
            COLLECT                                                  COUNT(*) as number
              year = DATE_YEAR(user.dateRegistered),          FROM users
              month = DATE_MONTH(user.dateRegistered)         WHERE active = 1
              WITH COUNT INTO number                          GROUP BY name, gender
              FILTER number > 20                              HAVING number > 20;
              RETURN {
                year: year, 
                month: month, 
                number: number 
              }
 ```
 #### Juntando diferentes coleções:
 No exemplo abaixo, vamos selecionar todos os dados onde há uma junção de coleções.
 
                AQL                                                                  SQL
                
 ```

       FOR user IN users                                      SELECT * FROM users
          FOR friend IN friends                               INNER JOIN friends
          FILTER friend.user == user._key                     ON (friends.user = users.id);
          RETURN MERGE(user, friend)                

 ```              
 
 
 ##  Replicação e arquitetura de distribuição de dados
 ### Replicação
   A replicação pode ser definida como cópias dos dados de um nó do sistema para outro, de forma a permitir recuperação de falhas no caso de quedas de conexões entre servidores. Para isso o ArangoDB oferece dois tipos de replicação: síncrona e assíncrona.
   
   #### Replicação síncrona
   A replicação síncrona funciona apenas entre servidores dentro de um mesmo cluster, e é normalmente utilizada para operações críticas, onde os dados devem estar disponíveis a todo o momento. Normalmente este tipo de replicação guarda uma cópia de um fragmento dos dados em um ou mais outros servidores. Desta forma as operações de escrita só são devidamente validadas quando todas as réplicas efetuarem aquela operação de escrita, o que aumenta a latência desta operação, porém caso uma falha ocorra logo após a escrita, temos a garantia de que os dados presentes em qualquer uma das réplicas é o dado mais recente.
   
   #### Replicação assíncrona
   A replicação assíncrona é usada principalmente na arquitetura de master/slave, de forma que os slaves conectam-se aos seus respectivos masters e aplicam localmente os eventos já aplicados ao master em mesma ordem, como resultado os slaves terão os dados no mesmo estado que os seus respectivos masters.
 
 #### Nesta seção serão exemplicados todos os tipos de arquitetura presentes no ArangoDB.
 
 ### Single Instance
   *Single Instance* é a arquitetura mais simples que existe neste banco de dados. Ao utilizar esta arquitetura sozinha, não existirá métodos de replicações, oportunidades de *failover* e nenhum *cluster* com outros nós.
   
   Mesmo sem todos os recursos citados anteriormente, é possível rodar múltiplos processos na mesma máquina com esta arquitetura, contanto que as portas e os dados sejam configurados diferentemente.
   
 ### Master Slave
  O ArangoDB possui a arquitetura *Master/Slave* onde os *Slaves* recebem dados assíncronos de um *Master*. Nos *Slaves* deveria ser possível apenas realizar a leitura dos dados, enquanto o *Master* realiza inserções e atualizações dos dados.
  Devido algumas limitações na implementação do ArangoDB, o slave pode realizar alterações na base de dados, mesmo isso sendo incorreto conceitualmente.[2] Na figura 1, pode ser observado, de modo simplificada, esta arquitetura.
  
   ![](https://raw.githubusercontent.com/rabbit11/Massive-Data-Processing/master/Project/img/master-slave1.png)</br>
   **Figura 2: Arquitetura Master/Slave**
  
  Nota: Devido algumas limitações na implementação do ArangoDB, o slave pode realizar alterações na base de dados, mesmo isso sendo incorreto conceitualmente.[2]
  
### Active Failover
  Uma arquitetura de *Active Failover* possui as seguintes características:
  
  * Uma instância chamada de Leader que clientes podem realizar operações de leitura e escrita.
  * Ao menos uma instância chamada de Follower, que clientes podem realizar apenas operações de leitura, essas instâncias replicam os dados do Leader de maneira assíncrona.
  *  Uma instância chamada de Agency, cuja função é ser uma espécie de testemunha, que decide qual servidor assume a posição de líder, caso alguma falha ocorra.
  
  ![](https://raw.githubusercontent.com/rabbit11/Massive-Data-Processing/master/Project/img/leader-follower(1).png)
  **Figura 3: Arquitetura failover**
  
   A vantagem do uso de Active Failover quando comparada a arquitetura Master/Slave tradicional seria a terceira entidade chamada Agency que observa e está envolvida em todos os processos dos servidores. Followers podem contar com o Agency para determinar se estão conectados ao server Leader correto. Isso faz com que este tipo de arquitetura tenha uma maior resiliência, já que todos os drivers oficiais do ArangoDB são capazes de determinar o servidor Leader corretamente e redirecionar aquela requisição apropriadamente.
   
### Cluster
  Enquanto as arquiteturas citadas acima se referiam a arquitetura entre os nós dentro dos clusters, aqui definimos a arquitetura implementada no ArangoDB entre clusters, ou seja, como diferente clusters se relacionam dentro do sistema.
  A arquitetura de clusters no ArangoDB é CP master/master, o que significa (em termos do teorema CAP) que durante uma falha de conexão entre nós no servidor, este SGBD prioriza consistência interna no lugar de disponibilidade. Além disso uma arquitetura master/master permite que clientes podem mandar requisições de maneira arbitrária para qualquer nó e obter a mesma "visão" dos dados, sem um único ponto de falha, já que o cluster pode ainda servir a requisições mesmo com falhas em algumas máquinas.

## Implementação de Propriedades

  ### Consistência
   Como ArangoDB é um banco de dados de múltiplos modelos, a consistência dos dados se torna um empecilho, devido a cada um dos modelos não terem sido desenvolvidos para conversarem entre si, o que faz com que se tenha que desenvolver alguma funcionalidade de transação a fim de manter seus dados consistentes através dos diversos modelos suportados.
   
   Assim no ArangoDB, há uma única aplicação back-end gerenciando cada modelo de dados com suporte de transações ACID para desse modo manter uma forte consistência tanto em instâncias únicas como em operações atômicas no modo cluster.
   
 ### Transações
  As transações no ArangoDB  são atômicas, consistentes, isoladas e duráveis (ACID). Tais propriedades do ACID fornecem as seguintes garantias para as transações:
  
  * A atomicidade faz com que as transações sejam executadas completamente ou não sejam executadas.
  * A consistência garante que as transações ou alteram o estado para um estado novo e válido em caso de sucesso ou voltam ao estado anterior sem perda dos dados.
  * O isolamento mantém a transação atual não finalizada sem sofrer alterações de outras transações correntes.
  * A durabilidade garante que as operações das transações confirmadas sejam mantidas persistentes. Tal durabilidade da transação é configurável no ArangoDB, assim como a durabilidade no nível da coleção.
  
### Disponibilidade
  O grau de disponibilidade do ArangoDB pode ser descrito como de certa forma "flexível", já que depende da forma como ele é configurado. A arquitetura de cluster CP utilizada por essa base de dados em geral prioriza consistência interna em detrimento da disponibilidade. Para amenizar este efeito, o sistema pode ser configurado de forma a utilizar replicação síncrona apenas, o que geraria grandes ganhos em termos de disponibilidade em troca de grande parcela da performance, devida a alta latência gerada por operações de escrita.
 
### Escalabilidade
   Como o ArangoDB é uma base de dados distribuída em 3 tipos de modelos de dados diferentes, ele pode escalar de maneira horizontal, ou seja, pode se utilizar de diversos servidores. Essa técnica além de resultar em uma maior performance, também gera maior resiliência e melhora em capacidade.
   
   Base de dados que podem escalar de maneira horizontal também permitem que sistemas adéquem sua capacidade de processamento e armazenamento de acordo com a demanda, ou seja, durante altas demandas podemos aumentar o número de servidores (aumentando a escala horizontal) e durante momentos de baixa demanda, podemos desalocar alguns nós do sistema de forma a cortar custos.
   
   Apesar de todas as vantagens citadas acima sobre escalar o sistema horizontalmente, o ArangoDB também permite que o sistema o escale de maneira vertical, apesar de ser menos eficiente na maior parte dos casos.
   
   Infelizmente na documentação do ArangoDB não foi encontrada nenhuma forma de mostrar os efeitos da escalabilidade de maneira nativa, apenas exemplos de como podemos utilizar ferramentas externas para mensurar o tempo, uso de CPU e memória para diferentes operações, como no caso deste blog [3]. 
   
### Quórum
  Quórum em banco de dados NoSQL, pode ser definido breviamente como o número mínimo de nós necessários para realizar uma operação de leitura ou escrita na base de dados. Essa característica existe para preservar a consistência dos dados em arquiteturas com diversos nós, normalmente este conceito é reforçado para operações de inserção ou atualização de dados, de forma a garantir que inicialmente pelo menos uma parcela (normalmente metade dos nós presentes no sistema) receba aquela atualização de maneira imediata e então essa atualização seja repassada para os outros nós do sistema.
  Apesar da documentação do ArangoDB não definir explicitamente o conceito de Quórum, ou até mesmo sua implementação, observamos seu funcionamento durante este tutorial ao derrubar dois nós de uma arquitetura *Master/Slave* com três nós, o sistema impede qualquer acesso à base de dados, seja para leitura ou escrita. Portanto acreditamos que o conceito de Quórum esteja presente nativamente no ArangoDB em sistemas com arquitetura *Master/Slave* que possuem fator de replicação, de forma a forçar que pelo menos metade dos nós presentes na arquitetura estejam conectados e funcionando a todo momento, acreditamos também que este número de nós, esteja relacionado com o *Replication Factor* (fator de replicação do sistema) definido durante a criação da base de dados. Mostraremos como este fator de replicação pode ser definido durante a parte prática deste tutorial.
   
## Quando utilizar ArangoDB

  O ArangoDB por possuir vários tipos de armazenamento de dados, seja em formato de chave/valor, documentos ou grafos, isso traz uma maior performance nas aplicações e uma maior escabilidade horizontal dos dados quando combinados os três formatos. Além disso, é possível em uma única query relacionar os três formatos.
  
  Existem três principais situações onde o este banco de dados é extremamente recomendável, que são:
  
  * Ao realizar um projeto, pode não se ter todos os requisitos do sistema e de como organizar os dados, assim futuras modificações podem ocorrer na estruturação dos dados. Ao utilizar o ArangoDB não tem essa preocupação, já que possui um modelo de vários formatos de estruturar os dados, é possível alterar os dados de forma fácil para novos casos e novas características de um sistema.
  * Quando se está desenvolvendo uma aplicação em uma equipe, este banco permite que os times se cooperem através de casos de usos. Por exemplo, um time trabalha na formatação dos dados em chave/valor, enquanto outro pode trabalhar na parte dos grafos e depois trocar experiências e otimizando a usabilidade do sistema. Além disso, permite um time ter uma curva de aprendizado mais rápido.
  * Neste banco de dados, por ter um incrível sistema de vários formatos de estruturas de dados, é possível também ter uma única query para obtenção dos dados a fim de modular diferentes aplicações. 
  
  Empresas que utilizam o ArangoDb são: Barclays, Cisco, Thomson Reuters, Kabbage, Kaseware, entre outras.
  
## Quando não utilizar

  O uso do ArangoDB não é recomendado em situações em que um cluster é configurado em esquema Master/Slave no qual seja estritamente necessário o não acesso dos slaves à operações de escrita, devido a existir tal limitação no sistema nesse caso.

## Parte Prática
  Agora que já cobrimos a parte teórica, nesta seção iremos dar a você o passo a passo de como utilizar o ArangoDB para criar um cluster com 3 nós (sendo cada computador um nó diferente) na arquitetura Master/Slave.
  
## Configuração

  ### Criação do cluster
  Primeiro devemos configurar aquele nó que inicialmente será o Master, podemos escolher qualquer nó arbitrariamente. O seguinte comando inicia uma instância do arangodb, define um diretório no qual os arquivos armazenados dentro da base de dados deverão ser salvos, e a porta a qual os outros nós devem se conectar. E então no terminal do futuro nó que escolhemos, devemos executar o seguinte comando:
    ```
      arangodb --starter.data-dir=./db1 --starter.port="8530"
    ```
  E devemos receber uma mensagem de sucesso como: ArangoDB Starter listening on 0.0.0.0:8530.
  
  Após esta mensagem de sucesso, devemos executar o seguinte comando no terminal do segundo computador:
   ```
      arangodb --starter.data-dir=./db2 --starter.port="8530" --starter.join 192.168.15.6:8530
   ```
   
   Devemos então receber uma mensagem de sucesso semelhante a primeira. Este comando executado define o diretório onde os dados daquele nó deverão ser armazenados, a porta a qual outros nós devem se conectar a ele, e o IP e porta do nó o qual ele deve se conectar (no caso seria o IP do computador o qual executamos o primeiro comando, substitua "192.168.15.6" pelo IP do primeiro computador).
   
   Após esta mensagem de sucesso, devemos executar um comando similar no terminal do terceiro computador:
   ```
      arangodb --starter.data-dir=./db3 --starter.port="8530" --starter.join 192.168.15.6:8530
   ```
  Devemos então receber uma mensagem de sucesso e o endereço de IP e porta que deveremos acessar em nosso browser para acessar a interface do ArangoDB. Normalmente deve ser algo semelhante à: http://127.0.0.1:8529/
  
  Na figura a seguir, ilustramos de fato o que está acontecendo no terminal.
  
  ![](https://raw.githubusercontent.com/rabbit11/Massive-Data-Processing/master/Project/img/tentou.jpeg)</br>
  **Figura 4: Ilustração do terminal**
  
  ### Configuração do Banco de Dados
   Após acessar a interface web do ArangoDB, devemos selecionar o DB System e e clickar em *Select DB:_system*, como nesta imagem:
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/selectDB-system-arangodb.jpeg?raw=true)</br>
   **Figura 5: Tela inicial DB:System**
   
   E então veremos uma tela semelhante à essa:
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/dashboard-arangodb.jpeg?raw=true)</br>
   **Figura 6: Web interface**
   
   Iremos então para a aba *Database* e clicar em *Add Database*, que então gerará esta tela:
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/createdb-arangodb.jpeg?raw=true)</br>
   **Figura 7: Adicionando banco de dados**
   
   Neste caso daremos o nome de Restaurants para o nosso database.
   
   Após isso devemos clicar nas setas brancas no topo da página, ao lado de *DB:_System*. Como mostrado na figura a seguir:
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/troca.png?raw=true)</br>
   **Figura 8: Trocar de banco**
   
   Ao clicar seremos redirecionados a tela inicial, onde desta vez devemos escolher o database Restaurants e clicar no botão *Select DB: Restaurants*.
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/restaurants.png?raw=true)</br>
   **Figura 9: Selecionando Restaurants**
   
   Então deveremos acessar a aba Collections e adicionar uma nova coleção chamada *restaurants*, alterando as seguintes opções:
   
   * Number of shards: 1
   * Replication Factor: 3
    
   Isso irá permitir que a replicação seja realizada em nosso banco.
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/replicacao.jpeg)</br>
   **Figura 10: Adicionando replicação**
   
   Nota: Essa opções para colocar a quantidade de replicação e o fator só podem ser feitos quando existe um ou mais slaves.
   
  ## Inserção de dados na coleção Restaurants
   Ao acessar nossa collection Restaurants, podemos visualizar um botão chamado de import, onde iremos inserir o arquivo json que você encontra neste link: https://drive.google.com/file/d/1AvvqCcpD64Rg1ckVX-4GoJ9eRJXg7sj8/view. Aguarde alguns minutos após a confirmação até que o banco importe todos os dados (essa operação pode ser demorada, pois este arquivo possui milhares de documentos).
   
  ## Consultas na base de dados
   Suponha que você deseja consultar todos os restaurantes da base de dados. Para isso podemos ir até a aba Queries e realizar a seguinte consulta:
   ```
    FOR restaurant in restaurants RETURN restaurant
   ```
   Essa consulta deve retornar todos os restaurantes da base de dados, incluindo o número total de documentos encontrados.
   
  ## Testando a replicação dos dados
   A replicação de dados implica que em alguns ou até mesmo todos os nós, devem possuir cópias de parte ou de todos os dados do database. Neste caso definimos nosso *Replication Factor* desta nossa coleção como 3, o que implica que todos os 3 nós de nosso sistema possuem todos os dados de nossa base de dados. Sendo assim para testarmos a replicação, montamos o seguinte passo a passo:
   
   1. Finalize a instância do ArangoDB no nó master atual.
   2. Aguarde a eleição de um novo master (isso pode ser observado através do terminal, onde em um dos computadores deve retornar uma mensagem como: *Just Became Master*).
   
   ![](https://github.com/rabbit11/Massive-Data-Processing/blob/master/Project/img/master.jpeg)</br>
   **Figura 11: Eleição do master**
   
   3. Realize a seguinte consulta:
   ```
    FOR restaurant in restaurants RETURN restaurant
   ```
   4. Observe o resultado, que deve ser igual ao resultado obtido quando realizamos esta mesma consulta com as 3 instâncias do ArangoDB ativas.
   5. Reconecte a instância previamente desligada utilizando o seguinte comando:
   ```
   arangodb --starter.data-dir=./db1 --starter.port="8530" --starter.join 192.168.15.6:8530
   ```
   Supondo que o IP depois de *--starter.join* seja o IP daquele nó que foi eleito como master.
   
 ## Exercícios para praticar
 Nesta seção, selecionamos alguns exercícios para praticar a linguagem do ArangoDB, AQL.
 ```
 1. Escreva uma consulta para inserir o restaurante "Oliver Garden", bairro "Itaim Bibi" e gastronomia "Italian".
 ```
 ```
 2. Escreva uma consulta para atualizar a gastronomia do restaurante "Oliver Garden" para "Brazilian" 
 ```
 ```
 3. Escreva uma consulta para remover o restaurante "Oliver Garden".
 ```
 ```
 4. Escreva uma consulta para retornar os campos nome do restaurante, bairro, gastronomia e endereço dos restaurantes que estão no bairro "Brooklin".
 ```
 ```
 5. Escreva uma consulta para retornar os campos nome do restaurante, bairro, gastronomia e endereço dos restaurantes que estão no bairro "Queens" de forma que o nome dos restaurantes estejam em ordem crescente.
 ```
 ```
 6. Escreva uma consulta para contar quantos restaurantes existem por bairro.
 ```
 
 Respostas: https://raw.githubusercontent.com/rabbit11/Massive-Data-Processing/master/Project/respostas
   
## Referências

  [1] ArangoDB. Documentaçao completa. Página inicial. Disponível em: <https://www.arangodb.com/>. Acesso em: 11 de Out. de 2019.
  
  [2] ArangoDB. Master/Slave Limitations. Disponível em: <https://www.arangodb.com/docs/3.5/architecture-deployment-modes-master-slave-limitations.html>. Acesso em: 03 de Nov. de 2019
  
  [3] Blog. Escalabilidade no ArangoDB. Disponível em: https://d2iq.com/blog/arangodb-benchmark-dcos Acesso em: 16 de Nov. de 2019.

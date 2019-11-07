# Arquitetura de Distribuição
O RavenDB opera sobre *clusters* que providenciam redundância e disponibilidade dos dados. No contexto do RavenDB, *clusters* são um grupo de servidores chamados de *nós* e podem estar em mais de uma máquina. O RavenDB possui uma divisão de duas camadas de funcionamento: **Camada de Cluster** e **Camada de Banco de Dados**.
A **Camada de Cluster** é composta por nós (3 ou mais, é recomendado um número ímpar) que realizam operações de acordo com o consenso de todos os nós. Esse protocolo é um algoritmo de consenso desenvolvido para o RavenDB (chamado de Consenso *Rachis*, uma implementação derivada do protocolo [Raft](https://raft.github.io/)), e ele define um nó líder responsável por escolher o melhor nó para um determinado cliente, dando preferência a consistência das operações. 
A **Camada de Banco de Dados** representa bancos de dados de um *cluster* que formam um grupo e trabalham em união e cooperativamente. A quantidade de membros nos grupos é definido pelo Fator de Replicação. Cada nó possui uma cópia completa da topologia do *cluster* (informações dos nós que compõe o cluster), no qual especifica qual nó possui um determinado banco de dados. Esse processo de replicação ocorre sobre uma conexão TCP entre os nós.
Essa distinção é importante para manter várias propriedades do RavenDB. A eleição de um nó líder pelo consenso *Rachis* oferece a consistência forte, permitindo a continuidade da operação com a garantia do líder de que a maioria dos nós estão funcionando. Isso também significa que cada operação do *cluster* será futuramente aceita, ou não, pelo *cluster* inteiro. 
De acordo com o **Teorema CAP**, o *RavenDB* consegue ser **tolerante a partições**, **consistênte** e **disponível**. Mesmo contradizendo o teorema CAP, isso é possível pois ele aborda cada propriedade em uma camada diferente. A camada de *cluster* é  CP, ou seja, consistente mas pode não estar disponível. A camada de *banco de dados* é AP, ou seja, sempre disponível mas pode não ser consistente.

# Replicação de Dados
Um banco de dados pode ser replicado em vários nós dentro de um *cluster*, dependendo do Fator de Replicação. Quando um nó possui um banco de dados ele é chamado de *nó de banco de dados*. Cada um desses *nós* possuem uma cópia completa do banco de dados localmente, incluindo os documentos e índices, e assim podendo realizar qualquer operação de busca e escrita de dados. Além dessas operações locais em cada *nó*, há também operações que impactam o *cluster* inteiro, sendo no caso operações de, por exemplo, criação de documento. Um *grupo de banco de dados* pode ser formado por vários desses nós, e quando um banco de dados novo é criado, pode-se escolher em quais nós ele existe.
 Cada nó se mantém sincronizado a partir de replicação *master-master*, ou seja, todo nó possui uma cópia do banco de dados de seu grupo e a cada mudança é replicada automaticamente a outras instâncias do banco de dados. Isso significa que se tivermos um *cluster* com os nós A, B e C e criarmos um banco de dados com 3 de fator de replicação no nó A, esse banco também estará nos nós B e C.
 Na camada de *clusters* é feita a replicação entre dois grupos de bancos de dados (Denominada de Replicação Externa) de forma semelhante a replicação tradicional. 
Atualmente o RavenDB não dá suporte para *sharding*, sendo previsto para ser implementado em uma versão futura. 

Fontes:
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview)
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/distribution/distributed-database](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/distribution/distributed-databaserainsen-oerations)
[https://ravendb.net/learn/inside-ravendb-book/reader/4.0/6-ravendb-clusters](https://ravendb.net/learn/inside-ravendb-book/reader/4.0/6-ravendb-clusters)


<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA0NTE1NjE2OCwyNDc0NTQ5MDcsODgwNz
QxNzI2LDczNDk5NDEwOSwtOTQzODU1MzQ0LC0yMDUxMjIwOTQw
LDE4Mzk4ODAxMjcsLTExNDk5NzQzMjYsMTY5NDA0MjEyMCwtMj
g0OTI5OTk5LDExNDM3ODM3NjEsLTQ3MjU4MDE4MSwtMTg2NzMz
NTAyMSwtNjg1MDgwNzE2LDcxMTU3NzUxMiwyODI0NTY0MDQsMT
U0OTEwOTU2NSwxNDY4NTM1MjkyLDExODQ0MjM2MDIsNjAxMDM4
NTY2XX0=
-->
# Arquitetura de Distribuição
O RavenDB opera sobre *clusters* que providenciam redundância e disponibilidade dos dados. No contexto do RavenDB, *clusters* são um grupo de servidores chamados de *nós* e podem estar em mais de uma máquina. O RavenDB possui uma divisão de duas camadas de funcionamento: **Camada de Cluster** e **Camada de Banco de Dados**.

## Camada de Cluster

A **Camada de Cluster** é composta por nós (3 ou mais, é recomendado um número ímpar) que realizam operações de acordo com o consenso de todos os nós. Esse protocolo é um algoritmo de consenso desenvolvido para o RavenDB (chamado de Consenso *Rachis*, uma implementação derivada do protocolo [Raft](https://raft.github.io/)), e ele define um nó líder responsável por escolher o melhor nó para um determinado cliente, dando preferência a consistência das operações. 

## Camada de Banco de Dados

A **Camada de Banco de Dados** representa bancos de dados de um *cluster* que formam um grupo e trabalham em união e cooperativamente. A quantidade de membros nos grupos é definido pelo Fator de Replicação. Cada nó possui uma cópia completa da topologia do *cluster* (informações dos nós que compõe o cluster), no qual especifica qual nó possui um determinado banco de dados. Esse processo de replicação ocorre sobre uma conexão TCP entre os nós.

> Essa distinção entre duas camadas é importante para manter várias propriedades do RavenDB. A eleição de um nó líder pelo consenso *Rachis* oferece a consistência forte, permitindo a continuidade da operação com a garantia do líder de que a maioria dos nós estão funcionando. Isso também significa que cada operação do *cluster* será futuramente aceita, ou não, pelo *cluster* inteiro.

## RavenDB e o Teorema CAP

De acordo com o **Teorema CAP**, o *RavenDB* consegue ser **tolerante a partições**, **consistênte** e **disponível**. Mesmo contradizendo o teorema CAP, isso é possível pois ele aborda cada propriedade em uma camada diferente. A camada de *cluster* é  CP, ou seja, consistente mas pode não estar disponível. A camada de *banco de dados* é AP, ou seja, sempre disponível mas pode não ser consistente.

Fontes:
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview)
[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/distribution/distributed-database](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/distribution/distributed-databaserainsen-oerations)


<!--stackedit_data:
eyJoaXN0b3J5IjpbNDI0NjcyNDI0XX0=
-->
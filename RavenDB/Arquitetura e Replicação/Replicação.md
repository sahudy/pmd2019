# Replicação de Dados

Um banco de dados pode ser replicado em vários nós dentro de um *cluster*, dependendo do Fator de Replicação. Quando um nó possui um banco de dados ele é chamado de *nó de banco de dados*. Cada um desses *nós* possuem uma cópia completa do banco de dados localmente, incluindo os documentos e índices, e assim podem realizar qualquer operação de busca e escrita de dados. Além dessas operações locais em cada *nó*, há também operações que impactam o *cluster* inteiro, sendo no caso operações de, por exemplo, criação de documento. Um *grupo de banco de dados* pode ser formado por vários desses nós, e quando um banco de dados novo é criado, pode-se escolher em quais nós ele existe.

>Na camada de *clusters* é feita a replicação entre dois grupos de bancos de dados (Denominada de Replicação Externa) de forma semelhante a replicação tradicional. 

 Cada nó se mantém sincronizado a partir de replicação *master-master*. Todo nó possui uma cópia do banco de dados de seu grupo e cada mudança é replicada automaticamente à outras instâncias do banco de dados. Isso significa que se tivermos um *cluster* com os nós A, B e C e criarmos um banco de dados com 3 de fator de replicação no nó A, esse banco também estará nos nós B e C.
 
> Atualmente o RavenDB não dá suporte para *sharding*, sendo previsto para ser implementado em uma versão futura. 

[https://ravendb.net/learn/inside-ravendb-book/reader/4.0/6-ravendb-clusters](https://ravendb.net/learn/inside-ravendb-book/reader/4.0/6-ravendb-clusters)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI4ODE2Mzc4MiwtMTkyNDgyODIzOSwxMD
E2NTE0NDQ5XX0=
-->
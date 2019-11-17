
## Escalabilidade

Se tratando de escalabilidade, quando um novo nó é inserido no _Grup_ do Banco de Dados, um Mentor decidido pelo sistema de gerenciamento fica responsável por atualizá-lo, inserindo-o no contexto do _Group Database_. O novo nó terá o estado _Promotable_  até receber e indexar todos os documentos vindos do Mentor.

Temos uma replicação _master-master_ onde os dados são enviados de maneira assíncrona toda vez que um novo nó entrar no _cluster_ ou algum dado mudar no _Master_.

Assim fica possível escalar sistemas a nível de _clusters_ com simplicidade.

Fonte: https://ravendb.net/docs/article-page/4.2/Csharp/server/clustering/distribution/distributed-database

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwOTU2NjY1MSwtNjY2ODYyMzEsMTQ3OD
I0MTAyOSwtMjEzMzczODg0XX0=
-->
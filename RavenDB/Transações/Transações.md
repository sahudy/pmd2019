## Transações
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
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA1ODAwNTY5MF19
-->
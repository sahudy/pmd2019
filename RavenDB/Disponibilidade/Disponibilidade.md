
# Disponibilidade

Algumas ações, como criar banco de dados ou índices requerem um consenso do _cluster_, para isto o RavenDB utiliza o  _Rachis_, uma implementação proprietária de algoritmo de consenso para sistemas distribuídos. Requerendo um quórum de N/2 + 1 processos para que seja possível a eleição de líder.

No RavenDB temos o banco de dados replicado entre vários nós, um grupo de nós que mantém o mesmo banco de dados replicado é chamado de *Group Database* e, o fator de replicação pode ser definido, lembrando que a versão gratuita do RavenDB suporta fator de replicação de até 3 nós. Documentos são mantidos em sincronia em nós pertencentes à um _Database Group_ com uma replicação _master to master_

Tendo um _cluster_ com vários nós é possível realizar requisições espalhadas em vários nós, com a finalidade de maior desempenho, porém a replicação nos permite mesmo com a maioria do _cluster_ inoperante, desde que tenhamos um único nó vivo, temos acesso à leituras e escritas no banco.

A seguir teremos a demonstração de uma topologia com possíveis falhas junto com o passo a passo utilizando _Docker_.

Para exemplificar iremos criar uma topologia de 3 nós sendo o nó **A** definido como *Master* e os nós **B** e **C** como *Members*.

- Para subirmos as máquinas:
	> $ sudo docker run --name <nome_do_nó> -d -p 8080:8081 -p 38888:38888 ravendb/ravendb
	- Onde o primeiro parâmetro de porta (8080) refere-se a porta da máquina física e o segundo (8081) à porta do serviço _Docker_. Para criarmos várias máquinas basta variar o seu nome e porta de serviço.

![enter image description here](https://lh3.googleusercontent.com/MgNJC99AoQCBJOjNjTcNeUJhisnHGXXEI4_zZZhRPM97eh55lO5dCfuvAhYbtD-FTMNzzPb51as)

Existem quatro tipos de nó:

 ![Tipos de nó](https://lh3.googleusercontent.com/uRTulWUIS99gzAZh4LWxYenhbIln2SIYEmf3LzglHeBlPU_Ab_MTfRAIN-AR5fJrjp2OjfBE5jQ)
- *Leader*: Nó líder do *Group Database*, é responsável por manter a saúde do _cluster_ e assegurar a consistência das decisões.
- *Member*: Nó membro do *Group Database*, pode se tornar um Líder em caso de eleição.
- *Watcher*: Nó membro com menos privilégios; não pode ser eleito como Líder em caso de votação, não participando do Quórum.
- *Promotable*: Estado de nó aplicável apenas em caso de eleição; um nó elegível deve ser membro e estar em pleno funcionamento.

Ao derrubarmos o nó **A** teremos uma nova eleição que será realizada pelo quórum restante de Membros:

- Para vermos quais máquinas estão funcionando:
	>  $ sudo docker container ls
- Para derrubarmos um nó, removendo a máquina:
	>  $ sudo docker stop <nome_do_nó>

![enter image description here](https://lh3.googleusercontent.com/_Y01ZYw1RYHJqHdWXL8QSF6RBqicHRjaEIVhwjMkcWDwM-03O3RZouAY0HJ2PQy69bfoSLcBtZU)

Em um outro cenário onde **C** foi rebaixado, ficando como um _Watcher_. Ao derrubarmos o atual _Leader_ **B** o quórum de _Members_ não é suficiente para eleger outro nó, desta forma a eleição fica esperando um quórum suficiente (N/2 + 1):

![enter image description here](https://lh3.googleusercontent.com/3nzanwWTxJskAwdI-GX9YnNXpGNwEmVgcajBcyU7a8xvMuFGtQf9vlCl3ioocI9tFTKkhVjdaN4)

- Para voltarmos um nó ao funcionamento podemos excluir o nó que foi parado:
	> $ sudo docker rm <nome_do_nó>
	- Em seguida subi-lo novamente.
	
Fonte:[https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview](https://ravendb.net/docs/article-page/4.2/csharp/server/clustering/overview)

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc3NDM5MDE5NCwtMTE0ODM1MTIyOSwxMz
g3NzczMzQ5LDIxNzA2NDY3NSwtMTc0MjcyMzQyN119
-->
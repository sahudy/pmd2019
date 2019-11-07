## Segurança
No modo de desenvolvimento, o RavenDB roda no modo inseguro, porém em estágio de desenvolvimento é interessante a utilização do modo seguro, onde, por padrão somente o `localhost` está autenticado, desta forma, outros usuários terão que ser previamente autenticados para poderem acessar remotamente. O RavenDB  usa certificados de cliente `X509` para autenticação.

RavenDB oferece criptografia completa do banco e dados usando a biblioteca _libsodium_.

Fonte: https://ravendb.net/docs/article-page/4.2/csharp/server/security/overview
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNTYwNzI0NjYsLTE0Mzc5ODExNzEsND
g4MjM4MzA4XX0=
-->
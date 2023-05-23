# Sharding

Sharding é um conceito para bancos não-relacionais visando a escalar horizontalmente. Isso porque, em bancos relacionais, o escalamento horizontal multiplica a instância. Deste modo, o cliente sempre consegue acessar as informações que são feitas via SQL query. Porém, no banco não-relacional, essas buscas são feitas usando chave-valor. Desta forma, o cliente precisaria acessar a instância certa do banco para conseguir buscar por aquela chave específica.

Uma forma comum de fazer sharding é elegendo uma chave comum, como o `id` e deixar cada instância do sharding responsável por uma faixa específica. Então, por exemplo, a instância 1 (I1) seria responsável pelos `ids` na faixa `0-10000`, I2 pela faixa `10001-20000`, etc. Essa implementação é utilizada, por exemplo, pelo MongoDB.

![MongoDB sharding schema](https://www.mongodb.com/docs/manual/images/sharded-cluster-production-architecture.bakedsvg.svg "MongoDB sharding schema")

Essa forma de implementação de sharding necessita de cada shard ter um cluster de redundância, com [bancos de espera](stand-by-types.md) para evitar pontos únicos de falha. A fim de evitar isso, Cassandra distribui a redundância entre nós do mesmo cluster. Ou seja, os dados de cada nó estão distribuídos entre os outros nós para garantir que o dado consiga ser acessado mesmo na falha de algum nó. Para isso, Cassandra utiliza (necessariamente) o sistema de particionamento por hash, tirando um pouco da flexibilidade dos bancos não-relacionais.

![Cassandra sharding schema](https://webimages.mongodb.com/_com_assets/cms/l0k25cjrgl94wfmmb-image5.png?auto=format%252Ccompress "Cassandra sharding schema")

Apesar de ser uma boa solução para escalonamento horizontal, sharding conta com alguns empecilhos. O principal é que, ao aumentar o número de instâncias, também é necessário que se redistribua as faixas de cada instância. Esse conceito é chamado de Resharding.

Outro problema comum é o chamado `hotspot` ou `problema da celebridade`. Imagine que existe uma linha que é "muito famosa". Por exemplo, imagine que, a cada "gostei" de uma publicação em rede social de um famoso, acesse o banco e incremente o campo de "quantidades de gosteis". Desse modo, as publicações de figuras públicas seriam constantemente acessadas, causando um congestionamento para uma instância específica, enquanto o resto da aplicação não está operando em nível máximo.

Apesar de bancos não-relacionais implementarem soluções de query SQL, é importante ressaltar que, como as informações estão guardadas em chave-valor e dispersas entre os shards, operações mais complexas, como `transações` e `joins`, podem ser mais difíceis ou menos eficientes em ambientes de sharding distribuídos. Transações, por exemplo, podem precisar ser executadas em apenas um shard ou serem fragmentadas em vários shards, o que pode aumentar a complexidade da aplicação.

Por fim, é importante lembrar que sharding não é a única técnica para melhorar o acesso ao seu banco de dados. Também existem técnicas de particionamento vertical, que dividem a tabela em colunas, ao invés de linhas, com o objetivo de reduzir o volume de dados acessados em uma determinada consulta, e a indexação adequada dos dados, que permite um acesso mais rápido aos dados em consultas específicas. Cada técnica tem suas vantagens e desvantagens, e a escolha depende do tipo de aplicação e das necessidades de escalabilidade do banco de dados em questão.

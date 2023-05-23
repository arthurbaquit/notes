# Como Escolher um Banco de Dados

Escolher o banco de dados correto para a sua aplicação é um processo fundamental no desenvolvimento dela. Alguns fatores essenciais devem ser considerados ao decidir qual banco de dados vai ser usado. Estes são:

- Estrutura do dado
- Escalabilidade
- Teorema ACID
- Teorema CAP

## Estrutura do Dado

O tipo de dado e a finalidade para o qual ele será usado são de extrema importância ao escolher um banco de dados, uma vez que ele vai ser definidor se será necessário um banco relacional ou não-relacional.

### Banco Relacional vs Banco Não-Relacional

#### Banco Relacional

Bancos relacionais são bancos que os dados compartilham relações entre si. Ou seja, somos capazes de criar dados **estruturados** com restrições que se possuem conexão entre si, com, por exemplo, chaves estrangeiras. Desta forma, o dado que será armazenado nesse tipo de banco, são dados estruturados sem muita maleabilidade.

Por mais que soluções de bancos relacionais aceitem tipos de dados menos estruturados, como JSON, trabalhar com esses dados é computacionalmente custoso, visto que o banco não é otimizado para este tipo de dado.

Pensando nisso, fazer `joins` neste tipo de dado é extremamente simples, uma vez que os dados estão estruturados e comunicam entre si, com, por exemplo, chaves primárias e estrangeiras. Exemplos de banco de dados relacionais são MySQL e Postgres.

Além disso, bancos relacionais compartilham do sistema ACID (atomicidade, consistência, isolamento e durabilidade).

#### Teorema ACID

- Atomicidade garante que a transação no banco de dados é indivisível, ou seja, ou a transação ocorreu ou não ocorreu. Isto garante que, caso a transação retorne erro no final da operação, todas as mudanças do começo e meio são restauradas ao estado original.

- Consistência, no escopo do ACID, garante que todos os dados seguem as restrições impostas (se uma coluna possue restrição de valor positivo, você nunca conseguirá salvar um valor negativo).

- Isolamento garante que aquela transação ocorre somente no escopo dela, ou seja, cada transação age como somente ela estivesse ocorrendo naquele momento.

- Durabilidade garante que, uma vez a transação seja bem sucedida, esse dado será persistido.

#### Banco Não-Relacional

Bancos não-relacionais, por outro lado, não compartilham de estruturas e relações entre os dados. Dessa forma, você possui mais flexibilidade em qual dado está sendo salvo, além de possuir otimização de busca em dados não-estruturados como hashmap e JSON.

Por fim, diferentemente de bancos relacionais que podem não performar bem quando possui milhões de registros, existem bancos não relacionais especializados para lidar com quantidade massiva de dados, uma vez que usam a abordagem de banco de dados em colunas.

Desta forma, caso o banco seja usado para armazenar uma grande quantidade de dados, mas que não necessariamente irá ser acessado com frequência, bancos como Cassandra, que implementa o banco de dados em colunas, são escolhas mais assertivas. Porém, caso você possua dados não estruturados, mas que são muito variáveis e é necessário fazer diversas buscas, bancos não-relacionais como MongoDB e CouchBase, que implementam o banco em forma de banco de dados em documento são melhores opções.

### Teorema CAP

Um bom hacksheet para a escolha do banco de dados seria o teorema CAP (Consistency, Availability, Partition Tolerance). Nesse teorema é dito que um banco de dados pode ter dois essas três qualidades, mas não todas. Atualmente, os bancos conseguem ser otimizados de forma que diminua drasticamente seus contras, porém eles não conseguem extinguí-los.

Um ponto de atenção é que, diferente do `ACID`, para o CAP, consistência significa que todos os clientes tem acesso ao mesmo dado no mesmo momento, ou seja, não existe tempo de replicação desse dado.

Desta forma, o seguinte hacksheet pode ser criado

![Hacksheet to choose database](https://3cs1e07ms.files.wordpress.com/2016/02/cap.jpg "Hacksheet to choose database")

# Tipos de Bancos de Dados de Espera

Um banco de dados de espera é uma cópia redundante de um banco de dados principal, usada para garantir que os dados estejam disponíveis em caso de falha do banco principal.

## Failover

Failover ou Switchover são sistemas que monitoram constatemente os bancos de dados e detectam quando eles dão erros ou ficam indisponíveis. Dependendo da sua solução de banco de espera, esses sistemas também ficam responsáveis por trocar a conexão entre o banco principal, que falhou, e o banco em espera. Algumas opções de sistemas são

- Oracle Data Guard
- Microsoft SQL Server Always On Availability Groups
- Amazon RDS Multi-AZ
- PostgreSQL Replication

Note que no caso de bancos não-relacionais, a maioria já possui um sistema de replicação nativo, como o caso do

- Apache Cassandra
- Amazon DynamoDB

## Frio

O banco de espera frio é o banco que recebe um backup O banco de espera frio é o tipo mais simples de banco de dados de espera. Ele é atualizado periodicamente com um backup do banco de dados principal. Embora seja fácil de configurar e gerenciar, o banco de espera frio não é tão confiável quanto outros tipos, pois pode haver perda de dados entre o momento do último backup e o momento em que o banco precisou ser utilizado como banco principal. Além disso, não existe um mediador para essa troca. Normalmente, é necessário restaurar manualmente o banco principal com os dados do banco frio.

## Morno

O banco de espera morno é uma opção intermediária entre o banco frio e o banco quente. Ele está constantemente sincronizado com o banco principal, atualizando suas informações regularmente. Embora a sincronização frequente aumente a confiabilidade do banco de dados de espera, ainda pode haver uma pequena inconsistência entre o banco principal e o banco de espera, caso o banco principal falhe antes de ser sincronizado. No entanto, o sistema faz a troca automática quando o banco principal falha, o que torna essa opção mais confiável do que o banco frio.

## Quente

O banco de espera quente é o tipo mais confiável e eficiente de banco de dados de espera. Ele é uma cópia fidedigna do banco de dados principal e é atualizado em tempo real à medida que o banco principal é atualizado. Cada vez que o servidor escreve no banco principal, ele também escreve diretamente no banco de dados de espera quente. Como resultado, o banco de espera quente é quase uma forma de escala horizontal do banco principal e pode ser usado para fins de leitura e gravação sem afetar o desempenho do banco principal.

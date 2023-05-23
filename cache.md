# Cache

Cache é o nome dado para a solução de acesso rápido de memória. Assim como para o computador, acessar a memória RAM é muito mais rápido e menos dispendioso do que acessar o disco rígido magnético. Para o sistema, é mais rápido acessar a memória cache do que pedir a informação para o banco de dados principal. As duas tecnologias de código aberto mais famosas no mercado são o memcached e o Redis. Algumas outras opções seriam Apache Ignite, Hazelcast e Couchbase.

## Quando usar

A solução de cache é usada pensando em escalar sua aplicação. Imagine um sistema com milhares de requisições por segundo. Sem uma solução de cache, toda requisição precisaria necessariamente fazer um requerimento ao banco de dados, estressando-o e o colocando em risco de falhar. Claro que sempre se pode aumentar a capacidade de CPU do banco, mas, além de ser custoso, existe a limitação física de hardware associada. Portanto, para evitar o estresse no banco, cria-se uma camada de acesso em memória para retornar os dados sem precisar acessar o banco. Essa camada de leitura é chamada de cache. É importante enfatizar que o cache não funciona para escritas, uma vez que o cache não é um banco persistente. Desta forma, o Caching é uma solução de escalabilidade se o seu sistema realiza muitos pedidos de leitura.

## Cuidados

Apesar de parecer uma solução mágica, é preciso ter cuidado ao subir uma solução de cache. Assim como bancos de disco rígido, bancos de memória também possuem espaço limitado. Portanto, é preciso criar e implementar soluções para coordenar e escolher quais dados serão cacheados. Também é necessário atentar que o cache não performa bem com dados complexos. Além disso, o cache vira um ponto de atenção dos desenvolvedores, uma vez que precisa de manutenção, como monitoramento e limpeza.

### Invalidação do Cache

Normalmente, o cache expira após um determinado tempo. Se esse tempo for curto demais, o cache perde sua capacidade de proteger o banco, pois precisa ficar sendo atualizado com frequência, aumentando o "cache miss". Se for longo, os dados da sua aplicação ficam desatualizados. Portanto, é necessário pensar em um trade-off entre atualizar o cache para manter a consistência e a quantidade de tempo que o banco primário fica exposto.

### Política de Expiração

#### LRU

LRU ou Least Recently Used (Menos Recentemente Usado) monitora quais dados foram usados mais recentemente. Desta forma, se for necessário substituir algum dado, o que não tem acesso há mais tempo será substituído. A implementação normalmente é uma lista de encadeamento duplo, com um ponteiro de `head` para o último dado acessado e o de `tail` para o primeiro dado acessado do cache.

#### LFU

LFU ou Least Frequently Used (Menos Frequentemente Usado) monitora as frequências de uso dos dados. Desta forma, se houver necessidade de substituir um dado, o que tiver tido menos frequência de acessos em dado período é o dado que será trocado.

#### FIFO

FIFO ou First In First Out (Primeiro a Entrar Primeiro a Sair) é uma fila simples. Então o primeiro dado colocado no cache será o dado que será substituído se houver necessidade.

### Hotspot ou Problema da celebridade

Como em qualquer banco de dados, por mais rápido que seja o acesso do servidor ao cache, também é importante que o cache esteja sendo monitorado para lidar com o problema da celebridade. Ou seja, não basta apenas ativar um cache, mas também é preciso monitorar o tráfego nele.

### Cold-start

O cache frio é o tipo de cache que sobe para produção vazio e vai sendo populado conforme as requests vão chegando no sistema. Isso não é um problema em si, mas se torna um problema se o seu banco não consegue lidar com o tráfego e o cache é **necessário** para a sua aplicação não cair. Nesses casos, é preciso usar um cache morno. O cache morno, ao contrário do cache frio, é aquecido antes de subir para produção. Algumas estratégias de aquecimento são:

- Pré-busca (Prefetching) metadados.
- Pré-busca ambos dados e metadados.
- Copiar as informações de outros caches já aquecidos.
- Criar dummy requests para manter o cache aquecido em períodos de baixo tráfego.

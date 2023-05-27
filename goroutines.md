# Goroutines

Em resumo, as Goroutines são threads leves que o Golang produz para gerenciar a execução do código em tempo de execução. No entanto, é um pouco mais complicado que isso.

## Definição

Embora sejam definidas como threads, as goroutines apenas simulam o comportamento delas. Antes de nos aprofundarmos nas Goroutines, vamos revisar rapidamente o conceito de Thread. As threads são a unidade básica de execução. Múltiplas threads permitem com que seu código seja executado de forma concorrente ou paralela. Com um pouco mais de abstração, pode-se dizer que threads são contextos de execução que permitem que a CPU gerencie e execute instruções. Em um nível mais baixo, podemos dizer que as threads são os registros da CPU.

Tradicionalmente, o gerenciamento dessas threads é responsabilidade do Sistema Operacional (SO). No entanto, isso acarreta um custo operacional relativamente alto, pois exigiria a alocação de recursos até o nível mais baixo. É aí que entra a beleza das Goroutines.

As Goroutines são meras simulações de threads. Elas usam o mesmo espaço na memória da thread em que o SO abriu para executar o código, ou seja, a primeira goroutine, geralmente a função main. A coordenação dessas rotinas é realizada pelo próprio Go, usando o Go Scheduler, que se comunica com o SO para realizar chamadas de baixo nível, como operações de I/O e outras chamadas do sistema. Dessa forma, o gerenciamento da concorrência é realizado pelo Go em tempo de execução, e é por isso que as goroutines são consideradas threads leves.

Agora que entendemos o que são as goroutines, vamos explorar alguns casos de uso relevantes.

## Casos de Uso

Como discutido, as Goroutines são o conceito básico da concorrência em Go. Antes de prosseguirmos com a discussão, gostaria de revisar o seguinte código:

```go
now := time.Now()
for i := 0; i < 100; i++ {
    for j := 0; j < 100000; j++ {
    }
    for k := 0; k < 100000; k++ {
    }
    for l := 0; l < 100000; l++ {
    }
}
fmt.Println("Time taken: ", time.Since(now))
```

Ao executar esse código, que simula a execução sequencial de três funções (cada uma representada por um loop for) repetidas 100 vezes, você notará que o tempo de execução será de aproximadamente 15ms.

Por outro lado, se executarmos o seguinte código:

```go
var wg sync.WaitGroup
now = time.Now()
for i := 0; i < 100; i++ {
    wg.Add(3)
    go func() {
        for j := 0; j < 100000; j++ {
        }
        wg.Done()
    }()
    go func() {
        for j := 0; j < 100000; j++ {
        }
        wg.Done()
    }()
    go func() {
        for j := 0; j < 100000; j++ {
        }
        wg.Done()
    }()
    wg.Wait()
}
fmt.Println("Time taken: ", time.Since(now))
```

você notará que o resultado será aproximadamente 6ms. Ao analisar os dois códigos, podemos observar algumas peculiaridades. Cada loop "for" está envolvido por uma função anônima que é chamada usando a palavra-chave especial da linguagem, `go`. E o que essa palavra-chave `go` faz? Ela instrui nosso programa a executar essa função em uma nova Goroutine. Dessa forma, estamos criando uma nova Goroutine para cada loop "for" e executando-os de forma concorrente. Como meu computador possui mais de um núcleo, o código está executando em as funções em paralelo. É por isso que o tempo de execução cai para apenas 6ms. Se executarmos o primeiro código com apenas um loop "for", o tempo será em torno de 5ms. Ou seja, estamos executando três funções com um tempo de execução praticamente igual ao de uma função.

A segunda peculiaridade é a presença da variável `wg`. Ela é responsável por sincronizar nosso código. Com a concorrência, pode acontecer de solicitarmos que nosso código execute uma Goroutine, mas a função `main` termine sua execução antes que a Goroutine seja concluída. Isso resultaria no encerramento abrupto do programa. Para evitar isso, chamamos `wg.Add(3)` para informar que estamos executando 3 Goroutines. No final de cada Goroutine, chamamos `wg.Done()`. Isso informa ao nosso WaitGroup que a função foi executada e a Goroutine foi concluída, reduzindo em um o contador de Goroutines. O `wg.Wait()` força o programa a aguardar até que a contagem total de Goroutines (que agora é igual a três) chegue a zero, ou seja, quando recebermos o `wg.Done()` de cada Goroutine.

Em resumo, o uso da goroutine dá a capacidade do nosso código otimizar sua execução, seja executando as funções de forma paralela, quando possível, seja executando operações de CPU enquanto aguarda a resposta de operações I/O.

### Diferença entre Concorrência e Paralelismo

Concorrência é a capacidade de lidar com várias tarefas ao mesmo tempo, mas não necessariamente realizá-las simultaneamente. Para ilustrar, vamos imaginar que você está cuidando da sua casa e precisa lavar roupas na máquina de lavar e lavar a louça. Um código sequencial (como o primeiro código mencionado neste artigo) executaria cada tarefa em ordem. Ou seja, colocaria as roupas na máquina e esperaria todo o ciclo de lavagem terminar antes de começar a lavar a louça. Já um código concorrente colocaria as roupas na máquina, a ligaria e começaria a lavar a louça. Depois de lavar parte da louça, voltaria para a máquina de lavar, verificaria seu estado e voltaria para a louça. Assim, um código concorrente executa múltiplas tarefas ao longo do tempo, embora elas não sejam realizadas simultaneamente. No nosso exemplo, a sensação é de simultaneidade porque estamos aguardando a máquina de lavar terminar a lavagem. Um exemplo prático de programação seria lidar com operações de entrada e saída (I/O), em que continuamos a execução enquanto aguardamos, por exemplo, uma informação ser salva no banco de dados.

Por outro lado, o paralelismo é a capacidade de executar tarefas verdadeiramente ao mesmo tempo. Para isso, é necessário ter mais de um processador. No exemplo mencionado anteriormente, seria como chamar seu cônjuge para ajudar com as tarefas da casa. Enquanto um lava a louça, o outro verifica a máquina de lavar. Ambas as tarefas são executadas simultaneamente. Portanto, se o seu código estiver sendo executado em um único núcleo de processamento, ele não será paralelo, mas sim concorrente.

## Cuidados

Ao utilizar várias Goroutines, seu programa pode enfrentar diversos problemas, como condições de corrida, términos inesperados e compartilhamento inadequado de memória. Por isso, é de vital importância utilizar Mutexes, WaitGroups e Channels. Quando eu tiver um artigo dedicado a cada um deles, eu os adicionarei aqui como recomendação.

## Referências

Algumas referências:

- [dev.to/what-are-goroutines-and-how-are-they-scheduled](https://dev.to/gophers/what-are-goroutines-and-how-are-they-scheduled-2nj3)
- [dev.to/concurrency](https://go.dev/tour/concurrency/1)
- [freecontent/concurrency-vs-parallelism](https://freecontent.manning.com/concurrency-vs-parallelism/#:~:text=Concurrency%20is%20about%20multiple%20tasks,resources%20like%20multi%2Dcore%20processor.)
- [dev-yakuza/goroutine](https://dev-yakuza.posstree.com/en/golang/goroutine/)
- [geekForGeeks/golang-goroutine-vs-thread](https://www.geeksforgeeks.org/golang-goroutine-vs-thread/)

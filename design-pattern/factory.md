# Fábrica Abstrata

O padrão de fábrica abstrata é um padrão que provê uma interface de objetos relacionados/dependentes sem especificar sua classe concreta.

Para exemplificar, vamos imaginar um aplicativo que permite que o usuário personalizar uma tela para ser usado de papel de parede. Nessa tela, ele tem opções de escolher um tema. Para fins de simplificação, assuma que cada tema só tivesse um "sticker" e um "fundo".

Dessa forma, poderíamos ter uma interface que iria definir a nossa fábrica abstrata como:

```go
type ThemeFactory interface {
    CreateBackground() IBackground
    CreateSticker() Sticker
}

type IBackground interface {
    CreateBackground(color string)
    GetBackground()
}

type ISticker interface {
    CreateStricker(stickerFile string)
    GetSticker()
}
```

Assim, independente de qual classe o nosso cliente vá acessar, este sabe que pode criar o objeto usando os métodos da fábrica, e qual contrato de cada objeto.

Assim, o usuário consegue interagir com diversos "temas" diferentes, somente se atentando ao contrato definido pela interface, sem se preocupar com a implementação do construtor. Desta forma, é relativamente simples trocar de tema, uma vez que basta trocar a fábrica que foi instancializada por outra implementação, visto que todas seguem o mesmo contrato.

Por mais que isso seja bom para gerar padrão, também geramos um certo engessamento no processo. Note que, caso o usuário decida escolher o fundo "moderno", a implementação concreta irá forçar que o sticker também seja o sticker "moderno", o que é conviniente tratando de uma aplicação para uma escolha de temas.

Além disso, também tornamos difícil a expansão de novos métodos. Pensando no SOLID, para criar novos métodos, deveríamos extender a interface da nossa fábrica abstrata para abarcar mais features. Porém, isso levaria a uma alteração em todas as subclasses/implementações concretas.

## Implementação

Cada fábrica deve instanciada somente uma vez, e usada para gerar quantos objetos forem necessários. Dessa forma, o método de fábrica abstrata geralmente está associada com o método Singleton.

Após a definição da interface, deve-se implementar a classe concreta seguindo a lógica para cada uma. Neste passo, normalmente existem duas estratégias. O padrão de implementação pode ser tanto usando "Métodos de Fábrica (Factory Methods)" ou "Protótipos (Prototypes)".

O primeiro é mais simples e muitas vezes usado antes de criar toda a estrutura de fábricas abstratas.　 Ele se baseia em criar uma classe para cada implementação concreta e, através dela, utilizar o polimorfismo para sobrescrever o método definido pela fábrica abstrata. Por exemplo:

```go
type modernThemeFactory struct {}
func (mwf *modernThemeFactory) CreateBackground() Background {
    return Background{color: "modern-color"}
}
func (mwf *modernThemeFactory) CreateSticker() Sticker {
    return Sticker{stickerImage: "modern-sticker.png"}
}
```

Este método, apesar de simples e eficaz, gera uma carga de trabalho maior, uma vez que precisa-se criar a implementação da fábrica para cada tipo "produto", nesse caso, tema, mesmo que os códigos sejam parecidos.

O segundo método, de protótipo, baseia-se em retornar uma cópia de um objeto a partir do seu valor base, ou, protótipo. Desta forma, o objeto não é criado do zero, e sim customizado a partir do objeto inicial. Pode ser útil quando a criação do objeto demanda muitos passos, é muito custoso, ou os produtos são muito parecidos, sendo mais fácil editar um produto já existente que criar outro do zero.

### Resumo das Entidades

- Creator(Criador abstrato) — declara o factory method (método de fabricação) que retorna o objeto da classe Product (produto). Este elemento também pode definir uma implementação básica que retorna um objeto de uma classe ConcreteProduct (produto concreto) básica;
- ConcreteCreator(Criador concreto) — sobrescreve o factory method e retorna um objeto da classe ConcreteProduct;
- Product(Produto abstrato) — define uma interface para os objectos criados pelo factory method;
- ConcreteProduct(Produto concreto) — uma implementação para a interface Product.

## Código Exemplo usando Factory Method

```go
package main

import "fmt"

// Abstract Factory
type BrandFactory interface {
    CreateShoe() IShoe
    CreateBag() IBag
}

// Abstract Product A
type IShoe interface {
    SetLogo(logo string)
    SetColor(color string)
    GetLogo() string
    GetColor() string
}

// Abstract Product B
type IBag interface {
    SetLogo(logo string)
    SetModel(model string)
    GetLogo() string
    GetModel() string
}

// Concrete Product A
type Bag struct {
    logo  string
    model string
}

func (b *Bag) SetLogo(logo string) {
    b.logo = logo
}

func (b *Bag) SetModel(model string) {
    b.model = model
}

func (b *Bag) GetLogo() string {
    return b.logo
}

func (b *Bag) GetModel() string {
    return b.model
}

// Concrete Product B
type Shoe struct {
    logo  string
    color string
}

func (s *Shoe) SetLogo(logo string) {
    s.logo = logo
}

func (s *Shoe) SetColor(color string) {
    s.color = color
}

func (s *Shoe) GetLogo() string {
    return s.logo
}

func (s *Shoe) GetColor() string {
    return s.color
}

// Concrete Factory A
type LuxuryBrandFactory struct{}

func (l *LuxuryBrandFactory) CreateShoe() IShoe {
    return &Shoe{
        logo:  "Luxury",
        color: "white",
    }
}

func (l *LuxuryBrandFactory) CreateBag() IBag {
    return &Bag{
        logo:  "Luxury",
        model: "Cross Body",
    }
}

// Concrete Factory B
type SportBrandFactory struct{}

func (s *SportBrandFactory) CreateShoe() IShoe {
    return &Shoe{
        logo:  "Sport",
        color: "red",
    }
}

func (s *SportBrandFactory) CreateBag() IBag {
    return &Bag{
        logo:  "Sport",
        model: "Backpack",
    }
}

func GetBrandFactory(brand string) (BrandFactory, error) {
    switch brand {
    case "luxury":
        return &LuxuryBrandFactory{}, nil
    case "sport":
        return &SportBrandFactory{}, nil
    default:
        return nil, fmt.Errorf("Brand %s not found", brand)
    }
}

func main() {
    factory, _ := GetBrandFactory("sport")
    shoe := factory.CreateShoe()
    bag := factory.CreateBag()
    printDetails(shoe, bag)

    factory, _ = GetBrandFactory("luxury")
    shoe = factory.CreateShoe()
    bag = factory.CreateBag()
    printDetails(shoe, bag)
}

func printDetails(s IShoe, b IBag) {
    fmt.Printf("Logo: %s", s.GetLogo())
    fmt.Println()
    fmt.Printf("Color: %s", s.GetColor())
    fmt.Println()
    fmt.Printf("Logo: %s", b.GetLogo())
    fmt.Println()
    fmt.Printf("Model: %s", b.GetModel())
    fmt.Println()
}

```

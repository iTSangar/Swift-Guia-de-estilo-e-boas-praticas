# Guia de Estilo Swift.

Nós criamos este guia de estilo para manter o código em nossos projetos agradáveis e consistentes.

Nossos objetivos gerais são concisão, clareza e simplicidade.

Esta é uma tentativa de incentivar padrões que alcancem os seguintes objetivos:

 1. Diminuição da probabilidade de erro do programador
 1. Aumento da clareza de intenções
 1. Reduzir a verbosidade
 1. Menos debates sobre estética
 

## Tabela de Conteúdos

* [Nomenclatura](#naming)
  * [Prefixos de Classe](#class-prefixes)
* [Espaçamento](#spacing)
* [Comentários](#comments)
* [Classes e Structs](#classes-and-structures)
  * [Uso do Self](#use-of-self)
  * [Conformidade de Protocolos](#protocol-conformance)
  * [Computed Properties](#computed-properties)
* [Declarações de função](#function-declarations)
* [Expressões Closure](#closure-expressions)
* [Tipos](#types)
  * [Constantes](#constants)
  * [Opcionais](#optionals)
  * [Inicializadores Struct](#struct-initializers)
  * [Inferência de Tipos](#type-inference)
  * [Açúcar Sintático](#syntactic-sugar)
* [Fluxo de Controle](#control-flow)
* [Ponto e Vírgula](#semicolons)
* [Idioma](#language)
* [Considerações](#credits)


## Nomenclatura

Use nomes descritivos com [CamelCase](https://pt.wikipedia.org/wiki/CamelCase) para as classes, métodos, variáveis, etc. Nomes de classe devem começar com letra Maiúscula(_UpperCamelCase_), enquanto nomes de métodos e variáveis devem começar com uma letra minúscula(_lowerCamelCase_).

**Prefira:**

```swift
private let maximumWidgetCount = 100

class WidgetContainer {
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

**Evite:**

```swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

Para as funções e métodos de inicialização, prefira parâmetros nomeados para todos os argumentos, a menos que o contexto seja muito claro. Incluia nomes de parâmetros externos se isso fizer a chamada da função mais legível.

```swift
func dateFromString(dateString: String) -> NSDate
func convertPointAt(column column: Int, row: Int) -> CGPoint
func timedAction(afterDelay delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// como seria em uso:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(afterDelay: 1.0, perform: someOtherAction)
```

Para métodos de classe, siga a convenção padrão da Apple de se referir ao primeiro parâmetro no nome do método:

```swift
class Counter {
  func combineWith(otherCounter: Counter, options: Dictionary?) { ... }
  func incrementBy(amount: Int) { ... }
}
```

A Apple presta muita atenção para manter uma nomeação consistente ao longo de suas APIs. Ao desenvolver com Cocoa, é muito mais fácil para novas pessoas participarem do projeto se você seguir as [convenções de nomenclatura da Apple][cocoa-coding-guidelines].

[cocoa-coding-guidelines]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

Aqui estão alguns tópicos básicos que você pode começar a usar imediatamente:

Um método começando com um _verbo_ indica que ele executa alguma ação, mas não vai retornar nada:
`- (void)loadView;`
`- (void)startAnimating;`

Qualquer método começando com um _substantivo_, retorna um objeto e deve fazê-lo sem efeitos colaterais:
`- (UINavigationItem *)navigationItem;`
`+ (UILabel *)labelWithText:(NSString *)text;`

Vale a pena saber a diferença e aplicar os dois separadamente. Isso irá manter seus efeitos colaterais em seções menores do código, o que torna mais compreensível e facilita a depuração.


### Enums

Use UpperCamelCase para valores de enums:

```swift
enum Shape {
  case Rectangle
  case Square
  case Triangle
  case Circle
}
```

### Prefixos de Classe

Você **não deve** adicionar prefixos as suas classes Swift.
Classes em Swift são automaticamente _namespaced_ pelo módulo que os contém e você não deve adicionar um prefixo de classe.

## Espaçamento

* Indentar com 2 espaços em vez de tabulações para economizar espaço e ajudar a prevenir a quebra de linha. Certifique-se de definir essa preferência no Xcode como mostrado abaixo:

  ![Xcode indent settings](https://raw.githubusercontent.com/raywenderlich/swift-style-guide/swift2/screens/indentation.png)

* Chaves de método e outras chaves (`if `/` else `/` switch` / `while` etc.) devem sempre ser abertos na mesma linha que a declaração, mas fechados em uma nova linha.
* Dica: Você pode re-indentar selecionando um trecho de código (ou ⌘A para selecionar tudo) e, em seguida, Control-I (ou Editor \ Estrutura \ Re-Indentar no menu). Alguns códigos de modelo Xcode terá 4 espaços codificado, por isso esta é uma boa maneira de corrigir isso.

**Prefira:**
```swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```

**Evite:**
```swift
if user.isHappy
{
    // Do something
}
else {
    // Do something else
}
```

* Deve haver exatamente uma linha em branco entre os métodos para ajudar na clareza visual e organização. Use espaço em branco dentro de métodos para separar alguma funcionalidade, mas ter muitas seções em um método muitas vezes significa que você deve refatorar em vários métodos.
* Terminar arquivos com uma nova linha.
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but having too many sections in a method often means you should refactor into several methods.

## Comentários

Quando eles são necessários, use os comentário para explicar **por que** um determinado trecho de código faz alguma coisa. Os comentários devem ser mantidos atualizados ou excluídos.

Evite comentários de bloco na mesma linha com o código, o código deve ser tão auto-documentado o possível. *Exceção: Isto não se aplica a essas observações utilizadas para gerar a documentação.*

### Estrutura

Comentários `MARK:` são uma ótima maneira de agrupar seus métodos, especialmente em controladores de views. Aqui está um exemplo para uma estrutura comum que funciona com quase qualquer controlador de view:

```swift

import SomeExternalFramework

class FooViewController : UIViewController {

    let foo: Foo

    private let fooStringConstant = "FooConstant"
    private let floatConstant = 1234.5

    // MARK: Lifecycle

    // Custom initializers go here

    // MARK: View Lifecycle

    override func viewDidLoad() {
        super.viewDidLoad()
        // …
    }

    // MARK: Layout

    private func makeViewConstraints() {
        // …
    }

    // MARK: User Interaction

    func foobarButtonTapped() {
        // …
    }

    // MARK: Additional Helpers

    private func displayNameForFoo(foo: Foo) {
        // …
    }

}
```

## Classes e Structs

### Qual deles usar?

A menos que você necessite de funcionalidades que só podem ser fornecidas por uma classe, implemente um struct.

Os recursos adicionais de classes são:

- Herança: Permite uma classe para herdar as características de outras
- Type casting: Permite-lhe verificar e interpretar o tipo de uma instância de classe em tempo de execução
- Deinitializers: Ativar uma instância de uma classe para liberar quaisquer recursos que atribuiu
- Contagem de referência: Permite que mais de uma referência seja feita a uma instância de classe
- Compatibilidade: Classes que estão disponíveis em Objective-C

Note-se que a herança (por si só) geralmente não é uma boa razão para usar as classes, porque polimorfismo pode ser fornecido por protocolos, e reutilização de implementação pode ser fornecida através de composição.

Por exemplo, esta hierarquia de classes:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * Float(numberOfWheels)
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}
```

poderia ser refatorado com estas definições:

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * Float(vehicle.numberOfWheels)
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

### Uso do Self

Para concisão, evitar o uso de `self` uma vez que Swift não o exige para acessar as propriedades de um objeto ou chamar seus métodos.

Use `self` quando necessário diferenciar entre nomes de propriedades e argumentos em inicializadores, e ao fazer referência a propriedades dentro de closure_(similar aos blocks em Objective-C)_ como exigido pelo compilador:

```swift
class BoardLocation {
  let row: Int, column: Int

  init(row: Int, column: Int) {
    self.row = row
    self.column = column
    
    let closure = {
      println(self.row)
    }
  }
}
```

Ou quando utilizar um vínculo opcional com propriedades opcionais:

**Prefira:**

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

**Evite:**

```swift
if let maybeThisCouldBeTextContainer = textContainer {
  // do many things with maybeThisCouldBeTextContainer
}
```


### Conformidade de Protocolos

Ao adicionar conformidade de protocolo a uma classe, prefira adicionar uma extensão de classe separada para os métodos de protocolo. Isso mantém os métodos relacionados agrupados em conjunto com o protocolo e pode simplificar instruções para adicionar um protocolo para uma classe com os seus métodos associados.

Além disso, não se esqueça do comentário `// MARK: -` para manter as coisas bem organizadas!

**Prefira:**
```swift
class MyViewcontroller: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewcontroller: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewcontroller: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**Evite:**
```swift
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

### Computed Properties

Para concisão, se uma computed property é somente leitura, omita a cláusula get. A cláusula get é necessário somente quando uma cláusula set é fornecida.

**Prefira:**
```swift
var diameter: Double {
  return radius * 2
}
```

**Evite:**
```swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```

## Declarações de Função

Mantenha declarações de função curtas e em uma linha incluindo a abertura da chave:

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

Para funções com assinaturas grandes, adicione quebras de linha em locais apropriados e adicione uma identação extra nas linhas subseqüentes:

```swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
  // reticulate code goes here
}
```


## Expressões Closure

Usar nomes descritivos para os parâmetros da closure.

**Prefira:**
```swift
UIView.animateWithDuration(1.0) {
  self.myView.alpha = 0
}

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  },
  completion: { finished in
    self.myView.removeFromSuperview()
  }
)
```

**Evite:**
```swift
UIView.animateWithDuration(1.0, animations: {
  self.myView.alpha = 0
})

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  }) { f in
    self.myView.removeFromSuperview()
}
```

No caso das expressão únicas de closures, onde o contexto é claro, use retornos implícitos:

```swift
attendeeList.sort { a, b in
  a > b
}
```


## Tipos

Sempre use tipos nativos do Swift, quando disponível. Swift oferece ponte para Objective-C para que você ainda possa usar o conjunto completo de métodos conforme necessário.

**Prefira:**
```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**Evite:**
```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

### Constantes

Constantes são definidas usando a palavra-chave `let`, e variáveis com a palavra-chave` var`. Sempre use `let` ao invés de` var` se o valor da variável não vai mudar.

**Dica:** Uma boa técnica é definir tudo usando `let` e só alterar para` var` se o compilador reclamar!

#### Recursos de Código

Em Swift, você pode usar estruturas definidas em um arquivo chamado `Constants.swift`, podendo assim armazenar e acessar as constantes de todo o app de forma limpa:

```swift

struct Config {
    static let baseURL: NSURL(string: "http://www.example.org/")!
    static let splineReticulatorName = "foobar"
}

struct Color {
    static let primaryColor = UIColor(red: 0.22, green: 0.58, blue: 0.29, alpha: 1.0)
    static let secondaryColor = UIColor.lightGrayColor()
}

```

Atualmente constantes são type-safe, têm escopo explícito (não estão disponíveis em todos os arquivos importados), não podem ser redefinidas ou eliminadas em partes posteriores do código, e estão disponíveis no debugger.


### Opcionais

Declare variáveis e tipos de retorno de função como opcional com `?` onde um valor vazio é aceitável.

Use tipos implicitamente unwrapped declaradas com `!` apenas para variáveis de instância que você sabe que serão inicializadas antes da utilização, como subviews que serão criados em `viewDidLoad`.

Ao acessar um valor opcional, utilize o encadeamento opcional se o valor é acessado apenas uma vez ou se houver muitos opcionais na sequência:

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

Use opcional vinculado quando é mais conveniente para unwrap uma vez e executar várias operações:

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

Ao nomear variáveis opcionais e propriedades, evitar nomeá-las como `optionalString` ou `maybeView` pois sua opcionalidade já está na declaração do tipo.

Para opcional vinculado, prefira o nome original quando apropriado em vez de usar nomes como `unwrappedView` ou `actualLabel`.

**Prefira:**
```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, volume = volume {
  // do something with unwrapped subview and volume
}
```

**Evite:**
```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

### Inicializadores Struct

Use os inicializadores de struct nativos do Swift em vez de construtores legados do CGGeometry.

**Prefira:**
```swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
let centerPoint = CGPoint(x: 96, y: 42)
```

**Evite:**
```swift
let bounds = CGRectMake(40, 20, 120, 80)
let centerPoint = CGPointMake(96, 42)
```
Prefira usar as constantes do escopo da struct `CGRect.infinite`, `CGRect.null`, etc. ao invés de constantes globais `CGRectInfinite`, `CGRectNull`, etc.
Para variáveis existentes, você pode usar o `.zero` mais curto.

### Inferência de Tipos

Prefira um código compacto e deixe o compilador inferir o tipo para uma constante ou variável, a menos que você precisa de um tipo específico diferente do padrão, como `CGFloat` ou `Int16`.

**Prefira:**
```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = [String]()
let maximumWidth: CGFloat = 106.5
```

**Evite:**
```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
var names: [String] = []
```

**NOTA**: Seguindo essa diretriz significa que escolher nomes descritivos é ainda mais importante do que antes.


### Açúcar Sintático

Prefira as versões de atalho de declarações de tipo do que a sintaxe genérica completa.

**Prefira:**
```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Evite:**
```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```


## Fluxo de Controle

Prefira usar o estilo `for-in` para loops `for` do que o estilo `for-condition-increment`.

**Prefira:**
```swift
for _ in 0..<3 {
  println("Hello three times")
}

for (index, person) in attendeeList.enumerate() {
  println("\(person) is at position #\(index)")
}
```

**Evite:**
```swift
for var i = 0; i < 3; i++ {
  println("Hello three times")
}

for var i = 0; i < attendeeList.count; i++ {
  let person = attendeeList[i]
  println("\(person) is at position #\(i)")
}
```


## Ponto e Vírgula

Swift não requer um ponto e vírgula após cada declaração em seu código. Eles só são necessários se você desejar combinar várias instruções em uma única linha.

Não escreva várias instruções em uma única linha separada por ponto e vírgula.

A única exceção a essa regra é na construção de um `for-conditional-increment`, o que exige ponto e vírgula. No entanto, alternativas de construções `for-in` devem ser utilizados sempre que possível.

**Prefira:**
```swift
let swift = "not a scripting language"
```

**Evite:**
```swift
let swift = "not a scripting language";
```

**NOTA**: Swift é muito diferente de JavaScript, onde omitindo ponto e vírgula é [geralmente considerado inseguro](http://stackoverflow.com/questions/444080/do-you-recommend-using-semicolons-after-every-statement-in-javascript)

## Idioma

Use a ortografia no código US English para combinar com a API da Apple.

**Prefira:**
```swift
let color = "red"
```

**Evite:**
```swift
let colour = "red"
```



## Considerações

Este documento baseia-se em outros guias de estilo:

* [raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-guide/blob/master/README.markdown)
* [Github Swift Style Guide](https://github.com/github/swift-style-guide/blob/master/README.md)
* [Hyper's Swift Style Guide](https://github.com/hyperoslo/iOS-playbook/blob/master/style-guidelines/Swift.md)

Boas ideias para desenvolvimento iOS - Swift 
==================


## Por que?

O inicio em IOS pode ser intimidador. Nem Swift, nem Objective-C são amplamente utilizados em outros lugares, a plataforma possue seus próprios nomes e dispositivos. Essa documentação tem como objetivo ajudá-lo em seus primeiros passos ou introduzir o leitor de como fazer as coisas "da maneira certa". Todas as informações abaixo são apenas sugestões, então se você tiver uma razão para fazer diferente a siga.


## Conteúdo

Se você está procurando algo específico, você pode ir direto para a seção relevante a partir daqui.

1. [Estilo de código](#Estilo-de-código)
1. [Começando](#Começando)
1. [Bibliotecas comuns](#Bibliotecas-comuns)
1. [Arquitetura](#Arquitetura)
1. [Networking](#networking)
1. [Assets](#assets)
1. [Segurança](#Segurança)
1. [Diagnosticos](#diagnosticos)
1. [Analytics](#analytics)
1. [Building](#building)
1. [Deployment](#deployment)
1. [In-App Purchases (IAP)](#in-app-purchases-iap)
1. [Licença](#licença)

## Estilo de Código

Criamos um guia de estilo de código em um arquivo separado para uma melhor organização.

[Acessar Guia de Estilo Swift](Style Guide.md)


## Começando


### Configuração do Projeto

Uma pergunta comum quando iniciamos um novo projeto em iOS é se devemos escrever todas as views do código utilizando Interface Builder com Storyboards ou arquivos XIB. Ambos casos resultam no funcionamento do software. No entanto, existem algumas considerações:

#### Por que o código?
* Storyboards são mais propensas a conflitos de versão devido a sua estrutura complexa de XML. Isso torna muito mais difícil de fazer um Merge entre os códigos.
* É mais fácil estruturar e reutilizar views em código, mantendo assim sua base de código [limpo][dry]..
* Todas as informações estão em um só lugar. No Interface Builder você tem que clicar através de todos os inspetores para encontrar o que você está procurando.
* Storyboards introduz acoplamento entre o seu código e interface do usuário, que pode levar a falhas. Por exemplo, quando uma ação não está configurada corretamente. Esses problemas não são detectados pelo compilador.

[dry]: https://en.wikipedia.org/wiki/Don%27t_repeat_yourself

#### Por que Storyboards?
* Para os menos tecnicos, Storyboards pode ser uma ótima maneira de contribuir com o projeto diretamente. Por exemplo: ajustando cores ou restrições de layout. No entanto, isso requer uma configuração de projeto e algum tempo para aprender o básico.
* A iteração é muitas vezes mais rápida, pois você pode visualizar certas mudanças sem construir o projeto.
* Fontes personalizadas e elementos de interface do usuário são representados visualmente em Storyboards, dando-lhe uma idéia muito melhor do aspecto final.
* Para classes disponíveis a partir iOS 8, o Interface Builder dá-lhe uma pré-visualização de layout para os dispositivos de sua escolha, incluindo multitarefa no iPad split-screen.


#### Por que não ambos?
Para obter o melhor dos dois mundos, você também pode ter uma abordagem híbrida: Comece por esboçar o projeto inicial com Storyboards, para ajustes e mudanças rápidas. Você ainda pode convidar designers para participar neste processo. Em seguida, a transição para uma configuração baseada em código é mais fácil de manter e colaborar.

### Ignores

Um bom primeiro passo ao colocar um projeto sob controle de versão é ter um arquivo `.gitignore`. Dessa forma, os arquivos indesejados (configurações do usuário, arquivos temporários, etc.) nunca vão estar em seu repositório. Felizmente, o GitHub cobre tanto o [Swift] [swift-gitignore] quanto o [Objective-C] [objc-gitignore].

[swift-gitignore]: https://github.com/github/gitignore/blob/master/Swift.gitignore
[objc-gitignore]: https://github.com/github/gitignore/blob/master/Objective-C.gitignore

### Gerenciamento de dependência

#### CocoaPods

Se você está pensando em incluindo dependências externas (bibliotecas de terceiros por exemplo) em seu projeto, [CocoaPods] [cocoapods] oferece uma integração fácil e rápida. Instale-o assim:

    sudo gem install cocoapods

Para começar, vá para a pasta de projeto iOS e use o comando

    pod init

Isso cria um Podfile, que vai colocar todas as suas dependências em um só lugar. Depois de adicionar suas dependências para o Podfile, use o comando

    pod install

para instalar as bibliotecas e incluí-los como parte do projeto. É geralmente [recomendado para cometer as dependências instaladas no seu próprio repositório]  [comprometendo-pods], em vez de confiar que cada desenvolvedor execute `pod install` após um novo checkout.

Note-se que a partir de agora, você vai precisar abrir o arquivo `.xcworkspace` ao invés de` .xcproject`, ou o seu código não irá compilar. O comando

    pod update

atualizará todos os pods para as versões mais recentes permitidas pelo Podfile. Você pode usar uma variedade de [operadores] [cocoapods-pod-sintaxe] para especificar suas necessidades exatas de versão.

[cocoapods]: https://cocoapods.org/
[cocoapods-pod-sintaxe]: http://guides.cocoapods.org/syntax/podfile.html#pod
[comprometendo-pods]: https://www.dzombak.com/blog/2014/03/including-pods-in-source-control.html

### Estrutura do Projeto

Para manter todas aquelas centenas de arquivos no mesmo diretório, é uma boa idéia criar uma pasta dependendo de sua arquitetura. Por exemplo, você pode usar o seguinte:

    ├─ Models
    ├─ Views
    ├─ Controllers (or ViewModels, se sua arquitetura é MVVM)
    ├─ Stores
    ├─ Helpers

Primeiro, crie-os como grupos (pequenas "pastas" amarelas) dentro do grupo com o nome do seu projeto no Project Navigator do Xcode. Então, para cada um dos grupos, ligá-los a um diretório real no seu projeto. O mesmo serve para os seus subdiretórios.

#### Localização (Traduções)

Mantenha todas as strings de usuário em arquivos de localização desde o início. Isso é bom não só para traduções, mas também para encontrar texto voltados para o usuário rapidamente. Você pode adicionar um argumento de inicialização para o seu esquema de compilação para iniciar o aplicativo em uma determinada língua, por exemplo,

    -AppleLanguages (Finnish)

Para traduções mais complexos, tais como formas plurais que, dependendo de um número de itens (por exemplo, "uma pessoa" versus "3 pessoas"), você deve usar o [`formato .stringsdict`] [stringsdict-format] em vez de um regular arquivo` localizable.strings`. Assim você tem uma ferramenta poderosa que sabe como fazer plurais conforme necessário [por exemplo em russo ou árabe ] [language-plural-rules].

Encontre mais informações sobre localização [nesses slides de apresentação] [l10n-slides] a partir do fevereiro 2012 HelsinkiOS meetup. A maior parte da conversa ainda é relevante em Outubro de 2014.

[stringsdict-format]: https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/StringsdictFileFormat/StringsdictFileFormat.html
[language-plural-rules]: http://www.unicode.org/cldr/charts/latest/supplemental/language_plural_rules.html
[l10n-slides]: https://speakerdeck.com/hasseg/localization-practicum

### Branching Model

Especialmente quando há uma distribuição de um aplicativo para o público (por exemplo através da App Store), é uma boa idéia utilizar branch para para isolar lançamentos. Além disso, recursos que envolve vários commits deve ser feito em sua própria brach. [`git-flow`] [gitflow-github] é uma ferramenta que ajuda você seguir estas convenções. É simplesmente um wrapper do projeto que possui marcações e comandos utilizando Git. Pode ajudar a manter uma estrutura de ramificação adequada especialmente para as equipes. Fazer o desenvolvimento em Branches, pode se dar através do comando

    git flow release finish <version>

[gitflow-github]: https://github.com/nvie/gitflow


## Arquitetura

* [Model-View-Controller-Store (MVCS)][mvcs]
    * Esta é a arquitetura padrão da Apple (MVC), estende uma camada e lida com o trabalho em rede, armazenamento em cache etc.
* [Model-View-ViewModel (MVVM)][mvvm]
    * Motivado por "enorme controladores da view (view controllers)": MVVM considera `UIViewController` uma subclasses responsável apenas pelo que será mostrado na View, mantendo todo o estado no ViewModel.
    * Para saber mais sobre isso, veja [Introdução Fantástica] [sprynthesis-mvvm] de Bob Spryn.
    * É uma arquitetura bastante exótico que pode valer a pena olhar em projetos maiores, porém MVVM é confusa a implementação de teste é uma grande preocupação.

[mvcs]: http://programmers.stackexchange.com/questions/184396/mvcs-model-view-controller-store
[mvvm]: https://www.objc.io/issues/13-architecture/mvvm/
[sprynthesis-mvvm]: http://www.sprynthesis.com/2014/12/06/reactivecocoa-mvvm-introduction/
[viper]: https://www.objc.io/issues/13-architecture/viper/

### “Evento” Patterns

Estas são as maneiras de notificar os componentes sobre o acontecimentos em um determinada ação:

* __Delegation:__ _(one-to-one)_ Apple usa muito isso (alguns diriam, muito). Use quando quiser retornar uma informação.
* __Callback blocks:__ _(one-to-one)_ Possui um baixo grau de acoplamento. 
* __Notification Center:__ _(one-to-many)_ Possivelmente a maneira mais comum de emitir "eventos" para vários observadores. Baixo acoplamento - notificações podem ainda ser executadas em um nível global.
* __Key-Value Observing (KVO):__ _(one-to-many)_ Não exige que o objeto observado emita eventos, enquanto ele é _Key Valor Coding (KVC)_ compatível para as chaves observadas (propriedades). Geralmente não é recomendado o seu uso devido à sua natureza implícita e da API complicada.
* __Signals:__ _(one-to-many)_ eles permitem encadeamento e combina o conteúdo do seu núcleo, oferecendo, assim, uma forma de sair [callback hell][elm-escape-from-callback-hell].

[elm-escape-from-callback-hell]: http://elm-lang.org/learn/escape-from-callback-hell

### Modelos

Mantem seus modelos imutável, é utilizado para traduzir semântica e tipos de API para o app. Para projetos Objective-C, Github do [Mantle] (https://github.com/Mantle/Mantle) é uma boa escolha. Em Swift, você pode usar estruturas em vez de aulas para assegurar a imutabilidade, e usar uma biblioteca de análise, tais como [SwiftyJSON] [swiftyjson] ou [Argo] [argo] para fazer o mapeamento JSON-to-model.

[swiftyjson]: https://github.com/SwiftyJSON/SwiftyJSON
[argo]: https://github.com/thoughtbot/Argo

### Views

Devido ao tamanhos das tela nos equipamentos da Apple e do advento da divisão de telas multitarefas no iPad, as fronteiras entre dispositivos e fatores de forma tornar-se cada vez mais ténue. Muito parecido com sites de hoje, os equipamentos são esperados para se adaptar a diferentes tamanhos de janela do navegador, o aplicativo deve lidar com mudanças na tela imóveis disponíveis de uma forma graciosa. Isto pode acontecer, por exemplo, se o usuário gira o dispositivo em um aplicativo de iPad.

Em vez de manipular as Views diretamente, você deve usar as classes de tamanho e Auto layout para declarar restrições em suas Views. O sistema irá calcular os quadros apropriados com base nestas regras, e re-avaliar-los quando o ambiente muda.

[Abordagem recomendada] [wwdc-autolayout-mysteries] da Apple para configurar as suas restrições de layout é criar e ativá-las uma vez durante a inicialização. Se você precisa mudar suas limitações de forma dinâmica, siga as referências a eles e, em seguida, desativar ou ativá-las, conforme necessário. O caso de uso principal para ` updateConstraints` de UIView` (ou seu homólogo UIViewController` ,` updateViewConstraints`) é quando você deseja que o sistema execute atualizações em lote para um melhor desempenho. No entanto, isso vem com o custo de ter que chamar `setNeedsUpdateConstraints` em outro lugar no seu código, aumentando sua complexidade.

Se você substituir `updateConstraints` em uma exibição personalizada, você deve declarar explicitamente que a sua visão exige um layout baseado em restrição:

Swift:
```swift
override class func requiresConstraintBasedLayout() -> Bool {
    return true
}
```

Objective-C:
```objective-c
+ (BOOL)requiresConstraintBasedLayout {
    return YES
}
```

Caso contrário, você pode encontrar bugs estranhos quando o sistema não chamar `updateConstraints ()`. [Este post] [edward-huynh-requiresconstraintbasedlayout] de Edward Huynh oferece uma explicação mais detalhada.

[wwdc-autolayout-mysteries]: https://developer.apple.com/videos/wwdc/2015/?id=219
[edward-huynh-requiresconstraintbasedlayout]: http://www.edwardhuynh.com/blog/2013/11/24/the-mystery-of-the-requiresconstraintbasedlayout/

### Controllers

Faça uso de dependência, ou seja, passe quaisquer objetos necessários como parâmetros, em vez de manter em torno de singletons.

Swift:
```swift
let fooViewController = FooViewController(viewModel: fooViewModel)
```

Objective-C:
```objective-c
FooViewController *fooViewController = [[FooViewController alloc] initWithViewModel:fooViewModel];
```

Soroush Khanlou tem um [bom artigo] [khanlou-destroy-massive-vc] de como evitar o inchaço de seus controladores, e arquiteturas como [MVVM] (#arquitetura) trata viewControlles como views, reduzindo significativamente a sua complexidade.

[khanlou-destroy-massive-vc]: http://khanlou.com/2014/09/8-patterns-to-help-you-destroy-massive-view-controller/


## Assets(Ativos)

[Asset catalogs][asset-catalogs] é a melhor maneira de gerenciar todas as imagens do seu projeto. Eles podem conter tanto dispositivos universais e específicos (iPhone 4, iPhone Retina, iPad, etc.) Assets acessará automaticamente as imagens certas para cada tipo de dispositivo utilizando um determinado nome. Ele também permite que as alterações possam ser realizadas instantaneamentes, se necessário.

[asset-catalogs]: https://developer.apple.com/library/ios/recipes/xcode_help-image_catalog-1.0/Recipe.html

### Usando imagens bitmap

Catálogos de Assets expõe apenas os nomes dos conjuntos de imagens, abstraindo os nomes dos arquivos reais. Isso evita conflitos de nomes dos assets, tais como `button_large @ 2x.png`. Os modificadores `-568h`,` @ 2x`, `~ iphone` e` ~ ipad` não são obrigatorios, por si só, mas tê-los no nome do arquivo ao arrastar o arquivo para um conjunto de imagens vai colocá-los automaticamente no diretório, evitando assim erros de atribuição que podem ser difíceis de encontrar.

### Usando imagens vetoriais

Você pode incluir os originais [gráficos vetoriais (PDFs)] [vector-assets] produzidos por designers nos catálogos de assets, o Xcode gera automaticamente os bitmaps a partir daí. Isso reduz a complexidade de seu projeto (e o número de arquivos a serem gerenciados.)

[vector-assets]: http://martiancraft.com/blog/2014/09/vector-images-xcode6/

## Segurança

Mesmo em uma era em que confiamos a nossos dispositivos portáteis nossos dados mais privados, a segurança continua a ser um assunto muitas vezes negligenciado em nossos apps. Tente encontrar um bom trade-off para a natureza dos seus dados; seguindo apenas algumas regras simples. Um bom recurso para começar é da própria Apple [iOS Security Guide][apple-security-guide].

### Armazenamento de dados

Se o seu aplicativo precisa de armazenar dados confidenciais, como nome de usuário, senha, um token de autenticação ou alguns detalhes pessoais do usuário, você precisa mantê-los em um local onde eles não podem ser acessados de fora do app. Nunca use `NSUserDefaults`, arquivos plist ou Core Data para isso, já que não são criptografados! Na maioria desses casos, o iOS Keychain é a melhor opção. Você pode usar uma biblioteca de wrapper como [SSKeychain] [sskeychain] ou [UICKeyChainStore] [uickeychainstore].

Ao armazenar arquivos e senhas, certifique-se de definir o nível de proteção, e escolhê-lo de forma conservadora. Se você precisa de acesso enquanto o dispositivo está bloqueado (por exemplo, para tarefas em segundo plano), use o "acesso após a primeira desbloqueio" de forma variável. Em outros casos, você provavelmente deve exigir que o dispositivo é desbloqueado para acessar os dados.

### Networking

Mantenha todo o tráfego HTTP para servidores remotos criptografados com TLS em todas as vezes. Para evitar ataques man-in-the-middle que interceptam o tráfego criptografado, você pode configurar [certificate pinning] [certificate-pinning]. Bibliotecas de redes populares, como [Alamofire] [alamofire-github] suporta este tipo de tráfico.

### Logging

Tome cuidado extra para configurar níveis de registo adequada antes de liberar o seu aplicativo. Produtos construidos nunca devem obter senhas, tokens de API e afins de registro, pois isso pode facilmente causar vazamento. Por outro lado, registrando o fluxo de controle pode ajudá-lo a identificar problemas que os usuários estão enfrentando.

### Insterface de Usuário

Ao usar `UITextField` para senha, lembre-se de definir sua propriedade true em `secureTextEntry` para evitar mostrar a senha. Você também deve desativar auto-correção para o campo de senha e limpar o campo sempre que necessário, tal como quando seu app entra em background.

Uma boa prática é limpar a Área de trabalho para evitar que senhas e outros dados confidenciais vazem quando o app entre em background. Como iOS pode tirar screenshots do seu aplicativo, certifique-se de limpar os dados confidenciais da UI _antes_ do retorno em `applicationDidEnterBackground`.

[apple-security-guide]: https://www.apple.com/business/docs/iOS_Security_Guide.pdf
[sskeychain]: https://github.com/soffes/sskeychain
[uickeychainstore]: https://github.com/kishikawakatsumi/UICKeyChainStore
[certificate-pinning]: https://possiblemobile.com/2013/03/ssl-pinning-for-increased-app-security/
[alamofire-github]: https://github.com/Alamofire/Alamofire

## Diagnosticos

### Debugging (Depuração)

Quando seu aplicativo trava, o Xcode não mostra o debugger por padrão. Para conseguir isso, adicionamos um break pointer (clique no botão "+" na parte inferior do Xcode Debug Navigator) para parar a execução quando uma exceção é gerada. Em muitos casos, você então verá a linha de código responsável pela exceção. O Xcode também mantém quebra nas exceções em bibliotecas de terceiros, você pode ser capaz de diminuir isso escolhendo _Edit Breakpoint_ e definindo o _Exception_ suspensa para _Objective-C_.

A view de depuração, [Reveal] [reveal] e a [Spark Inspector] [spark-inspector] são dois poderosos inspetores visuais que podem salvar horas do seu tempo, especialmente se você estiver usando Auto Layout e querem localizar pontos de vista que estão com problemas ou fora das views.

[reveal]: http://revealapp.com/
[spark-inspector]: http://sparkinspector.com

### Profiling

Xcode vem com um conjunto de perfis chamado Instruments. Ele contém uma infinidade de ferramentas para uso de memória, CPU, rede de comunicações, gráficos e muito mais. É uma "fera", uma das ferramentas mais utilizadas são os rastreadores de vazamentos de memória. Basta escolher _Product_> _perfil_ no Xcode, selecione o instrumento Allocations, aperte o botão Record e filter, mostrando assim o resumo dos objetos alocados, como o prefixo de nomes de classe do seu próprio app. A contagem na coluna persistente, em seguida, informa quantas instâncias de cada objeto que você tem. Qualquer classe para a qual a contagem de instância aumenta indiscriminadamente indica um aumento da memória.

Também é bom saber que Instruments tem uma ferramenta de automação para gravar e reproduzir interações UI como arquivos JavaScript. [UI Auto Monkey][ui-auto-monkey] é um script que irá usar automação para bater aleatoriamente em seu aplicativo causando stress na aplicação, muito utilizada em testes.

Preste atenção extra quando for criar classes com um grande número de processamento. `NSDateFormatter`, por exemplo, é muito dispendioso para a aplicação fazê-lo em rápida execução.

[ui-auto-monkey]: https://github.com/jonathanpenn/ui-auto-monkey

## Analytics

Incluir alguns frameworks analytics em seu aplicativo é altamente recomendável, pois permite que você possa obter insights sobre como as pessoas realmente estão usando. Será que a função X agrega valor? Botão Y é muito difícil de encontrar? Para responder a estes, você pode enviar eventos, horários e outras informações mensuráveis a um serviço que agrega e visualiza-los - por exemplo, [Google Tag Manager] [google-tag-manager]. Este último é mais versátil do que o Google Analytics em que ele insere uma camada de dados entre aplicativos e Analytics, de modo que a lógica de dados pode ser modificada através de um serviço de web sem ter que atualizar o aplicativo.

[google-tag-manager]: https://www.google.com/analytics/tag-manager/

Uma boa prática é criar uma simples classe auxiliar, por exemplo, `AnalyticsHelper`, que lida com a tradução a partir de modelos e formatos de dados (` FooModel`, `NSTimeInterval`, ...) para a camada de dados:

```swift

func pushAddItemEventWithItem(item: Item, editMode: EditMode) {
    let editModeString = nameForEditMode(editMode)

    pushToDataLayer([
        "event": "addItem",
        "itemIdentifier": item.identifier,
        "editMode": editModeString
    ])
}

```
Isto tem a vantagem adicional de permitir que você troque o quadro Analytics inteiras, se necessário, sem que o resto do app percebesse.

### Crash Logs

Primeiro você deve fazer o seu app enviar logs de colisão a um servidor para que você possa acessá-los. Você pode implementar isso manualmente (usando [PLCrashReporter] [plcrashreporter] e seu próprio backend), mas é recomendável que você use um serviço já existente - por exemplo, um dos seguintes procedimentos:

* [Crashlytics](http://www.crashlytics.com)
* [HockeyApp](http://hockeyapp.net)
* [Crittercism](https://www.crittercism.com)
* [Splunk MINTexpress](https://mint.splunk.com)

[plcrashreporter]: https://www.plcrashreporter.org

Assim que tiver esta configuração, garantir que você salve o arquivo no Xcode (`.xcarchive`) de cada. O arquivo contém o binário do aplicativo e os símbolos de depuração (`dSYM`) que você vai precisar para gerar relatórios de falhas a partir dessa versão específica do seu app.

## Implantação (Deployment)

A implantação de software em dispositivos iOS não é exatamente simples. Dito isto, aqui estão alguns conceitos centrais que, uma vez compreendida, irá ajudá-lo.

### Assinatura

Sempre que você quiser executar o software em um dispositivo real (ao contrário do simulador), você terá que assinar sua compilação com um __certificate__ emitido pela Apple. Cada certificado está ligada a um par de chaves pública / privada, a metade privada de que reside no Keychain do seu Mac. Existem dois tipos de certificados:

*__Development certificate:__ Cada desenvolvedor em uma equipe tem o seu próprio, e ele é gerado mediante solicitação. Xcode pode fazer isso por você, mas é melhor não pressionar o botão mágico "Fix issue" e entender o que está realmente acontecendo. Este certificado é necessário para implantar o desenvolvimento em um dispositivos.

* __Distribution certificate:__ Não pode haver vários, mas é melhor mantê-lo um por organização, e compartilhar sua chave associada através de algum canal interno. Este certificado é necessário para enviar para a App Store.


### Provisioning

Além de certificados, existem também __provisioning profiles__, que são, basicamente, o elo perdido entre dispositivos e certificados. Mais uma vez, existem dois tipos de distinguir entre fins de desenvolvimento e de distribuição:

* __Development provisioning profile:__ Ele contém uma lista de todos os dispositivos que estão autorizados a instalar e executar o software. Ela está também relacionada com um ou mais certificados de desenvolvimento, uma para cada colaborador que está autorizada a utilizar o perfil. O perfil pode ser vinculado a um aplicativo específico ou usar um App ID (*). O último é [desencorajador] [jared-sinclair-signing-tips], porque Xcode é notoriamente ruim em escolher os arquivos corretos para a assinatura. Além disso, alguns recursos como Push Notifications ou Grupos App exigem uma App ID explícito.

* __Distribution Perfil de aprovisionamento:__ Existem três formas diferentes de distribuição, cada uma para um caso de uso diferente. Cada perfil de distribuição está ligado a um certificado de distribuição, e não será válida quando o certificado expirar.

	* __Ad-Hoc:__ Assim como perfis de desenvolvimento, que contém uma lista de permissões de dispositivos do aplicativo, este tipo de perfil pode ser usado para testes beta em 100 aparelhos por ano. Para uma experiência mais suave e até 1000 usuários distintos, você pode usar o serviço [TestFlight] [testflight] recém-adquirido pela Apple. Você pode encontrar [resumo de suas vantagens e problemas aqui] [testflight-discussion].
	* __App Store:__ Este perfil não tem lista de dispositivos permitidos, como qualquer pessoa pode instalá-lo através do canal de distribuição oficial da Apple. Este perfil é necessária para todas as versões da App Store.
	* __Enterprise:__ Assim como App Store, não existe uma lista livre de dispositivos, o aplicativo pode ser instalado por qualquer pessoa com acesso interno "app store" da empresa, que pode ser apenas um site com links. Este perfil está disponível somente em contas da empresa.

[jared-sinclair-signing-tips]: http://blog.jaredsinclair.com/post/116436789850/
[testflight]: https://developer.apple.com/testflight/
[testflight-discussion]: http://blog.supertop.co/post/108759935377/app-developer-friends-try-testflight

Para sincronizar todos os certificados e perfis para a sua máquina, vai em Contas nas Preferências do Xcode, e adicione o seu Apple ID, se necessário, e clique duas vezes no nome da equipe. Há um botão de atualização na parte inferior, mas às vezes você só precisa reiniciar Xcode para fazer tudo aparecer.

#### Debugging Provisioning

Às vezes você precisa utilizar o Debug para depurar um problema de provisioning. Por exemplo, Xcode pode se recusar a instalar um projeto em um dispositivo conectado, se este último não estiver na lista de dispositivos de perfil (desenvolvimento ou ad-hoc).  Nesses casos, você pode usar o excelente [Provisioning] [provisioning] plugin Craig Hockenberry, navegando para `~ / Library / MobileDevice / Provisioning Profiles`, a seleção de um arquivo` .mobileprovision` e clicar em espaço para lançar recurso do Finder. Ele irá mostrar-lhe uma riqueza de informações, tais como dispositivos, direitos, certificados e do ID App.

[provisioning]: https://github.com/chockenberry/Provisioning

### Uploading

[iTunes Connect][itunes-connect] este portal da Apple é utilizados para gerenciar seus aplicativos na App Store. Para fazer upload de um projeto, o Xcode exige um ID da Apple que faz parte da conta de desenvolvedor. Isso pode tornar as coisas complicada quando você é parte de várias contas de desenvolvedores e deseja fazer o upload de seus aplicativos, como por razões misteriosas _any given Apple ID can only be associated with a single iTunes Connect account_. Uma solução é criar um novo ID Apple para cada iTunes Connect.

Após o upload da compilação, seja paciente, pois pode levar até uma hora para aparecer na seção de sua versão de app construidos. Quando ele aparece, você pode vinculá-lo a versão de aplicativo e enviar o seu app para revisão.

[itunes-connect]: https://itunesconnect.apple.com

## In-App Purchases (IAP)

Ao validar recibos de compra, lembre-se de fazer os seguintes controles:

- __Authenticity:__ Se o recebimento vem da Apple
- __Integrity:__ Que o recibo não foi adulterado
- __App match:__ Que o ID pacote app no recebimento corresponde identificador de conjunto do seu aplicativo
- __Product match:__ Que a identificação do produto no recebimento corresponde ao seu identificador do produto esperado
- __Freshness:__ Que você não tenha visto o mesmo ID recibo antes.

Sempre que possível, projetar seu sistema IAP para armazenar o conteúdo para a venda do lado do servidor, e fornecê-lo para o cliente apenas em troca de um recibo válido que após passar por todas as verificações acima. Este tipo de projeto impede mecanismos comuns de pirataria, e - uma vez que a validação é executada no servidor - permite usar o serviço de validação de recebimento HTTP da Apple em vez de interpretar o recebimento.

Para obter mais informações sobre este assunto, consulte a [Futurice blog: Validating in-app purchases in your iOS app][futu-blog-iap].

[futu-blog-iap]: http://futurice.com/blog/validating-in-app-purchases-in-your-ios-app

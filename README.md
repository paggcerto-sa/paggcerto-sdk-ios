# PaggcertoSDK 1.1.0

A PaggcertoSDK foi criada para facilitar a integração de sua aplicação iOS com a API da Paggcerto. Esse SDK possui os principais métodos da API de pagamentos, além disso, o SDK também disponibiliza diversos métodos de comunicação com os pinpads que a Paggcerto trabalha.

## Pré-Requisitos

A SDK da Paggcerto necessita que as bibliotecas abaixo também estejam instaladas no seu projeto, as duas podem ser baixadas pelo Carthage ou CocoaPods.

```
github "Alamofire/Alamofire" ~> 4.7.
github "Hearst-DD/ObjectMapper" ~> 3.3
```

Para utilizar os métodos de pagamento com Pinpad das classes ``PinpadServices`` e ``BluetoothServices`` é necessário que você entre em contato com a fabricante da maquininha e peça o protocolo de MFi para que você insira no arquivo Info.plist da sua aplicação.


## Integrando o SDK na aplicação

Para utilizar todos os recursos do SDK é necessário fazer alguns ajustes iniciais em sua aplicação.

### Instancie o SDK em sua aplicação

Faça o download do SDK clicando [aqui](https://github.com/paggcerto-sa/paggcerto-sdk-ios/raw/master/PaggcertoSDK.framework.zip).

Com o seu projeto aberto no Xcode clique no arquivo principal do projeto, abra a aba General e adicione o arquivo ``PaggcertoSDK`` em Embedded Binaries.


### Utilizando o SDK em sua aplicação

```PaggcertoSDK```

Ao iniciar a sua aplicação é necessário ativar o SDK informando seu token e em qual ambiente irá trabalhar. Para ativar o SDK você deve utilizar o seguinte método: 

```activate(environment: String, token: String, completion: @escaping (Bool, String?) -> ())```


No parâmetro ```token``` você irá setar seu token de acesso.
Para saber como conseguir seu token clique [aqui](https://desenvolvedor.paggcerto.com.br/v2/account/).

No parâmetro ```environment``` você irá declarar o ambiente de trabalho. O SDK fornece constantes para acessar esses ambientes:

* ```PaggcertoSDK.sandbox```: Esse ambiente permite o usuário trabalhar em sandbox.
* ```PaggcertoSDK.production```: Esse ambiente permite o usuário trabalhar em produção.

O método também pede um callback com tupla(Bool, String?) que retorna respectivamente se o SDK foi ativado com sucesso e a mensagem de erro caso o processo tenha dado falha.

Lembrando que o token de acesso gerado em sandbox só irá funcionar no ambiente ```sandbox```, do mesmo modo um token gerado para o ambiente de produção só irá funcionar no ambiente ```production```.

```isActive() -> Bool```

Retorna verdadeiro caso o SDK tenha sido ativado. 
Como o SDK só preciso ser ativado uma vez na aplicação, esse método auxilia o desenvolvedor a evitar múltiplas ativações.


### Exemplo

Segue um exemplo de como fazer a configuração inicial do SDK

```
PaggcertoSDK.activate(environment: PaggcertoSDK.sandbox, token: "MEU_TOKEN"){
    (result, errorMessage) in
}
```

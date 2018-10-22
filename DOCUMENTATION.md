# Documentação

## Pagamento com Pinpad

A SDK da Paggcerto disponibiliza uma série de métodos para auxiliar o desenvolvedor a realizar pagementos utilizando um pinpad.

As classes ``PinpadServices``, ``BluetoothServices`` e ``CommandService`` são essenciais para a captura dos dados do cartão do cliente.

A tabela abaixo lista os métodos dispostos na classe ``BluetoothServices``

Nome do método | Descrição | Retorno
--- | --- | ---
listConnectedPinpads() | Lista de acessórios Bluetooth conectados ao dispositivo e que podem ser Pinpads. | [EAAccessory]
connectPinpad(pinpad: EAAccessory, completion: @escaping (Bool) -> ()) | Conecta ao dispositivo escolhido com um callback de retorno de sucesso | -

A tabela abaixo lista os métodos dispostos na classe ```PinpadServices```

Nome do método | Descrição | Retorno
--- | --- | ---
disconnect(commandService: CommandService, completion: @escaping (Bool) -> ()) | Desconecta-se do pinpad conectado anteriormente passando a instância do CommandService e um callback de retorno de timeout. | -
getCard(commandService: CommandService, amount: String, installments: Int, credit: Bool, delegate: ReadCardProtocol, completion: @escaping (Bool, Pagg_Card? , String?) -> ()) | Retorna as informações do cartão passando uma instância do CommandService e um callback de retorno, seguindo o exemplo mostrado mais abaixo neste documento. | -
getPaymentDevice(commandService: CommandService, completion: @escaping (Bool, Pagg_PaymentDevice?) -> ()) | Busca informações referentes ao pinpad com um callback de retorno de sucesso e do objeto do pinpad. | -

A classe ```CommandService``` não possui métodos, porém deve ser instanciada e utilizada como parâmentro nos 3 métodos disponíveis em ```PinpadServices```. É importante que num mesmo fluxo seja utilizada a mesma instância desta classe.

### Exemplo de leitura de cartão

```
var payment = Pagg_CardPay()
payment.cards = []

let pinpads = BluetoothServices.listConnectedPinpads()
if(pinpads.count > 0){
    BluetoothServices.connectPinpad(pinpad: pinpads[0]){
        (success) in

        if(success){
            self.pinpad = pinpads[0]
            let commandService = CommandService()
            PinpadServices.getCard(commandService: commandService, amount: "300", installments: 1, credit: true, delegate: self){
                (shouldAskCVV, card, errorMessage) in

                if(card != nil){
                    payment.cards!.append(card!)
                }else{
                    self.printResult(text: errorMessage ?? "Erro inesperado.")
                }

                PinpadServices.disconnect(commandService: commandService){
                    (timeout) in
                }
            }

        }else{
            self.printResult(text: "Erro ao conectar ao pinpad")
        }
    }
}else{
    self.printResult(text: "Sem pinpads conectados.")
}
```

## Métodos de pagamento

Essa seção irá abordar todos os métodos da SDK disponíveis para a API de pagamentos. 

Todos os métodos de pagamentos da SDK estão disponíveis na Classe ```PaymentsApi```. 
Eles trabalham de forma assíncrona, por isso é necessário passar sempre um callback de retorno que na maioria dos métodos possui o padrão (Error?, String?, ObjetoRetorno?) que significa respectivamente, Erro de conexão, Mensagem de erro (opcional), Objeto esperado por cada método (opcional). 

Esses callbacks customizados serão mostrados nos exemplos abaixo.

Para utilizar essa classe certifique-se que a SDK foi ativada com o método ```PaggcertoSDK.activate()```.

#### Consultar bandeiras de cartão
```bins(completion: @escaping (Error?, String?, [Pagg_Bin]?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/consultar-bandeira-de-cartao)

#### Pagamento com cartão
```cardPay(pay: Pagg_CardPay, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/efetuar-pagamento-cartao)

#### Cadastrar cartão
```createCard(card: Pagg_Card, callBack: PaggcertoCallBack<Pagg_Card>)```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/cadastrar-cartao)

#### Listar cartões
```cards(filter: Pagg_CardFilter, completion: @escaping (Error?, String?, Pagg_CardList?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/listar-cartoes)

#### Pesquisar cartão
```findCard(id: String, completion: @escaping (Error?, String?, Pagg_Card?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/pesquisar-cartao)

#### Remover cartão
```deleteCard(id: String, completion: @escaping (Error?, String?, Pagg_Card?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/remover-cartao)

#### Simular pagamento com cartão
```simulate(simulation: Pagg_Simulation, completion: @escaping (Error?, String?, Pagg_SimulationResult?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/simular-pagamento-cartao)

#### Enviar comprovante de transação no cartão
```sendReceipt(nsu: String, sendReceipt: Pagg_SendReceipt, completion: @escaping (Error?, String?, Bool) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/enviar-comprovante)

#### Efetuar pagamento com boleto
```bankSlipPay(pay: Pagg_BankSlipPay, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/efetuar-pagamento-boleto)

#### Substituir boleto
```replaceBankslip(bankSlipNumber: String, bankslipData: Pagg_BankSlipData, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/substituir-boleto)

#### Gerar PDF dos boletos do pagamento
```pdfBankSlipPayment(paymentId: String, completion: @escaping (Error?, String?, Data?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/gerar-pdf-do-boleto-pagamento)

#### Cadastrar recebedor
```createSplitter(splitter: Pagg_Splitter, completion: @escaping (Error?, String?, Pagg_Splitter?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/cadastrar-recebedor)

#### Atualizar recebedor
```updateSplitter(id: String, splitter: Pagg_Splitter, completion: @escaping (Error?, String?, Pagg_Splitter?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/atualizar-recebedor)

#### Listagem de recebedores
```splitters(filter: Pagg_SplitterFilter, completion: @escaping (Error?, String?, Pagg_SplitterList?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/listar-recebedores%20)

#### Pesquisar recebedor
```findSplitter(id: String, completion: @escaping (Error?, String?, Pagg_Splitter?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/pesquisar-recebedores%20)

#### Finalizar pagamento
```finalizePayment(paymentId: String, note: String, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/finalizar-pagamento)

#### Cancelar pagamento
```cancelPayment(paymentId: String, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/cancelar-pagamento)

#### Cancelar transação de cartão
```cancelCardTransaction(nsu: String, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/cancelar-transacao-cartao)

#### Cancelar boleto
```cancelBankslip(bankSlipNumber: String, cancelationReason: String, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/cancelar-boleto)

#### Detalhes do pagamento
```findPayment(paymentId: String, completion: @escaping (Error?, String?, Pagg_Payment?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/detalhes-pagamento)

#### Listagem de pagamentos
```payments(filter: Pagg_PaymentFilter, completion: @escaping (Error?, String?, Pagg_PaymentHistoryList?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/consultar-pagamentos)

#### Consultar saldo de repasse
```balance(completion: @escaping (Error?, String?, Pagg_Balance?) -> ())```
[Detalhes](https://desenvolvedor.paggcerto.com.br/v2/payments/#operation/consultar-saldo-de-repasse)

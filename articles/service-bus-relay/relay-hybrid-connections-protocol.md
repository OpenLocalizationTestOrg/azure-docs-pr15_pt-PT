<properties 
    pageTitle="Reencaminhamento Azure híbrido ligações protocolo | Microsoft Azure"
    description="zure reencaminhamento híbrido ligações protocolo guia."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de ligações de híbrido reencaminhamento Azure

Azure reencaminhamento é um dos montantes a capacidade de chave da plataforma Azure Service Bus. Capacidade de "Híbrido ligações" nova o reencaminhamento é uma evolução segura, abrir protocolo com base em HTTP e WebSockets.

Substitui o anterior, com igualmente o nome de funcionalidade "BizTalk serviços" que foi criada com base numa Fundação de protocolo proprietário. A integração de ligações de híbrido dos serviços de aplicação do Azure irão continuar a funcionar como-é.

"Híbrido ligações" permite que estabelece comunicação bidirecional, binário sequência entre duas aplicações utilizarão, através das quais podem residem uma ou ambas as partes atrás NAT ou Firewalls.

Este documento descreve as interações do lado do cliente com o reencaminhamento de ligações de híbrido para ligar a clientes em funções de remetente e escuta e como listeners aceitam novas ligações.

## <a name="interaction-model"></a>Modelo de interação

O reencaminhamento de ligações de híbrido liga duas partes, fornecendo um ponto de rendezvous na nuvem Azure que tanto partes podem descobrir e ligar a perspetiva os seus próprios rede. Esse ponto rendezvous chama "Híbrido ligação" neste e outros documentação, as APIs e também no Portal do Azure. O ponto final de serviço de ligações de híbrido será referido como "serviço" para o resto do documento.

O modelo de interação leans no nomenclatura estabelecida pelo serviço muitas outras API de rede:

Existe uma escuta que pela primeira vez, indica se está pronta para processar ligações de entrada e subsequentemente aceita-los à medida que chegam. No outro lado, existe um cliente de conexão que irá ligar escuta, espera aceites para estabelecer um caminho de comunicação bidirecional dessa ligação. "Ligação", "Ouvir", "Aceitar" é os mesmos termos que irá encontrar na maioria dos socket APIs.

Qualquer modelo de comunicação transmitido tiver qualquer uma das partes efectuar ligações de saída no sentido de um ponto final de serviço, que torna a "escuta" também "cliente" no seu utilização e também pode fazer com que outros overloads terminologia; a terminologia precisa, por conseguinte, utilizamos para ligações de híbrido é da seguinte forma:

Os programas em ambos os lados de uma ligação são denominados "cliente", uma vez que são clientes ao serviço. O cliente que aguarda e aceita ligações é "escuta" ou disse ter a função"escuta". O cliente de que inicia uma nova ligação no sentido uma escuta através do serviço chama-se "remetente" ou "função de remetente".

## <a name="listener-interactions"></a>Interações escuta

A escuta tem quatro interações com o serviço; todos os detalhes de fio são descritos mais adiante neste documento, na secção referência.

### <a name="listen"></a>Ouvir

Para indicar a disponibilidade para o serviço que um ouvinte esteja pronto para aceitar as ligações, que cria uma ligação de socket web saída. O handshake ligação executa o nome de uma ligação de híbrido configurado no espaço de nomes de reencaminhamento e um token de segurança que confere "Ouvir" a esse nome. Quando o socket web for aceite pelo serviço, o registo estiver concluído e o socket estabelecida web é mantido vivo como o "canal de controlo" para ativar todas as interações subsequentes. O serviço permite até 25 listeners em simultâneo numa ligação híbrido. Se existirem listeners 2 ou mais ativos, ligações de entrada irão balanceamento através de lhes ordem aleatória; não se garante a distribuição da feira da ciência.

### <a name="accept"></a>Aceitar

Sempre que abre um remetente de uma ligação nova no serviço, o serviço será escolha e notificar um das listeners ativas na ligação híbrido. A notificação é enviada para o serviço de escuta através do canal de controlo aberto como uma mensagem JSON que contém o URL do ponto final de socket Web que a escuta tem de ligar para aceitar a ligação.

O URL pode e tem de ser utilizado diretamente pela escuta sem qualquer trabalho adicional; a informação codificada só é válida para um breve período de tempo, essencialmente para enquanto o remetente é disposto a aguardar para a ligação é estabelecida ponto a ponto, mas até um máximo de 30 segundos. O URL só pode ser utilizado para tentativa de uma ligação com êxito. Assim que a ligação de socket Web com o URL de rendezvous é estabelecida, toda a actividade mais neste socket Web é reencaminhada de e para o remetente, sem qualquer intervenção ou interpretação pelo serviço.

### <a name="renew"></a>Renovar 

O token de segurança que deve ser utilizado para registar a escuta e manter o canal de controlo poderá expirar enquanto a escuta está ativa. O termo token não irá afectar ligações em curso, mas irá fazer com que o canal de controlo para ignorado pelo serviço no ou mais cedo após instantâneas do termo. O gesto "renovar" é uma mensagem JSON que pode enviar a escuta para substituir o token associado o canal de controlo, para que possam ser mantido o canal de controlo para períodos adicional.

### <a name="ping"></a>Ping 

Se o canal de controlo permanece inactivo muito tempo, intermediários da forma, tais como carga balanceadores ou NAT poderá largue a ligação TCP. O gesto de "ping" evita que ao enviar uma pequena quantidade de dados no canal que lhe lembrar todas as pessoas a rota de rede que a ligação se destinar a ser vivo e também funciona como um teste liveness a escutar. Se o ping falhar, o canal de controlo deve ser considerado inutilizável e o serviço de escuta deve restabelecer a ligação.

## <a name="sender-interaction"></a>Interação do remetente

O remetente tem apenas uma única interação com o serviço, liga-se.

### <a name="connect"></a>Ligar

O gesto "ligar" é aberto um socket Web o serviço, fornecer o nome da ligação híbrido e uma (opcional, mas necessários por predefinição) token de segurança que confira permissões "Enviar" na cadeia de consulta. O serviço, em seguida, irá interagir com a escuta da forma descrito acima e tem a escuta de criar uma ligação de rendezvous será associada com este socket Web. Depois do socket Web for aceite, todas as interações mais no Web socket serão por conseguinte, com uma escuta ligada.

## <a name="interaction-summary"></a>Resumo de interação

O resultado deste modelo de interação é que o cliente do remetente vem terminar handshake com um "limpo" socket de Web que está ligado a uma escuta e que precisa de sem ainda mais preambles ou preparação. Esta opção permite-praticamente qualquer aplicação de cliente do Web socket existente facilmente tirar partido do serviço híbrido ligações apenas, fornecendo um URL corretamente construído na camada de cliente socket Web.

A ligação rendezvous Socket Web que obtém a escuta através da interação aceitar também é limpa e pode ser entregue qualquer existente Web socket implementação do servidor com algumas produção extra mínima que distinga entre operações de "aceitar" no listeners de rede local do seu quadro e operações de remota "aceitar" dos híbrido ligações.

## <a name="protocol-reference"></a>Referência de protocolo

Esta secção descreve os detalhes das interações protocolo descritos acima.

Todas as ligações de socket Web são efetuadas na porta 443 como uma atualização a partir do 1.1 de HTTPS, que são recolhidas frequentemente por alguns framework de socket Web ou API. A descrição da aqui é mantida pós-implementação neutros, sem sugerir um quadro específico.

## <a name="listener-protocol"></a>Protocolo escuta

O protocolo escuta consiste em duas gestos de ligação e três operações de mensagem.

### <a name="listener-control-channel-connection"></a>Ligação de canal de controlo de escuta

É aberto o canal de controlo com a criação de uma ligação de socket Web para:

*wss: / / {espaço de nomes endereço} /* *$servicebus* */* *hybridconnection /**{caminho}? ação do hc sb =... e sb-hc-id =... e token de hc sb =... "*

O *espaço de nomes e endereços* é o nome de domínio completamente qualificado do espaço de nomes de reencaminhamento de Azure que aloja a ligação híbrido, normalmente do formulário {*myname}. servicebus.windows.net.*

As opções de parâmetro da cadeia de consulta são os seguintes

| Param        | Obrigatório? | Descrição                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ação do hc SB | Sim       | Para a função escuta o parâmetro tem de ser **ação do hc sb = ouvir**                                                                                                                                |
| {caminho}       | Sim       | O caminho de URL codificada espaço de nomes da ligação híbrido pré-configurado para registar este escuta no. Esta expressão é acrescentado para o fixo * **$servicebus**/**hybridconnection /** * parte do caminho. |
| token de hc SB  | Sim\*     | A escuta deve fornecer uma válido, URL codificada serviço Bus partilhado acesso Token para a ligação de híbrido que confira o direito de **ouvir** ou espaço de nomes.                                           |
| id de hc SB     | N        | Este ID opcional fornecido pelo cliente permite que o rastreio diagnóstico do fim para fim.                                                                                                                             |

Se a ligação do Web Socket falhar devido ao caminho da ligação de híbrido não a ser registado, ou um token inválido ou em falta ou outro tipo de erro, o comentários do erro será fornecido utilizando o modelo de comentários de estado HTTP 1.1 normal. A descrição do estado irá conter um controlo-id de erro que pode ser comunicado para o suporte do Azure:

| Código | Erro          | Descrição                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Não foi encontrado      | O **caminho** de ligação híbrido é inválido ou o URL de base está mal |
| 401  | Não autorizado   | O token de segurança está em falta ou mal ou é inválido                  |
| 403  | Proibido      | Não é válido para este caminho para que esta ação do token de segurança          |
| 500  | Erro interno | Ocorreu um problema no serviço                                    |

Se a ligação do socket Web é encerrar intencionalmente pelo serviço após ter sido inicialmente definida para cima, a razão para fazê-lo comunicadas utilizando um adequado Web socket protocolo código de erro juntamente com uma mensagem de erro descritiva que também irá incluir um id de controlo. O serviço não irá encerrar o canal de controlo sem se deparar com uma condição de erro. Qualquer encerramento LIMPARB é controlado de cliente.

| Estado de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | O caminho da ligação de híbrido foi eliminado ou desativado.                           |
| 1008      | O token de segurança expirou e a política de autorização, por isso, é suspenso. |
| 1011      | Algo correu mal dentro o serviço.                                           |

### <a name="accept-handshake"></a>Aceitar handshake 

A notificação de aceitar é enviada pelo serviço para o serviço de escuta, através do canal de controlo estabelecida anteriormente como uma mensagem JSON numa moldura de texto socket Web. Não existe nenhuma responda a esta mensagem.

A mensagem contém um objeto JSON denominado "aceitar", que define as seguintes propriedades neste momento:

-   **endereço** – a cadeia de URL para ser utilizado para estabelecer o Socket Web para o serviço para aceitar uma ligação a receber.

-   **id de** – o identificador exclusivo para esta ligação. Se o id foi fornecido pelo cliente do remetente, é o valor de remetente fornecido, caso contrário, é um valor de sistema gerado.

-   **connectHeaders** – todos os cabeçalhos HTTP que tenham sido fornecidos para o ponto final de Relay ao remetente, o qual também inclui o protocolo de WebSocket de Sec e os cabeçalhos de Sec-WebSocket-extensões.

| Aceitar a mensagem                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

O URL do endereço fornecido na mensagem JSON é utilizado pela escuta para estabelecer Socket Web para aceitar ou rejeitar o socket do remetente.

#### <a name="accepting-the-socket"></a>Aceitar o socket

Para aceitar, a escuta estabelece uma ligação de WebSocket para o endereço fornecido.

Conta que para o período de pré-visualização, o endereço URI pode utilizar um endereço IP simples e o certificado de TLS fornecido pelo servidor irá falhar validação nesse endereço. Isto vai ser corrigido durante a pré-visualização.

Se a mensagem "aceitar" executa num cabeçalho de "Protocolo da WebSocket seg", é esperado que a escuta aceitará apenas a WebSocket se suporta esse protocolo e que define o cabeçalho tal como o WebSocket é estabelecida.

O mesmo se aplica ao cabeçalho "Extensões da WebSocket seg". Se a arquitetura suportar uma extensão, deverá definida o cabeçalho para a resposta de lado do *servidor* de handshake "Extensões da WebSocket seg" necessário para a extensão.

O URL deve ser utilizado como-é para estabelecer o socket aceitar, mas contém seguintes parâmetros:

| Param        | Obrigatório? | Descrição                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ação do hc SB | Sim       | Para aceitar um socket o parâmetro tem de ser **ação do hc sb = aceitar**                                                                                                                                                                                                                          |
| {caminho}       | Sim       | O caminho de URL codificada espaço de nomes da ligação híbrido pré-configurado para registar este escuta no. Esta expressão é acrescentado para o fixo * **$servicebus**/**hybridconnection /** * parte do caminho.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| id de hc SB | No        | Consulte o artigo Descrição do **id** .                                                                                                                                                                                                                                                              |

Se existir um erro, o serviço poderá responder da seguinte forma:

| Código | Erro          | Descrição                         |
|------|----------------|-------------------------------------|
| 403  | Proibido      | O URL não é válido.               |
| 500  | Erro interno | Ocorreu um problema no serviço |

Após ter sido estabelecida a ligação, o servidor irá encerrar o socket Web quando encerra o socket de Web do remetente, ou com o seguinte estado

| Estado de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | O cliente do remetente encerra a ligação                                        |
| 1001      | O caminho da ligação de híbrido foi eliminado ou desativado.                           |
| 1008      | O token de segurança expirou e a política de autorização, por isso, é suspenso. |
| 1011      | Algo correu mal dentro o serviço.                                           |

#### <a name="rejecting-the-socket"></a>Rejeitar o socket

Rejeitar o socket depois da inspeção da mensagem "aceitar" requer um handshake semelhante, para que o código de estado e a descrição de Estado comunicar o motivo da rejeição podem fluir novamente para o remetente.

A escolha de estrutura do protocolo é utilizar um handshake WebSocket (que foi concebido para terminar num estado definido erro) para que implementações do cliente escuta pode continuar a depender de um cliente WebSocket e não precisa de um ficheiro extra de empregam, bare cliente HTTP.

Para rejeitar o socket, o cliente leva-o até URI de endereço a partir da mensagem "aceitar" e acrescenta dois parâmetros de cadeia de consulta:

| Param             | Obrigatório? | Descrição                             |
|-------------------|-----------|-----------------------------------------|
| statusCode        | Sim       | Código de estado HTTP numérico                |
| DescriçãoDoStatus | Sim       | Humano motivo legível da rejeição |

O URI resultante, em seguida, é utilizado para estabelecer uma ligação de WebSocket; novamente, lembre-se de que o certificado TLS pode não coincidir com o endereço durante a pré-visualização, para que a validação poderá ter de ser desativados.

Quando concluir corretamente, este handshake intencionalmente falhará com um código de erro HTTP 410, desde que não WebSocket foi estabelecida. Se ocorre um erro, estas são as opções:

| Código | Erro          | Descrição                         |
|------|----------------|-------------------------------------|
| 403  | Proibido      | O URL não é válido.               |
| 500  | Erro interno | Ocorreu um problema no serviço |

### <a name="listener-token-renewal"></a>Renovação de tokens escuta

Quando token a escuta está prestes a expirar, pode substituí-la ao enviar uma mensagem de moldura de texto para o serviço através do canal de controlo de estabelecimento. A mensagem contém um objeto JSON denominado "renewToken", que define a propriedade seguinte neste momento:

-   **token** – um válido, URL codificada serviço Bus partilhado acesso Token para o espaço de nomes ou uma ligação de híbrido que confira o direito de **ouvir** .

| renewToken mensagem                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Se a validação token falhar, acesso negado e serviço na nuvem irá fechar o websocket de canal de controlo com um erro, caso contrário não é sem resposta.

| Estado de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | O token de segurança expirou e a política de autorização, por isso, é suspenso. |

<a name="sender-protocol"></a>Protocolo de remetente
---------------

O protocolo de remetente é eficazmente idêntico ao modo como uma escuta é estabelecida. O objetivo é máximo de transparência para o socket de Web do fim para fim. O endereço para ligar à é a mesma escuta, mas "acção" difere e o token precisa de um permissões diferentes:

*wss: / / {espaço de nomes endereço} /* *$servicebus* */* *hybridconnection /**{caminho}? ação do hc sb =... e sb-hc-id =... \[& token de hc sbc =... \]*

O *espaço de nomes e endereços* é o nome de domínio completamente qualificado do espaço de nomes de reencaminhamento de Azure que aloja a ligação híbrido, normalmente do formulário {*myname}. servicebus.windows.net.*

O pedido pode conter arbitrários extra os cabeçalhos de HTTP, incluindo aquelas definidas pela aplicação. Todos os cabeçalhos fornecidos fluam para o serviço de escuta e podem ser encontrados no objeto "connectHeader" da mensagem de controlo "aceitar".

As opções de parâmetro da cadeia de consulta são os seguintes

| Param        | Obrigatório? | Descrição                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ação do hc SB | Sim       | Para a função escuta o parâmetro tem de ser **ação = ligar**                                                                                                                                                    |
| {caminho}       | Sim       | O caminho de URL codificada espaço de nomes da ligação híbrido pré-configurado para registar este escuta no.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| token de hc SB | Yes\*     | A escuta deve fornecer uma válido, URL codificada serviço Bus partilhado acesso Token para o espaço de nomes ou a ligação de híbrido que confira o direito de **Enviar** .                                                            | | id de hc SB | No        | Um ID opcional que permite que o rastreio diagnóstico ponto a ponto e é disponibilizado para o serviço de escuta durante o handshake aceitar.                                                                                       |

Se a ligação do Web Socket falhar devido ao caminho da ligação de híbrido não a ser registado, ou um token inválido ou em falta ou outro tipo de erro, o comentários do erro será fornecido utilizando o modelo de comentários de estado HTTP 1.1 normal. A descrição do estado irá conter um controlo-id de erro que pode ser comunicado para o suporte do Azure:

| Código | Erro          | Descrição                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Não foi encontrado      | O **caminho** de ligação híbrido é inválido ou o URL de base está mal |
| 401  | Não autorizado   | O token de segurança está em falta ou mal ou é inválido                  |
| 403  | Proibido      | Não é válido para este caminho para que esta ação do token de segurança          |
| 500  | Erro interno | Ocorreu um problema no serviço                                    |

Se a ligação do socket Web é encerrar intencionalmente pelo serviço após ter sido inicialmente definida para cima, a razão para fazê-lo comunicadas utilizando um adequado Web socket protocolo código de erro juntamente com uma mensagem de erro descritiva que também irá incluir um id de controlo.

| Estado de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | A escuta encerra o socket.                                                 |
| 1001      | O caminho da ligação de híbrido foi eliminado ou desativado.                           |
| 1008      | O token de segurança expirou e a política de autorização, por isso, é suspenso. |
| 1011      | Algo correu mal dentro o serviço.                                           |

## <a name="next-steps"></a>Passos seguintes:

- [Reencaminhamento perguntas mais frequentes](relay-faq.md)
- [Criar um espaço de nomes](relay-create-namespace-portal.md)
- [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)
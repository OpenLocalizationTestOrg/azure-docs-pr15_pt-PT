<properties 
    pageTitle="Descrição geral do modelo de autenticação e de segurança do evento concentradores | Microsoft Azure"
    description="Evento concentradores autenticação e segurança descrição geral do modelo."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Evento concentradores autenticação e segurança descrição geral do modelo

O modelo de segurança do evento concentradores cumpre os seguintes requisitos:

- Apenas os dispositivos que apresentarem credenciais válidas podem enviar dados a um concentrador de evento.
- Um dispositivo não é possível representar noutro dispositivo.
- Pode ser bloqueado um dispositivo rogue enviar dados a um concentrador de evento.

## <a name="device-authentication"></a>Autenticação do dispositivo

O modelo de segurança do evento concentradores baseia-se uma combinação de tokens de [Assinatura partilhadas do Access (SA)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) e *fabricantes de evento*. O publisher um evento define um ponto final de virtual para um concentrador de evento. O publisher só pode ser utilizado para enviar mensagens para um concentrador de evento. Não é possível receber mensagens de um fabricante.

Normalmente, um concentrador de evento utiliza um editor por dispositivo. Todas as mensagens que são enviadas a qualquer um dos fabricantes de um concentrador de evento são colocado em fila dentro desse concentrador de evento. Fabricantes de ativar o controlo de acesso extensivamente e limitação.

Cada dispositivo é atribuído um token exclusivo, que é carregado para o dispositivo. Os tokens são produzidos assim que cada token exclusivo concede o acesso a um fabricante exclusivo diferente. Um dispositivo que possua um token só pode enviar para um editor, mas não outros publisher. Se vários dispositivos partilharem o mesmo token, em seguida, cada um destes dispositivos partilha um fabricante.

Embora não recomendado, é possível equipamento dispositivos com tokens que conceder acesso direto a um concentrador de evento. Todos os dispositivos que detém isto token podem enviar mensagens diretamente concentrador esse evento. Como um dispositivo não será sujeito a limitação. Além disso, o dispositivo não pode ser na lista negra de enviar a esse evento concentrador.

Todos os tokens tem sessão iniciados com uma chave de SA. Normalmente, todos os tokens tem sessão iniciados com a mesma chave. Dispositivos não estão em mente a chave; Isto impede que dispositivos tokens de fabrico.

### <a name="create-the-sas-key"></a>Criar a chave de SA

Ao criar um espaço de nomes do evento concentradores, o Azure evento concentradores gera uma chave de SA 256 bits com o nome **RootManageSharedAccessKey**. Esta chave concede envia, ouvir e gerir direitos para o espaço de nomes. Pode criar teclas adicionais. É recomendado que produzir uma chave que concede envia permissões para o centro do evento específico. Para o resto neste tópico, é assumido que nomeadas esta tecla `EventHubSendKey`.

O exemplo seguinte cria uma chave só enviar durante a criação do concentrador de evento:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Gerar tokens

Pode gerar tokens de utilizar a chave de SA. Tem de produzir token de apenas uma por dispositivo. Em seguida, podem ser produzidos tokens utilizando o método seguinte. Todos os tokens são gerados utilizando a chave de **EventHubSendKey** . Cada token é atribuído um URI exclusivo.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Quando chamar este método, o URI deverá ser especificado como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos os tokens, o URI é idêntico, com excepção dos `PUBLISHER_NAME`, que devem ser diferente para cada token. Idealmente, `PUBLISHER_NAME` representa o ID do dispositivo que recebe esse token.

Este método gera um token com a seguinte estrutura:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

O tempo de expiração do token é especificado em segundos a partir de 1 de Janeiro de 1970. Segue-se um exemplo de um token de:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, os tokens têm uma esperança de vida que se assemelha ou excede esperança de vida do dispositivo. Se o dispositivo tiver a capacidade de obter um novo token, podem ser utilizados tokens com uma endereço mais curta esperança de vida.

### <a name="devices-sending-data"></a>Enviar dados de dispositivos

Assim que os tokens de tem sido criados, cada dispositivo está aprovisionado com as suas próprias token exclusivo.

Quando o dispositivo envia dados a um concentrador de evento, o dispositivo etiquetas respectivo token com o pedido de enviar. Para impedir que um intruso interceptem e retire as o token, a comunicação entre o dispositivo e o concentrador de evento deve ocorrer ao longo de um canal encriptado.

### <a name="blacklisting-devices"></a>Dispositivos confidenciais

Se um token for roubado pelo intruso, o intruso pode representar o dispositivo cujo token foi roubado. Blacklisting um dispositivo compõe o dispositivo inutilizável até que o dispositivo é dado um novo token de que utiliza um fabricante diferente.

## <a name="authentication-of-back-end-applications"></a>Autenticação de aplicações back-end

Para autenticar aplicações de back-end consumam os dados gerados por dispositivos, evento concentradores utiliza um modelo de segurança é semelhante ao modelo que é utilizado para tópicos Bus de serviço. Um grupo de consumidor do evento concentradores é equivalente a uma subscrição de um tópico Bus de serviço. Um cliente pode criar um grupo do consumidor se o pedido para criar o grupo do consumidor é acompanhar por um token que concede gerir privilégios para o concentrador de evento, ou para o espaço de nomes a que pertence o concentrador de evento. Um cliente é permitido consumir dados a partir de um grupo do consumidor se o pedido receber é acompanhar por um token que concede direitos de receção num grupo consumidor, centro do evento ou o espaço de nomes a que pertence o concentrador de evento.

A versão atual do serviço Bus não suporta SA regras para subscrições individuais. O mesmo se aplica para grupos do consumidor concentradores do evento. Suporte de SA será adicionado para ambas as funcionalidades no futuro.

Em caso de ausência de autenticação de SA para grupos do consumidor individual, pode utilizar teclas de SA para proteger todos os grupos do consumidor com uma chave de comuns. Esta abordagem permite que uma aplicação consumir dados a partir de qualquer um dos grupos consumidor de um concentrador de evento.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o evento concentradores, visite os seguintes tópicos:

- [Descrição geral de concentradores do evento]
- Uma [solução de mensagens em fila de espera] utilizando serviço Bus filas.
- Uma [aplicação de exemplo que utiliza o evento concentradores]concluída.

[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solução mensagens em fila de espera]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

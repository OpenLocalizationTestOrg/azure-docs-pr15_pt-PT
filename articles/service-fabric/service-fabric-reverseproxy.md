<properties
   pageTitle="Serviço de Proxy inverso ferro | Microsoft Azure"
   description="Utilizar o proxy inverso do serviço ferro para comunicação para microservices a partir de dentro e fora do cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Proxy inverso do serviço ferro

O serviço ferro inversa proxy é um ferro incorporados no serviço de proxy inverso que lhe permite endereçar microservices no cluster ferro serviço que expõem os pontos finais HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicação Microservices

Microservices no ferro de serviço, normalmente, execute um subconjunto do VM no cluster e pode mover de um VM para outro por diversas razões. Por isso, podem alterar dinamicamente os pontos finais para microservices. O normal padrão para comunicar com o microservice é o ciclo de resolver abaixo,

1. Resolva a localização do serviço inicialmente através do serviço de atribuição de nomes.
2. Ligar ao serviço.
3. Determinar a causa do falhas de ligação e voltar a resolver a localização do serviço quando for necessário.

Geralmente este processo envolve moldagem bibliotecas de comunicação do lado do cliente num ciclo de repetir implementa as políticas de resolução e volte a tentar de serviço.
Para mais informações sobre este tópico, consulte o artigo [comunicar com os serviços](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Comunicar através de proxy inverso IC
Serviço ferro proxy inverso é executado em todos os nós no cluster. -Executa o processo de resolução de todo o serviço em nome de um cliente e, em seguida, reencaminha o pedido do cliente. Por isso, clientes em execução no cluster só podem utilizar bibliotecas de comunicação de HTTP qualquer lado do cliente para falar com o serviço de destino através de proxy inverso IC a ser executado localmente no mesmo nó.

![Comunicação interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Chegar Microservices a partir de fora do cluster
O modelo de comunicação externa predefinido para microservices é **optar ativamente por participar no** local onde cada serviço por predefinição não pode ser acedido diretamente a partir de clientes externos. O [Azure Balanceador de carga](../load-balancer/load-balancer-overview.md) é um limite de rede entre microservices e clientes externos, que executa a tradução de endereços de rede e que reencaminha os pedidos externos para interno **IP: Port** pontos finais. Encaminhá-ponto final de uma microservice diretamente acessível a clientes externos, o Balanceador de carga Azure primeiro tem de ser configurado para reencaminhar tráfego para cada porta utilizada pelo serviço no cluster. Além disso, a maior parte dos microservices (especialmente com estado microservices) não live em todos os nós do cluster e pode deslocar-se entre nós em falha na ligação, por isso nestes casos, o Balanceador de carga Azure eficazmente não pode determinar o nó de destino das réplicas estão localizados para encaminhar o tráfego para.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Chegar Microservices através de fora do cluster, o proxy inverso IC

Em vez de configurar as portas de serviço individuais numa Balanceador de carga azure, apenas a porta do proxy inverso IC pode ser configurada de Balanceador de carga o Azure. Isto permite aos clientes fora do cluster aceder a serviços dentro do cluster através de proxy inverso sem uma configurações adicionais.

![Comunicação externa][0]

>[AZURE.WARNING] Configurar porta do proxy inverso no balanceador de carga, faz com que todos os serviços micro no cluster que expor um ponto final de http, para ser addressible a partir de fora do cluster.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Formato URI para endereçar serviços através de proxy inverso

O proxy inverso utiliza um formato URI específico para identificar qual partição de serviço, deverá ser reencaminhado o pedido de entrada:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http:** O proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Em caso de tráfego HTTPS, terminação SSL ocorre o proxy inverso. Pedidos de serem reencaminhados, o proxy inverso aos serviços no cluster são através de http.
 - **Cluster FQDN | IP internos:** Para clientes externos, o proxy inverso pode ser configurado para que seja acessível através do domínio cluster (por exemplo, mycluster.eastus.cloudapp.azure.com). Por predefinição que o proxy inverso é executado em todos os nós, por isso, para o tráfego interno-possa ser contactado localhost ou qualquer IP nó interna (por exemplo, 10.0.0.1).
 - **Porta:** A porta que foi especificada para o proxy inverso. Por ex: 19008.
 - **ServiceInstanceName:** Este é o nome da instância completamente qualificado implementado serviço do serviço que está a tentar entrar a "ferro: /" esquema. Por exemplo, para alcançar serviço *ferro: / myapp/Omeuserviço/*, utilizaria *myapp/Omeuserviço*.
 - **Caminho sufixo:** Este é o caminho real do URL para o serviço que pretende ligar. Por exemplo, *myapi/valores/Adicionar/3*
 - **PartitionKey:** Para um serviço com partições, esta é a chave de calculado partição da partição que pretende contactar. Tenha em atenção que este é *não* o ID GUID da partição. Este parâmetro não é necessário para serviços a utilizar o esquema de partição singleton.
 - **PartitionKind:** O esquema de partição de serviço. Isto pode ser 'Int64Range' ou 'Com o nome'. Este parâmetro não é necessário para serviços a utilizar o esquema de partição singleton.
 - **Tempo limite:**  Especifica o tempo limite para o pedido de http criado pelo proxy inverso ao serviço em nome do pedido do cliente. O valor predefinido é 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de utilização

Por exemplo, vamos serviço **ferro: / MyApp/Omeuserviço** que abre uma escuta HTTP no URL seguinte:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Com os seguintes recursos:

 - `/index.html`
 - `/api/users/<userId>`

Se o serviço utiliza o esquema de partições singleton, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* não são necessários e o serviço pode ser acedido através do gateway como:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - Internamente:`http://localhost:19008/MyApp/MyService`

Se o serviço utiliza o esquema de partições Int64 uniforme, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* tem de ser utilizados para atinja uma partição do serviço:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para chegar os recursos expostos pelo serviço, basta coloca o caminho de recurso depois do nome do serviço no URL:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway reencaminhará, em seguida, estes pedidos para o URL do serviço:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para a partilha de porta serviços

O Gateway aplicação tentativas para voltar a resolver um endereço de serviço e repetir o pedido quando não é possível alcançar um serviço. Este é um dos principais benefícios do gateway, tal como código do cliente não é necessário implementar a sua própria resolução de serviço e resolver ciclo.

Normalmente, quando um serviço não é possível alcançar significa que a instância do serviço ou réplica foi movido para um nó diferente como parte do seu ciclo de vida normal. Quando isto acontece, o gateway poderá receber um erro de ligação de rede que indica que um ponto final já não está aberto no endereço originalmente resolvido.

No entanto, réplicas ou instâncias do serviço, pode partilhar um processo de anfitrião e também pode partilhar uma porta quando alojados por um servidor web baseadas em sys, incluindo:

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [WebListener principais do ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Esta situação é provável que o servidor web está disponível no processo do anfitrião e responder a pedidos de mas a instância do serviço resolvido ou réplica já não está disponível no anfitrião do. Neste caso, o gateway irá receber uma resposta de HTTP 404 a partir do servidor web. Como resultado, um HTTP 404 tem dois significados distintos:

 1. O endereço de serviço está correto, mas não existe no recurso pedido pelo utilizador.
 2. O endereço de serviço está incorrecto e o recurso pedido pelo utilizador pode existir realmente num nó diferente.

No primeiro caso, este é um HTTP 404 normal, que é considerada um erro de utilizador. No entanto, no segundo caso, o utilizador solicitou um recurso que existam, mas o gateway não foi possível para localizá-lo porque o próprio serviço foi movido, caso em que o gateway tem de voltar a resolver o endereço e tente novamente.

O gateway, por conseguinte, necessitará de uma forma para distinguir entre estes dois casos. Para poder efetuar esse distinção, é necessária uma sugestão do servidor.

 - Por predefinição, o Gateway aplicação assume caso #2 e tenta resolver novamente e voltar a emitir o pedido.
 - Para indicar maiúsculas/minúsculas #1 para o Gateway de aplicação, o serviço deve devolver o cabeçalho de resposta HTTP seguinte:

`X-ServiceFabric : ResourceNotFound`

Este cabeçalho de resposta HTTP indica uma situação HTTP 404 normal, na qual o recurso pedido não existir, e o gateway não irá tentar resolver novamente o endereço de serviço.

## <a name="setup-and-configuration"></a>Instalação e configuração
Pode ser ativado o proxy inverso de ferro do serviço para o cluster através do [Gestor de recursos do Azure modelo](./service-fabric-cluster-creation-via-arm.md).

Assim que tiver o modelo para o cluster que pretende implementar (a partir de modelos de exemplo ou através da criação de um modelo de Gestor de recursos personalizadas), o proxy inverso pode ser ativado no modelo através dos seguintes passos.

1. Defina uma porta para o proxy inverso na [secção parâmetros](../resource-group-authoring-templates.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos tipo de nó na [secção tipo de recurso](../resource-group-authoring-templates.md) **Cluster**

    Para apiVersion antes de ' 2016-09-01' a porta está identificada pelo parâmetro nome ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    Para apiVersion no ou após ' 2016-09-01' a porta está identificada pelo parâmetro nome ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Para resolver o proxy inverso de fora do azure cluster, configurar as **regras do Balanceador de carga azure** para a porta especificada no passo 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Para configurar os certificados SSL na porta para o proxy inverso, adicionar o certificado para a propriedade httpApplicationGatewayCertificate na [secção tipo de recurso](../resource-group-authoring-templates.md) **Cluster**

    Para apiVersion antes de ' 2016-09-01' o certificado está identificado pelo parâmetro nome ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Para apiVersion no ou após ' 2016-09-01' o certificado está identificado pelo parâmetro nome ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Próximos passos
 - Ver um exemplo de comunicação de HTTP entre serviços num [projeto de exemplo no GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chamadas de procedimento remoto com remoto serviços fiáveis](service-fabric-reliable-services-communication-remoting.md)

 - [Web API que utiliza OWIN nos serviços do fiável](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicação WCF, utilizando os serviços fiável](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

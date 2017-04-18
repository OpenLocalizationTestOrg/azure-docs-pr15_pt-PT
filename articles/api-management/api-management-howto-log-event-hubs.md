<properties 
    pageTitle="Como iniciar sessão eventos Azure evento concentradores na gestão de API do Azure | Microsoft Azure" 
    description="Saiba como registar eventos Azure evento concentradores na gestão de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como iniciar sessão eventos Azure evento concentradores na gestão de API do Azure

Azure concentradores de evento é um serviço de penetração altamente dimensionáveis dados que pode ingerir esta última milhões de eventos por segundo para que possa processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. Evento concentradores age como "porta da frente" para uma pipeline de evento, e assim que os dados recolhidos a um concentrador de evento, podem ser transformado e armazenadas utilizando qualquer fornecedor de análise em tempo real ou adaptadores de lotes/armazenamento. Evento concentradores decouples produção de uma sequência de eventos do consumo desses eventos, para que os consumidores de evento possam aceder os eventos na sua própria agenda.

Este artigo é um Assistente para o vídeo de [Gestão de API de Azure integrar com evento concentradores](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descreve como registar eventos de gestão de API utilizando Azure evento concentradores.

## <a name="create-an-azure-event-hub"></a>Criar um concentrador de evento Azure

Para criar um novo concentrador de evento, início de sessão no [portal clássica Azure](https://manage.windowsazure.com) e clique em **Novo**->**Serviços de aplicação**->**Serviço Bus**->**Concentrador de evento**->**Criação rápida**. Introduza um nome de concentrador de evento, região, selecione uma subscrição e, selecione um espaço de nomes. Se ainda não tiver criado um espaço de nomes pode criar uma ao escrever um nome na caixa de texto **espaço de nomes** . Assim que estiverem configuradas todas as propriedades, clique em **criar um novo concentrador de evento** para criar o concentrador de evento.

![Criar concentrador de evento][create-event-hub]

Em seguida, navegue até ao separador **Configurar** para o seu novo Centro de evento e criar duas **políticas de acesso partilhado**. Atribua um nome a um primeiro **Enviar** e dar-lhe permissões **Enviar** .

![Política de envio][sending-policy]

Nome o segundo a **receber**, atribua-lhe permissões **ouvir** e clique em **Guardar**.

![Política de receção][receiving-policy]

Cada política de acesso partilhado permite que as aplicações enviar e receber eventos para e a partir do centro do evento. Para aceder às cadeias de ligação para estas políticas, navegue para o separador **Dashboard** do centro do evento e clique em **informações de ligação**.

![Cadeia de ligação][event-hub-dashboard]

A cadeia de ligação de **envio** é utilizada quando o registo de eventos e a cadeia de ligação **receber** é utilizada quando eventos a ser transferidas a partir do centro do evento.

![Cadeia de ligação][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Criar um registo de gestão de API

Agora que tem um concentrador de evento, o próximo passo é configurar um [registo](https://msdn.microsoft.com/library/azure/mt592020.aspx) no seu serviço de gestão de API para que-pode iniciar sessão eventos centro do evento.

Combinações de teclas de gestão de API são configuradas utilizando a [Gestão de API REST API](http://aka.ms/smapi). Antes de utilizar a API REST pela primeira vez, reveja os [Pré-requisitos](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) e certifique-se de que tem [com permissão para aceder à REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Para criar um registo, certifique um pedido de HTTP colocar utilizando o modelo de URL seguinte.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Substituir `{your service}` com o nome do seu instância do serviço de gestão de API.
-   Substituir `{new logger name}` com o nome pretendido para o seu novo registo. Irá referenciar este nome quando configurar a política de [registo para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Adicione os seguintes cabeçalhos para o pedido.

-   Tipo de conteúdo: aplicação/json
-   Autorização: SharedAccessSignature uid =...
    -   Para obter instruções sobre a gerar o `SharedAccessSignature` consulte [Azure API gestão REST API autenticação](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Especifique o corpo do pedido utilizando o modelo seguinte.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`tem de estar definida `AzureEventHub`.
-   `description`Fornece uma descrição opcional do registo e pode ser uma cadeia de comprimento zero, se pretender.
-   `credentials`contém o `name` e `connectionString` do seu centro de evento Azure.

Quando se o pedido, se o registo é criado um código de estado de `201 Created` é devolvido. 

>[AZURE.NOTE] Para outros códigos de retorno possíveis e os respetivos razões, consulte o artigo [criar um registo](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Para ver como executar outras operações, tais como da lista, atualizar e eliminar, consulte a documentação de entidade de [registo](https://msdn.microsoft.com/library/azure/mt592020.aspx) .

## <a name="configure-log-to-eventhubs-policies"></a>Configurar políticas de registo para eventhubs

Assim que o registo estiver configurado na gestão de API, pode configurar as políticas de registo para eventhubs para iniciar sessão os eventos pretendidos. A política de registo para eventhubs pode ser utilizada na secção de política de entrada ou a secção política de saída.

Para configurar políticas, inicie sessão no [portal clássica Azure](https://manage.windowsazure.com), navegue seu serviço de gestão de API e clique em **publisher portal** ou **Gerir** para aceder ao portal do publisher.

![Portal do Publisher][publisher-portal]

Clique em **políticas** no menu de gestão de API à esquerda, selecione o produto pretendido e API e clique em **Adicionar política**. Neste exemplo estamos estiver a adicionar uma política à **API eco** no produto **ilimitado** .

![Adicionar política][add-policy]

Posicione o cursor na `inbound` política de secção e clique na política de **registo para EventHub** para inserir o `log-to-eventhub` modelo da declaração de política.

![Editor de políticas][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Substituir `logger-id` com o nome do registo de gestão de API configurado no passo anterior.

Pode utilizar qualquer expressão que devolve uma cadeia como o valor para o `log-to-eventhub` elemento. Neste exemplo tem sessão iniciada uma cadeia que contém a data e hora, nome do serviço, id de pedido, endereço de ip do pedido e o nome da operação.

Clique em **Guardar** para guardar a configuração de política actualizada. Assim que é guardado a política está ativa e eventos são registados ao designada concentrador de evento.

## <a name="next-steps"></a>Próximos passos

-   Saiba mais sobre Azure evento concentradores
    -   [Introdução ao Azure evento concentradores](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Guia de programação de concentradores de evento](../event-hubs/event-hubs-programming-guide.md)
-   Saiba mais sobre a integração de gestão de API e concentradores de evento
    -   [Referência da entidade de registo](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [referência da política de registo para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Monitorizar o seu APIs com gestão de Azure API, evento concentradores e Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Veja uma vídeo instruções passo a passo

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png







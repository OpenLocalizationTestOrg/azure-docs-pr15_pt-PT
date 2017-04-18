<properties
    pageTitle="Volte a tentar lógica no SDK de serviços de multimédia para .NET | Microsoft Azure"
    description="O tópico fornece uma descrição geral de lógica de repetição no SDK de serviços de multimédia para .NET."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Volte a tentar lógica no SDK de serviços de multimédia para .NET

Quando trabalha com os serviços do Microsoft Azure, falhas breves podem ocorrer. Se ocorre uma falha de breves, na maioria dos casos, após várias tentativas alguns a operação é concluída com êxito. O SDK de serviços de multimédia para .NET implementa a lógica de repetição para processar falhas breves associadas com exceções e erros que problemas são causados pela pedidos da web, executar consultas, guardar alterações e operações de armazenamento.  Por predefinição, o SDK de serviços de multimédia para .NET executa repetições de quatro antes de voltar a deitar exceção à aplicação. O código na sua aplicação, em seguida, deve processar esta exceção corretamente.  
  
 Segue-se uma breve a orientação das políticas de pedido Web, armazenamento, consulta e SaveChanges:  
  
-   A política de armazenamento é utilizada para operações de armazenamento de BLOBs (carregamentos ou transferências de ficheiros de elementos).  
  
-   A política de pedido Web é utilizada para pedidos de web genérico (por exemplo, para obter um token de autenticação e resolver o ponto final de cluster de utilizadores).  
  
-   A política de consulta é utilizada para consultar entidades de resto (por exemplo, mediaContext.Assets.Where(...)).  
  
-   A política de SaveChanges é utilizada para fazer nada que muda dados dentro do serviço (por exemplo, a criação de uma entidade atualizar uma entidade, chamar uma função de serviço para uma operação).  
  
 Este tópico apresenta os tipos de exceção e códigos de erro que são processados pelo SDK de serviços de multimédia para .NET a lógica de repetição.  
  
## <a name="exception-types"></a>Tipos de exceção  

A tabela seguinte descreve as exceções que o SDK de serviços de multimédia para .NET trata ou não processar para algumas operações que podem causar falhas breves.  
  
Exceção|Pedido de Web|Armazenamento|Consulta|SaveChanges
----|------|----|---|---
WebException<br/>Para mais informações, consulte a secção [WebException os códigos de estado](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Sim|Sim|Sim|Sim  
DataServiceClientException<br/> Para mais informações, consulte o artigo [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N|Sim|Sim|Sim
DataServiceQueryException<br/> Para mais informações, consulte o artigo [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N|Sim|Sim|Sim  
DataServiceRequestException<br/> Para mais informações, consulte o artigo [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N|Sim|Sim|Sim  
DataServiceTransportException|N|N|Sim|Sim
TimeoutException|Sim|Sim|Sim|N
SocketException|Sim|Sim|Sim|Sim  
StorageException|N|Sim|N|N 
IOException|N|Sim|N|N
  
###  <a name="WebExceptionStatus"></a>Códigos de estado WebException  

A tabela seguinte mostra quais WebException para códigos de erro a lógica de repetição é implementada. A enumeração [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define os códigos de estado.  
  
Estado|Pedido de Web|Armazenamento|Consulta|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|Sim|Sim|Sim|Sim
NameResolutionFailure|Sim|Sim|Sim|Sim  
ProxyNameResolutionFailure|Sim|Sim|Sim|Sim  
SendFailure|Sim|Sim|Sim|Sim
PipelineFailure|Sim|Sim|Sim|N  
ConnectionClosed|Sim|Sim|Sim|N  
KeepAliveFailure|Sim|Sim|Sim|N  
UnknownError|Sim|Sim|Sim|N 
ReceiveFailure|Sim|Sim|Sim|N  
RequestCanceled|Sim|Sim|Sim|N  
Tempo limite|Sim|Sim|Sim|N
ProtocolError <br/>Repetir num ProtocolError é controlada pelo processamento de código de estado HTTP. Para mais informações, consulte o artigo [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Sim|Sim|Sim|Sim|  
  
###  <a name="HTTPStatusCode"></a>Códigos de estado de erro HTTP  

Quando operações de consulta e SaveChanges accionar DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, é devolvido o código de estado de erro HTTP na propriedade StatusCode.  A tabela seguinte mostra quais para códigos de erro a lógica de repetição é implementada.  
  
 
Estado|Pedido de Web|Armazenamento|Consulta|SaveChanges 
---|----|----|----|----
401|N|Sim|N|N
403|N|Sim<br/>Número de tentativas de processamento com aguarda mais longa.|N|N  
408|Sim|Sim|Sim|Sim
429|Sim|Sim|Sim|Sim  
500|Sim|Sim|Sim|N  
502|Sim|Sim|Sim|N  
503|Sim|Sim|Sim|Sim  
504|Sim|Sim|Sim|N  
  
Caso pretenda retirar um olhar sobre a execução real do SDK de serviços de multimédia para .NET a lógica de repetição, consulte [sdk de azure para serviços multimédia](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

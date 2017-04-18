<properties 
    pageTitle="Referência de política de gestão de Azure API" 
    description="Saiba mais sobre as políticas que foram disponíveis para configurar a gestão de API." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Referência de política de gestão de Azure API

Esta secção fornece um índice remissivo para as políticas que foram na [referência da política de gestão de API][]. Para obter informações sobre como adicionar e configurar políticas, consulte o artigo [políticas de gestão de API no][].

Expressões de política podem ser utilizados como valores de atributo ou valores de texto em qualquer uma das políticas de gestão de API, a menos que a política caso contrário, especifica. Algumas políticas, tais como as políticas de [fluxo de controlo][] e [Definir variável][] são baseadas em expressões de política. Para obter mais informações, consulte o artigo [Avançadas políticas][] e [expressões de política][]

## <a name="policy-reference-index"></a>Índice de referência da política

-   [Políticas de restrição de acesso][]
    -   [Cabeçalho de verificar HTTP][] - impõe existência e/ou o valor de um cabeçalho de HTTP.
    -   [Taxas de chamada de limite através de uma subscrição][] - utilização de API impede pontas ao limitar taxas de chamada, numa base por subscrição.
    -   [Taxas de chamada de limite pela chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - utilização de API impede pontas ao limitar taxas de chamada, numa base de chave por.
    -   [Restringir autor IPs][] - filtros (permite/recusa) chamadas a partir de endereços IP específicos e/ou intervalos de endereços.
    -   [Definir quota de utilização através de uma subscrição][] - permite-lhe para impor a uma ou não duração da chamada de volume e/ou a largura de banda quota, numa base por subscrição.
    -   [Definir quota de utilização por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - permite-lhe para impor a uma ou não duração da chamada de volume e/ou a largura de banda quota, numa base de chave por.
    -   [Validar JWT][] - impõe existência e validade de um JWT extraída de um cabeçalho de HTTP especificado ou um parâmetro de consulta especificada.
-   [Políticas de avançadas][]
    -   [Fluxo de controlo][] - condicionalmente aplica-se com base nos resultados da avaliação de booleanas [expressões][]de declarações de política.
    -   [Reencaminhar pedidos][] - reencaminha o pedido para o serviço de back-end.
    -   [Registo de evento concentrador][] - envia mensagens no formato especificado para uma mensagem de destino definido por uma entidade de [registo](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
    -   [Volte a tentar](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - repetições de execução das declarações política incluído, se e até que a condição é cumprida. Execução irá repetir nos intervalos de tempo especificado e por excesso para o especificado Repetir contagem.
    -   [Devolver resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - anula execução de tubagem e devolve a resposta especificada diretamente para o autor da chamada.
    -   [Enviar pedido de uma forma](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - envia um pedido para o URL especificado sem aguardar uma resposta.
    -   [Enviar pedido](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - envia um pedido para o URL especificado.
    -   [Método de pedido de set](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - permite-lhe alterar o método para um pedido de HTTP.
    -   [Definir o estado](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - altera o código de estado HTTP para o valor especificado.
    -   [Definir variável][] - persistirem um valor numa variável com nome [contexto][] para um acesso mais tarde.
    -   [Rastreio](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - adiciona uma cadeia para o resultado da [API Inspector](../api-management/api-management-howto-api-inspector.md) .
    -   [Aguarde](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - aguarda fechado enviar pedido, o valor de obter a partir de cache ou políticas de fluxo de controlo para concluir antes de continuar.
-   [Políticas de autenticação][]
    -   [Autenticar com Basic][] - autenticar com um serviço de back-end utilizando a autenticação básica.
    -   [Autenticação com o certificado de cliente][] - autenticar com um serviço de back-end utilizando os certificados de cliente.
-   [Políticas de colocação em cache][] 
    -   [Obter da cache][] - executar cache procurar e devolver uma resposta em cache válida quando se encontra disponível.
    -   [Arquivo de cache][] – resposta Caches de acordo com a configuração de controlo de cache especificado.
    -   [Obter o valor da cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - recuperar um item em cache pela chave.
    -   [Valor de arquivo na cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - arquivo de um item na cache pela chave.
    -   [Remover o valor da cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - remover um item na cache pela chave.
-   [Cruzada políticas de domínio][] 
    -   [Permitir domínios chamadas][] - faz com que a API acessíveis a partir dos clientes Adobe Flash e Microsoft Silverlight baseada no browser.
    -   [CORS][] - adiciona partilha (CORS) suporte para uma operação ou uma API para permitir que as chamadas de domínios a partir dos clientes baseada no browser e de recursos de publicação em origem.
    -   [JSONP][] - adiciona JSON com preenchimento (JSONP) suporte para uma operação ou uma API para permitir que as chamadas de domínios a partir de JavaScript baseada no browser e clientes.
-   [Políticas de transformação][] 
    -   [Converter JSON para XML][] - converte pedido ou uma resposta do corpo de JSON para XML.
    -   [Converter XML para JSON][] - converte pedido ou uma resposta do corpo de XML para JSON.
    -   [Localizar e substituir cadeia no corpo][] - encontra uma subcadeia pedido ou uma resposta e substitui-la por uma subcadeia diferente.
    -   [URLs de máscaras de introdução no conteúdo][] - novamente escreve (máscaras) ligações na resposta do corpo, para que apontem para a ligação equivalente através do gateway.
    -   [Configurar o serviço de back-end][] - altera o serviço de back-end para um pedido de entrada.
    -   [Definir o corpo][] - define o corpo da mensagem para pedidos de receção e enviados.
    -   [Cabeçalho de HTTP definir][] - atribui um valor para uma resposta existente e/ou cabeçalho pedido ou adiciona um cabeçalho de resposta e/ou pedido de novo.
    -   [Definir o parâmetro da cadeia de consulta][] - adiciona, substitui o valor de ou elimina pedido de parâmetro da cadeia de consulta.
    -   [Reescrita de URL][] - converte um URL de pedido a partir do seu formulário público para o formulário esperado pelo serviço web.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre expressões de política, consulte o vídeo seguinte.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Políticas de restrição de acesso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Cabeçalho de verificação HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Taxas de chamada de limite através de uma subscrição]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restringir o autor da chamada IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Definir a quota de utilização através de uma subscrição]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validar JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Políticas de avançadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Fluxo de controlo]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Definir a variável de]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressões]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexto]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Reencaminhar pedido]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Registo a concentrador de evento]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Políticas de autenticação]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Autenticar com Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Autenticar com certificado de cliente]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Políticas de colocação em cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obter da cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Armazenar a colocar em cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cruzada políticas de domínio]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permitir que as chamadas de domínios]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Políticas de transformação]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Converter JSON XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Converter XML JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Localizar e substituir cadeia no corpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[URLs de máscaras de introdução no conteúdo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Definir o serviço de back-end]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Conjunto de corpo]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Cabeçalho de HTTP conjunto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Conjunto de parâmetro da cadeia de consulta]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Reescrita de URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Políticas de gestão de API no]: api-management-howto-policies.md
[Referência da política de gestão de API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expressões de política]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

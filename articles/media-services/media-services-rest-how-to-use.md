<properties 
    pageTitle="Descrição geral de serviços de multimédia REST API | Microsoft Azure" 
    description="Descrição geral de serviços de multimédia REST API" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Descrição geral de serviços de multimédia REST API 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Serviços de multimédia do Microsoft Azure é um serviço que aceita pedidos de HTTP com base em OData e pode responder novamente na verboso JSON ou atom + pub. Uma vez que está em conformidade com diretrizes de estrutura Azure dos serviços de multimédia, existe um conjunto de cabeçalhos de HTTP necessários que cada cliente tem de utilizar quando se liga dos serviços de multimédia, bem como um conjunto de cabeçalhos opcionais que pode ser utilizado. As secções seguintes descrevem os cabeçalhos e verbos HTTP, pode utilizar quando criar os pedidos e receber as respostas de serviços de multimédia.

##<a name="considerations"></a>Considerações sobre 

As seguintes considerações aplicam-se ao utilizar os restantes.

- Quando consultar entidades, existe um limite de 1000 entidades devolvida uma só vez porque público resto v2 limita os resultados da consulta para os resultados de 1000. Tem de utilizar **Ignorar** e **tomar** (.NET) / **início** (REST) como descrito neste [exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [Este exemplo REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Quando utilizar JSON e especificar para utilizar a palavra-chave **__metadata** no pedido de (por exemplo, para faz referência a um objeto ligado) tem de definir o cabeçalho **Aceitar** para o [formato JSON verboso](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (consulte o exemplo seguinte). OData não compreender a propriedade **__metadata** no pedido, a menos que defina-o para verboso.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>Cabeçalhos de pedido HTTP padrão suportados dos serviços de multimédia

Para cada chamada que transformar dos serviços de multimédia, existe um conjunto de cabeçalhos necessários, que tem de incluir no seu pedido e também um conjunto de cabeçalhos opcionais que poderá pretende incluir. A tabela seguinte lista os cabeçalhos necessários:


Cabeçalho|Tipo|Valor
---|---|---
Autorização|Tipo de ligação|Tipo de ligação é o mecanismo apenas autorização aceite. O valor também tem de incluir o token de acesso fornecido pelo ACS.
versão da ms de x|Decimal|2.11
DataServiceVersion|Decimal|3.0
MaxDataServiceVersion|Decimal|3.0



>[AZURE.NOTE] Visto que dos serviços de multimédia utiliza OData para expor respectivo repositório de metadados elementos subjacentes através de API REST, os cabeçalhos de DataServiceVersion e MaxDataServiceVersion deverão ser incluídos na qualquer pedido; No entanto, se não estiverem, em seguida, atualmente dos serviços de multimédia assume que o valor de DataServiceVersion utilizado é 3.0.

Segue-se um conjunto de cabeçalhos opcionais:

Cabeçalho|Tipo|Valor
---|---|---
Data|Data de RFC 1123|Carimbo de data/hora do pedido de
Aceitar|Tipo de conteúdo|O tipo de conteúdo pedido para a resposta tal como o seguinte:<p> -aplicação/json; odata = verboso<p> -aplicação/atom + xml<p> As respostas poderão ter um tipo de conteúdo diferentes, tais como a obtenção de BLOBs, onde uma resposta com êxito irá conter a sequência de BLOBs como a carga de útil.
Codificação aceitar|Gzip, esvaziar|GZIP e DEFLATE codificação, quando aplicável. Nota: Para obter recursos grandes, dos serviços de multimédia pode ignorar este cabeçalho e devolver dados não comprimidos.
Idioma aceitar|"pt", "ãs" e assim sucessivamente.|Especifica o idioma preferido para a resposta.
Conjunto de caracteres aceitar|Tipo de conjunto de caracteres como "UTF-8"|A predefinição é UTF-8.
Método de HTTP X|Método de HTTP|Permite que clientes ou firewalls que não suportam métodos HTTP como colocar ou eliminar para utilizar estes métodos, túneis através de uma chamada de obter.
Tipo de conteúdo|Tipo de conteúdo|Pedidos de tipo de conteúdo de corpo do pedido numa mensagem ou local.
id de pedido de cliente|Cadeia|Um valor definido pelo autor da chamada que identifica o pedido indicado. Se especificado, este valor será incluído na mensagem de resposta de forma para mapear o pedido. <p><p>**Importante**<p>Valores devem ser limitados a 2096b (2k).

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta padrão do HTTP suportados dos serviços de multimédia

Segue-se um conjunto de cabeçalhos que podem ser devolvidos para si, dependendo do recurso que foram pedir e a ação que pretendia para efetuar.


Cabeçalho|Tipo|Valor
---|---|---
id de pedido|Cadeia|Um identificador exclusivo para o funcionamento atual, serviço gerado.
id de pedido de cliente|Cadeia|Um identificador especificado pelo autor da chamada no pedido de original, se existir.
Data|Data de RFC 1123|A data em que o pedido foi processado.
Tipo de conteúdo|Varia|O tipo de conteúdo do corpo da resposta.
Codificação de conteúdo|Varia|Gzip ou esvaziar, conforme adequado.


## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP padrão suportados dos serviços de multimédia

Segue-se uma lista completa dos verbos HTTP que podem ser utilizadas quando os pedidos de efetuar HTTP:


Verbais|Descrição
---|---
OBTER|Devolve o valor atual de um objeto.
MENSAGEM|Cria um objeto com base nos dados fornecidos ou submete um comando.
COLOCAR|Substitui um objeto ou cria um objeto com nome (quando aplicável).
ELIMINAR|Elimina um objeto.
IMPRESSÃO EM SÉRIE|Atualiza um objecto existente com alterações da propriedade com nome.
CABEÇA|Devolve os metadados de um objeto para obter uma resposta de obter.

##<a name="limitation"></a>Limitação

Quando consultar entidades, existe um limite de 1000 entidades devolvida uma só vez porque público resto v2 limita os resultados da consulta para os resultados de 1000. Tem de utilizar **Ignorar** e **tomar** (.NET) / **início** (REST) como descrito neste [exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [Este exemplo REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Descobrir o modelo de serviços de multimédia

Para tornar o entidades dos serviços de multimédia mais detetável, a operação de $metadata pode ser utilizada. Permite-lhe obter todos os tipos de entidade válido, propriedades entidade, as associações, funções, ações e assim sucessivamente. O exemplo seguinte mostra como construir o URI: https://media.windows.net/API/$ metadados.

Deve acrescentar "? api version=2.x" para o fim do URI se pretende ver os metadados num browser ou não inclui o cabeçalho x-ms-versão no seu pedido.



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 

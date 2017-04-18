<properties 
    pageTitle="Como criar um processador de multimédia | Microsoft Azure" 
    description="Saiba como criar um componente de processador de multimédia para codificar, converter o formato, encriptar ou desencriptar conteúdo multimédia para serviços de multimédia do Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Como: obter uma instância de processador de multimédia


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTO](media-services-rest-get-media-processor.md)

##<a name="overview"></a>Descrição geral

Formate dos serviços de multimédia que um processador de multimédia é um componente que se trata de uma tarefa de processamento específico, tal como codificação, conversão, encriptar ou desencriptar conteúdo multimédia. Normalmente, criar um processador de multimédia quando estiver a criar uma tarefa para codificar, encriptar ou converter o formato de conteúdo de multimédia.

A tabela seguinte fornece o nome e descrição de cada processador de multimédia disponíveis.

Nome de processador de multimédia|Descrição|Obter mais informações
---|---|---
Padrão de descodificador de multimédia|Fornece capacidades de padrão para codificação a pedido. |[Descrição geral e comparação do Azure no pedido codificadores de multimédia](media-services-encode-asset.md)
Fluxo de trabalho do Media Encoder Premium|Permite-lhe executar as tarefas codificação utilizando o fluxo de trabalho do Media Encoder Premium.|[Descrição geral e comparação do Azure no pedido codificadores de multimédia](media-services-encode-asset.md)
Indexador de multimédia do Microsoft Azure| Permite-lhe tornar os ficheiros de multimédia e conteúdo pesquisável, bem como gerar faixas legendas fechadas e palavras-chave.|[Indexador de multimédia do Microsoft Azure](media-services-index-content.md)
Azure multimédia Hyperlapse (pré-visualização)|Permite-lhe lisa saída "isentos" no seu vídeo com estabilização vídeo. Também lhe permite acelerar o seu conteúdo para um clip consumíveis.|[Hyperlapse de multimédia do Microsoft Azure](media-services-hyperlapse-content.md)
Descodificador de multimédia do Microsoft Azure|Amortizado
Armazenamento desencriptação| Amortizado|
Gestor de multimédia do Microsoft Azure|Amortizado|
EncDec de multimédia do Microsoft Azure|Amortizado|

##<a name="get-mediaprocessor"></a>Obter MediaProcessor

>[AZURE.NOTE] Quando trabalha com os serviços de multimédia REST API, aplicam-se as seguintes considerações:
>
>Ao aceder ao entidades nos serviços de multimédia, tem de definir os campos de cabeçalho específico e valores nos pedidos de HTTP. Para obter mais informações, consulte [configuração para o desenvolvimento de API de resto de serviços de multimédia](media-services-rest-how-to-use.md).

>Após com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI tal como descrito em [para ligar ao utilizar a REST API dos serviços de multimédia](media-services-rest-connect-programmatically.md). 


A chamada resto seguinte mostra como obter uma instância de processador de multimédia pelo nome (neste caso, **Media Encoder padrão**). 



    
Pedido:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Resposta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Próximos passos

Agora que sabe como obter uma instância de processador de multimédia, ir para o tópico de [como codificar um ativo](media-services-rest-get-started.md) que lhe mostrará como utilizar o padrão de descodificador de multimédia para descodificar um ativo.

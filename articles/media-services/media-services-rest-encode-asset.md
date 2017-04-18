<properties 
    pageTitle="Como codificar um ativo utilizando Media Encoder padrão | Microsoft Azure" 
    description="Saiba como utilizar o padrão de descodificador de multimédia para codificar o conteúdo de multimédia dos serviços de multimédia. Exemplos de código utilizam REST API." 
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Como codificar um ativo utilizando Media Encoder padrão


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [RESTO](media-services-rest-encode-asset.md)
- [Portal](media-services-portal-encode.md)

##<a name="overview"></a>Descrição geral
Para fornecer vídeo digital na internet tem comprimir ficheiros multimédia. Ficheiros de vídeo digital são bastante grandes e poderão ser demasiado grandes para a prestação através da internet ou para dispositivos dos seus clientes apresentar corretamente. Codificação é o processo de compressão de vídeo e áudio para que os seus clientes possam ver os ficheiros de multimédia.

Codificação as tarefas são uma das operações de transformação mais comuns dos serviços de multimédia. Criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Quando codificar, pode utilizar o codificador incorporado dos serviços de multimédia (Media Encoder padrão). Também pode utilizar um codificador fornecido por um parceiro dos serviços de multimédia; codificadores de terceiros estão disponíveis através do Azure Marketplace. Pode especificar os detalhes de codificação de tarefas ao utilizar cadeias predefinidas definidas para o seu codificador ou utilizando a ficheiros de configuração predefinido. Para ver os tipos de predefinições que estão disponíveis, consulte o artigo [Predefinições de tarefa para Media Encoder padrão](http://msdn.microsoft.com/library/mt269960).

Cada tarefa pode ter uma ou mais tarefas dependendo do tipo de processamento que pretende efetuar. Através da API REST, pode criar tarefas e as suas tarefas relacionadas numa de duas formas:

- Tarefas podem ser definido inline através da propriedade de navegação de tarefas em entidades de tarefa, ou
- através de processamento batch de OData.


Recomenda-se para codificar sempre os ficheiros de mezzanine para uma velocidade ajustável MP4 definir e, em seguida, converter o conjunto para o formato pretendido através da [Embalagem dinâmicos](media-services-dynamic-packaging-overview.md). Para tirar partido da embalagem dinâmico, terá de obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [como escala dos serviços de multimédia](media-services-portal-manage-streaming-endpoints.md).

Se a elementos de saída armazenamento encriptado, tem de configurar políticas de entrega de elementos. Para obter mais informações consulte o artigo [política de entrega de elementos de configurar](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Antes de começar a referenciar processadores de multimédia, certifique-se de que tem o suporte correcto ID processador. Para mais informações, consulte o artigo [Obter processadores de multimédia](media-services-rest-get-media-processor.md).

##<a name="create-a-job-with-a-single-encoding-task"></a>Criar uma tarefa com uma única tarefa codificação

>[AZURE.NOTE] Quando trabalha com os serviços de multimédia REST API, aplicam-se as seguintes considerações:
>
>Ao aceder ao entidades nos serviços de multimédia, tem de definir os campos de cabeçalho específico e valores nos pedidos de HTTP. Para obter mais informações, consulte [configuração para o desenvolvimento de API de resto de serviços de multimédia](media-services-rest-how-to-use.md).

>Após com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI tal como descrito em [para ligar ao utilizar a REST API dos serviços de multimédia](media-services-rest-connect-programmatically.md).
>
>Quando utilizar JSON e especificar para utilizar a palavra-chave **__metadata** no pedido de (por exemplo, para faz referência a um objeto ligado) tem de definir o cabeçalho **Aceitar** para o [formato JSON verboso](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): aceitar: aplicação/json; odata = verboso.

O exemplo seguinte mostra-lhe como criar e publicar uma tarefa com um que conjunto de tarefas para codificar um vídeo a uma resolução específica e qualidade. Quando a codificação com Media Encoder padrão, pode utilizar predefinições de configuração de tarefa especificadas [aqui](http://msdn.microsoft.com/library/mt269960).

Pedido:

MENSAGEM https://media.windows.net/API/Jobs HTTP/1.1-tipo de conteúdo: aplicação/json; odata = aceitar verboso: aplicação/json; odata = DataServiceVersion verboso: 3.0 MaxDataServiceVersion: 3.0 x-ms-versão: autorização 2.11: portadores <token value> 
 x-ms-cliente-pedido-id: 00000000-0000-0000-0000-000000000000 anfitrião: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Defina o nome do elemento de saída

O exemplo seguinte mostra como definir o atributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Considerações sobre

- Propriedades de TaskBody tem de utilizar literal XML para definir o número de entrada ou saída de activos que serão utilizados pela tarefa. O tópico da tarefa contém a definição de esquema XML para o XML.
- Na definição do TaskBody, cada interiores valor do <inputAsset> e <outputAsset> tem de estar definida como JobInputAsset(value) ou JobOutputAsset(value).
- Uma tarefa pode ter vários elementos de saída. Um JobOutputAsset(x) só pode ser utilizado uma vez como um resultado de uma tarefa numa tarefa.
- Pode especificar JobInputAsset ou JobOutputAsset como um teclado ativo de uma tarefa.
- Tarefas não tem a formar um ciclo.
- O parâmetro do valor transmitir JobInputAsset ou JobOutputAsset representa o valor de índice de um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets sobre a definição da entidade de tarefa. 
- Uma vez que dos serviços de multimédia é criada com base em OData v3, os ativos individuais InputMediaAssets e OutputMediaAssets coleções de propriedade navegação são referenciados através de um "__metadata: uri" par valor do nome.
- Conjunto mapas do InputMediaAssets para um ou mais elementos de ter criado dos serviços de multimédia. OutputMediaAssets são criados pelo sistema. Não faça referência a um ativo existente.
- OutputMediaAssets pode ter o nome utilizando o atributo assetName. Se não existe este atributo, em seguida, o nome do OutputMediaAsset será independentemente do valor de texto interna do <outputAsset> elemento for com um sufixo do valor do nome da tarefa ou o valor de Id da tarefa (neste caso onde não é definida a propriedade nome). Por exemplo, se definir um valor para assetName para "Exemplo", em seguida, a propriedade nome do OutputMediaAsset seria definida para "Exemplo". No entanto, se não tiver definido um valor para assetName, mas tiver definido o nome da tarefa para "NewJob", em seguida, o nome de OutputMediaAsset seria "JobOutputAsset (valor) _NewJob". 


##<a name="create-a-job-with-chained-tasks"></a>Criar uma tarefa com tarefas em cadeia

Em muitos cenários de aplicação, os programadores pretende criar uma série de processamento de tarefas. Serviços de multimédia, pode criar uma série de tarefas em cadeia. Cada tarefa executa os passos de processamento de diferentes e pode utilizar processadores de suporte de dados diferente. As tarefas em cadeia podem entregar um ativo a partir de uma tarefa para outro, efetuar uma sequência linear de tarefas no elemento. No entanto, as tarefas executadas numa tarefa não têm de ser numa sequência. Quando cria uma tarefa em cadeia, os objetos **ITask** interligados são criados num único objeto **in** .

>[AZURE.NOTE] Atualmente existe um limite de 30 tarefas por tarefa. Se precisar de mais de 30 tarefas em cadeia, crie mais do que uma tarefa para conter as tarefas.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Considerações sobre

Para ativar a interligação de tarefa:

- Uma tarefa tem de ter, pelo menos, 2 tarefas
- Tem de ter, pelo menos, uma tarefa cuja entrada é saída do outra tarefa da tarefa.

## <a name="use-odata-batch-processing"></a>Utilizar o processamento batch de OData 

O exemplo seguinte mostra como utilizar o processamento batch de OData para criar uma tarefa e de tarefas. Para obter informações sobre o processamento batch, consulte o artigo [Processamento de lote Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Criar uma tarefa utilizando um JobTemplate


Quando está a processar vários elementos utilizando um conjunto comum de tarefas, JobTemplates são úteis para especificar as predefinições de tarefa predefinida, ordem das tarefas e assim sucessivamente.

O exemplo seguinte mostra como criar um JobTemplate com uma inline TaskTemplate definido. O TaskTemplate utiliza o padrão de descodificador de multimédia como o MediaProcessor para codificar o ficheiro ativo; No entanto, outros MediaProcessors podem ser utilizadas também. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Ao contrário de outras entidades dos serviços de multimédia, tem de definir um identificador GUID novo para cada TaskTemplate e colocá-la taskTemplateId e propriedade Id no seu corpo do pedido. O esquema de identificação conteúdo tem de seguir o esquema, descrito nas identificar entidades de serviços de multimédia do Azure. Além disso, JobTemplates não podem ser atualizados. Em vez disso, tem de criar uma nova com as suas alterações atualizadas.
 

Se tiver êxito, é devolvida a resposta seguinte:
    
    HTTP/1.1 201 Created
    
    . . .


O exemplo seguinte mostra como criar uma tarefa de referenciar um Id de JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

Se tiver êxito, é devolvida a resposta seguinte:
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Próximos passos
Agora que sabe como criar uma tarefa para descodificar um assset, vá para o tópico [Como para verificar progresso do projecto com os serviços de multimédia](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Consulte também

[Obter processadores de multimédia](media-services-rest-get-media-processor.md)

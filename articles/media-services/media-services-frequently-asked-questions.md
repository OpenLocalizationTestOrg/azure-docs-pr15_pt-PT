<properties 
    pageTitle="Perguntas mais frequentes | Microsoft Azure" 
    description="Perguntas mais frequentes (FAQ)" 
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


#<a name="frequently-asked-questions"></a>Perguntas mais frequentes

##<a name="general-ams-faqs"></a>Perguntas mais frequentes AMS de geral

P: como é dimensionar a indexação

R: as unidades reservadas são as mesmas para as tarefas de codificação e indexação. Siga as instruções sobre [como unidades reservadas de codificação de escala](media-services-scale-media-processing-overview.md). **Nota** que o desempenho do indexador não é afetado por tipo de unidade reservadas.

P: Posso carregado, codificado e publicado um vídeo. O que seria a razão o vídeo não é reproduzido quando tento transmitir em fluxo-lo?

R: uma das razões mais comuns é que não tem, pelo menos, uma unidade de transmissão reservada atribuída no ponto final de transmissão a partir do qual está a tentar reprodução.  Siga as instruções sobre [como unidades reservadas da transmissão escala](media-services-portal-scale-streaming-endpoints.md).

P: Posso composição numa sequência live?

R: composição em fluxos de direto atualmente não é oferecida nos serviços de multimédia do Azure, para que o que precisa para compor previamente no seu computador.

P: Posso utilizar o Azure CDN com transmissão Live?

R: Media Services suporta a integração com o Azure CDN (para obter mais informações, consulte [como gerir transmissão pontos finais numa conta de serviços de multimédia](media-services-portal-manage-streaming-endpoints.md)).  Pode utilizar o Live transmissão com CDN. Azure dos serviços de multimédia fornece saídas de transmissão, HLS e MPEG-TRAÇO suaves. Todos estes formatos utilizam HTTP para transferir dados e obter vantagens de HTTP colocação em cache. No live transmissão real dados de áudio/vídeo seja divididos para fragmentos e este fragmentos individuais obtenham em cache no CDN. Apenas as necessidades de dados para ser atualizados é de dados. CDN periodicamente atualiza os dados.

P: serviços de multimédia do Azure de é que suportam armazenar imagens?

R: se apenas está à procura para armazenar imagens em formato JPEG ou PNG, deve manter as armazenamento de Blobs do Azure. Não existe nenhuma vantagem para colocar em-los na sua conta dos serviços de multimédia, a menos que pretende mantê-las associados com o vídeo ou áudio elementos. Ou, se pode ter uma necessidade de utilizar as imagens como sobreposições no vídeo codificador. Suporta Media Encoder padrão sobreposto imagens na parte superior de vídeos e que é o que listas JPEG e PNG como suportadas formatos de entrada. Para mais informações, consulte o artigo [Criar sobreposições](media-services-custom-mes-presets-with-dotnet.md#overlay).

P: como copiar elementos a partir de uma conta de início dos serviços de multimédia para outra.

R: para copiar elementos de uma conta de início dos serviços de multimédia para outro utilizando o .NET, utilize o método de extensão de [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório de [Extensões de SDK de .NET de serviços de multimédia do Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Para obter mais informações, consulte [Este](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) tópico Fórum.

P: quais são os carateres para ficheiros de nomenclatura quando trabalha com AMS suportados?

R: Media Services utiliza o valor da propriedade IAssetFile.Name durante a criação de URLs para o conteúdo da transmissão (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, codificação percentual não é permitida. O valor da propriedade **nome** não pode ter qualquer um dos seguintes [carateres percentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, só pode haver um '.' para a extensão de nome de ficheiro.


P: como ligar através do resto?

R: após com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI tal como descrito em [para ligar ao utilizar a REST API dos serviços de multimédia](media-services-rest-connect-programmatically.md). 


P: como rodar um vídeo durante o processo de codificação.

R: a [Media Encoder padrão](media-services-dotnet-encode-with-media-encoder-standard.md) suporta rotação pelos ângulos de 180/90/270. O comportamento predefinido é "Automático", onde tenta para detetar os metadados de rotação no ficheiro MP4/MOV recebida e o Adquirente na íntegra para a mesma. Inclua o seguinte elemento de **origens** para um da json predefinições definido [aqui](http://msdn.microsoft.com/library/azure/mt269960.aspx):
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

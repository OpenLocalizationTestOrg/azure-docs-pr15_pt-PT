<properties
    pageTitle="Descrição geral do processamento de multimédia de dimensionamento | Microsoft Azure"
    description="Este tópico é uma descrição geral do processamento de multimédia dimensionamento com os serviços de multimédia do Azure."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Descrição geral do processamento de multimédia de dimensionamento

Esta página dá uma descrição geral de como e porquê dimensionar o processamento de multimédia. 

## <a name="overview"></a>Descrição geral

Uma conta dos serviços de multimédia está associada um tipo de unidade reservadas, que determina a velocidade com que são processados os ficheiros de multimédia processar tarefas. Pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2**ou **S3**. Por exemplo, a mesma tarefa de codificação é executada mais rapidamente quando utiliza o comparar **S2** reservada unidade tipo com o tipo de **S1** . Para obter mais informações, consulte os [Tipos de unidade reservadas](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Para além de especificar o tipo de unidade reservadas, pode especificar aprovisionar a sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que pode ser processada em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, em seguida, tarefas de cinco multimédia irão estar em execução em simultâneo, desde como existem tarefas que deve ser processada. As tarefas restantes irão aguardar na fila de espera e irão selecionadas para processamento sequencialmente quando termina uma tarefa em execução. Se uma conta não tem qualquer unidades reservadas aprovisionadas, em seguida, tarefas vai ser selecionadas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de ser concluída e iniciar um seguinte irá dependem a disponibilidade de recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolher entre os tipos de unidade reservadas diferente

A seguinte tabela ajuda-lo a tomar decisões quando escolher entre diferentes velocidades codificação. Também fornece alguns casos de teste de referência e fornece SA URLs que pode utilizar para transferir vídeos no qual pode executar o seus próprio testes:

Cenários|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Caso de utilização prevista| Codificação de velocidade única. <br/>Ficheiros SD ou abaixo resoluções, tempo não sensíveis a maiúsculas e, em baixo custo.|Velocidade única e várias codificação de velocidade.<br/>Utilização de normal para codificação SD e HD. |Velocidade única e várias codificação de velocidade.<br/>Vídeos de resolução do HD e 4K completos. Tempo de codificação de entrega sensíveis a maiúsculas e, mais rápida. 
Teste de referência|[Ficheiro de introdução: 5 minutos por extenso 640x360p na 29.97 pacotes/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codificação para uma única velocidade ficheiro MP4, com a mesma resolução, demora cerca de 11 minutos.|[Ficheiro de entrada: 5 minutos por extenso 1280x720p na 29.97 pacotes/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Codificação com "H264 a velocidade única 720p" Predefinir demorar cerca de 5 minutos.<br/><br/>Codificação com "H264 velocidade vários 720p" predefinido demora aproximadamente 11.5 minutos.|[Ficheiro de introdução: 5 minutos por extenso 1920x1080p na 29.97 pacotes/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Codificação com "H264 a velocidade única 1080p" Predefinir demorar cerca de 2.7 minutos.<br/><br/>Codificação com "H264 velocidade vários 1080p" predefinido demora aproximadamente 5.7 minutos.

##<a name="considerations"></a>Considerações sobre

>[AZURE.IMPORTANT] Reveja considerações descritas nesta secção.  

- Unidades reservadas funcionam parallelizing todo o processamento de multimédia, incluindo a indexação tarefas utilizando indexador de multimédia do Azure.  No entanto, ao contrário de codificação, indexação não obter processada mais rápido com mais rápidas reservadas unidades.

- Se utilizar o conjunto partilhado, ou seja, sem qualquer unidades reservadas, em seguida, as suas tarefas codificar têm o mesmo comportamento funcional tal como acontece com S1 RUs. No entanto, não existe nenhuma vínculo superior para a hora podem passam a suas tarefas em fila estado e, em qualquer altura determinada, no máximo, uma tarefa é estar em execução.

- Os seguintes centros de dados não oferecer o tipo de unidade **S2** reservada: sul do Brasil, Índia oeste, Índia Central e Índia Sul.

- Os seguintes centros de dados não oferecer o tipo de unidade **S3** reservada: sul do Brasil, Índia oeste, Índia Central.

- O número máximo de unidades especificadas para o período de 24 horas é utilizado para calcular o custo.


##<a name="quotas-and-limitations"></a>Quotas e limitações

Para obter informações acerca de quotas e limitações e como abrir um bilhetes de suporte, consulte o artigo [Quotas e limitações](media-services-quotas-and-limitations.md).

##<a name="next-step"></a>Passo seguinte

Alcançar a tarefa de processamento de multimédia dimensionamento com uma destas tecnologias: 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-scale-media-processing.md)
- [RESTO](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

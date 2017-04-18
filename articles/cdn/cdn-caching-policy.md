<properties
    pageTitle="CDN colocação em cache de política na extensão de serviços de multimédia"
    description="Este tópico fornece uma descrição geral de uma CDN colocação em cache de política na extensão de serviços de multimédia."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>CDN colocação em cache de política na extensão de serviços de multimédia

Azure dos serviços de multimédia fornece que HTTP baseado no Streaming ajustável e transferir gradual. HTTP com base transmissão é altamente dimensionável com vantagens de colocação em cache no proxy e camadas CDN, bem como colocação em cache do lado do cliente. Transmissão pontos finais fornece capacidades de transmissão gerais e também a configuração de cabeçalhos de cache HTTP. Transmissão pontos finais de conjuntos de HTTP Cache-Control: máximo idade e cabeçalhos expira. Pode obter mais informações para cabeçalhos de cache HTTP a partir do [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>Cabeçalhos de colocação em cache predefinido

Por predefinição pontos finais de transmissão aplicam os cabeçalhos da cache de 3 dia para dados em tempo real a pedido (segmentos de multimédia real/blocos) e manifest(playlist). Para direto streaming, pontos finais de transmissão aplicam cabeçalhos de cache de dia 3 para os dados (segmentos de multimédia real/blocos) e 2 segundos cabeçalho para pedidos de manifest(playlist) em cache. Quando o programa direto transforma-se a pedido (direto arquivo) aplicam-se os cabeçalhos de cache transmissão a pedido.

##<a name="azure-cdn-integration"></a>Integração de CDN Azure

Azure dos serviços de multimédia fornece [integrada CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos finais de transmissão. Cabeçalhos de controlo de cache aplica-se da mesma forma como os pontos finais transmissão à CDN activado transmissão pontos finais. Utilizações CDN Azure transmissão ponto final configurado valores de cache para definir o tempo de vida dos objetos internamente em cache e também utiliza este valor para definir a entrega cabeçalhos de cache. Quando utilizar CDN ativado pontos finais de transmissão não é recomendado para definir os valores de cache pequenas. Definição de valores pequenas diminuir o desempenho e reduzir o benefício de CDN. Não é permitida para definir os cabeçalhos de cache menores do que 600 segundos para CDN ativado transmissão pontos finais.

>[AZURE.IMPORTANT] Azure dos serviços de multimédia integração com o Azure CDN é implementado no **Azure CDN a partir do Verizon**.  Se pretender utilizar **CDN Azure a partir do Akamai** para dos serviços de multimédia do Azure, tem de [configurar manualmente o ponto final](cdn-create-new-endpoint.md).  Para mais informações sobre as funcionalidades de Azure CDN, consulte o artigo [Descrição geral CDN](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Configurar os cabeçalhos da cache com dos serviços de multimédia do Azure

Pode utilizar o portal de gestão do Azure ou APIs de serviços de multimédia do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar os cabeçalhos de cache utilizando a gestão de portal consulte [como gerir os pontos finais de transmissão](../media-services/media-services-portal-manage-streaming-endpoints.md) secção Configurar o ponto final de transmissão.
2. Serviços de multimédia do Azure REST API, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Serviços de multimédia do Azure .NET SDK, [StreamingEndpointCacheControl propriedades](http://go.microsoft.com/fwlink/?LinkId=615302).

##<a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração de cache

1. Valor da cache dos serviços de multimédia configurado Azure substitui o valor predefinido.
2. Se não existe nenhuma configuração manual, aplica-se os valores predefinidos.
3. Por cache de 2 segundos predefinida cabeçalhos aplica-se ao live transmissão manifest(playlist), independentemente de configuração de multimédia do Azure ou armazenamento do Windows Azure e substituir deste valor não está disponível.

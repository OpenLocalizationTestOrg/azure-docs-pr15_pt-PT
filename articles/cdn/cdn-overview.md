<properties
    pageTitle="Descrição geral Azure CDN | Microsoft Azure"
    description="Saiba o que é a rede de entrega de conteúdos de Azure (CDN) e como utilizá-la para fornecer o conteúdo de largura de banda elevada por colocação em cache blobs e em conteúdo estático."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Descrição geral da rede de entrega de conteúdos Azure (CDN)

> [AZURE.NOTE] Este documento descreve o que é a rede Azure entrega conteúdo (CDN), como funciona e as funcionalidades de cada produto de Azure CDN.  Se pretender ignorar esta informação e ir diretamente para um tutorial sobre como criar um ponto final de CDN, consulte o artigo [Utilizar o Azure CDN](cdn-create-new-endpoint.md).  Se pretender ver uma lista de localizações de nó CDN atuais, consulte o artigo [Azure CDN POP localizações](cdn-pop-locations.md).

Rede de entrega de conteúdos (CDN) o Azure coloca em cache conteúdo da web estática em localizações estrategicamente para fornecer débito máximo para fornecer conteúdo aos utilizadores.  A CDN oferece os programadores uma solução global para fornecer o conteúdo de largura de banda elevada por colocação em cache o conteúdo em nós físicas em todo o mundo. 

Os benefícios da utilização CDN para elementos de web site de cache incluem:

- Melhor desempenho e utilizador experimentar para utilizadores finais, especialmente quando utilizar aplicações onde vários IDA é necessários para carregar conteúdo.
- Grande escala para uma melhor processar caso de carga elevado instantâneo, como no início de um evento de lançamento do produto.
- Distribuir a pedidos de utilizador e servir de conteúdos a partir de servidores edge, menos tráfego é enviado para a origem.


## <a name="how-it-works"></a>Como funciona

![Descrição geral CDN](./media/cdn-overview/cdn-overview.png)

1. Um utilizador (Alice) os pedidos de um ficheiro (também designado de um ativo) utilizando um URL com um nome de domínio especiais, tais como `<endpointname>.azureedge.net`.  DNS encaminha o pedido para o melhor efetuar a localização do ponto de presença (POP).  Normalmente, este é o POP que esteja geograficamente mais próximo ao utilizador.

2. Se os servidores edge no POP não tiver o ficheiro na sua cache, servidor edge pede o ficheiro a partir da origem.  A origem pode ser uma aplicação Web do Azure, Azure serviço em nuvem, conta de armazenamento do Windows Azure ou qualquer servidor web acessível publicamente.

3. A origem devolve o ficheiro para o servidor edge, incluindo os cabeçalhos de HTTP opcionais que descreve Time-para-Live o ficheiro (TTL).

4. Servidor edge coloca em cache o ficheiro e devolve o ficheiro para o autor do pedido original (Alice).  O ficheiro permanece em cache no servidor edge até o valor TTL expira.  Se a origem não especificar um TTL, o TTL predefinido é de sete dias.

5. Utilizadores adicionais, em seguida, podem pedir o mesmo ficheiro utilizar esse mesmo URL e também podem ser direcionados para esse mesmo POP.

6. Se o TTL para o ficheiro não expirado, servidor edge devolve o ficheiro da cache de.  Isto resulta numa experiência de utilizador de forma mais rápida e mais responder.


## <a name="azure-cdn-features"></a>Funcionalidades Azure CDN

Existem três Azure CDN produtos: **Azure CDN padrão de Akamai**, **Azure CDN padrão de Verizon**e **Azure CDN Premium a partir do Verizon**.  A tabela seguinte lista as funcionalidades disponíveis com cada produto.

|       | Akamai padrão | Verizon padrão | Premium Verizon |
|-------|-----------------|------------------|-----------------|
| Integração simples com o Azure serviços como o [armazenamento](cdn-create-a-storage-account-with-cdn.md), [Serviços em nuvem](cdn-cloud-service-with-cdn.md), [Aplicações Web](../app-service-web/cdn-websites-with-cdn.md)e [Dos serviços de multimédia](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;**|
| Gestão de através de [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](./cdn-app-dev-net.md), [Node.js](./cdn-app-dev-node.md)ou [PowerShell](./cdn-manage-powershell.md). | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Suporte HTTPS | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Balanceamento de carga | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Proteção de [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Pilha de dupla IPv4/IPv6 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Suporte de nome de domínio personalizado](cdn-map-content-to-custom-domain.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Colocação em cache de cadeia de consulta](cdn-query-string.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Filtragem geo](cdn-restrict-access-by-country.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Remover rapidamente](cdn-purge-endpoint.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Carregamento pré activo](cdn-preload-endpoint.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Análise de Core](cdn-analyze-usage-patterns.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Suporte HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713;**  |  |  |
| [Relatórios HTTP avançados](cdn-advanced-http-reports.md) | | | **& #x 2713;** |
| [Estatística em tempo real](cdn-real-time-stats.md) | | | **& #x 2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md) | | | **& #x 2713;** |
| [Motor de personalizáveis, com base em regra de entrega de conteúdos](cdn-rules-engine.md) | | | **& #x 2713;** |
| Definições de cache/cabeçalho (utilizando o [motor de regras](cdn-rules-engine.md))  | | | **& #x 2713;** |
| Redirecionar/reescrita de URL (utilizando o [motor de regras](cdn-rules-engine.md)) | | | **& #x 2713;** |
| Regras do dispositivo móvel (utilizando o [motor de regras](cdn-rules-engine.md))  | | | **& #x 2713;** |

>[AZURE.TIP] Existe uma funcionalidade que pretende ver no Azure CDN?  [Fornecer comentários](https://feedback.azure.com/forums/169397-cdn)! 

## <a name="next-steps"></a>Próximos passos

Para começar com CDN, consulte o artigo [Utilizar o Azure CDN](./cdn-create-new-endpoint.md).

Se for um cliente CDN existente, podem agora gerir a sua os pontos finais CDN através do [portal do Microsoft Azure](https://portal.azure.com) ou com o [PowerShell](cdn-manage-powershell.md).

Para ver a CDN em ação, veja o [vídeo da nossa sessão de compilação 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Saiba como automatizar Azure CDN com [.NET](./cdn-app-dev-net.md) ou [Node.js](./cdn-app-dev-node.md).

Para informações sobre os preços, consulte o artigo [CDN preços](https://azure.microsoft.com/pricing/details/cdn/).

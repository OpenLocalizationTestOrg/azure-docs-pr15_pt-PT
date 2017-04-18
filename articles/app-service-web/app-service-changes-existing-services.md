<properties
    pageTitle="Azure de aplicação de serviço e o seu impacto nos serviços de Azure existentes"
    description="Explica como o novo serviço de aplicação do Azure e suas funcionalidades afeta a serviços existentes no Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Azure de aplicação de serviço e os serviços Azure existentes

Este artigo descreve as alterações aos serviços Azure existentes como parte da alteração para trazer em conjunto vários serviços Azure para a [Aplicação de serviço de Azure](https://azure.microsoft.com/services/app-service/), uma nova oferta integrada.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Descrição geral

[Aplicação de serviço de Azure](https://azure.microsoft.com/services/app-service/) é um serviço de nuvem novo e único que permite que os programadores criar web e aplicações móveis para qualquer plataforma e qualquer dispositivo. Aplicação de serviço é uma solução integrada concebida para simplificar a funções de codificação repetidas, integrar com o enterprise e sistemas de SaaS e automatizar processos de negócio durante a reunião às suas necessidades de segurança, fiabilidade e escalabilidade.

Aplicação de serviço reunindo os seguintes existentes Azure serviços - [sites públicos](https://azure.microsoft.com/services/websites/), [Serviços Mobile](https://azure.microsoft.com/services/mobile-services/)e [Serviços de Biztalk](https://azure.microsoft.com/services/biztalk-services/) um único serviço combinado, ao adicionar novas capacidades poderosas.  Serviço de aplicação permite-lhe os seguintes tipos de aplicação do anfitrião:

-   Web Apps
-   Aplicações móveis
-   API aplicações
-   Aplicações de lógica

A seguinte tabela explica o mapa de serviços Azure como existente para a aplicação de serviço e os tipos de aplicação disponíveis dentro da mesma.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Serviço do Azure existente</th>
<th align="left", style="width:10%">Azure de aplicação de serviço</th>
<th align="left", style="width:80%">O que mudou</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sites públicos do Azure</td>
<td align="left">Web Apps</td>
<td align="left"><li>Para sites públicos do Azure, a aplicação de serviço é estritamente limitada para mudar o nome do Web sites para Web Apps.
<p><li>Todas as instâncias existentes de Web sites estão agora Web Apps no serviço de aplicação.</p>
<p><li>Pode aceder aos seus Web sites existentes através de do <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portal do Azure</a>, onde encontrará todos os sites existentes em <em>Aplicações Web</em>.</p>
<p><li><em>Web alojamento planear</em> está agora <em>Plano de serviço de aplicação</em>. Um <em>Plano de serviço de aplicação</em> , pode alojar qualquer tipo de aplicação de serviço de aplicação, como as aplicações Web, Mobile, lógica ou API.</p>
<p><li>Azure aplicação de serviço Web Apps no geral é disponibilidade.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Saiba mais sobre aplicações Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Azure serviços móveis</td>
<td align="left">Aplicações móveis</td>
<td align="left"><p><li>Serviços Mobile continuam a estar disponível como serviço autónomo e permanecem totalmente suportada.</p>
<p><li>Aplicações móveis é um tipo de aplicação no serviço de aplicação, que integra-se de todas as funcionalidades de serviços móveis e muito mais.</p>
<p><li>É fácil de <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">migrar de serviços móveis às aplicações móveis</a>.</p>
<p><li>Como parte da aplicação de serviço, aplicações Mobile obter novas funcionalidades para além dos serviços de Mobile, tal como a integração com o no local e sistemas de SaaS, teste faixas, WebJobs, opções de escala melhor e mais.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Saiba mais sobre aplicações Mobile</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API aplicações</td>
<td align="left">
<p><li>API aplicações é um novo tipo de aplicação na aplicação de serviço que permite-lhe facilmente construir e consuma APIs na nuvem.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Saiba mais sobre aplicações API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Aplicações de lógica</td>
<td align="left">
<p><li>Lógica aplicações é um novo tipo de aplicação na aplicação de serviço que permite-lhe automatizar facilmente o processo de negócio.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Saiba mais sobre aplicações de lógica</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Serviços de Azure BizTalk</td>
<td align="left">Aplicações de BizTalk API</td>
<td align="left">
<li><p>Serviços de BizTalk continuar a estar disponível como serviço autónomo e permanecem totalmente suportada.</p>
<li><p>Todas as capacidades dos serviços de BizTalk sejam integradas numa aplicação de serviço de como API aplicações permitindo aos utilizadores executar integração de aplicações empresariais e cenários de integração de B2B com qualquer um dos tipos de aplicação numa aplicação de serviço</p>
<li><p>Lógica Apps agora pode automatizar processos de negócio utilizando uma experiência de estrutura visual para criar fluxos de trabalho</p></td>
</tr>
</tbody>
</table>

Para saber mais, visite a [documentação do serviço de aplicação](https://azure.microsoft.com/documentation/services/app-service/).

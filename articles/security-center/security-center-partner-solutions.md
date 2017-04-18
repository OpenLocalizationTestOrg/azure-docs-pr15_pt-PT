<properties
   pageTitle="Gerir soluções de parceiros no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento irá guiar pelo modo como o Centro de segurança do Azure permite monitor rapidamente o estado de estado de funcionamento do seu soluções de parceiros integrado com a sua subscrição Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Monitorização soluções de parceiros com o Centro de segurança do Azure

Este documento explica como monitorizar o estado de estado de funcionamento do seu soluções de parceiros no Centro de segurança do Azure.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo. Não se trata de um guia passo a passo.

## <a name="monitoring-partner-solutions"></a>Monitorização soluções de parceiros

Mosaico **soluções de parceiros** no pá **Centro de segurança** permite monitor rapidamente o estado de estado de funcionamento do seu soluções de parceiros integrado com a sua subscrição Azure.
![Mosaico de soluções de parceiros][1]

O mosaico de **soluções de parceiros** apresenta o número de soluções de parceiros e um Estado de resumo para essas soluções.

Pode ser o **Estado** de uma solução de parceiro:

- Protegidos (verde) - não existe nenhum problema de estado de funcionamento.
- Unhealthy (vermelho) - existe um problema de estado de funcionamento que necessita de atenção imediata.
- Parado (laranja) de elaboração de relatórios - a solução deixou de elaboração de relatórios do seu estado de funcionamento.
- Estado da proteção desconhecido (laranja) - o estado de funcionamento da solução é desconhecido neste momento devido a um processo de falhado da ação de adicionar um novo recurso para a solução existente.
- Não comunicado (cinzento) - a solução não comunicou nada ainda, estado de uma solução pode ser não reportado se apenas foi ligado e ainda está a implementar.

Se não existem soluções integradas com a sua subscrição no mosaico irá indicar que não existem soluções. Selecionar o mosaico de **soluções de parceiros** permite-lhe abrir o pá **recomendações** para implementar soluções de segurança do parceiro.
![Não existem soluções de parceiros][2]

Para ver o estado de funcionamento do seu soluções de parceiros:

1. Selecione o mosaico de **soluções de parceiros** . Uma pá é aberta apresenta uma lista das suas soluções de parceiros ligado ao centro de segurança.
![Soluções de parceiros][3]

2. Selecione uma solução de parceiro. Neste exemplo, permite-selecione a solução **F5 WAF2** .  Uma pá abre-se de que mostra que o estado da solução de parceiro e a solução associados a recursos. Selecione **Consola de solução** para abrir a experiência de gestão de parceiro para esta solução.
![Detalhe de soluções de parceiros][4]

3. Voltar para a pá **F5 WAF2** e selecione **a aplicação de ligação**. A **Ligação aplicações** pá é aberta. Aqui pode ligar recursos para a solução do parceiro.
![Recursos de ligação para a solução de parceiros][5]

## <a name="see-also"></a>Consulte também
Neste documento, foram introduzidas para o mosaico de **Soluções de parceiros** no Centro de segurança. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do Azure Security](http://blogs.msdn.com/b/azuresecurity/) — obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

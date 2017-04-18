<properties 
   pageTitle="Azure Cativação Mobile guia - a análise de resolução de problemas" 
   description="Resolver problemas de análise, monitorização, segmentação e Dashboard no Azure Mobile Cativação" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guia de resolução de problemas para problemas de análise, monitorização, segmentação e dashboards

Seguem-se possíveis problemas que poderá encontrar com como o Azure Mobile Cativação reúne informações sobre as suas aplicações, dispositivos e utilizadores.

## <a name="missingdelayed-information"></a>Informações em falta/adiada

### <a name="issue"></a>Problema
- Informações estiver atrasadas na aparecer em análise, segmentação ou Dashboard.
- Faltam informações a partir de monitorização.
- Faltam informações a partir de análise, segmentação ou Dashboard.
- Limites de segmentação atingir.

### <a name="causes"></a>Faz com que

- Pode utilizar a API Analytics, API do Monitor, e segmentos de API para ver se todos os dados está em falta na IU é visível através das API.
- Se o SDK do Azure Mobile Cativação não está integrado corretamente na sua aplicação não poderá ver as informações na análise, segmentação, monitorização ou Dashboards.
- Segmentos não podem ser alteradas quando são criadas, segmentos só podem ser "clonado" (copiados) ou "destruídos" (eliminados). Segmentos só podem conter 10 critérios.
- É a melhor forma de testar informações em falta de monitorização para um dispositivo de teste do programa de configuração, desinstale e/ou reinstale a aplicação no dispositivo de teste.
- Informações são atualizadas a cada 24 horas para análise, segmentação ou Dashboards.
- Informações nos novos segmentos podem não ser apresentadas até 24 horas depois de serem criados mesmo se o segmento é com base na informação anterior.
- Filtrar os seus dados de análise na IU irá mostrar todos os exemplos deste tipo independentemente da versão da sua aplicação (por exemplo, "falhas" filtradas por nome irão apresentar a partir da versão 1 e 2 de versão da sua aplicação).
- O período de tempo para análise é com base na data a partir das definições de dispositivo dos utilizadores, para que um utilizador cuja phone tem a data de forma incorreta definida poderia sejam apresentadas num período de tempo errado.
- Do lado do servidor, os dados são registados quando utilizar o botão "Testar" emite, apenas os dados são registados para campanhas de real push.

## <a name="cant-locate-items-in-ui"></a>Não consegue localizar itens na IU

### <a name="issue"></a>Problema
- Não pode criar segmentos com base em determinados incorporada ou informações personalizadas app marcar critérios.
- Não consegue localizar determinados incorporada ou informações personalizadas app marcar critérios em análise, monitorização e Dashboards.
- Não consegue interpretar os dados na análise, monitorização, segmentação ou Dashboards.

### <a name="causes"></a>Faz com que

- Algumas integrado itens e etiquetas de informações da aplicação só estão disponíveis como critérios de push mas não podem ser adicionada a um segmento ou visíveis Analytics, monitorização ou Dashboard. 
- Para itens num e etiquetas de informações da aplicação que não podem ser adicionadas a um segmento, terá a lista de configuração de filtragem de critérios em cada campanha para efetuar a mesma função a filtrar com base num segmento.
- Consulte os menus de contexto nas secções Analytics, monitorização, segmentação e Dashboards da IU Azure Mobile Cativação para obter mais ajuda e como informações.

## <a name="crash-troubleshooting"></a>Uma falha de sistema de resolução de problemas

### <a name="issue"></a>Problema
- Aplicação falha a aparecer em análise, monitorização ou Dashboard.

### <a name="causes"></a>Faz com que

- Resolver problemas de aplicação falha vistos no Analytics monitorização de Dashboard, certifique-se verificar as notas de lançamento para problemas conhecidos com versões anteriores do SDK.
- Para continuar a resolução falhas de aplicações efetuar um evento a partir de um dispositivo de teste com a aplicação instalado e o aspeto o seu ID de dispositivo na secção "Monitorizar – eventos" da IU Azure Mobile Cativação. Em seguida, execute o evento que está a causar uma falha de sistema e procurar as informações adicionais na secção "Falha de sistema do Monitor –" da IU Azure Mobile Cativação a aplicação. 

 

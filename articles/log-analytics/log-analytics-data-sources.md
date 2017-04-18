<properties 
   pageTitle="Origens de dados no registo de análise | Microsoft Azure"
   description="Origens de dados definem os dados que recolhe de análise de registo da agentes e outro ligados origens.  Este artigo descreve o conceito de como a análise de registo utiliza origens de dados, explica os detalhes de como configurá-los e fornece um resumo das origens de dados diferentes disponíveis."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="data-sources-in-log-analytics"></a>Origens de dados no registo de análise

Recolhe dados das origens ligado na área de trabalho OMS e armazena no repositório de OMS de análise de registo.  Os dados que são recolhidos de cada são definidos pelas origens de dados que configurar.  Dados no repositório de OMS são armazenados como um conjunto de registos.  Cada origem de dados cria os registos de um tipo específico com cada tipo de ter o seu próprio conjunto de propriedades.

![Inicie sessão recolha de dados de análise](./media/log-analytics-data-sources/overview.png)

Origens de dados são diferentes das soluções de OMS que também recolher dados de origens ligadas e criar os registos no repositório de OMS.  Soluções podem ser adicionadas à área de trabalho a partir da Galeria de soluções e normalmente irão fornecer ferramentas de análise adicionais no portal do OMS.  

## <a name="summary-of-data-sources"></a>Resumo das origens de dados

Origens de dados que estão atualmente disponíveis no registo de análise estão listadas na tabela seguinte.  Cada tem uma ligação para um artigo separada fornecer detalhes para essa origem de dados.

| Origem de dados | Tipo de evento | Descrição |
|:--|:--|:--|
| [Registos personalizados](log-analytics-data-sources-custom-logs.md) | \<NomeRegisto\>_CL | Ficheiros de texto no Windows ou Linux agentes que contém informações de registo. |
| [Registos de eventos do Windows](log-analytics-data-sources-windows-events.md) | Evento | Eventos recolhidas a partir do registo de eventos em computadores com Windows. |
| [Contadores de desempenho do Windows](log-analytics-data-sources-performance-counters.md) | Desempenho | Contadores de desempenho recolhidas a partir do computadores com o Windows. |
| [Contadores de desempenho Linux](log-analytics-data-sources-performance-counters.md) | Desempenho | Contadores de desempenho recolhidas a partir do Linux computadores. |
| [Registos do IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Serviços de informação Internet registos no formato de W3C. |
| [Syslog](log-analytics-data-sources-syslog.md) | Syslog | Eventos do sistema em computadores Windows ou Linux. |

## <a name="configuring-data-sources"></a>Configurar as origens de dados

Configure a origens de dados a partir do menu de **dados** na análise de registo **Definições**.  Qualquer configuração é entregue a todas as origens ligadas na área de trabalho OMS.  Não é possível atualmente excluir quaisquer agentes desta configuração.

![Configurar eventos do Windows](./media/log-analytics-data-sources/configure-events.png)

2. Na consola do OMS selecione o mosaico **Definições** .
3. Selecione **dados**.
4. Clique em origem de dados para configurar.
5. Siga a hiperligação para a documentação para cada origem de dados na tabela acima para obter detalhes sobre a sua configuração.

## <a name="data-collection"></a>Recolha de dados

Configurações de origem de dados são entregues agentes que estão ligados diretamente para OMS dentro de alguns minutos.  Os dados especificados são recolhidos do agente de e entregues diretamente para o registo de análise em intervalos específicos para cada origem de dados.  Consulte a documentação para cada origem de dados para estes detalhes.

Para agentes de Gestor de operações de centro do sistema (SCOM) num grupo de gestão ligada, configurações de origem de dados são convertidas em pacotes de gestão e entregue ao grupo de gestão em 5 minutos por predefinição.  O agente transfere o pacote de gestão de como qualquer outro e recolhe os dados especificados. Dependendo da origem de dados os dados serão enviadas para um servidor de gestão que reencaminha os dados para a análise de registo ou o agente irá enviar os dados para a análise de registo sem ter de aceder através do servidor de gestão. Consulte [Detalhes de recolha de dados para as funcionalidades OMS e soluções](log-analytics-add-solutions.md#data-collection-details-for-oms-features-and-solutions) para obter detalhes.  Pode ler sobre detalhes de ligar SCOM e OMS e modificar a frequência de que a configuração é entregue às [Configurar integração com o Gestor de operações do Centro de sistema](log-analytics-om-agents.md).

## <a name="log-analytics-records"></a>Análise de registos

Todos os dados recolhidos pela análise de registo está armazenado no repositório de OMS como registos.  Os registos recolhidos pelo origens de dados diferentes terão o seu próprio conjunto de propriedades e identificados pela sua propriedade **tipo** .  Consulte a documentação para cada origem de dados e a solução para obter detalhes sobre cada tipo de registo.


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as [soluções](log-analytics-add-solutions.md) que adicionar funcionalidades a análise de registo e também recolher dados para o repositório OMS.
- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções.  
- Configure [alertas](log-analytics-alerts.md) para importante notificá-lo de dados críticos recolhidos a partir de origens de dados e soluções.

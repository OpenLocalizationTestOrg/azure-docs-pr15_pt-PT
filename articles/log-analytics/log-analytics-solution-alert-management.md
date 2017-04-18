<properties
   pageTitle="Solução de gestão de no conjunto de aplicações de gestão de operações (OMS) de alerta | Microsoft Azure"
   description="A solução de gestão de alerta no registo de análise ajuda-o a analisar todos os alertas no seu ambiente.  Além de consolidar os alertas gerados dentro OMS,-importa alertas a partir de grupos de gestão de Gestor de operações de centro do sistema (SCOM) ligada para o registo de análise."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="bwren" />

# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Solução de gestão de alerta no conjunto de aplicações de gestão de operações (OMS)

![Ícone de alerta de gestão](media/log-analytics-solution-alert-management/icon.png) Solução de gestão de alerta do ajuda-o a analisar todos os alertas no seu ambiente.  Além de consolidar os alertas gerados dentro OMS,-importa alertas a partir de grupos de gestão de Gestor de operações de centro do sistema (SCOM) ligada para o registo de análise.  Nos ambientes com vários grupos de gestão, a solução de gestão de alerta irá fornecer uma vista consolidada de alertas em todos os grupos de gestão.

## <a name="prerequisites"></a>Pré-requisitos

- Para importar os alertas do SCOM, esta solução requer uma ligação entre a área de trabalho OMS e um grupo de gestão de SCOM utilizando o processo descrito no [Gestor de operações de ligar para a análise de registo](log-analytics-om-agents.md).  


## <a name="configuration"></a>Configuração

Adicione a solução de gestão de alerta para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.

## <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão de SCOM estiver ligado à área de trabalho OMS, em seguida, os seguintes packs gestão serão instalados no SCOM quando adiciona esta solução.  Não existe nenhuma configuração ou a manutenção destes packs gestão necessários.  

- Gestão de alerta do Centro de sistema do Microsoft Advisor (Microsoft.IntelligencePacks.AlertManagement)

Para mais informações sobre como os pacotes de gestão de solução são atualizados, consulte o artigo [Ligar o Gestor de operações para a análise de registo](log-analytics-om-agents.md).

## <a name="data-collection"></a>Recolha de dados

### <a name="agents"></a>Agentes

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem ligada | Suporte | Descrição |
|:--|:--|:--|
| [Agentes do Windows](log-analytics-windows-agents.md) | N | Agentes de Windows diretos não geram alertas SCOM. |
| [Agentes de Linux](log-analytics-linux-agents.md) | N | Agentes de Linux diretos não geram alertas SCOM. |
| [Grupo de gestão de SCOM](log-analytics-om-agents.md) | Sim | Alertas gerados sobre agentes SCOM são entregues ao grupo de gestão e, em seguida, reencaminhados para a análise de registo.<br><br>Não é necessária uma ligação direta do agente de SCOM de análise de registo. Alertas de dados são reencaminhados do grupo de gestão para o repositório OMS. |
| [Conta de armazenamento Azure](log-analytics-azure-storage.md) | N | Alertas SCOM não são armazenadas nas contas do Azure armazenamento. |

### <a name="collection-frequency"></a>Frequência de coleções de sites

Os alertas gerados dentro OMS estão disponíveis para a solução imediatamente.  Alertas dados são enviados a partir do grupo de gestão de SCOM para a análise de registo de cada 3 minutos.  

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução de gestão de alerta para a área de trabalho OMS, o mosaico de **Gestão de alerta** será adicionado ao OMS dashboard.  Este mosaico apresenta uma contagem e uma representação gráfica do número de alertas atualmente ativos que foram geradas dentro últimas 24 horas.  Não é possível alterar este intervalo de tempo.

![Alerta de mosaico de gestão](media/log-analytics-solution-alert-management/tile.png)

Clique no mosaico **Gestão de alerta** para abrir o dashboard de **Gestão de alerta** .  O dashboard inclui as colunas na tabela seguinte.  Cada coluna enumera os alertas de dez principais por contagem de correspondência de critérios dessa coluna para o âmbito especificado e o intervalo de tempo.  Pode executar uma pesquisa de registo que fornece a lista inteira ao clicar em **Ver todos os** na parte inferior da coluna ou ao clicar no cabeçalho de coluna.

| Coluna| Descrição |
|:--|:--|
| Alertas críticos | Todos os alertas com um gravidade dos crítica agrupada por nome de alerta.  Clique no nome de um alerta para executar uma pesquisa de registo devolver todos os registos para que o alerta. |
| Alertas de aviso | Todos os alertas com um gravidade dos aviso agrupado por nome de alerta.  Clique no nome de um alerta para executar uma pesquisa de registo devolver todos os registos para que o alerta. |
| Alertas SCOM ativa | Todos os alertas SCOM com qualquer indicar além *fechado* agrupados por origem que gerou o alerta. |
| Todos os alertas ativos | Todos os alertas com qualquer gravidade agrupados por nome de alerta. Inclui apenas alertas SCOM com qualquer Estado que não seja *fechado*.|

Se deslocar para a direita, o dashboard lista várias consultas comuns que pode clicar em para efetuar uma [pesquisa de registo](log-analytics-log-searches.md) para os dados de alertas.

![Dashboard de gestão do alerta](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>Intervalo de âmbito e hora

Por predefinição, o âmbito dos alertas analisadas na solução de gestão de alerta do é todos os grupos de gestão ligada geradas nos últimos 7 dias.  

![Âmbito de gestão de alerta](media/log-analytics-solution-alert-management/scope.png)

- Para alterar os grupos de gestão incluídos na análise, clique em **âmbito** na parte superior do dashboard.  Pode selecionar quer **Global** para todos os grupos de gestão ligada ou **Ao grupo de gestão** para selecionar um grupo de gestão única.

- Para alterar o intervalo de tempo de alertas, selecione **dados com base** na parte superior do dashboard.  Pode selecionar os alertas gerados situada nos últimos 7 dias, 1 dia ou 6 horas.  Ou pode selecionar **personalizado** e especificar um intervalo de datas personalizado.

## <a name="log-analytics-records"></a>Análise de registos

Solução de gestão de alerta do analisa qualquer registo com um tipo de **alerta**.  Também irá importar alertas de SCOM e criar um registo correspondente para cada uma com um tipo de **alerta** e um SourceSystem de **OpsManager**.  Estes registos tem as propriedades da tabela seguinte.  

| Propriedade | Descrição |
|:--|:--|
| Tipo | *Alerta* |
| SourceSystem | *OpsManager* |
| AlertContext | Detalhes do item de dados que causaram o alerta para ser gerado no formato XML. |
| AlertDescription | Descrição detalhada do alerta. |
| AlertId | GUID do alerta. |
| AlertName | Nome do alerta. |
| AlertPriority | Nível de prioridade do alerta. |
| AlertSeverity | Nível de gravidade do alerta. |
| AlertState | Estado de resolução mais recente do alerta. |
| LastModifiedBy | Nome do utilizador última pessoa que modificou o alerta. |
| ManagementGroupName | Nome do grupo de gestão de onde foi gerado o alerta. |
| RepeatCount | Número de tempo que o mesmo alerta foi criado para a mesma monitorizadas objeto desde a ser resolvido. |
| ResolvedBy | Nome do utilizador ao qual resolvido o alerta. Esvaziar se o alerta ainda não tiver sido resolvido. |
| SourceDisplayName | Nome a apresentar do objeto monitorização que gerou o alerta. |
| SourceFullName | Nome completo do objeto monitorização que gerou o alerta. |
| TicketId | ID de bilhetes para o alerta, se o ambiente de SCOM está integrado com um processo para atribuir bilhetes para os alertas.  ID de vazia de sem bilhetes é atribuído. |
| TimeGenerated | Data e hora em que foi criado o alerta. |
| TimeLastModified | Data e hora no alerta última modificação. |
| TimeRaised | Data e hora em que foi gerado o alerta. |
| TimeResolved | Data e hora em que foi resolvido o alerta. Esvaziar se o alerta ainda não tiver sido resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte fornece as pesquisas de registo de exemplo para registos alertas recolhidas por esta solução.  

| Consulta | Descrição |
|:--|:--|
| Tipo = SourceSystem alerta = OpsManager AlertSeverity = erro TimeRaised > agora 24 horas | Alertas críticos elevadas durante das últimas 24 horas |
| Tipo = AlertSeverity alerta = aviso TimeRaised > agora 24 horas | Alertas de aviso elevadas durante das últimas 24 horas  |
| Tipo = SourceSystem alerta = OpsManager AlertState! = TimeRaised fechada > agora 24 horas e #124; medir count() como contagem por SourceDisplayName | Origens de alertas ativas elevadas durante das últimas 24 horas |
| Tipo = SourceSystem alerta = OpsManager AlertSeverity = erro TimeRaised > agora 24 horas AlertState! = fechado | Alertas críticos elevadas durante das últimas 24 horas que ainda estão ativas |
| Tipo = SourceSystem alerta = OpsManager TimeRaised > agora 24 horas AlertState = fechado | Alertas elevadas durante das últimas 24 horas que são agora fechadas |
| Tipo = SourceSystem alerta = OpsManager TimeRaised > #124; & agora - 1 dia medir count() como contagem por AlertSeverity | Alertas elevadas durante o dia 1 passado agrupado por sua gravidade |
| Tipo = SourceSystem alerta = OpsManager TimeRaised > #124; & agora - 1 dia Ordenar RepeatCount desc | Alertas elevadas durante o dia 1 passado, ordenado pelo respetivo valor contagem de repetição |

## <a name="next-steps"></a>Próximos passos

- Saiba mais acerca de [alertas de análise de registo](log-analytics-alerts.md) para obter detalhes sobre gerar alertas a partir do registo de análise.

<properties 
   pageTitle="Registos de eventos do Windows na análise de registo | Microsoft Azure"
   description="Registos dos eventos do Windows são um das origens de dados mais comuns utilizadas pela análise de registo.  Este artigo descreve como configurar o conjunto de registos dos eventos do Windows e detalhes de registos criam no repositório de OMS."
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

# <a name="windows-event-log-data-sources-in-log-analytics"></a>Origens de dados do Windows registo de eventos no registo de análise

Registos dos eventos do Windows são um das mais comuns de [origens de dados](log-analytics-data-sources.md) utilizados para agentes do Windows, uma vez que este é o método utilizado pela maioria das aplicações para iniciar sessão informações e erros.  Pode recolher eventos de registos padrão, tal como o sistema e de aplicações para além de especificar quaisquer registos personalizados criados por aplicações de que precisa para monitorizar a.

![Eventos do Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Os registos de configurar eventos do Windows

Configure os registos de eventos do Windows no [menu de dados em definições de análise de registo](log-analytics-data-sources.md#configuring-data-sources).

Registo Analytics só será recolher eventos dos registos de eventos do Windows que são especificados nas definições de.  Pode adicionar um novo registo escrevendo na nome o registo e clicando em **+**.  Para cada registo, serão recolhidos apenas eventos com os severities selecionados.  Verifique os severities para o registo específico ao qual pretende recolher.  Não pode fornecer qualquer dos critérios adicional para filtrar eventos.

![Configurar eventos do Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Recolha de dados

Análise de registo irá recolher cada evento que corresponde a uma gravidade selecionada a partir de um registo de eventos monitorizada, tal como o evento for criado.  O agente registará o seu lugar no cada registo de eventos recolhe a partir de.  Se o agente de ficar offline para um período de tempo, em seguida, a análise de registo irá recolher eventos a partir de onde-última parou, mesmo se esses eventos foram criados enquanto o agente estava offline.


## <a name="windows-event-records-properties"></a>Propriedades de registos de eventos do Windows

Registos de eventos do Windows tenham um tipo de **evento** e tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Computador            | Nome do computador que o evento foi recolhido a partir do. |
| EventCategory       | Categoria do evento. |
| EventData           | Todos os dados de evento no formato não processado. |
| IDdoevento             | Número do evento. |
| EventLevel          | Gravidade do evento no formulário numérico. |
| EventLevelName      | Gravidade do evento no formulário de texto. |
| Registo de eventos            | Nome do que o evento foi recolhido a partir do registo de eventos. |
| ParameterXml        | Valores de parâmetros de evento no formato XML. |
| ManagementGroupName | Nome do grupo de gestão de agentes SCOM.  Para os outros agentes, esta é AOI-<workspace ID> |
| RenderedDescription | Descrição do evento com valores de parâmetros |
| Origem              | Origem do evento. |
| SourceSystem  | Tipo de agente que o evento foi recolhido a partir do. <br> Ligar OpsManager – agente do Windows, quer directo ou SCOM <br> Linux – todos os agentes de Linux  <br> AzureStorage – diagnósticos do Azure |
| TimeGenerated       | Data e hora o evento foi criado no Windows. |
| Nome de utilizador            | Nome de utilizador da conta que registou o evento. |



## <a name="log-searches-with-windows-events"></a>Pesquisas de registo de eventos do Windows

A tabela seguinte fornece exemplos diferentes de pesquisas de registo que obter registos de eventos do Windows.

| Consulta | Descrição |
|:--|:--|
| Tipo = evento | Todos os eventos do Windows. |
| Tipo = evento EventLevelName = erro | Todos os eventos do Windows com gravidade do erro. |
| Tipo = evento & #124; Medir count() pela origem | Eventos de contagem do Windows pela origem. |
| Tipo = evento EventLevelName = erro & #124; Medir count() pela origem | Eventos de erro de contagem do Windows pela origem. |

## <a name="next-steps"></a>Próximos passos

- Configure a análise de registo para recolher outras [origens de dados](log-analytics-data-sources.md) para uma análise.
- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções.  
- Utilize [Campos personalizados](log-analytics-custom-fields.md) para analisar os registos de eventos para os campos individuais.
- Configure a [coleção de contadores de desempenho](log-analytics-data-sources-performance-counters.md) a partir do seu agentes do Windows.
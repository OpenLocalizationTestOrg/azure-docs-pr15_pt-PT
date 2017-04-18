<properties
   pageTitle="O IIS regista no registo de análise | Microsoft Azure"
   description="Serviços de informação Internet (IIS) armazena atividade de utilizador em ficheiros de registo que podem ser recolhidos pela análise de registo.  Este artigo descreve como configurar o conjunto de registos do IIS e detalhes de registos criam no repositório de OMS."
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

# <a name="iis-logs-in-log-analytics"></a>Registos de IIS na análise de registo
Serviços de informação Internet (IIS) armazena atividade de utilizador em ficheiros de registo que podem ser recolhidos pela análise de registo.  

![Registos do IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Os registos de configuração do IIS
Análise de registo recolhe entradas de ficheiros de registo criados pelo IIS, para que tem de [configurar o IIS para o registo](https://technet.microsoft.com/library/hh831775.aspx).

Registo apenas suporta ficheiros de registo do IIS armazenados no formato de W3C e de análise não suporta campos personalizados ou IIS avançadas registo.  
Análise de registo não recolher registos no formato nativo NCSA ou IIS.

Configure registos do IIS no registo a análise no [menu de dados em definições de análise de registo](log-analytics-data-sources.md#configuring-data-sources).  Não existe nenhuma configuração obrigatório além selecionar **ficheiros de registo de formato de recolher W3C IIS**.

Recomendamos que quando ativa a coleção de registo do IIS, deve configurar a definição de sobreposição de registo do IIS em cada servidor.


## <a name="data-collection"></a>Recolha de dados

Análise de registo recolhe entradas de registo do IIS a partir de cada origem ligada (Product Key) a cada 15 minutos.  O agente regista o seu lugar na cada registo de eventos que recolhe a partir de.  Se o agente de ficar offline, em seguida, a análise de registo recolhe eventos em-última a interrompeu, mesmo se esses eventos foram criados enquanto o agente estava offline.


## <a name="iis-log-record-properties"></a>Propriedades de registo de registo do IIS

Registos de registo do IIS tenham um tipo de **W3CIISLog** e tem as propriedades da tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
| Computador | Nome do computador que o evento foi recolhido a partir do. |
| cIP | Endereço IP do cliente. |
| csMethod | Método de pedido de como obter ou mensagem. |
| csReferer | Site que o utilizador seguiu uma ligação a partir do site atual. |
| csUserAgent | Tipo de browser do cliente. |
| csUserName | Nome do utilizador autenticado que pode ser consultada o servidor. Os utilizadores anónimos são indicados por um hífen. |
| csUriStem | Alvo do pedido de tal como uma página web. |
| csUriQuery | Consulta, se existirem, que o cliente estava a tentar executar. |
| ManagementGroupName | Nome do grupo de gestão de agentes do Gestor de operações.  Para os outros agentes, esta é AOI -\<ID da área de trabalho\> |
| RemoteIPCountry | País/região do endereço IP do cliente. |
| RemoteIPLatitude | Latitude do endereço IP do cliente. |
| RemoteIPLongitude | Longitude do endereço IP do cliente. |
| scStatus | Código de estado HTTP. |
| scSubStatus | Código de erro de subestado. |
| scWin32Status | Código de estado do Windows. |
| sIP | Endereço IP do servidor web. |
| SourceSystem  | OpsMgr |
| Desporto | Porta do servidor o cliente de ligado. |
| sSiteName | Nome do site do IIS. |
| TimeGenerated | Data e hora que a entrada foi registada. |
| TimeTaken | Período de tempo para processar o pedido em milissegundos. |

## <a name="log-searches-with-iis-logs"></a>Registo de pesquisas com registos do IIS

A tabela seguinte fornece exemplos diferentes de consultas de registo que obtêm IIS registos.

| Consulta | Descrição |
|:--|:--|
| Tipo = IISLog | Todos os registos de registo do IIS. |
| Tipo = IISLog EventLevelName = erro | Todos os eventos do Windows com gravidade do erro. |
| Tipo = W3CIISLog & #124; Medir count() por cIP | Contagem de IIS entradas de registo ao endereço IP do cliente. |
| Tipo = W3CIISLog csHost = "www.contoso.com" & #124; Medir count() por csUriStem | Contagem de IIS inicie sessão entradas através do URL para o anfitrião www.contoso.com. |
| Tipo = W3CIISLog & #124; Medir Sum(csBytes) por computador e #124; início 500000| Total de bytes recebidos por cada computador IIS. |

## <a name="next-steps"></a>Próximos passos

- Configure a análise de registo para recolher outras [origens de dados](log-analytics-data-sources.md) para uma análise.
- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções.
- Configure alertas em análises de registo para importante notificá-lo de condições importantes que se encontram nos registos do IIS.

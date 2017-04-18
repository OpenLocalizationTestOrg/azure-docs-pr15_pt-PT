<properties
    pageTitle="Documentação do Azure administração pública | Microsoft Azure"
    description="Isto fornece uma comparação das funcionalidades e orientações sobre como desenvolver aplicações para administração pública do Azure."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Azure administração pública monitorização e gestão

Este artigo descreve a monitorização e serviços de gestão de variações e considerações para o ambiente do Azure Governo.

## <a name="automation"></a>Automatização

Automatização está normalmente disponível no Azure Governo.

### <a name="variations"></a>Variações

As seguintes funcionalidades de automatização não estão atualmente disponíveis no Azure Governo.

+ Criação de uma credencial do princípio de serviço para autenticação

Para mais informações, consulte a [documentação de pública de automatização](../automation/automation-intro.md).

## <a name="log-analytics"></a>Análise de registo

Análise de registo está normalmente disponível no Azure Governo.

### <a name="variations"></a>Variações

As seguintes funcionalidades de análise de registo e soluções não estão atualmente disponíveis no Azure Governo.

+ Soluções que estão na pré-visualização no Microsoft Azure, incluindo:
  - Solução de monitorização de rede
  - Solução de monitorização de dependência de aplicação
  - Solução o Office 365
  - Solução de análise de atualizar do Windows 10
  - Solução de informações da aplicação
  - Solução de funcionamento em rede Analytics Azure
  - Solução de análise de automatização Azure
  - Solução de chave cofre Analytics
+ Soluções e funcionalidades que requerem atualizações do software no local, incluindo:
  - Integração com o sistema de centro de operações Gestor 2016 (versões anteriores do Gestor de operações são suportados)
  - Grupos de computadores a partir do Gestor de configuração do Centro de sistema
  - Solução concentrador de superfície
+ Funcionalidades que estão na pré-visualização no Azure público, incluindo:
  - Exportação de dados ao Power BI
+ Métricas Azure e diagnósticos de Azure
+ Aplicações móveis do operações gestão conjunto de aplicações

Os URLs para análise de registo são diferentes no Azure Governo:

| Azure público | Azure administração pública | Notas |
|--------------|------------------|-------|
| MMS.microsoft.com | OMS.microsoft.us | Portal de análise de registo |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Recolha API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicação agente - [Configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicação agente - [Configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicação agente - [Configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |


As seguintes funcionalidades de análise de registo se comportam de forma diferente em Azure Governo:

+ O Windows Agent têm de ser transferido a partir do [portal de análise de registo](https://oms.microsoft.us) para administração pública do Azure.
+ Para ligar o seu servidor de gestão do Gestor de operações do Centro de sistema para a análise de registo, tem de transferir e importar pacotes de gestão atualizado.
  1. Transfira e guarde [pacotes de gestão de atualizados](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Deszipe o ficheiro que transferiu.
  3. Importe os pacotes de gestão para o Gestor de operações. Para obter informações sobre como importar um pacote de gestão de um disco, veja [como importar um pacote de gestão do Gestor de operações](http://technet.microsoft.com/library/hh212691.aspx) no Web site da Microsoft TechNet.
  4. Para ligar o Gestor de operações a análise de registo, siga os passos no [Gestor de operações de ligar para a análise de registo](../log-analytics/log-analytics-om-agents.md).


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

+ Posso migrar dados de análise de registo no Microsoft Azure para administração pública do Azure?
  - Não. Não é possível mover dados ou a área de trabalho do Microsoft Azure para administração pública do Azure.
+ Posso mudar entre o Microsoft Azure e administração pública do Azure áreas de trabalho a partir do portal de análise de registo de conjunto de aplicações de gestão de operações?
  - Não. Portais para Microsoft Azure e administração pública do Azure são separados e não a partilhar informações.

Para mais informações, consulte a [documentação de pública de análise de registo](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximos passos

Para informações suplementares e atualizações, subscreva o <a href="https://blogs.msdn.microsoft.com/azuregov/">blogue do Microsoft Azure administração pública.</a>

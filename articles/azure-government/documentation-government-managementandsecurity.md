<properties
    pageTitle="Documentação do Azure administração pública | Microsoft Azure"
    description="Este procedimento fornece uma comparision das funcionalidades e orientações sobre como desenvolver aplicações para administração pública do Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Gestão de administração pública Azure e segurança

## <a name="automation"></a>Automatização

Automatização está normalmente disponível no Azure Governo.

### <a name="variations"></a>Variações

As seguintes funcionalidades de automatização não estão atualmente disponíveis no Azure Governo.

+ Criação de uma credencial do princípio de serviço para autenticação

Para mais informações, consulte a [documentação de pública de automatização](../automation/automation-intro.md).


##  <a name="key-vault"></a>Chave Cofre
Para obter detalhes sobre este serviço e como utilizá-lo, consulte o artigo o <a href="https://azure.microsoft.com/documentation/services/key-vault">documentação pública Azure chave cofre.</a>
### <a name="data-considerations"></a>Considerações de dados
As seguintes informações identificam o limite do Azure Governo para Azure Cofre de chave:

| Dados regulamentada/controlados permitidos | Dados regulamentada/controlados não é permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados são encriptados com uma chave de Azure chave cofre podem conter dados Regulated/controlados. | Não são permitido Azure chave cofre metadados que contenha dados exportar controlado. Este metadados incluem todos os dados de configuração introduzidos quando criar e manter o seu Cofre chave.  Não introduza dados Regulated/controlados para os seguintes campos: nomes de grupo de recursos, nomes de chave cofre, nome da subscrição |

Chave Cofre está normalmente disponível no Azure Governo. Tal como público, não existe nenhuma extensão, para que a chave cofre só está disponível através do PowerShell e clip.
## <a name="log-analytics"></a>Análise de registo
Análise de registo está normalmente disponível no Azure Governo. 

### <a name="variations"></a>Variações

As seguintes funcionalidades de análise de registo e soluções não estão atualmente disponíveis no Azure Governo. Esta lista é atualizada quando o estado das funcionalidades / altera de soluções.

+ Soluções que estão na pré-visualização no Azure público, incluindo:
  - Solução de monitorização de rede
  - Monitorização de dependência de aplicações
  - Solução o Office 365
  - Solução de análise de atualizar do Windows 10
  - Informações de aplicação
  - Solução de funcionamento em rede Analytics Azure
  - Análise de automatização Azure
  - Chave cofre Analytics
+ Soluções e funcionalidades que requerem atualizações do software no local, incluindo
  - Integração com o sistema de centro de operações Gestor 2016 (versões anteriores do Gestor de operações são suportados)
  - Grupos de computadores a partir do Gestor de configuração do Centro de sistema
  - Solução concentrador de superfície
+ Funcionalidades que estão na pré-visualização no Azure público, incluindo
  - Exportação de dados para obter
+ Métricas Azure e diagnósticos de Azure
+ Aplicações móveis OMS

Os URLs para análise de registo são diferentes no Azure Governo:

| Azure público | Azure administração pública | Notas |
|--------------|------------------|-------|
| MMS.microsoft.com | OMS.microsoft.us | Portal de análise de registo |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [Recolha API](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicação agente - [Configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicação agente - [Configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicação agente - [Configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |


As seguintes funcionalidades de análise de registo que têm um comportamento diferente no Azure Governo:

+ O Windows agent têm de ser transferido a partir do [portal de análise de registo](https://oms.microsoft.us) para administração pública do Azure.
+ Para ligar o seu servidor de gestão do Gestor de operações do Centro de sistema para a análise de registo, tem de transferir e importar atualizada de gestão de pacotes.
  1. Transfira e guarde [atualizados pacotes de gestão](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. O ficheiro que transferiu descomprimir o ficheiro.
  3. Importe os pacotes de gestão para o Gestor de operações. Para obter informações sobre como importar um pacote de gestão da partir de um disco, consulte o tópico de [como importar um pacote de gestão do Gestor de operações](http://technet.microsoft.com/library/hh212691.aspx) no Web site da Microsoft TechNet.
  4. Para ligar o Gestor de operações a análise de registo, siga os passos no [Gestor de operações de ligar para a análise de registo](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

+ Pode migrar dados de análise de registo no público Azure para Azure Governo?
  - Não. Não é possível mover dados ou a área de trabalho de pública Azure para Azure administração pública.
+ Pode alternar entre públicas Azure e administração pública do Azure áreas de trabalho a partir do portal de análise de registo de OMS?
  - Não. Portais para o público Azure e administração pública do Azure são separados e não a partilhar informações. 

Para mais informações, consulte a [documentação de pública de análise de registo](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximos passos

Para informações suplementares e atualizações, subscreva o <a href="https://blogs.msdn.microsoft.com/azuregov/">blogue do Microsoft Azure administração pública.</a>
 

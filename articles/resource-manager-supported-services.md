<properties
   pageTitle="Gestor de recursos suportados serviços | Microsoft Azure"
   description="Descreve os fornecedores de recursos que suportam o Gestor de recursos, respectivos esquemas e versões API disponíveis e as regiões que podem alojar os recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Fornecedores de Gestor de recursos, regiões, as versões API e esquemas

Gestor de recursos do Azure fornece uma nova forma para implementar e gerir os serviços que constituem as suas aplicações. Gestor de recursos de suporte de serviços sua maioria, mas não em todas as e alguns serviços apenas parcialmente Gestor de recursos de suporte. Este tópico fornece uma lista de fornecedores de recursos suportados para o Gestor de recursos do Azure.

Quando implementar os recursos, também precisa de saber quais regiões esses recursos de suporte e quais as versões API estão disponíveis para os recursos. A secção [suportados regiões](#supported-regions) mostra-lhe como saber que trabalho regiões para a sua subscrição e recursos. A secção [API suportadas versões](#supported-api-versions) mostra-lhe como determinar quais as versões API pode utilizar.

Para ver quais os serviços são suportados no portal do Azure e portal clássico, consulte o artigo [gráfico de disponibilidade portal Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver quais os serviços de suporte mover recursos, consulte o artigo [Mover recursos para novo grupo de recursos ou subscrição](resource-group-move-resources.md).

As tabelas seguintes listam quais implementação de suporte do Microsoft services e gestão de através do Gestor de recursos e que não o fizer. A ligação na coluna **Modelos de guia de introdução** envia uma consulta para o repositório de modelos do Azure guia de introdução para o fornecedor de recurso especificado. Modelos de guia de introdução são adicionados e actualizados frequentemente. A presença de uma ligação para um serviço específico não seja necessariamente significa que a consulta devolve os modelos do repositório. Também existem vários fornecedores de recursos de terceiros que suportam o Gestor de recursos. Saiba como ver todos os fornecedores de recursos na secção [fornecedores de recursos e tipos de](#resource-providers-and-types) .


## <a name="compute"></a>Cluster

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------------------------ |-------- | ------ | ------ |
| Batch   | Sim     | [Lote resto](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Contentor | Sim | [Serviço de contentor resto](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Serviços de ciclo de vida do Dynamics | Sim  |      |        |  |
| Conjuntos de escala | Sim | [Escala definida resto](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| Serviço ferro | Sim  | [Serviço ferro resto](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuais | Sim | [RESTO VM](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Máquinas virtuais (clássico) | Limitado | - | - | - |
| Aplicação Remote | N      | -  | - | - |
| Serviços em nuvem (clássico) | Limitado (ver abaixo) | - | - | - |

Máquinas virtuais (clássico) refere-se para recursos que foram implementados através do modelo de implementação clássica, em vez de através do modelo de implementação do Gestor de recursos. Em geral, estes recursos não suportam operações de Gestor de recursos, mas existem algumas operações que tenham sido activadas. Para mais informações sobre estes modelos de implementação, consulte [Noções sobre o Gestor de recursos de implementação e implementação clássica](resource-manager-deployment-model.md). 

Serviços em nuvem (clássicos) podem ser utilizados com outros recursos clássicos; No entanto, recursos clássicos não tirar partido de todas as funcionalidades de Gestor de recursos e não são uma boa opção para futuras soluções. Em vez disso, considere alterar a sua infraestrutura de aplicação para utilizar os recursos dos espaços de nomes Microsoft.Compute, Microsoft.Storage e Microsoft.Network.


## <a name="networking"></a>Funcionamento em rede

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | -------  | -------- | ------ | ------ |
| Gateway de aplicação | Sim | [Aplicação Gateway resto](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Sim | [RESTO DE DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Sim  | [ExpressRoute resto](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Balanceador de carga | Sim  | [Balanceador de carga resto](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gestor de tráfego | Sim | [Gestor de tráfego resto](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Redes virtuais | Sim| [Rede virtual resto](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN Gateway | Sim | [RESTO de Gateway de rede](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[ligações](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Armazenamento

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Armazenamento | Sim  | [Armazenamento resto](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Conta de armazenamento](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Sim  |         |        |  |

## <a name="databases"></a>Bases de dados

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sim  | [DocumentDB resto](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis Cache | Sim |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de dados SQL | Sim | [Base de dados do SQL resto](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-pré-visualização](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Armazém de dados SQL | Sim |   |      |


## <a name="web--mobile"></a>Web & Mobile

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| API aplicações | Sim |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [API aplicações](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gestão de API | Sim  | [Gestão de API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Conteúdo moderador | Sim |   |   |   |
| Aplicação de função | Sim |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Aplicações de lógica | Sim   | [API REST do serviço de fluxo de trabalho](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Aplicações móveis | Sim |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Atribuições de dispositivos móveis | Sim  |  [Cativação móvel resto](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Pesquisa | Sim  | [Pesquisa resto](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Web Apps | Sim |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Análise

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | -------  | -------- | ------ | ------ |
| Catálogo de dados | Sim  | [Catálogo de dados restantes](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Fábrica de dados | Sim | [Dados fábrica resto](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Lake a análise de dados | Sim |   |   [2015-10-01-pré-visualização](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Arquivo de dados de Lake | Sim  | [RESTO de arquivo de Lake de dados](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [2015-10-01-pré-visualização](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Sim | [HDInsights resto](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Formação do computador | Sim | [RESTO de aprendizagem automática](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [05-01-pré-visualização 2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Análise da cadeia | Sim  | [Análise de vapor resto](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | Sim | [Power BI incorporado resto](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Informações da empresa

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| Disfunção serviços | Sim |  | [02-01-pré-visualização 2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet ações

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| Concentrador de evento | Sim  | [Concentrador de evento resto](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Sim | [Concentrador IoT resto](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notificação concentradores | Sim | [Notificação concentrador resto](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Multimédia & CDN

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| CDN | Sim | [RESTO DE CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Serviço de multimédia | Sim | [RESTO dos serviços de multimédia](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Integração de híbrido

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| Serviços de BizTalk | Sim |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Serviço de recuperação | Sim | [Recuperação resto do site](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Serviço Bus | Sim | [Serviço Bus resto](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identidade e gestão de acesso 

Azure Active Directory funciona com o Gestor de recursos para ativar o controlo de acesso baseado em funções para a sua subscrição. Para saber como utilizar o controlo de acesso baseado em funções e do Active Directory, consulte o artigo [O controlo de acesso baseado em funções Azure](./active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Serviços de programador 

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| Informações de aplicação | Sim  | [Informações restantes](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Mapas Bing | Sim  |          |        |  |
| DevTest Labs | Sim |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Conta do Visual Studio | Sim   |          | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Gestão e segurança

| Serviço | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------- | ------ | ------ |
| Automatização | Sim | [Automatização resto](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Cofre chave | Sim | [Chave cofre resto](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Cofre chave](resource-manager-template-keyvault.md)<br />[Secreta cofre chave](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Informações operacionais avançadas | Sim |          |        |  |
| Programador | Sim  | [Scheduler resto](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Segurança (pré-visualização) | Sim | [Segurança resto](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Gestor de recursos

| Funcionalidade | Gestor de recursos do activado | REST API | Esquema | Modelos de guia de introdução |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorização | Sim | [Bloqueios de gestão](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controlo de acesso baseado em funções](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Bloquear de recursos](resource-manager-template-lock.md)<br />[Atribuições de funções](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos | Sim | [Recursos ligados](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Ligações de recursos](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Fornecedores de recurso e de tipos

Quando implementar recursos, é necessário frequentemente obter informações sobre os fornecedores de recursos e tipos de. Pode obter estas informações através de REST API, Azure PowerShell ou clip de Azure.

Para trabalhar com um fornecedor de recurso, que fornecedor de recursos deve estar registado com a sua conta. Por predefinição, vários fornecedores de recursos são registados automaticamente; No entanto, poderá ter de registar manualmente alguns fornecedores de recursos. Os exemplos abaixo mostram como obter o estado de registo do fornecedor de recursos e registar o fornecedor de recursos, se necessário.

### <a name="portal"></a>Portal

Pode facilmente ver uma lista de fornecedores de recursos suportados com os seguintes passos:

1. Selecione **as minhas permissões**.

    ![minhas permissões](./media/resource-manager-supported-services/my-permissions.png)

2. Selecione o **Estado do fornecedor de recursos**

    ![Estado do fornecedor de recursos](./media/resource-manager-supported-services/resource-provider-status.png)

3. Consulte a lista completa dos fornecedores de recursos para a sua subscrição.

    ![lista de fornecedores de recursos](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>REST API

Para todos os recursos disponíveis fornecedores, incluindo os respetivos tipos, localizações, as versões API e estado de registo, utilizam a operação de [todos os fornecedores de recursos da lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Se precisar de registar um fornecedor de recursos, consulte o artigo [registar uma subscrição com um fornecedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

O exemplo seguinte mostra como todos os fornecedores de recursos disponíveis.

    Get-AzureRmResourceProvider -ListAvailable
    

O exemplo seguinte mostra como obter os tipos de recurso para um fornecedor de recurso específico.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
O resultado é semelhante a:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Para registar um fornecedor de recursos, forneça o espaço de nomes:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Clip Azure

O exemplo seguinte mostra como todos os fornecedores de recursos disponíveis.

    azure provider list
    
O resultado é semelhante a:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Pode guardar as informações de um fornecedor de recurso específico para um ficheiro com o seguinte comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Para registar um fornecedor de recursos, forneça o espaço de nomes:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Regiões suportadas

Quando implementar recursos, normalmente, tem de especificar uma região para os recursos. Gestor de recursos é suportado em todas as regiões, mas os recursos que implementar podem não ser suportados em todas as regiões. Além disso, poderão existir limitações na sua subscrição do que o impedem de utilizar algumas regiões que suportam o recurso. Estas limitações poderão estar relacionado com problemas para o seu país doméstica do imposto ou o resultado de uma política de colocado pelo seu administrador de subscrição para utilizar apenas determinadas regiões. 

Para obter uma lista completa de todas as regiões suportadas para todos os serviços Azure, consulte o artigo [serviços por região](https://azure.microsoft.com/regions/#services); No entanto, esta lista pode incluir regiões que não suporta a sua subscrição. Pode determinar as regiões para um tipo de recurso específico compatível com a sua subscrição através do portal, REST API, PowerShell ou clip de Azure.

### <a name="portal"></a>Portal

Pode ver as regiões suportadas para um tipo de recurso através dos seguintes passos:

1. Selecione **mais serviços** > **Explorer do recurso**.

    ![Explorador de recursos](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Abra o nó **fornecedores** .

    ![Selecione fornecedores](./media/resource-manager-supported-services/select-providers.png)

3. Selecionar um fornecedor de recursos e ver as versões de API e regiões suportadas.

    ![fornecedor de visualização](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API

Para descobrir que regiões estão disponíveis para um tipo de recurso específico na sua subscrição, utilize a operação de [todos os fornecedores de recursos da lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

O exemplo seguinte mostra como obter as regiões suportadas para web sites.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
O resultado é semelhante a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>Clip Azure

O exemplo seguinte devolve todas as localizações suportadas para cada tipo de recurso.

    azure location list

Também pode filtrar os resultados de localização com um utilitário JSON como [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Qual devolve:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versões de API suportadas

Quando implementar um modelo, tem de especificar uma versão de API para utilizar para criar cada recurso. A versão da API corresponde à versão de operações de REST API disponibilizadas pelo fornecedor de recursos. Tal como um fornecedor de recurso permite que as novas funcionalidades, disponibilização de uma nova versão do REST API. Por conseguinte, a versão da API do especificar no seu modelo afeta as propriedades que pode especificar no modelo. Em geral, que pretende selecionar a versão mais recente da API quando a criação de modelos. Para obter modelos existentes, pode decidir se pretende continuar a utilizar uma versão anterior do API ou atualizar o seu modelo para a versão mais recente tirar partido das novas funcionalidades.

### <a name="portal"></a>Portal

Para saber qual as versões de API suportadas da mesma forma que determinada suportadas regiões (apresentadas anteriormente).

### <a name="rest-api"></a>REST API

Para descobrir quais as versões API estão disponíveis para tipos de recursos, utilize a operação de [todos os fornecedores de recursos da lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

O exemplo seguinte mostra como obter as versões de API disponíveis para um tipo de recurso específico.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
O resultado é semelhante a:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>Clip Azure

Pode guardar as informações (incluindo as versões de API disponíveis) para um fornecedor de recurso para um ficheiro com o seguinte comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Pode abrir o ficheiro e localize o elemento **apiVersions**

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a criação de modelos de Gestor de recursos, consulte [Gestor de recursos do Azure criação de modelos](resource-group-authoring-templates.md).
- Para obter informações sobre como implementar recursos, consulte o artigo [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).

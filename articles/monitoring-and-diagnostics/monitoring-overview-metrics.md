<properties
    pageTitle="Descrição geral de métricas no Microsoft Azure | Microsoft Azure"
    description="Descrição geral de métricas e respectivas utilizações no Microsoft Azure"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Descrição geral de métricas no Microsoft Azure 

Este artigo descreve o que são métricas no Microsoft Azure, os respetivos benefícios e como começar a utilizá-los.  

## <a name="what-are-metrics"></a>O que são métricas?

Azure Monitor permite-lhe consumir telemetria para ganham visibilidade sobre o desempenho e o estado de funcionamento do seu das cargas de trabalho no Azure. O tipo de dados de telemetria Azure mais importante são métricas (também denominadas contadores de desempenho) emitidas por recursos mais Azure. O Monitor Azure fornece várias formas para configurar e consumir nestas métricas de monitorização e resolução de problemas.


## <a name="what-can-you-do-with-metrics"></a>O que fazer com métricas?

Métricas são uma fonte útil de telemetria e permitem-lhe fazer o seguinte:

- **Controlar o desempenho** do seu recurso (como VM, Web site ou aplicação lógica) ao desenho de respetiva métricas num gráfico de portal e afixação esse gráfico a um dashboard.
- **Receber uma notificação de um problema** com que afetam o desempenho do seu recurso quando uma métrica este se cruza um determinado limiar.
- **Configurar ações de automatizado**, tal como autoscaling um recurso ou accionar um livro de execuções quando uma métrica este se cruza um determinado limiar.
- **Executar avançadas analytics** ou elaboração de relatórios no desempenho ou utilização de tendências dos seus recursos.
- **Arquivo** o histórico de desempenho ou estado de funcionamento do seu efeitos **para conformidade/auditoria** dos recursos.

##  <a name="metric-characteristics"></a>Métricas características
Métricas têm as seguintes características:

- Todas as métricas tem **frequência 1 minutos**. Recebe um valor métrico minuto a partir do seu recurso, que lhe dá perto visibilidade em tempo real sobre o estado e o estado de funcionamento do seu recurso.
- Métricas estão **disponível fora-de-de-box sem ser necessário especificá-las optar ativamente por participar no** ou configurar o diagnóstico adicional.
- Pode aceder a **30 dias do histórico** para cada métrica. Pode procurar rapidamente tendências recentes e mensal no desempenho ou estado de funcionamento do seu recurso.

Pode:

- Descobrir facilmente, access e **Ver todas as métricas** através do portal do Azure quando selecionar um recurso e desenhá-los num gráfico. 
- Configure uma métrica **regra alerta que envia uma notificação ou entra em ação automatizada** quando a métrica deste se cruza o limiar que definiu. Autoscale é uma acção automatizada especial que permite-lhe dimensionar saída seu recurso para cumprir os pedidos recebidos ou carregar no seu web site ou calcular recursos. Pode configurar uma regra de definição de Autoscale para dimensionar out / com base numa métrica passagem um determinado limiar.
- Métricas de **arquivo** para já ou utilizá-los para elaboração de relatórios offline. Encaminhar o seu métricas de armazenamento de blob quando configurar definições de diagnóstico para o seu recurso.
- Métricas de **sequência** a um concentrador de evento, permitindo-lhe, em seguida, encaminhá-los para a análise da cadeia de Azure ou aplicações personalizadas para uma análise de tempo perto real. Que pode fazer a com definições de diagnóstico.
- **Encaminhar** todas as métricas para OMS a análise de registo para desbloquear analytics instantâneas, pesquisa e personalizado alertar para dados de métricas dos recursos.
- **Consumir** métricas por novas Azure o Monitor REST APIs.
- Métricas de **consulta** utilizando os Cmdlets do PowerShell ou em diferentes plataformas REST API.

 ![Encaminhamento de métricas no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Métricas de acesso através do portal
Eis uma rápida instruções sobre a criação de um gráfico métrico através do portal do Azure

### <a name="view-metrics-after-creating-a-resource"></a>Métricas de vista depois de criar um recurso
1. Abrir portal Azure
2. Crie um novo serviço de aplicação - Web Site.
3. Depois de criar um Web site, aceda a pá a descrição geral do web site.
4. Pode ver a nova métricas como um mosaico 'Monitorização'. Pode editar o mosaico e selecione mais métricas

 ![Métricas num recurso no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Aceder a todas as métricas num único local
1. Abra o portal do Azure 
2. Navegue para o novo separador Monitor e selecione a opção de métricas em-lo 
3. Pode selecionar a sua subscrição, grupo de recursos e o nome do recurso a partir da lista pendente. 
4. Agora pode ver a lista de métricas disponíveis. 
5. Selecione a métrica do que lhe interessam e desenhá-lo. 
6. Pode afixá-lo para o dashboard clicando no pino no canto superior direito.

 ![Aceder a todas as métricas num único local no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] Pode aceder a nível do anfitrião métricas de VMs (Gestor de recursos do Azure com base) e conjuntos de escala de VM sem qualquer programa de configuração de diagnóstico adicional. Estas novas métricas de nível de anfitrião estão disponíveis para Windows e Linux instâncias. Estes métricas não são devem ser confundidas com as métricas de nível de SO convidado tiver acesso a ativar diagnósticos do Azure no seu VMs ou VMSS. Para saber mais sobre como configurar diagnósticos do Azure, consulte o artigo [o que é diagnósticos do Microsoft Azure](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Métricas de acesso através da REST API
Métricas Azure podem ser acedidas através do Azure Monitor APIs. Existem duas APIs que ajudá-lo a descobrir e aceder a métricas. Utilizar o: 

- [Definições de azure Monitor métrica REST API](https://msdn.microsoft.com/library/mt743621.aspx) para aceder à lista de métricas disponíveis para um serviço.
- [Azure Monitor métricas REST API](https://msdn.microsoft.com/library/mt743622.aspx) para aceder aos dados de métricas real

>[AZURE.NOTE] Este artigo abrange as métricas através da [nova API para métricas](https://msdn.microsoft.com/library/dn931930.aspx) para recursos Azure. A versão de API para as novas definições de métricas API é 2016-03-01 e a versão para métricas API é 2016-09-01. As definições de métricas de legado e métricas podem ser acedidas com a versão de api 2014-04-01.

Para obter instruções detalhadas mais utilizando o Azure Monitor REST APIs, consulte o artigo [Azure Monitor REST API instruções passo a passo](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opções para métricas de exportação
Pode ir para o pá de registos de diagnóstico no separador Monitor e visualizar as opções de exportação para métricas. Pode selecionar métricas (e registos de diagnóstico) a ser encaminhado para armazenamento de BLOBs, evento concentradores ou para a análise de registo OMS para casos de utilização mencionados anteriormente neste artigo. 

 ![Opções de exportação para métricas no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Pode configurar esta através do Gestor de recursos modelos, [PowerShell](insights-powershell-samples.md), [clip](insights-cli-samples.md) ou [REST APIs](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Fazer uma ação em métricas
Pode receber notificações ou efetuar ações automatizadas métrica dados. Pode configurar regras de alertas ou definições da Autoscale fazê-lo.

### <a name="alert-rules"></a>Regras de alertas
Pode configurar regras de alertas nas métricas. Estas regras alertas podem verificar se uma métrica tem cruzados um determinado limiar e notificá-lo por correio eletrónico ou fogo um webhook que, em seguida, pode ser utilizado para executar qualquer dos scripts personalizados. Também pode utilizar o webhook para configurar 3º integrações de produto.

 ![Métricas e alertas regras no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoscale
Alguns recursos Azure suportam várias instâncias para dimensionar ou para processar as cargas de trabalho. Autoscale aplica-se para serviços de aplicação (Web apps), conjuntos de escala de Máquina Virtual (VMSS) e serviços em nuvem clássica. Pode configurar regras de autoscale para dimensionar ou no quando uma determinada métrica que afetam a sua carga de trabalho se cruza um determinado limiar que especificar. Para mais informações, consulte o artigo [Descrição geral de autoscaling](monitoring-overview-autoscale.md).

 ![Métricas e autoscale no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Métrica e serviços suportados
Azure Monitor é uma novo infraestrutura de métricas. Fornece suporte para os seguintes serviços Azure no portal do Azure e a nova versão do Azure Monitor API:

- VMs (Gestor de recursos do Azure com base)
- Conjuntos de escala VM
- Batch
- Espaço de nomes de concentrador de evento 
- Espaço de nomes do serviço Bus (apenas premium SKU)
- SQL (versão 12)
- Agrupamento de SQL flexível
- Web Sites
- Farms Web
- Aplicações de lógica
- IoT concentradores
- Redis Cache
- Rede: Gateways da aplicação
- Pesquisa

Pode ver uma uma lista detalhada de todos os serviços suportados e os respetivos métricas no [Monitor do Azure métricas - métricas suportados por tipo de recurso](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Próximos passos

Consulte as ligações ao longo deste artigo. Para além disso, saiba mais:  

- sobre [métricas comuns para autoscaling](insights-autoscale-common-metrics.md)
- como [criar regras de alertas](insights-alerts-portal.md)





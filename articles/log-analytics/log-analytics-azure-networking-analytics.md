<properties
    pageTitle="Azure solução de análise de rede no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução de análise de funcionamento em rede Azure na análise de registo para rever os registos de grupo de segurança de rede Azure e os registos de Gateway de aplicação do Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>

# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Azure solução de análise de rede (pré-visualização) no registo de análise

>[AZURE.NOTE] Esta é uma [solução de pré-visualização](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Pode utilizar a solução de análise de funcionamento em rede Azure na análise de registo para rever os registos de Gateway de aplicação do Azure e registos de grupo de segurança de rede Azure.

Pode ativar a funcionalidade de registo para registos de Gateway de aplicação do Azure e grupos de segurança de rede Azure. Estes registos são escritos Blob armazenamento onde estes podem, em seguida, ser indexadas pela análise de registo para procurar e análise.

Registos que se seguem são suportados para a aplicação de Gateways:

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

Registos que se seguem são suportados para grupos de segurança de rede:

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução

Utilize as instruções seguintes para instalar e configurar a solução de análise de funcionamento em rede Azure:

1.  Ativar o registo de diagnóstico para os recursos que pretende monitorizar:
  + [Gateway de aplicação](../application-gateway/application-gateway-diagnostics.md)
  + [Grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)
2.  Configure a análise de registo para ler os registos a partir do armazenamento de BLOBs utilizando o processo descrito na [ficheiros JSON armazenamento de BLOBs](../log-analytics/log-analytics-azure-storage-json.md).
3.  Ative a solução de análise de funcionamento em rede Azure utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  

Se não ativar o registo de diagnóstico para um tipo de recurso em particular, as lâminas de dashboard para esse recurso estará em branco.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Reveja os detalhes de coleção de dados de análise de rede do Azure

A solução de análise de funcionamento em rede Azure recolhe registos de diagnóstico do armazenamento de Blobs do Azure dos Gateways de aplicação do Azure e grupos de segurança de rede.
Nenhum agente é necessário para recolha de dados.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para análise de funcionamento em rede Azure.

| Plataforma | Agente de direta | Agente de Gestor de operações centro (SCOM) sistemas | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | Frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Azure|![N](./media/log-analytics-azure-networking/oms-bullet-red.png)|![N](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Sim](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![N](./media/log-analytics-azure-networking/oms-bullet-red.png)|![N](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10 minutos|

## <a name="use-azure-networking-analytics"></a>Utilizar a análise de funcionamento em rede Azure

Depois de instalar a solução, pode ver o resumo do cliente e erros do servidor para sua Gateways aplicação monitorizada utilizando a **Análise de funcionamento em rede Azure** dispor em mosaico na página **Descrição geral** na análise de registo.

![imagem de mosaico de análise de rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Depois de clicar no mosaico de **Descrição geral** , pode ver resumos dos seus registos de início e, em seguida, pesquisa para obter detalhes para as seguintes categorias:

+ Os registos de acesso de Gateway de aplicação
  - Erros de cliente e servidor para registos de acesso de Gateway de aplicação
  - Pedidos por hora para cada Gateway de aplicação
  - Ocorreu uma falha pedidos por hora para cada Gateway de aplicação
  - Erros ao agente do utilizador para a aplicação de Gateways
+ Desempenho do Gateway de aplicações
  - Estado de funcionamento do sistema anfitrião do Gateway de aplicação
  - Percentil máximo e 95 para pedidos de Gateway de aplicação Ocorreu uma falha de
+ Grupo de segurança de rede bloqueados monetários
  - Regras de grupo de segurança de rede com fluxos bloqueados
  - Endereços MAC com fluxos bloqueados
+ Grupo de segurança de rede permitido monetários
  - As regras de grupo de segurança com permitidos fluxos de rede
  - Endereços MAC com fluxos permitidos


![imagem do dashboard de análise de rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![imagem do dashboard de análise de rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![imagem do dashboard de análise de rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![imagem do dashboard de análise de rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Para ver detalhes de resumo do registo

1. Na página **Descrição geral** , clique no mosaico de **Análise de funcionamento em rede Azure** .
2. No dashboard de **Análise de funcionamento em rede Azure** , reveja as informações de resumo de uma das lâminas e, em seguida, clique em para ver informações detalhadas acerca do mesmo na página de pesquisa de registo do.

    Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por hora, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para limitar os resultados.

## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo de análise](log-analytics-log-searches.md) para ver dados detalhados do Azure a análise de rede.

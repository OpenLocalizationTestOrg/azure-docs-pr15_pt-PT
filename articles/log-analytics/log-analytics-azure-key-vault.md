<properties
    pageTitle="Azure solução de chave cofre no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução Azure chave cofre na análise de registo para rever os registos do Azure chave cofre."
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
    ms.date="07/12/2016"
    ms.author="richrund"/>

# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Azure solução de chave cofre (pré-visualização) no registo de análise

>[AZURE.NOTE] Esta é uma [solução de pré-visualização](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Pode utilizar a solução Azure chave cofre na análise de registo para rever o Azure chave cofre AuditEvent registos.

Pode ativar a funcionalidade de registo de eventos de auditoria Azure chave cofre. Estes registos são escritos ao armazenamento de Blobs do Azure onde estes podem, em seguida, ser indexadas pela análise de registo para procurar e análise.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução

Utilize as instruções seguintes para instalar e configurar a solução Azure Cofre de chave:

1.  Ativar o [registo de diagnóstico para chave cofre](../key-vault/key-vault-logging.md) recursos que pretende monitorizar
2.  Configure a análise de registo para ler os registos a partir do armazenamento de BLOBs utilizando o processo descrito na [ficheiros JSON armazenamento de BLOBs](../log-analytics/log-analytics-azure-storage-json.md).
3.  Ative a solução Azure chave cofre utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Reveja os detalhes da coleção de dados Azure chave Cofre

Solução de chave cofre Azure recolhe registos de diagnóstico do armazenamento de Blobs do Azure para Azure chave cofre.
Nenhum agente é necessário para recolha de dados.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para Azure chave cofre.

| Plataforma | Agente de direta | Agente de Gestor de operações centro (SCOM) sistemas | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | Frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Azure|![N](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![N](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Sim](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![N](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![N](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 minutos|

## <a name="use-azure-key-vault"></a>Utilizar o Azure cofre chave

Depois de instalar a solução, pode ver o resumo do pedido de Estados ao longo do tempo para o seu cofres chave monitorizada utilizando o **Azure chave cofre** dispor em mosaico na página **Descrição geral** do registo de análise.

![imagem de mosaico de Cofre de chave do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Depois de clicar no mosaico de **Descrição geral** , pode ver resumos dos seus registos de início e, em seguida, pesquisa para obter detalhes para as seguintes categorias:

- Volume de todas as operações de cofre chave ao longo do tempo
- Ocorreu uma falha volumes de operação ao longo do tempo
- Latência de em média operacional por operação
- Qualidade de serviço para operações com o número de operações demorar mais de 1000 ms e uma lista de operações demorar mais de 1000 ms

![imagem de dashboard do Cofre de chave do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![imagem de dashboard do Cofre de chave do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para ver detalhes de qualquer operação

1. Na página **Descrição geral** , clique no mosaico do **Azure chave cofre** .
2. No dashboard de **Azure chave cofre** , reveja as informações de resumo de uma das lâminas e, em seguida, clique em para ver informações detalhadas acerca do mesmo na página de pesquisa de registo do.

    Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por hora, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para limitar os resultados.

## <a name="log-analytics-records"></a>Análise de registos

A solução Azure chave cofre analisa registos que tenham um tipo de **KeyVaults** que são recolhidos a partir do [AuditEvent registos](../key-vault/key-vault-logging.md) no Azure diagnósticos.  Propriedades para estes registos estão na seguinte tabela.  

| Propriedade | Descrição |
|:--|:--|
| Tipo | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| CallerIpAddress | Endereço IP do cliente quem fez o pedido |
| Categoria      | Nos registos de chave cofre, AuditEvent é o valor único, disponível.|
| CorrelationId | Um GUID opcional que o cliente pode passar para correlacionar registos do lado do cliente com os registos do serviço lado (tecla cofre). |
| DurationMs | Data/hora demorou de pedido de REST API, em milissegundos do serviço. Isto não inclui o latência da rede, para que a hora em que medida do lado do cliente pode não corresponder a este período de tempo. |
| HttpStatusCode_d | Código de estado HTTP devolvido pelo pedido |
| Id_s       | ID exclusivo do pedido de |
| Identity_o | Identidade do token que foi apresentado quando efetuar o pedido de REST API. Este nome costuma um "utilizador", "principal do serviço" ou uma combinação "utilizador + appId" tal como um pedido de resultantes um cmdlet do PowerShell do Azure maiúsculas/minúsculas. |
| OperationName      | Nome da operação, como documentado no [Registo do Cofre de chave da Azure](../key-vault/key-vault-logging.md)|
| OperationVersion      | Versão REST API pedido pelo cliente|
| RemoteIPLatitude | Latitude do cliente quem fez o pedido |
| RemoteIPLongitude | Longitude do cliente quem fez o pedido |
| RemoteIPCountry | País/região do cliente quem fez o pedido  |
| RequestUri_s | URI do pedido de |
| Recurso   | Nome da chave Cofre |
| ResourceGroup | Grupo de recursos de cofre chave |
| ResourceId | ID do Azure Gestor de recursos do recurso. Nos registos de chave cofre, esta é sempre o ID do recurso chave cofre. |
| ResourceProvider | *MICROSOFT. KEYVAULT* |
| ResultSignature  | Estado de HTTP|
| ResultType      | Resultado do pedido de REST API|
| SubscriptionId | ID da subscrição Azure da subscrição que contém o Cofre chave |


## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo de análise](log-analytics-log-searches.md) para ver dados detalhados do Azure chave cofre.

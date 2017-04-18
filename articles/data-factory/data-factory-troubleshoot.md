<properties 
    pageTitle="Resolução de problemas de fábrica de dados do Azure" 
    description="Saiba como resolver problemas com a utilização de fábrica do Azure dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Resolução de problemas de fábrica de dados
Este artigo fornece sugestões de resolução de problemas para problemas quando utilizar o Azure fábrica de dados. Este artigo não lista possíveis problemas quando utilizar o serviço, mas cobrir alguns problemas e sugestões de resolução de problemas gerais.   

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Erro: A subscrição não estiver registada para utilizar o espaço de nomes 'Microsoft.DataFactory'
Se receber este erro, o fornecedor de recursos do Azure dados fábrica do mesmo não foi registado no seu computador. Faça o seguinte: 

1. Inicie o Azure PowerShell. 
2. Inicie sessão na sua conta Azure utilizando o seguinte comando.
        AzureRmAccount de início de sessão 
3. Execute o seguinte comando para registar o fornecedor de fábrica do Azure dados.
        Register-AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: O erro não autorizado quando a ser executado um cmdlet fábrica de dados
Provavelmente não está a utilizar a conta Azure para a direita ou subscrição com o PowerShell Azure. Utilize os seguintes cmdlets para selecionar a conta para a direita Azure e de subscrição para utilizar com o PowerShell Azure. 

1. Início de sessão-AzureRmAccount - utilizar o ID de utilizador à direita e palavra-passe
2. Get-AzureRmSubscription - ver todas as subscrições para a conta. 
3. Selecione AzureRmSubscription &lt;nome da subscrição&gt; -selecione a subscrição à direita. Utilize o mesmo que utilizar para criar uma fábrica de dados no portal do Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: A iniciação Gateway Express configuração do Data Management a partir do Azure portal falhar
A configuração da Express para o Data Management Gateway requer o Internet Explorer ou um Microsoft ClickOnce compatíveis browser. Se a configuração Express não inicia, efetue um dos seguintes procedimentos: 

- Utilize o Internet Explorer ou um Microsoft ClickOnce compatíveis browser.

    Se estiver a utilizar o Chrome, aceda à [loja na web Chrome](https://chrome.google.com/webstore/), procurar com palavra-chave de "ClickOnce", selecione uma das extensões de ClickOnce e instalá-lo. 
    
    Faça o mesmo para o Firefox (instalar suplemento). Clique em Abrir Menu na barra de ferramentas (três linhas horizontais no canto superior direito), clique em suplementos, procurar com palavra-chave de "ClickOnce", selecione uma das extensões de ClickOnce e instalá-lo. 

- Utilize a hiperligação de **Configuração Manual** apresentada na pá no portal do mesmo. Utilizar esta abordagem para transferir o ficheiro de instalação e executá-la manualmente. Após a instalação bem sucedida, verá a caixa de diálogo de configuração de Gateway da gestão de dados. Copiar a **chave** no ecrã de portal e utilizá-lo no Gestor de configuração para registar o gateway manualmente com o serviço.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: Falhar ligar ao servidor de SQL no local 
Inicie o **Gestor de configuração de Gateway de gestão de dados** na máquina de gateway e utilize o separador de **resolução de problemas** para testar a ligação para o SQL Server a partir de máquina de gateway. Consulte o artigo [resolver problemas de gateway problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para sugestões sobre resolução de problemas de ligação/gateway relacionados com problemas.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: Os setores do Input estão no aguardar nunca por Estado

Os setores dever-se no estado **que aguardam** devido a vários motivos. Uma das razões comuns é que a propriedade **externos** não está definida como **Verdadeiro**. Qualquer conjunto de dados é produzido fora do âmbito do Azure dados fábrica deve ser marcado com propriedade **externos** . Esta propriedade indica que os dados são externos e não com cópia de segurança por qualquer tubagens dentro da fábrica de dados. Os setores de dados são marcados como **pronto** quando os dados estão disponíveis na loja respetivas. 

Consulte o artigo o exemplo seguinte para a utilização da propriedade **externos** . Opcionalmente, pode especificar **externalData*** quando definir externos como verdadeiro.

Consulte o artigo [conjuntos de dados](data-factory-create-datasets.md) artigo para obter mais detalhes sobre esta propriedade.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Para resolver o erro, adicione a propriedade **externos** e a secção opcional **externalData** à definição JSON da tabela de entrada e recrie a tabela. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: Operação de cópia de híbrido falhar
Consulte o artigo [resolução de problemas de gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter os passos para resolver problemas com a copiar para a um arquivo de dados no local, utilizando o Data Management Gateway. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: A pedido HDInsight falha de aprovisionamento
Quando utilizar um serviço do tipo HDInsightOnDemand ligado, é necessário especificar uma linkedServiceName que aponta para um armazenamento de Blobs do Azure. Serviço de dados de fábrica do mesmo utiliza esta armazenamento para armazenar registos e ficheiros de suporte para o seu cluster de HDInsight a pedido.  Por vezes o aprovisionamento de um cluster de HDInsight a pedido falha com o seguinte erro:

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Normalmente, este erro indica que a localização da conta de armazenamento especificada na linkedServiceName não está na mesma localização de centro de dados onde o aprovisionamento de HDInsight está a acontecer. Exemplo: se a fábrica de dados está nos EUA oeste e o Azure armazenamento está na Leste dos EUA, o aprovisionamento da pedido na falha EUA Ocidental.

Para além disso, existe uma segunda additionalLinkedServiceNames de propriedade JSON onde as contas de armazenamento adicional podem ser especificadas na HDInsight a pedido. As contas ligadas de armazenamento adicional devem ser na mesma localização como HDInsight cluster ou ocorre uma falha com o mesmo erro.

### <a name="problem-custom-net-activity-fails"></a>Problema: Personalizada falha de atividade de .NET
Consulte o artigo [Depurar uma tubagem com atividade personalizada](data-factory-use-custom-activities.md#debug-the-pipeline) para obter passos detalhados. 

## <a name="use-azure-portal-to-troubleshoot"></a>Utilizar o Azure portal para resolver 

### <a name="using-portal-blades"></a>Utilizar o portais pás
Consulte o artigo [Monitor pipeline](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) para obter os passos. 

### <a name="using-monitor-and-manage-app"></a>Utilizar o Monitor e gerir a aplicação
Consulte o artigo [Monitor e gerir dados fábrica tubagens utilizando o monitor do computador e gerir App](data-factory-monitor-manage-app.md) para obter detalhes. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Utilizar o PowerShell do Azure para resolução de problemas

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Utilizar o Azure PowerShell para resolver um erro  
Consulte o artigo [tubagens Monitor fábrica de dados através do Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) para obter detalhes. 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 
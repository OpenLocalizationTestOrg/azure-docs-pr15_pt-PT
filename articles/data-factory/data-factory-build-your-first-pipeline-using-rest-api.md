<properties
    pageTitle="Construir a sua primeira fábrica de dados (REST) | Microsoft Azure"
    description="Neste tutorial, crie um pipeline de Azure fábrica de dados de exemplo com dados fábrica REST API."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Tutorial: Criar a primeira fábrica de dados Azure utilizando dados fábrica REST API
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo de Gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste artigo, utilize dados fábrica REST API para criar a sua primeira fábrica de dados Azure.

## <a name="prerequisites"></a>Pré-requisitos
- Leia o artigo [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md) e concluir os passos de **pré-requisito** .
- Instale o [Curl](https://curl.haxx.se/dlwiz/) no seu computador. Utilizar a ferramenta de LAÇO com comandos resto para criar uma fábrica de dados. 
- Siga as instruções do deste [artigo](../resource-group-create-service-principal-portal.md) para: 
    1. Crie uma aplicação Web com o nome **ADFGetStartedApp** no Azure Active Directory.
    2. Obter o **ID de cliente** e **chave secreta**. 
    3. Obter o **ID do inquilino**. 
    4. Atribua a aplicação de **ADFGetStartedApp** para a função **Contribuinte fábrica de dados** .  
- Instale o [Azure PowerShell](../powershell-install-configure.md).  
- Inicie o **PowerShell** e execute o seguinte comando. Mantenha Azure PowerShell aberta até ao fim do tutorial. Se fechar e reabrir, que precisa para executar os comandos novamente.
    1. Executar **AzureRmAccount de início de sessão** e introduza o nome de utilizador e palavra-passe que utiliza para iniciar sessão portal do Azure.  
    2. Execute **Get-AzureRmSubscription** para ver todas as subscrições para esta conta.
    3. Executar **Get AzureRmSubscription - SubscriptionName NameOfAzureSubscription | Conjunto AzureRmContext** para selecionar a subscrição à qual pretende trabalhar com. Substitua o nome da sua subscrição do Azure **NameOfAzureSubscription** . 
3. Crie um grupo de recursos Azure denominado **ADFTutorialResourceGroup** executando o seguinte comando no PowerShell:  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Alguns dos passos neste tutorial partem do princípio de que utiliza o grupo de recursos com o nome ADFTutorialResourceGroup. Se utilizar um grupo de recursos diferentes, terá de utilizar o nome do seu grupo de recursos em vez de ADFTutorialResourceGroup neste tutorial.

## <a name="create-json-definitions"></a>Criar JSON definições
Crie sequência JSON ficheiros na pasta onde se encontra curl.exe. 

### <a name="datafactoryjson"></a>DataFactory.JSON 
> [AZURE.IMPORTANT] Nome tem de ser exclusivo global, pelo que poderá pretende prefixo/sufixo ADFCopyTutorialDF para torná-lo um nome exclusivo. 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.JSON
> [AZURE.IMPORTANT] Substitua **accountname** e **accountkey** nome e a chave da sua conta de armazenamento Azure. Para aprender a obter a sua chave de acesso de armazenamento, consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

A tabela seguinte fornece descrições para as propriedades JSON utilizadas no fragmento de:

| Propriedade | Descrição |
| :------- | :---------- |
| Versão | Especifica que a versão do HDInsight criado para ser 3,2. | 
| ClusterSize | Tamanho do HDInsight cluster. | 
| TimeToLive | Especifica que o tempo de espera para HDInsight cluster, antes de ser eliminada. |
| linkedServiceName | Especifica a conta de armazenamento é utilizada para armazenar os registos que são gerados por HDInsight |

Tenha em atenção os seguintes pontos: 

- A fábrica de dados cria um cluster de HDInsight **baseados no Windows** por si com o JSON acima. Pode também ter-criar um cluster de HDInsight **baseado em Linux** . Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
- Pode utilizar o **seu próprio cluster HDInsight** em vez de utilizar um cluster de HDInsight a pedido. Consulte o artigo [Serviço ligadas HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
- HDInsight cluster cria um **contentor predefinido** no armazenamento de Blobs do que especificou na JSON (**linkedServiceName**). HDInsight não elimina neste contentor quando o cluster é eliminado. Este comportamento é. Com o serviço de HDInsight ligada a pedido, um HDInsight cluster é criado sempre que um setor é processada, a menos que não existe uma existente live cluster (**timeToLive**) e é eliminado quando concluir o processamento.

    Como são processados os setores do gráfico mais, consulte contentores muitos no seu armazenamento de Blobs do Azure. Se não precisá-los para resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes nestes contentores siga um padrão de: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Utilize ferramentas, como o [Explorador de armazenamento da Microsoft](http://storageexplorer.com/) para eliminar contentores no seu armazenamento de Blobs do Azure.

Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 

### <a name="inputdatasetjson"></a>inputdataset.JSON

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


O JSON define um conjunto de dados denominado **AzureBlobInput**, que representa a dados de entrada para uma actividade no pipeline de. Além disso, especifica que os dados de entrada estão localizados no contentor blob denominado **adfgetstarted** e na pasta chamada **inputdata**.

A tabela seguinte fornece descrições para as propriedades JSON utilizadas no fragmento de:

| Propriedade | Descrição |
| :------- | :---------- |
| tipo | A propriedade tipo está definida para AzureBlob porque se encontram no armazenamento de Blobs do Azure dados. |  
| linkedServiceName | refere-se para StorageLinkedService que criou anteriormente. |
| nome de ficheiro | Esta propriedade é opcional. Se omitir esta propriedade, todos os ficheiros a partir do caminhopasta são recolhidos. Neste caso, apenas o input.log é processada. |
| tipo | Os ficheiros de registo estão no formato de texto, por isso utilizamos TextFormat. | 
| columnDelimiter | colunas nos ficheiros de registo são delimitadas por um caráter de ponto e vírgula () |
| frequência/intervalo | frequência definida para o mês e o intervalo for 1, o que significa que os setores entrados estão disponíveis mensalmente. | 
| externos | Esta propriedade estiver definida como VERDADEIRO, se os dados de entrada não são gerados pelo serviço fábrica de dados. | 

### <a name="outputdatasetjson"></a>outputdataset.JSON

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

O JSON define um conjunto de dados denominado **AzureBlobOutput**, que representa os dados de saída para uma actividade no pipeline de. Além disso, especifica que os resultados são armazenados no contentor blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. Secção **disponibilidade** da Especifica que o conjunto de dados de saída é produzido mensais.

### <a name="pipelinejson"></a>pipeline.JSON
> [AZURE.IMPORTANT] Substitua **storageaccountname** nome da sua conta de armazenamento Azure. 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

No fragmento JSON, está a criar uma tubagem que consiste numa única atividade que utiliza Hive para processar dados num HDInsight cluster.

O ficheiro de script do ramo, **partitionweblogs.hql**, é armazenado na conta de armazenamento Azure (especificado por scriptLinkedService, denominado **StorageLinkedService**) e na pasta de **script** no contentor **adfgetstarted**.

A secção **define** especifica definições de runtime que são transmitidas para o script ramo como valores de configuração do ramo (por exemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

As propriedades de **início** e de **fim** do pipeline de Especifica o período ativo do pipeline de.

Na atividade JSON, pode especificar que o script ramo é executada no cluster especificado pela **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Consulte o artigo [anatomia de uma tubagem](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre as propriedades JSON utilizadas no exemplo anterior. 

## <a name="set-global-variables"></a>Conjunto de variáveis globais

No Azure PowerShell, execute os seguintes comandos depois de substituir os valores com o seu próprio:

> [AZURE.IMPORTANT] Consulte a secção de [Pré-requisitos](#prerequisites) para obter instruções sobre como obter o ID de cliente, segredo de cliente, de inquilinos ID e o ID da subscrição.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>Autenticar com AAD

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>Criar a fábrica de dados

Neste passo, crie uma fábrica de dados do Azure denominada **FirstDataFactoryREST**. Uma fábrica de dados pode ter um ou mais tubagens. Uma tubagem pode ter um ou mais atividades. Por exemplo, uma atividade de copiar para copiar dados de uma origem de um arquivo de dados de destino e uma atividade de HDInsight Hive para executar o script de Hive para transformar dados. Execute os seguintes comandos para criar a fábrica de dados: 

1. O comando atribua a variável denominada **cmd**. 

    Confirme que o nome da fábrica dados especificar aqui (ADFCopyTutorialDF) corresponde ao nome especificado na **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Execute o comando utilizando **Comando invocar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Ver os resultados. Se a fábrica de dados foi criada com êxito, verá JSON para a fábrica de dados nos **resultados**; caso contrário, verá uma mensagem de erro.  

        Write-Host $results

Tenha em atenção os seguintes pontos:
 
- O nome da fábrica dados Azure tem de ser exclusivo global. Se vir o erro nos resultados de: **nome da fábrica de dados "FirstDataFactoryREST" não está disponível**, execute os seguintes passos:  
    1. Altere o nome (por exemplo, yournameFirstDataFactoryREST) no ficheiro **datafactory.json** . Consulte o tópico de [Dados Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefactos fábrica de dados.
    2. No primeiro comando onde a variável de **$cmd** é atribuída um valor, substitua FirstDataFactoryREST com o novo nome e executar o comando. 
    3. Execute os comandos para invocar API REST para criar a fábrica de dados e imprimir os resultados da operação de dois. 
- Para criar instâncias de fábrica de dados, tem de ser um contribuinte administrador da subscrição do Azure
- O nome da fábrica dados poderá estar registado como um nome DNS no futuro e, consequentemente, ficam publicamente visível.
- Se receber o erro: "**Esta subscrição não estiver registada para utilizar o espaço de nomes Microsoft.DataFactory**", efetue um dos seguintes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor de dados fábrica: 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Pode executar o seguinte comando para confirmar que a fábrica de dados está registado o fornecedor de: 
    
            Get-AzureRmResourceProvider
    - Inicie sessão utilizando a subscrição Azure para o [portal do Azure](https://portal.azure.com) e navegue para uma pá fábrica de dados (ou) criar uma fábrica de dados no portal do Azure. Esta ação regista automaticamente o fornecedor para si.

Antes de criar uma tubagem, tem de criar alguns dados fábrica entidades pela primeira vez. Tem de criar primeiro ligadas services para ligar dados armazena/fórmula calcula o arquivo de dados, definir entrada e saída conjuntos de dados para representar dados armazena dados ligados. 

## <a name="create-linked-services"></a>Criar serviços ligados 
Neste passo, aos quais ligação sua conta de armazenamento do Windows Azure e um cluster de Azure HDInsight a pedido a fábrica de dados. A conta de armazenamento do Windows Azure contém os dados de entrada e saídos de tubagem neste exemplo. O serviço de HDInsight ligada é utilizado para executar o script de ramo especificado na atividade de tubagem neste exemplo. 

### <a name="create-azure-storage-linked-service"></a>Criar o serviço de armazenamento do Windows Azure ligadas
Neste passo, aos quais ligação sua conta de armazenamento do Windows Azure a fábrica de dados. Com este tutorial, utiliza a mesma conta de armazenamento do Windows Azure para armazenar dados de entrada/saída e o ficheiro de script HQL.

1. O comando atribua a variável denominada **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Execute o comando utilizando **Comando invocar**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Ver os resultados. Se o serviço ligado tiver sido criado com êxito, consulte JSON para o serviço ligado nos **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço do Azure HDInsight ligadas
Neste passo, pode ligar um cluster de HDInsight a pedido a sua fábrica de dados. HDInsight cluster é automaticamente criado o tempo de execução e eliminado depois de o fazer transformação e inactivo durante o período de tempo especificado. Pode utilizar o seu próprio cluster HDInsight em vez de utilizar um cluster de HDInsight a pedido. Consulte o artigo [Calcular serviços ligados](data-factory-compute-linked-services.md) para obter detalhes.  

1. O comando atribua a variável denominada **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Execute o comando utilizando **Comando invocar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Ver os resultados. Se o serviço ligado tiver sido criado com êxito, consulte JSON para o serviço ligado nos **resultados**; caso contrário, verá uma mensagem de erro.  

        Write-Host $results

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, criar conjuntos de dados para representar a entrada e saída de dados para processamento de ramo. Estes conjuntos de dados referenciem **StorageLinkedService** que criou anteriormente no tutorial. Especificar os pontos de serviço ligadas para uma conta de armazenamento do Windows Azure e conjuntos de dados contentor, pasta, nome de ficheiro no armazenamento que detém entrada e saída de dados.   

### <a name="create-input-dataset"></a>Criar o conjunto de dados de entrada
Neste passo, crie o conjunto de dados de entrada para representar os dados armazenados no armazenamento de Blobs do Azure de entrada.

1. O comando atribua a variável denominada **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Execute o comando utilizando **Comando invocar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Ver os resultados. Se o conjunto de dados foi criado com êxito, verá JSON para o conjunto de dados nos **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results
### <a name="create-output-dataset"></a>Criar o conjunto de dados de saída
Neste passo, crie o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. O comando atribua a variável denominada **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Execute o comando utilizando **Comando invocar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Ver os resultados. Se o conjunto de dados foi criado com êxito, verá JSON para o conjunto de dados nos **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Criar em curso
Neste passo, criar a primeira pipeline com uma atividade de **HDInsightHive** . Setor entrada está disponível mensalmente (frequência: mês, intervalo: 1), setor de saída é produzido mensal e a propriedade do programador para a atividade também é definida como mensal. As definições para o conjunto de dados de saída e o Programador de atividade tem de corresponder. Atualmente, o conjunto de dados de saída é o que unidades agenda, para que o mesmo se a atividade não produz qualquer saída, tem de criar um conjunto de dados de saída. Se a atividade não tomar qualquer entrada, também pode ignorar a criar o conjunto de dados de entrada.  

Confirme que consulte o ficheiro **input.log** na pasta **adfgetstarted/inputdata** no armazenamento de Blobs do Azure e execute o seguinte comando para implementar o pipeline. Uma vez que as horas de **início** e de **fim** são definidas no passado e **isPaused** estiver definido como FALSO, é executada a tubagem (actividade no pipeline de) imediatamente após a implementar. 

1. O comando atribua a variável denominada **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Execute o comando utilizando **Comando invocar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Ver os resultados. Se o conjunto de dados foi criado com êxito, verá JSON para o conjunto de dados nos **resultados**; caso contrário, verá uma mensagem de erro.  

        Write-Host $results
5. Parabéns, foi criado com êxito o primeiro pipeline através do Azure PowerShell!

## <a name="monitor-pipeline"></a>Em curso do monitor
Neste passo, utilize dados fábrica REST API para monitorizar os setores do gráfico endereçados produzidos pelas em curso.

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> Criação de um cluster de HDInsight a pedido normalmente leva-o até algures (cerca de 20 minutos). Espera por conseguinte, a tubagem para demorar **cerca de 30 minutos** para processar no setor.  

Execute o comando invocar e o próximo até ver no setor no estado **pronto** ou **falhou** . Quando está no setor no estado pronta, selecione a pasta **partitioneddata** no contentor **adfgetstarted** no seu armazenamento blob para os dados de saída.  A criação de um cluster de HDInsight a pedido normalmente leva algum tempo.

![dados de saída](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] O ficheiro de entrada é eliminado quando no setor é processado com êxito. Por isso, se pretender executar novamente no setor ou não faça novamente o tutorial, carregue o ficheiro de entrada (input.log) para a pasta de inputdata do contentor adfgetstarted.

Também pode utilizar o Azure portal para monitorizar os setores do gráfico e resolver quaisquer problemas com. Ver detalhes de [Monitorizar tubagens através do portal Azure](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) .  

## <a name="summary"></a>Resumo 
Neste tutorial, que criou uma fábrica dados Azure aos dados do processo ao executar o script ramo num cluster de hadoop HDInsight. O Editor de fábrica do mesmo de dados que utilizou no portal do Azure para execute os seguintes passos:  

1.  Criado um Azure **fábrica de dados**.
2.  Criadas dois **ligados serviços**:
    1.  Serviço de **Armazenamento do Windows azure** ligada para ligar o armazenamento de Blobs do Azure que detém entrada/saída ficheiros para a fábrica de dados.
    2.  **Azure HDInsight** a pedido ligado o serviço para ligar um cluster de HDInsight Hadoop a pedido a fábrica de dados. Azure dados fábrica cria uma HDInsight Hadoop cluster just-in-time para processar dados de entrada e produzir dados de saída. 
3.  Criado dois **conjuntos de dados**, o que descrevem os dados de entrada e saídos de atividade de HDInsight ramo no pipeline de. 
4.  Criado uma **pipeline de** com uma atividade de **Ramo HDInsight** . 

## <a name="next-steps"></a>Próximos passos
Neste artigo, que criou uma tubagem com uma atividade de transformação (HDInsight actividade) que é executada um script ramo num cluster Azure HDInsight a pedido. Para ver como utilizar uma atividade de copiar para copiar dados de uma BLOBs do Azure para Azure SQL, consulte [Tutorial: copiar dados de uma BLOBs do Azure para Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Referência da linguagem fábrica REST API](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  Consulte documentação abrangente sobre cmdlets fábrica de dados |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como HDInsight ramo transformação utilizados neste tutorial) suportados pelo Azure fábrica de dados. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos agendamento e execução de modelo de aplicação do Azure fábrica de dados. |
| [Tubagens](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender tubagens e atividades no Azure fábrica de dados e como utilizá-las para construir ponto a ponto condicionados por dados fluxos de trabalho para o seu cenário ou empresas. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender conjuntos de dados no Azure fábrica de dados.
| [Monitor do computador e tubagens gerir utilizando Azure pás portais](data-factory-monitor-manage-pipelines.md) | Este artigo descreve como monitorizar, gerir e depurar seu tubagens utilizando Azure pás portais. |
| [Monitorizar e gerir tubagens utilizando a aplicação de monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar tubagens utilizando a monitorização e gestão de aplicação. 


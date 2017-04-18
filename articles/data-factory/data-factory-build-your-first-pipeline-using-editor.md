<properties
    pageTitle="Construir a sua primeira fábrica de dados (Azure portal) | Microsoft Azure"
    description="Neste tutorial, crie um pipeline de fábrica do Azure dados de exemplo utilizando o Editor de fábrica do mesmo de dados no portal do Azure."
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
    ms.topic="hero-article" 
    ms.date="09/14/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-portal"></a>Tutorial: Criar o seu primeira fábrica de dados Azure através do portal Azure
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo de Gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste artigo, saiba como utilizar o [Azure portal](https://portal.azure.com/) para criar a sua primeira fábrica de dados Azure. 

## <a name="prerequisites"></a>Pré-requisitos        
1. Leia o artigo [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md) e concluir os passos de **pré-requisito** .
2. Este artigo não fornece uma descrição geral conceptual do serviço do Azure fábrica de dados. Recomendamos que, acede através do artigo [Introdução ao Azure dados fábrica](data-factory-introduction.md) para obter uma descrição detalhada do serviço.  

## <a name="create-data-factory"></a>Criar a fábrica de dados
Uma fábrica de dados pode ter um ou mais tubagens. Uma tubagem pode ter um ou mais atividades. Por exemplo, uma atividade de copiar para copiar dados de uma origem de um arquivo de dados de destino e uma atividade de HDInsight Hive para executar o script Hive para transformar dados de entrada para o produto de saída de dados. Vamos começar por criar a fábrica de dados neste passo. 

1.  Inicie a sessão [portal do Azure](https://portal.azure.com/).
2.  Clique em **Novo** no menu à esquerda, clique em **dados + Analytics**e clique em **Dados fábrica do mesmo**.
        
    ![Criar pá](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.  Na pá **Novo fábrica de dados** , introduza **GetStartedDF** para o nome.

    ![Nova Pá de fábrica do mesmo de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

    > [AZURE.IMPORTANT] 
    > O nome da fábrica dados Azure tem de ser **exclusivo global**. Se receber o erro: **o nome de fábrica de dados "GetStartedDF" não está disponível**. Alterar o nome da fábrica dados (por exemplo, yournameGetStartedDF) e tente criar novamente. Consulte o tópico de [Dados Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefactos fábrica de dados.
    > 
    > O nome da fábrica dados poderá estar registado como um nome **DNS** no futuro e, consequentemente, ficam publicamente visível.

3.  Selecione a **subscrição do Azure** onde pretende que a fábrica de dados seja criada. 
4.  Selecione o **grupo de recursos** de existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos com o nome: **ADFGetStartedRG**. 
5.  Clique em **Criar** no pá a **fábrica de dados nova** .

    > [AZURE.IMPORTANT] Para criar instâncias de fábrica de dados, tem de ser um membro da função [Contribuinte fábrica de dados](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) ao nível do grupo de subscrição/recurso. 
6.  Consulte a fábrica de dados a ser criada na **Startboard** do Azure portal da seguinte forma:   

    ![Criar o estado da fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Parabéns! Criou a primeira fábrica de dados com êxito. Depois da fábrica de dados foi criada com êxito, verá a página de fábrica do mesmo dados, que mostra-lhe os conteúdos da fábrica de dados.   

    ![Pá fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Antes de criar uma tubagem na fábrica dados, tem de criar alguns dados fábrica entidades pela primeira vez. Tem de criar primeiro ligadas services para ligar dados armazena/fórmula calcula o arquivo de dados, definir entrada e saída conjuntos de dados para representar a dados de entrada/saída no armazena dados ligados e, em seguida, crie pipeline de com uma atividade que utiliza estes conjuntos de dados. 

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, aos quais ligação sua conta de armazenamento do Windows Azure e um cluster de Azure HDInsight a pedido a fábrica de dados. A conta de armazenamento do Windows Azure contém os dados de entrada e saídos de tubagem neste exemplo. O serviço de HDInsight ligada é utilizado para executar o script de ramo especificado na atividade de tubagem neste exemplo. Identificar que [armazenam dados](data-factory-data-movement-activities.md)/[Calcular services](data-factory-compute-linked-services.md) são utilizadas no seu cenário e ligação desses serviços para a fábrica de dados através da criação de serviços ligados.  

### <a name="create-azure-storage-linked-service"></a>Criar o serviço de armazenamento do Windows Azure ligadas
Neste passo, aos quais ligação sua conta de armazenamento do Windows Azure a fábrica de dados. Neste tutorial, utiliza a mesma conta de armazenamento do Windows Azure para armazenar dados de entrada/saída e o ficheiro de script HQL. 

1.  Clique em **autor e implementar** no pá a **Fábrica de dados** para **GetStartedDF**. Deverá ver o Editor de fábrica do mesmo de dados. 
     
    ![Criar e implementar em mosaico](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.  Clique em **arquivo de dados novos** e selecione **armazenamento Azure**.

    ![Arquivo de dados novos menu - armazenamento do Windows Azure-](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

3.  Deverá visualizar o script JSON para criar um serviço de armazenamento do Windows Azure ligadas no editor. 
    
    ![Serviço de armazenamento ligado do Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
     
4. Substitua o **nome da conta** com o nome da sua conta de armazenamento Azure e **chave de conta** com a tecla de acesso da conta de armazenamento Azure. Para aprender a obter a sua chave de acesso de armazenamento, consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
5. Na barra de comando para implementar o serviço ligado, clique em **Implementar** .

    ![Implementar o botão](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Depois do serviço ligado é implementado com êxito, a janela de **rascunho-1** deverá desaparecer e ver **AzureStorageLinkedService** na vista de árvore à esquerda. 
    ![Serviço ligada de armazenamento no menu de lista](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)   

 
### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço do Azure HDInsight ligadas
Neste passo, pode ligar um cluster de HDInsight a pedido a sua fábrica de dados. HDInsight cluster é automaticamente criado o tempo de execução e eliminado depois de o fazer transformação e inactivo durante o período de tempo especificado. 

1. No **Editor de fábrica do mesmo de dados**, clique em **... Mais**, clique em **Novo calcular**e selecione **cluster HDInsight a pedido**.

    ![Cluster novo](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copie e cole o seguinte fragmento de para a janela de **rascunho-1** . O fragmento de JSON descreve as propriedades que são utilizadas para criar a HDInsight cluster a pedido. 

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
  	| ClusterSize | Especifica o tamanho do HDInsight cluster. | 
  	| TimeToLive | Especifica que o tempo de espera para HDInsight cluster, antes de ser eliminada. |
  	| linkedServiceName | Especifica a conta de armazenamento é utilizada para armazenar os registos que são gerados por HDInsight. |

    Tenha em atenção os seguintes pontos: 
    
    - A fábrica de dados cria um cluster de HDInsight **baseados no Windows** por si com o JSON. Pode também ter-criar um cluster de HDInsight **baseado em Linux** . Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
    - Pode utilizar o **seu próprio cluster HDInsight** em vez de utilizar um cluster de HDInsight a pedido. Consulte o artigo [Serviço ligadas HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
    - HDInsight cluster cria um **contentor predefinido** no armazenamento de Blobs do que especificou na JSON (**linkedServiceName**). HDInsight não elimina neste contentor quando o cluster é eliminado. Este comportamento é. Com o serviço de HDInsight ligada a pedido, um cluster de HDInsight é criado sempre que um setor é processada, a menos que existe um cluster direto existente (**timeToLive**). Cluster é eliminado automaticamente quando concluir o processamento.
    
        Como são processados os setores do gráfico mais, consulte contentores muitos no seu armazenamento de Blobs do Azure. Se não precisá-los para resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes nestes contentores siga um padrão de: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Utilize ferramentas, como o [Explorador de armazenamento da Microsoft](http://storageexplorer.com/) para eliminar contentores no seu armazenamento de Blobs do Azure.

    Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
3. Na barra de comando para implementar o serviço ligado, clique em **Implementar** . 

    ![Implementar o serviço de HDInsight ligada a pedido](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

4. Confirme que está a ver **AzureStorageLinkedService** e **HDInsightOnDemandLinkedService** na árvore do ver à esquerda.

    ![Vista de árvore com os serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, criar conjuntos de dados para representar a entrada e saída de dados para processamento de ramo. Estes conjuntos de dados referenciem **AzureStorageLinkedService** que criou anteriormente no tutorial. Especificar os pontos de serviço ligadas para uma conta de armazenamento do Windows Azure e conjuntos de dados contentor, pasta, nome de ficheiro no armazenamento que detém entrada e saída de dados.   

### <a name="create-input-dataset"></a>Criar o conjunto de dados de entrada

1. No **Editor de fábrica do mesmo de dados**, clique em **... Mais** na barra de comandos, clique em **novo conjunto de dados**e, selecione o **armazenamento de Blobs do Azure**.

    ![Novo conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie e cole o seguinte fragmento de para a janela de rascunho-1. No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobInput** que representa a dados de entrada para uma actividade no pipeline de. Além disso, pode especificar que os dados de entrada estão localizados no contentor blob denominado **adfgetstarted** e na pasta chamada **inputdata**.
        
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

    A tabela seguinte fornece descrições para as propriedades JSON utilizadas no fragmento de:

  	| Propriedade | Descrição |
  	| :------- | :---------- |
  	| tipo | A propriedade tipo está definida para AzureBlob porque se encontram no armazenamento de Blobs do Azure dados. |  
  	| linkedServiceName | refere-se para AzureStorageLinkedService que criou anteriormente. |
  	| nome de ficheiro | Esta propriedade é opcional. Se omitir esta propriedade, todos os ficheiros a partir do caminhopasta são recolhidos. Neste caso, apenas o input.log é processada. |
  	| tipo | Os ficheiros de registo estão no formato de texto, por isso utilizamos TextFormat. | 
  	| columnDelimiter | colunas nos ficheiros de registo são delimitadas por caráter de vírgula () |
  	| frequência/intervalo | frequência definida para o mês e o intervalo for 1, o que significa que os setores entrados estão disponíveis mensalmente. | 
  	| externos | Esta propriedade estiver definida como VERDADEIRO, se os dados de entrada não são gerados pelo serviço fábrica de dados. | 
        
3. Na barra de comando para implementar o conjunto de dados recentemente criado, clique em **Implementar** . Deverá ver o conjunto de dados na vista de árvore à esquerda. 


### <a name="create-output-dataset"></a>Criar o conjunto de dados de saída
Agora, pode criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure. 

1. No **Editor de fábrica do mesmo de dados**, clique em **... Mais** na barra de comandos, clique em **novo conjunto de dados**e, selecione o **armazenamento de Blobs do Azure**.  
2. Copie e cole o seguinte fragmento de para a janela de rascunho-1. No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobOutput**e especificando a estrutura dos dados que são produzidas pelo script ramo. Além disso, pode Especifica que os resultados são armazenados no contentor blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. Secção **disponibilidade** da Especifica que o conjunto de dados de saída é produzido mensais.
    
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

    Consulte a secção **criar o conjunto de dados de entrada** para as descrições estas propriedades. Não defina a propriedade externa num conjunto de dados de saída, como o conjunto de dados é produzido pelo serviço fábrica de dados.
3. Na barra de comando para implementar o conjunto de dados recentemente criado, clique em **Implementar** .
4. Certifique-se de que o conjunto de dados é criado com êxito.

    ![Vista de árvore com os serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-pipeline"></a>Criar em curso
Neste passo, criar a primeira pipeline com uma atividade de **HDInsightHive** . Setor entrada está disponível mensalmente (frequência: mês, intervalo: 1), setor de saída é produzido mensal e a propriedade do programador para a atividade também é definida como mensal. As definições para o conjunto de dados de saída e o Programador de atividade tem de corresponder. Atualmente, o conjunto de dados de saída é o que unidades agenda, para que o mesmo se a atividade não produz qualquer saída, tem de criar um conjunto de dados de saída. Se a atividade não tomar qualquer entrada, também pode ignorar a criar o conjunto de dados de entrada. As propriedades de utilizadas em JSON a seguinte são explicadas no final desta secção. 

1. No **Editor de fábrica do mesmo de dados**, clique em **nas reticências (…) Mais comandos** e, em seguida, clique em **novo em curso**.
    
    ![novo botão em curso](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie e cole o seguinte fragmento de para a janela de rascunho-1.

    > [AZURE.IMPORTANT] Substitua o nome da sua conta de armazenamento na JSON **storageaccountname** .
        
        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService",
                            "defines": {
                                "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                                "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                            }
                        },
                        "inputs": [
                            {
                                "name": "AzureBlobInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "AzureBlobOutput"
                            }
                        ],
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
                    }
                ],
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }
 
    No fragmento JSON, está a criar uma tubagem que consiste numa única atividade que utiliza Hive para o processo de dados num HDInsight cluster.
    
    O ficheiro de script do ramo, **partitionweblogs.hql**, é armazenado na conta de armazenamento Azure (especificado por scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta de **script** no contentor **adfgetstarted**.

    A secção **define** é utilizada para especificar as definições de runtime são transmitidas para o script ramo como valores de configuração do ramo (por exemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

    As propriedades de **início** e de **fim** do pipeline de Especifica o período ativo do pipeline de.

    Na atividade JSON, pode especificar que o script ramo é executada no cluster especificado pela **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Consulte o artigo [anatomia de uma tubagem](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter mais detalhes acerca das propriedades JSON utilizadas no exemplo. 

3. Confirme o seguinte: 
    1. ficheiro de **Input.log** existe na pasta **inputdata** do contentor **adfgetstarted** no armazenamento de Blobs do Azure
    2. ficheiro de **partitionweblogs.hql** existe na pasta **script** do contentor **adfgetstarted** no armazenamento de Blobs do Azure. Conclua o pré-requisito passos na [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md) se não vir a estes ficheiros. 
    3. Confirme que substituídos **storageaccountname** com o nome da sua conta de armazenamento no pipeline de JSON. 
2. Na barra de comando para implementar a tubagem, clique em **Implementar** . Uma vez que as horas de **início** e de **fim** são definidas no passado e **isPaused** estiver definido como FALSO, é executada a tubagem (actividade no pipeline de) imediatamente após a implementar. 
4. Confirme que está a ver a tubagem na vista de árvore.

    ![Vista de árvore com em curso](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Parabéns, foi criado com êxito o primeiro pipeline!

## <a name="monitor-pipeline"></a>Em curso do monitor

### <a name="monitor-pipeline-using-diagram-view"></a>Em curso monitor utilizando a vista de diagrama

6. Clique em **X** para fechar o Editor de fábrica do mesmo dados Pás e navegar de volta para o pá fábrica de dados e clique em **diagrama**.
  
    ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. Na vista de diagrama, pode ver uma descrição geral da tubagens e conjuntos de dados utilizados neste tutorial.
    
    ![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Para ver todas as atividades no pipeline de, com o botão direito em curso no diagrama e clique em abrir em curso. 

    ![Menu de tubagem aberta](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Confirme que está a ver a atividade de HDInsightHive no pipeline de. 
  
    ![Vista de tubagem aberta](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Para navegar de volta para a vista anterior, clique em **fábrica de dados** no menu de trilho na parte superior. 
10. Na **Vista de diagrama**, faça duplo clique sobre o conjunto de dados **AzureBlobInput**. Confirme que o setor está num estado **pronto** . Poderá demorar alguns minutos para setor Mostrar estado preparado. Se não acontecer depois de espera para algures, consulte o artigo se tiver o ficheiro de entrada (input.log) colocado na pasta (inputdata) e contentor direita (adfgetstarted).

    ![Entrada setor no estado pronta](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Clique em **X** para fechar **AzureBlobInput** pá. 
12. Na **Vista de diagrama**, faça duplo clique sobre o conjunto de dados **AzureBlobOutput**. Verá que no setor que está atualmente a ser processado.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Quando concluir o processo processamento, verá setor no estado **pronta** .
    
>[AZURE.IMPORTANT] Criação de um cluster de HDInsight a pedido normalmente leva-o até algures (cerca de 20 minutos). Espera por conseguinte, a tubagem para demorar **cerca de 30 minutos** para processar no setor.    

    ![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png) 
    
10. Quando está no setor no estado **pronta** , selecione a pasta **partitioneddata** no contentor **adfgetstarted** no seu armazenamento blob para os dados de saída.  
 
    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)
11. Clique no setor para ver detalhes acerca do mesmo na pá **setor de dados** .

    ![Detalhes do setor de dados](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)  
12. Clique numa actividade executar na **lista de será executado de atividade** para ver detalhes sobre uma actividade executar (ramo actividade nosso cenário) numa janela de **atividade executar detalhes** .   

    ![Executar detalhes de atividade](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)  
    
    A partir de ficheiros de registo, pode ver as informações de estado e ramo de consulta que foi executada. Estes registos são úteis para resolver quaisquer problemas com o problema.
Consulte o artigo [Monitor e gerir tubagens utilizando Azure pás portais](data-factory-monitor-manage-pipelines.md) artigo para obter mais detalhes. 

> [AZURE.IMPORTANT] O ficheiro de entrada é eliminado quando no setor é processado com êxito. Por isso, se pretender executar novamente no setor ou não faça novamente o tutorial, carregue o ficheiro de entrada (input.log) para a pasta de inputdata do contentor adfgetstarted.

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorizar pipeline através do Monitor gerir App
Também pode utilizar o Monitor e gerir aplicações para monitorizar a sua tubagens. Para obter informações detalhadas sobre como utilizar esta aplicação, consulte o artigo [Monitor e gerir tubagens Azure fábrica de dados utilizando a monitorização e gestão de aplicação](data-factory-monitor-manage-app.md).

1. Clique em mosaico **Monitor e gerir** na home page do seu fábrica de dados.

    ![Monitorizar e gerir mosaico](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png) 
2. Deverá ver **Monitor e gerir aplicações**. Alterar a **hora de início** e **hora de fim** para corresponder ao iniciar (04-01-2016 12:00 AM) horas e de fim (04-02-2016 12:00 AM) do seu pipeline e clique em **Aplicar**.

    ![Monitorizar e gerir a aplicação](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png) 
3. Selecione uma janela de atividade na lista de **Atividade Windows** para ver os detalhes acerca do mesmo. 
    ![Detalhes da janela de atividade](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)


## <a name="summary"></a>Resumo 
Neste tutorial, que criou uma fábrica dados Azure aos dados do processo ao executar o script ramo num cluster de hadoop HDInsight. O Editor de fábrica do mesmo de dados que utilizou no portal do Azure para execute os seguintes passos:  

1.  Criado um Azure **fábrica de dados**.
2.  Criadas dois **ligados serviços**:
    1.  Serviço de **Armazenamento do Windows azure** ligada para ligar o armazenamento de Blobs do Azure que detém entrada/saída ficheiros para a fábrica de dados.
    2.  **Azure HDInsight** a pedido ligado o serviço para ligar um cluster de HDInsight Hadoop a pedido a fábrica de dados. Azure dados fábrica cria uma HDInsight Hadoop cluster just-in-time para processar dados de entrada e produzir dados de saída. 
3.  Criado dois **conjuntos de dados**, o que descrevem os dados de entrada e saídos de atividade de HDInsight ramo no pipeline de. 
4.  Criado uma **pipeline de** com uma atividade de **Ramo HDInsight** . 

## <a name="next-steps"></a>Próximos passos
Neste artigo, que criou uma tubagem com uma atividade de transformação (HDInsight actividade) que é executada um script ramo num cluster HDInsight a pedido. Para ver como utilizar uma atividade de copiar para copiar dados de uma BLOBs do Azure para Azure SQL, consulte [Tutorial: copiar dados a partir de um Azure blob para Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como HDInsight ramo transformação utilizados neste tutorial) suportados pelo Azure fábrica de dados. | 
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos agendamento e execução de modelo de aplicação do Azure fábrica de dados. |
| [Tubagens](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender tubagens e atividades no Azure fábrica de dados e como utilizá-las para construir ponto a ponto condicionados por dados fluxos de trabalho para o seu cenário ou empresas. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender conjuntos de dados no Azure fábrica de dados.
| [Monitorizar e gerir tubagens utilizando a aplicação de monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar tubagens utilizando a monitorização e gestão de aplicação. 

  


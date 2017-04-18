<properties
    pageTitle="Construir a sua primeira fábrica de dados (Visual Studio) | Microsoft Azure"
    description="Neste tutorial, crie uma tubagem Azure fábrica de dados de exemplo utilizando o Visual Studio."
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
    ms.date="10/17/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-azure-first-data-factory-using-microsoft-visual-studio"></a>Tutorial: Criar o primeiro Azure dados fábrica do mesmo utilizando o Microsoft Visual Studio
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo de Gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste artigo, utilize o Microsoft Visual Studio para criar a sua primeira fábrica de dados Azure.

## <a name="prerequisites"></a>Pré-requisitos
1. Leia o artigo [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md) e concluir os passos de **pré-requisito** .
2. Tem de ser um **administrador da subscrição do Azure** para poder publicar entidades fábrica de dados a partir do Visual Studio para Azure fábrica de dados.
3. Tem de ter o seguinte instalado no seu computador: 
    - Visual Studio 2013 ou o Visual Studio 2015
    - Transferir o Azure SDK para Visual Studio 2013 ou o Visual Studio 2015. Navegue até à [Página de transferência do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET** .
    - Transferir o plug-in de fábrica do Azure dados mais recente para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Também pode atualizar o plug-in procedendo do seguinte modo: no menu, clique em **Ferramentas** -> **atualizações e extensões** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas de fábrica do Microsoft Azure dados para o Visual Studio** -> **Atualizar**. 
 
Agora, vamos utilizar o Visual Studio para criar uma fábrica dados Azure. 


## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio 
1. Inicie o **Visual Studio 2013** ou **Visual Studio 2015**. Clique em **ficheiro**, aponte para **Novo**e clique em **projeto**. Deverá ver a caixa de diálogo **Novo projeto** .  
2. Na caixa de diálogo **Novo projeto** , selecione o modelo de **DataFactory** e clique em **Projeto de fábrica do mesmo de dados vazia**.   

    ![Caixa de diálogo novo projeto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Introduza um **nome** para o projeto, **localização**e um nome para a **solução**e clique em **OK**.

    ![Explorador de soluções](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Uma fábrica de dados pode ter um ou mais tubagens. Uma tubagem pode ter um ou mais atividades. Por exemplo, uma atividade de copiar para copiar dados de uma origem de um arquivo de dados de destino e uma atividade de HDInsight Hive para executar o script Hive para transformar dados de entrada. Consulte o artigo [armazena dados suportadas](data-factory-data-movement-activities.md##supported-data-stores-and-formats) para todas as origens e sumidouros suportados pela atividade de cópia. Consulte o artigo [Calcular ligados serviços](data-factory-compute-linked-services.md) para a lista de serviços de cluster suportados por dados fábrica do mesmo. 

Neste passo, aos quais ligação sua conta de armazenamento do Windows Azure e um cluster de Azure HDInsight a pedido a fábrica de dados. A conta de armazenamento do Windows Azure contém os dados de entrada e saídos de tubagem neste exemplo. O serviço de HDInsight ligada é utilizado para executar o script de ramo especificado na atividade de tubagem neste exemplo. Identificar os dados que loja/cluster services são utilizadas no seu cenário e ligação desses serviços para a fábrica de dados através da criação de serviços ligados.  

Especifique o nome e as definições para a fábrica de dados mais tarde quando publicar a sua solução fábrica de dados.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço de armazenamento do Windows Azure ligadas
Neste passo, aos quais ligação sua conta de armazenamento do Windows Azure a fábrica de dados. Para este tutorial, utiliza a mesma conta de armazenamento do Windows Azure para armazenar dados de entrada/saída e o ficheiro de script HQL. 

4. Botão direito do rato **Serviços ligados** no Explorador de solução, aponte para **Adicionar**e clique em **Novo Item**.      
5. Na caixa de diálogo **Adicionar Novo Item** , selecione o **Serviço de armazenamento do ligadas Azure** a partir da lista e clique em **Adicionar**. 
3. Substitua o nome da sua conta de armazenamento Azure e a respectiva chave **accountname** e **accountkey** . Para aprender a obter a sua chave de acesso de armazenamento, consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)

    ![Armazenamento Azure ligadas serviço](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Guarde o ficheiro **AzureStorageLinkedService1.json** .

#### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço do Azure HDInsight ligadas
Neste passo, pode ligar um cluster de HDInsight a pedido a sua fábrica de dados. HDInsight cluster é automaticamente criado o tempo de execução e eliminado depois de o fazer transformação e inactivo durante o período de tempo especificado. Pode utilizar o seu próprio cluster HDInsight em vez de utilizar um cluster de HDInsight a pedido. Consulte o artigo [Calcular serviços ligados](data-factory-compute-linked-services.md) para obter detalhes. 

1. No **Explorador de soluções**, com o botão direito **Serviços ligados**, aponte para **Adicionar**e clique em **Novo Item**.
2. Selecione **HDInsight pedido ligadas serviço**e clique em **Adicionar**. 
3. Substitua o **JSON** com o seguinte:

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService1"
            }
          }
        }
    
    A tabela seguinte fornece descrições para as propriedades JSON utilizadas no fragmento de:
    
    Propriedade | Descrição
    -------- | -----------
    Versão | Especifica que a versão do HDInsight criado para ser 3,2. 
    ClusterSize | Especifica o tamanho do HDInsight cluster. 
    TimeToLive | Especifica que o tempo de espera para HDInsight cluster, antes de ser eliminada.
    linkedServiceName | Especifica a conta de armazenamento é utilizada para armazenar os registos que são gerados por HDInsight

    Tenha em atenção o seguinte procedimento: 
    
    - A fábrica de dados cria um cluster de HDInsight **baseados no Windows** por si com o JSON anterior. Pode também ter-criar um cluster de HDInsight **baseado em Linux** . Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
    - Pode utilizar o **seu próprio cluster HDInsight** em vez de utilizar um cluster de HDInsight a pedido. Consulte o artigo [Serviço ligadas HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
    - HDInsight cluster cria um **contentor predefinido** no armazenamento de Blobs do que especificou na JSON (**linkedServiceName**). HDInsight não elimina neste contentor quando o cluster é eliminado. Este comportamento é. Com o serviço de HDInsight ligada a pedido, um cluster de HDInsight é criado sempre que um setor é processada, a menos que existe um cluster direto existente (**timeToLive**). Cluster é eliminado automaticamente quando concluir o processamento.
    
        Como são processados os setores do gráfico mais, consulte contentores muitos no seu armazenamento de Blobs do Azure. Se não precisá-los para resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes nestes contentores siga um padrão de: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Utilize ferramentas, como o [Explorador de armazenamento da Microsoft](http://storageexplorer.com/) para eliminar contentores no seu armazenamento de Blobs do Azure.

    Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
4. Guarde o ficheiro **HDInsightOnDemandLinkedService1.json** .

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, criar conjuntos de dados para representar a entrada e saída de dados para processamento de ramo. Estes conjuntos de dados referenciem **AzureStorageLinkedService1** que criou anteriormente no tutorial. Especificar os pontos de serviço ligadas para uma conta de armazenamento do Windows Azure e conjuntos de dados contentor, pasta, nome de ficheiro no armazenamento que detém entrada e saída de dados.   

#### <a name="create-input-dataset"></a>Criar o conjunto de dados de entrada

1. No **Explorador de soluções**, **tabelas**de contexto, aponte para **Adicionar**e clique em **Novo Item**. 
2. Selecione **BLOBs do Azure** a partir da lista, altere o nome do ficheiro para **InputDataSet.json**e clique em **Adicionar**.
3. Substitua o **JSON** no editor de com o seguinte: 

    No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobInput** que representa a dados de entrada para uma actividade no pipeline de. Além disso, especifique a que os dados de entrada estão localizados no contentor blob denominado **adfgetstarted** e na pasta chamada **inputdata**
        
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService1",
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
  	| linkedServiceName | refere-se para AzureStorageLinkedService1 que criou anteriormente. |
  	| nome de ficheiro | Esta propriedade é opcional. Se omitir esta propriedade, todos os ficheiros a partir do caminhopasta são recolhidos. Neste caso, apenas o input.log é processada. |
  	| tipo | Os ficheiros de registo estão no formato de texto, por isso utilizamos TextFormat. | 
  	| columnDelimiter | colunas nos ficheiros de registo são delimitadas pelo caráter de ponto e vírgula () |
  	| frequência/intervalo | frequência definida para o mês e o intervalo for 1, o que significa que os setores entrados estão disponíveis mensalmente. | 
  	| externos | Esta propriedade estiver definida como VERDADEIRO, se os dados de entrada não são gerados pelo serviço fábrica de dados. | 
      
    
3. Guarde o ficheiro **InputDataset.json** . 

 
#### <a name="create-output-dataset"></a>Criar o conjunto de dados de saída
Agora, pode criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure. 

1. No **Explorador de soluções**, **tabelas**de contexto, aponte para **Adicionar**e clique em **Novo Item**. 
2. Selecione **BLOBs do Azure** a partir da lista, altere o nome do ficheiro para **OutputDataset.json**e clique em **Adicionar**. 
3. Substitua o **JSON** no editor de com o seguinte: 

    No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobOutput**e especificando a estrutura dos dados que são produzidas pelo script ramo. Além disso, pode Especifica que os resultados são armazenados no contentor blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. Secção **disponibilidade** da Especifica que o conjunto de dados de saída é produzido mensais.
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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

4. Guarde o ficheiro **OutputDataset.json** .


### <a name="create-pipeline"></a>Criar em curso
Neste passo, criar a primeira pipeline com uma atividade de **HDInsightHive** . No setor entrado está disponível mensalmente (frequência: mês, intervalo: 1), setor de saída é produzido mensal e a propriedade do programador para a atividade também é definida como mensal. As definições para o conjunto de dados de saída e o Programador de atividade tem de corresponder. Atualmente, o conjunto de dados de saída é o que unidades agenda, para que o mesmo se a atividade não produz qualquer saída, tem de criar um conjunto de dados de saída. Se a atividade não tomar qualquer entrada, também pode ignorar a criar o conjunto de dados de entrada. As propriedades de utilizadas em JSON a seguinte são explicadas no final desta secção.

1. No **Explorador de soluções**, com o botão direito **tubagens**, aponte para **Adicionar**e clique em **Novo Item.** 
2. Selecione **Hive Pipeline de transformação** a partir da lista e clique em **Adicionar**. 
3. Substitua o **JSON** fragmento que se segue.

    > [AZURE.IMPORTANT] Substitua **storageaccountname** com o nome da sua conta de armazenamento.

        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService1",
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
    
    No fragmento JSON, está a criar uma tubagem que consiste numa única atividade que utiliza Hive para o processo de dados num HDInsight cluster.
    
    O ficheiro de script do ramo, **partitionweblogs.hql**, é armazenado na conta de armazenamento Azure (especificado por scriptLinkedService, denominado **AzureStorageLinkedService1**) e na pasta de **script** no contentor **adfgetstarted**.

    A secção **define** é utilizada para especificar as definições de runtime são transmitidas para o script ramo como valores de configuração do ramo (por exemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

    As propriedades de **início** e de **fim** do pipeline de Especifica o período ativo do pipeline de.

    Na atividade JSON, pode especificar que o script ramo é executada no cluster especificado pela **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Consulte o artigo [anatomia de uma tubagem](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter mais detalhes acerca das propriedades JSON utilizadas no exemplo. 
3. Guarde o ficheiro **HiveActivity1.json** .

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Adicionar partitionweblogs.hql e input.log como uma dependência 

1. Botão direito do rato **dependências** na janela do **Explorador de solução** , aponte para **Adicionar**e clique em **Item existente**.  
2. Navegue para a **C:\ADFGettingStarted** e selecione **partitionweblogs.hql**, **input.log** ficheiros e clique em **Adicionar**. Tiver criado estes dois ficheiros como parte da pré-requisitos da [Descrição geral de iniciação](data-factory-build-your-first-pipeline.md).

Quando publicar a solução no próximo passo, o ficheiro **partitionweblogs.hql** é carregado para a pasta de scripts no contentor de Blobs do **adfgetstarted** .   

### <a name="publishdeploy-data-factory-entities"></a>Publicar/implementar entidades fábrica de dados

18. Project no Explorador de solução com o botão direito e clique em **Publicar**. 
19. Se vir a caixa de diálogo **Iniciar sessão sua conta Microsoft** , introduza as suas credenciais da conta que tenha subscrição Azure e clique em **Iniciar sessão**.
20. Deverá visualizar a caixa de diálogo seguintes:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Na página Configurar fábrica de dados, faça o seguinte: 
    1. Selecione a opção de **Criar novas fábrica de dados** .
    2. Introduza um único **nome** para a fábrica de dados. Por exemplo: **FirstDataFactoryUsingVS09152016**. O nome deve ser globalmente exclusivo.  
    
    
        > [AZURE.IMPORTANT] Se receber o erro **"FirstDataFactoryUsingVS" nome da fábrica de dados não está disponível** quando a publicação, altere o nome (por exemplo, yournameFirstDataFactoryUsingVS). Consulte o tópico de [Dados Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefactos fábrica de dados.
3. Selecione a subscrição à direita para o campo de **subscrição** .
     
     
        > [AZURE.IMPORTANT] Se não vir nenhuma subscrição, certifique-se de que iniciou sessão com uma conta que é um administrador ou co-administrador da subscrição.  
        
    4. Selecione o **grupo de recursos** para a fábrica de dados seja criada. 
    5. Selecione a **região** para a fábrica de dados. 
    6. Clique em **seguinte** para mudar para a página de **Publicar itens** . (Prima **SEPARADOR** para retirar o campo nome a se está desativado no botão **seguinte** ). 
23. Na página de **Publicar itens** , certifique-se de que fábricas dados entidades são selecionadas e clique em **seguinte** para mudar para a página de **Resumo** .     
24. Reveja o resumo e clique em **seguinte** para iniciar o processo de implementação e ver o **Estado da implementação**.
25. Na página de **Estado de implementação** , deverá ver o estado do processo de implementação. Quando concluir o processo de implementação, clique em Concluir. 

 
Pontos importantes a tenha em atenção: 

- Se receber o erro: "**Esta subscrição não estiver registada para utilizar o espaço de nomes Microsoft.DataFactory**", efetue um dos seguintes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor de dados fábrica do mesmo. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Pode executar o seguinte comando para confirmar que a fábrica de dados está registado o fornecedor. 
    
            Get-AzureRmResourceProvider
    - Inicie sessão utilizando a subscrição Azure no [portal do Azure](https://portal.azure.com) e navegue para uma pá fábrica de dados (ou) criar uma fábrica de dados no portal do Azure. Esta ação regista automaticamente o fornecedor para si.
-   O nome da fábrica dados poderá estar registado como um nome DNS no futuro e, consequentemente, ficam publicamente visível.
-   Para criar instâncias de fábrica de dados, tem de ser um administrador ou co-administrador da subscrição do Azure

 
## <a name="monitor-pipeline"></a>Em curso do monitor

### <a name="monitor-pipeline-using-diagram-view"></a>Em curso monitor utilizando a vista de diagrama
6. Inicie a sessão [portal do Azure](https://portal.azure.com/), efetue o seguinte procedimento:
    1. Clique em **mais serviços** e clique em **fábricas de dados**.
        ![Navegue até fábricas de dados](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
    2. Selecione o nome do seu fábrica de dados (por exemplo: **FirstDataFactoryUsingVS09152016**) na lista de fábricas de dados. 
        ![Selecione a fábrica de dados](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
7. Na página de base para a fábrica de dados, clique em **diagrama**.
  
    ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. Na vista de diagrama, pode ver uma descrição geral da tubagens e conjuntos de dados utilizados neste tutorial.
    
    ![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Para ver todas as atividades no pipeline de, com o botão direito em curso no diagrama e clique em abrir em curso. 

    ![Menu de tubagem aberta](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Confirme que está a ver a atividade de HDInsightHive no pipeline de. 
  
    ![Vista de tubagem aberta](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Para navegar de volta para a vista anterior, clique em **fábrica de dados** no menu de trilho na parte superior. 
10. Na **Vista de diagrama**, faça duplo clique sobre o conjunto de dados **AzureBlobInput**. Confirme que o setor está num estado **pronto** . Poderá demorar alguns minutos para setor Mostrar estado preparado. Se não acontecer depois de espera para algures, consulte o artigo se tiver o ficheiro de entrada (input.log) colocado na pasta (inputdata) e contentor direita (adfgetstarted).

    ![Entrada setor no estado pronta](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Clique em **X** para fechar **AzureBlobInput** pá. 
12. Na **Vista de diagrama**, faça duplo clique sobre o conjunto de dados **AzureBlobOutput**. Verá que no setor que está atualmente a ser processado.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Quando concluir o processo processamento, verá setor no estado **pronta** .

    > [AZURE.IMPORTANT] Criação de um cluster de HDInsight a pedido normalmente leva-o até algures (cerca de 20 minutos). Espera por conseguinte, a tubagem para demorar **cerca de 30 minutos** para processar no setor.  

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png) 
    
10. Quando está no setor no estado **pronta** , selecione a pasta **partitioneddata** no contentor **adfgetstarted** no seu armazenamento blob para os dados de saída.  
 
    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Clique no setor para ver detalhes acerca do mesmo na pá **setor de dados** .

    ![Detalhes do setor de dados](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Clique numa actividade executar na **lista de será executado de atividade** para ver detalhes sobre uma actividade executar (ramo actividade nosso cenário) numa janela de **atividade executar detalhes** .   
    ![Executar detalhes de atividade](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)  
    
    A partir de ficheiros de registo, pode ver as informações de estado e ramo de consulta que foi executada. Estes registos são úteis para resolver quaisquer problemas com o problema.  
 

Consulte o artigo [Monitor conjuntos de dados e em curso](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como utilizar o portal do Azure para monitorizar as em curso e conjuntos de dados que criou neste tutorial.

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorizar pipeline através do Monitor gerir App
Também pode utilizar o Monitor e gerir aplicações para monitorizar a sua tubagens. Para obter informações detalhadas sobre como utilizar esta aplicação, consulte o artigo [Monitor e gerir tubagens Azure fábrica de dados utilizando a monitorização e gestão de aplicação](data-factory-monitor-manage-app.md).

1. Clique em Monitor e gerir mosaico.

    ![Monitorizar e gerir mosaico](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png) 
2. Deverá ver Monitor e gerir aplicações. Alterar a **hora de início** e **hora de fim** para corresponder ao iniciar (04-01-2016 12:00 AM) horas e de fim (04-02-2016 12:00 AM) do seu pipeline e clique em **Aplicar**.

    ![Monitorizar e gerir a aplicação](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png) 
3. Selecione uma janela de atividade na lista de atividade Windows para ver os detalhes acerca do mesmo. 
    ![Detalhes da janela de atividade](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)


> [AZURE.IMPORTANT] O ficheiro de entrada é eliminado quando no setor é processado com êxito. Por isso, se pretender executar novamente no setor ou não faça novamente o tutorial, carregue o ficheiro de entrada (input.log) para a pasta de inputdata do contentor adfgetstarted.
 

## <a name="use-server-explorer-to-view-data-factories"></a>Utilizar o Explorador de servidor para ver fábricas de dados

1. No **Visual Studio**, clique em **Ver** no menu e clique em **Explorador do servidor**.
2. Na janela do Server Explorer, expanda **Azure** e expanda **Fábrica de dados**. Se vir a **Iniciar sessão no Visual Studio**, introduza a **conta** associada à sua subscrição Azure e clique em **continuar**. Introduzir **palavra-passe**e clique em **Iniciar sessão**. Tentará Visual Studio obter informações sobre todas as fábricas Azure dados na sua subscrição. Ver o estado desta operação na janela da **Lista de tarefas de fábrica do mesmo de dados** .

    ![Explorador de servidor](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Pode uma fábrica de dados com o botão direito e selecione **Exportar fábrica de dados para o novo projeto** para criar um projeto do Visual Studio com base numa fábrica de dados existente.

    ![Exportar dados fábrica](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Atualizar as ferramentas de dados fábrica para Visual Studio

Para atualizar as ferramentas de fábrica do Azure dados para o Visual Studio, faça o seguinte:

1. Clique em **Ferramentas** no menu e selecione **Extensions e atualizações**.
2. Selecione **as atualizações** no painel esquerdo e, em seguida, selecione a **Galeria do Visual Studio**.
3. Selecione **Ferramentas Azure dados fábrica para Visual Studio** e clique em **Atualizar**. Se não vir esta entrada, já tem a versão mais recente das ferramentas. 

## <a name="use-configuration-files"></a>Utilizar ficheiros de configuração
Pode utilizar ficheiros de configuração no Visual Studio para configurar propriedades para serviços/tabelas/tubagens forma diferente para cada ambiente ligadas. 

Considere a seguinte definição de JSON para um serviço de armazenamento do Windows Azure ligadas. Para especificar **connectionString** com valores diferentes para accountname e accountkey com base no ambiente (teste/Dev Center/produção) para o qual está a implementar entidades fábrica de dados. É possível alcançar este comportamento utilizando o ficheiro de configuração separada para cada ambiente. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    } 

### <a name="add-a-configuration-file"></a>Adicionar um ficheiro de configuração
Adicione um ficheiro de configuração para cada ambiente executando os seguintes passos:   

1. Botão direito do rato projeto fábrica de dados na sua solução do Visual Studio, aponte para **Adicionar**e clique em **novo item**.
2. Selecione **configuração** a partir da lista dos modelos instalados no lado esquerdo, selecione o **Ficheiro de configuração**, introduza um **nome** para o ficheiro de configuração e clique em **Adicionar**.

    ![Adicionar o ficheiro de configuração](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Adicione parâmetros de configuração e os respetivos valores no seguinte formato.

        {
            "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
            "AzureStorageLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            ],
            "AzureSqlLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            ]
        }

    Este exemplo configura connectionString propriedade de um serviço de armazenamento do Windows Azure ligados e de um serviço do SQL Azure ligadas. Repare que a sintaxe para especificar o nome é [JsonPath](http://goessner.net/articles/JsonPath/).   

    Se JSON tem uma propriedade que tenha uma matriz de valores, tal como mostrado no seguinte código:  

        "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
        ],
    
    Configure propriedades, conforme mostrado no seguinte ficheiro de configuração (utilizar baseado em zero indexação): 
        
        {
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### <a name="property-names-with-spaces"></a>Nomes de propriedades com espaços
Se um nome de propriedade tiver espaços, utilize entre parênteses Retos conforme mostrado no exemplo seguinte (nome de servidor de base de dados): 

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### <a name="deploy-solution-using-a-configuration"></a>Implementar a solução utilizar uma configuração
Quando estiver a publicar entidades Azure dados fábrica VS, pode especificar a configuração que pretende utilizar para essa operação de publicação. 

Para publicar entidades num projeto de fábrica do Azure dados utilizando ficheiro de configuração:   

1. Clique no separador projeto fábrica de dados e clique em **Publicar** para ver a caixa de diálogo **Publicar itens** . 
2. Selecione uma fábrica de dados existente ou especificar valores para a criação de uma fábrica de dados na página **Configurar dados fábrica do mesmo** e clique em **seguinte**.   
3. Na página **Publicar itens** : verá uma lista pendente com configurações disponíveis para o campo **Selecione Config implementação** .

    ![Selecione o ficheiro de configuração](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Selecione o **ficheiro de configuração** que pretende utilizar e clique em **seguinte**. 
5. Confirme se vir o nome do ficheiro JSON na página de **Resumo** e clique em **seguinte**. 
6. Clique em **Concluir** depois da operação de implementação está concluída. 

Quando implementar, os valores a partir de ficheiro de configuração são utilizados para definir valores para propriedades na ficheiros JSON para entidades fábrica de dados antes das entidades são implementadas ao serviço de fábrica do Azure dados.   

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

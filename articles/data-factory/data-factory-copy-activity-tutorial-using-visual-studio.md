<properties 
    pageTitle="Tutorial: Criar uma tubagem com cópia atividade utilizando o Visual Studio | Microsoft Azure" 
    description="Neste tutorial, crie uma pipeline de fábrica do Azure dados com uma atividade de cópia utilizando o Visual Studio." 
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
    ms.topic="get-started-article" 
    ms.date="10/17/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Tutorial: Criar uma tubagem com cópia atividade utilizando o Visual Studio
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copiar o Assistente](data-factory-copy-data-wizard-tutorial.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelo do Gestor de recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados Azure utilizando o Visual Studio. A tubagem na fábrica dados utiliza uma atividade de copiar para copiar dados de armazenamento de Blobs do Azure para base de dados do SQL Azure.

Eis os passos a que executar como parte deste Tutorial:

1. Criar duas serviços ligados: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. 

    O AzureStorageLinkedService1 liga um armazenamento Azure e AzureSqlLinkedService1 ligações uma base de dados do Azure SQL para a fábrica de dados: **ADFTutorialDataFactoryVS**. Os dados de entrada para a tubagem residem num contentor de BLOBs no armazenamento de Blobs do Azure e dados de saída estão armazenados numa tabela na base de dados Azure SQL. Por conseguinte, adicionar estes arquivos de dados de duas como serviços ligados a fábrica de dados.
2. Criar os dois conjuntos de dados: **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída que estão armazenados nos arquivos de dados. 

    Para InputDataset, especifique o contentor de BLOBs que contém um blob com a origem de dados. Para OutputDataset, especifique a tabela de SQL que armazena os dados de saída. Também pode especificar outras propriedades, tais como a estrutura, disponibilidade e a política.
3. Crie uma tubagem denominada **ADFTutorialPipeline** na ADFTutorialDataFactoryVS. 

    A tubagem tem uma **Atividade de copiar** dados de cópias de entrada a partir do Azure blob para a tabela do Azure SQL de saída. A atividade de cópia executa o movimento de dados no Azure fábrica de dados. A atividade é recorrendo a um serviço disponível globalmente que pode copiar dados de entre vários arquivos de dados de forma segura, fiável e dimensionáveis. Consulte o artigo [Dados movimento atividades](data-factory-data-movement-activities.md) artigo para obter detalhes sobre a atividade de cópia. 
4. Crie uma fábrica de dados denominada **VSTutorialFactory**. Implemente a fábrica de dados e todas as entidades de fábrica de dados (serviços ligados, tabelas e a tubagem).    

## <a name="prerequisites"></a>Pré-requisitos

1. Leia o artigo [Descrição geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e concluir os passos de **pré-requisito** . 
2. Tem de ser um **administrador da subscrição do Azure** possam publicar entidades de dados fábrica Azure fábrica de dados.  
3. Tem de ter o seguinte instalado no seu computador: 
    - Visual Studio 2013 ou o Visual Studio 2015
    - Transferir o Azure SDK para Visual Studio 2013 ou o Visual Studio 2015. Navegue até à [Página de transferência do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET** .
    - Transferir o plug-in de fábrica do Azure dados mais recente para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Também pode atualizar o plug-in executando os seguintes passos: no menu, clique em **Ferramentas** -> **atualizações e extensões** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas de fábrica do Microsoft Azure dados para o Visual Studio** -> **Atualizar**.

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio 
1. Inicie o **Visual Studio 2013**. Clique em **ficheiro**, aponte para **Novo**e clique em **projeto**. Deverá ver a caixa de diálogo **Novo projeto** .  
2. Na caixa de diálogo **Novo projeto** , selecione o modelo de **DataFactory** e clique em **Projeto de fábrica do mesmo de dados vazia**. Se não vir o modelo de DataFactory, feche o Visual Studio, instalar Azure SDK do Visual Studio 2013 e abrir novamente o Visual Studio.  

    ![Caixa de diálogo novo projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Introduza um **nome** para o projeto, **localização**e um nome para a **solução**e clique em **OK**.

    ![Explorador de soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png) 

## <a name="create-linked-services"></a>Criar serviços ligados
Serviços ligados ligar arquivos de dados ou calcular serviços a uma fábrica dados Azure. Consulte o artigo [armazena dados suportadas](data-factory-data-movement-activities.md##supported-data-stores-and-formats) para todas as origens e sumidouros suportados pela atividade de cópia. Consulte o artigo [Calcular ligados serviços](data-factory-compute-linked-services.md) para a lista de serviços de cluster suportados por dados fábrica do mesmo. Neste tutorial, não é utilizar qualquer serviço cluster. 

Neste passo, pode cria dois serviços ligados: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. AzureStorageLinkedService1 ligadas ligações de serviço uma conta de armazenamento do Azure e AzureSqlLinkedService liga uma base de dados do Azure SQL a fábrica de dados: **ADFTutorialDataFactory**. 

### <a name="create-the-azure-storage-linked-service"></a>Criar o serviço de armazenamento do Windows Azure ligadas

4. Botão direito do rato **Serviços ligados** no Explorador de solução, aponte para **Adicionar**e clique em **Novo Item**.      
5. Na caixa de diálogo **Adicionar Novo Item** , selecione o **Serviço de armazenamento do ligadas Azure** a partir da lista e clique em **Adicionar**. 

    ![Novo serviço ligado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Substituir `<accountname>` e `<accountkey>`* com o nome da sua conta de armazenamento Azure e a sua chave. 

    ![Armazenamento Azure ligadas serviço](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Guarde o ficheiro **AzureStorageLinkedService1.json** .

> Consulte o artigo [mover os dados ou para BLOBs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter mais detalhes acerca das propriedades JSON.

### <a name="create-the-azure-sql-linked-service"></a>Criar o serviço do SQL Azure ligadas

5. Com o botão direito em **Serviços ligados** nó no **Explorador de soluções** novamente, aponte para **Adicionar**e clique em **Novo Item**. 
6. Desta vez, selecione o **Serviço ligadas do SQL Azure**e clique em **Adicionar**. 
7. No **ficheiro AzureSqlLinkedService1.json**, substituir `<servername>`, `<databasename>`, `<username@servername>`, e `<password>` com nomes do seu servidor Azure SQL, base de dados, conta de utilizador e palavra-passe.    
8.  Guarde o ficheiro **AzureSqlLinkedService1.json** . 

> [AZURE.NOTE]
> Consulte o artigo [mover os dados ou para a base de dados do Azure SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter mais detalhes acerca das propriedades JSON.

## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, que criou serviços ligados **AzureStorageLinkedService1** e **AzureSqlLinkedService1** para ligar uma conta de armazenamento do Windows Azure e a base de dados Azure SQL a fábrica de dados: **ADFTutorialDataFactory**. Neste passo, pode definir dois conjuntos de dados – **InputDataset** e **OutputDataset** – que representam os dados de entrada/saída que estão armazenados nos arquivos de dados referidos por AzureStorageLinkedService1 e AzureSqlLinkedService1, respetivamente. Para InputDataset, especifique o contentor de BLOBs que contém um blob com a origem de dados. Para OutputDataset, especifique a tabela de SQL que armazena os dados de saída.

### <a name="create-input-dataset"></a>Criar o conjunto de dados de entrada
Neste passo, criar um conjunto de dados denominado **InputDataset** que aponta para um contentor de Blobs do armazenamento do Windows Azure representada pelo serviço **AzureStorageLinkedService1** ligadas. Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados suportado neste momento. 

9. **Tabelas** no **Explorador de soluções**de contexto, aponte para **Adicionar**e clique em **Novo Item**.
10. Na caixa de diálogo **Adicionar Novo Item** , selecione **BLOBs do Azure**e clique em **Adicionar**.   
10. Substitua o texto JSON com o seguinte texto e guardar o ficheiro **AzureBlobLocation1.json** . 

        {
          "name": "InputDataset",
          "properties": {
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
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

     Tenha em atenção os seguintes pontos: 
    
    - conjunto de dados **tipo** está definido para **AzureBlob**.
    - **linkedServiceName** está definido para **AzureStorageLinkedService**. Este serviço ligado que criou no passo 2.
    - **caminhopasta** está definido para o contentor **adftutorial** . Também pode especificar o nome de um blob dentro da pasta utilizando a propriedade **nome do ficheiro** . Uma vez que não está a especificar o nome do blob dados a partir de todos os blobs no contentor são considerados como dados de entrada.  
    - Formatar **tipo** está definido para **TextFormat**
    - Existem dois campos no ficheiro de texto – **NomePróprio** e **Apelido** – separado por um caráter de ponto e vírgula (**columnDelimiter**) 
    - A **disponibilidade** está definido para **cada hora** (**frequência** está definido para **hora** e **intervalo** está definido para **1**). Por conseguinte, dados fábrica procura dados de entrada cada hora na pasta de raiz do contentor de BLOBs (**adftutorial**) que especificou. 
    
    Se não especificar um **nome de ficheiro** de um conjunto de dados de **entrada** , todos os ficheiros por blobs a partir da pasta de entrada (**caminhopasta**) são considerados como entradas do tipo. Se especificar um nome de ficheiro a JSON, apenas o especificado ficheiro/blob é considerado asn entrada.
 
    Se não especificar um **nome de ficheiro** para uma **tabela de resultados**, os ficheiros gerados a **caminhopasta** são com nome no seguinte formato: dados. &lt;Guid\&gt;. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Para definir **caminhopasta** e **nome de ficheiro** dinamicamente com base na data e hora **SliceStart** , utilize a propriedade **partitionedBy** . No exemplo seguinte, caminhopasta utiliza ano, mês e dia a partir do SliceStart (hora de início do setor a ser processada) e hora a partir de SliceStart utiliza o nome de ficheiro. Por exemplo, se um setor é endereçados produzido para 2016-09-20T08:00:00, a nomedapasta está definida para wikidatagateway/wikisampledataout/2016/09/20 e o nome de ficheiro está definido para 08.csv. 

            "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": 
            [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 

> [AZURE.NOTE]
> Consulte o artigo [mover os dados ou para BLOBs do Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter mais detalhes acerca das propriedades JSON.

### <a name="create-output-dataset"></a>Criar o conjunto de dados de saída
Neste passo, criar um conjunto de dados de saída com o nome **OutputDataset**. Pontos este conjunto de dados a uma tabela SQL na base de dados Azure SQL representada pelo **AzureSqlLinkedService1**. 

11. Botão direito do rato **tabelas** no **Explorador de soluções** novamente, aponte para **Adicionar**e clique em **Novo Item**.
12. Na caixa de diálogo **Adicionar Novo Item** , selecione **Azure SQL**e clique em **Adicionar**. 
13. Substitua o texto JSON a seguinte JSON e guarde o ficheiro **AzureSqlTableLocation1.json** .

        {
          "name": "OutputDataset",
          "properties": {
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
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

     Tenha em atenção os seguintes pontos: 
    
    - conjunto de dados **tipo** está definido para **AzureSQLTable**.
    - **linkedServiceName** está definido para **AzureSqlLinkedService** (criou este serviço ligado no passo 2).
    - **tabela** está definido para **emp**.
    - Existem três colunas – **ID**, **NomePróprio**e **Apelido** – na tabela emp na base de dados. ID de é uma coluna de identidade, pelo que necessita especificar apenas **NomePróprio** e **Apelido** aqui.
    - A **disponibilidade** está definido para **cada hora** (**frequência** definir a **hora** e **intervalo** de definido para **1**).  O serviço de dados fábrica gera um setor de dados de saída cada hora na tabela **emp** na base de dados Azure SQL.

> [AZURE.NOTE]
> Consulte o artigo [mover os dados ou para a base de dados do Azure SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter mais detalhes acerca das propriedades JSON.

## <a name="create-pipeline"></a>Criar em curso 
Criou tabelas e serviços ligados entrada/saída até ao momento. Agora, crie uma tubagem com uma **Atividade de copiar** para copiar dados a partir do Azure blob a base de dados do Azure SQL. 


1. Botão direito do rato **tubagens** no **Explorador de soluções**, aponte para **Adicionar**e clique em **Novo Item**.  
15. Selecione **Copiar dados Pipeline** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
16. Substitua o JSON com a seguinte JSON e guardar o ficheiro **CopyActivity1.json** .
            
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "InputDataset"
                  }
                ],
                "outputs": [
                  {
                    "name": "OutputDataset"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "style": "StartOfInterval",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z",
            "isPaused": false
          }
        }

    Tenha em atenção os seguintes pontos:

    - Na secção de atividades, existe apenas uma atividade cujo **tipo** está definido para **Copiar**.
    - Modo de entrada para a atividade está definido para **InputDataset** e saída da atividade de está definida para **OutputDataset**.
    - Na secção **typeProperties** , **BlobSource** for especificada como o tipo de origem e **SqlSink** for especificada como o tipo de sink.

    Substitua o valor da propriedade **Iniciar** com o valor de dia e de **fim** atual com o dia seguinte. Pode especificar apenas a parte de data e ignorar a parte da hora de data hora. Por exemplo, "2016-02-03", que equivale às "2016-02-03T00:00:00Z"
    
    Inicie o ambos e final DataHora tem de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. A hora de **fim** é opcional, mas utilizamos neste tutorial. 
    
    Se não especificar valor da propriedade de **fim** , é calculada como "**Iniciar + 48 horas**". Para executar a tubagem indefinidamente, especifique a **9999-09-09** como o valor da propriedade de **fim** .
    
    No exemplo anterior, existem 24 dados os setores do gráfico cada setor de dados é produzido por hora.

## <a name="publishdeploy-data-factory-entities"></a>Publicar/implementar entidades fábrica de dados
Neste passo, publicar entidades fábrica de dados (serviços ligados, conjuntos de dados e em curso) que criou anteriormente. Também é especificar o nome da nova fábrica de dados criada para colocar em espera estas entidades.  

18. Project no Explorador de solução com o botão direito e clique em **Publicar**. 
19. Se vir a caixa de diálogo **Iniciar sessão sua conta Microsoft** , introduza as suas credenciais da conta que tenha subscrição Azure e clique em **Iniciar sessão**.
20. Deverá visualizar a caixa de diálogo seguintes:

    ![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
21. Na página Configurar dados fábrica do mesmo, efetue os seguintes passos: 
    1. Selecione a opção de **Criar novas fábrica de dados** .
    2. Introduza **VSTutorialFactory** para o **nome**.  
    
        > [AZURE.IMPORTANT]  
        > O nome da fábrica dados Azure tem de ser exclusivo global. Se receber um erro sobre o nome da fábrica de dados quando de publicação, altere o nome da fábrica de dados (por exemplo, yournameVSTutorialFactory) e experimente publicar novamente. Consulte o tópico de [Dados Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefactos fábrica de dados.     
    3. Selecione a sua subscrição do Azure para o campo de **subscrição** .
     
        > [AZURE.IMPORTANT]Se não vir nenhuma subscrição, certifique-se de que iniciou sessão com uma conta que é um administrador ou co-administrador da subscrição.  
    4. Selecione o **grupo de recursos** para a fábrica de dados seja criada. 5. Selecione a **região** para a fábrica de dados. Apenas as regiões suportados pelo serviço fábrica de dados são apresentadas na lista pendente.
6. Clique em **seguinte** para mudar para a página de **Publicar itens** .
    
        ![Configurar página fábrica de dados](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
23. Na página de **Publicar itens** , certifique-se de que fábricas dados entidades são selecionadas e clique em **seguinte** para mudar para a página de **Resumo** .
    
    ![Publicar a página de itens](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
24. Reveja o resumo e clique em **seguinte** para iniciar o processo de implementação e ver o **Estado da implementação**.

    ![Publicar a página de resumo](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
25. Na página de **Estado de implementação** , deverá ver o estado do processo de implementação. Quando concluir o processo de implementação, clique em Concluir. 
    ![Página de estado de implementação](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png) tenha em atenção os seguintes pontos: 

- Se receber o erro: "**Esta subscrição não estiver registada para utilizar o espaço de nomes Microsoft.DataFactory**", efetue um dos seguintes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor de dados fábrica do mesmo. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Pode executar o seguinte comando para confirmar que a fábrica de dados está registado o fornecedor. 
    
            Get-AzureRmResourceProvider
    - Inicie sessão utilizando a subscrição Azure para o [portal do Azure](https://portal.azure.com) e navegue para uma pá fábrica de dados (ou) criar uma fábrica de dados no portal do Azure. Esta ação regista automaticamente o fornecedor para si.
-   O nome da fábrica dados poderá estar registado como um nome DNS no futuro e, consequentemente, ficam publicamente visível.

> [AZURE.IMPORTANT] Para criar instâncias de fábrica de dados, tem de ser um administrador/co-administrador da subscrição do Azure

## <a name="summary"></a>Resumo
Neste tutorial, que criou uma fábrica Azure dados para copiar dados a partir de um Azure blob para uma base de dados do Azure SQL. Utilizava o Visual Studio para criar a fábrica de dados, serviços ligados, conjuntos de dados e uma tubagem. Eis os passos de alto nível executado neste tutorial:  

1.  Criado um Azure **fábrica de dados**.
2.  Criadas **ligados serviços**:
    1. Um serviço de **Armazenamento do Windows Azure** ligada para ligar a sua conta de armazenamento do Windows Azure que detém dados de entrada.    
    2. Um serviço de ligadas **Azure SQL** para ligar a sua base de dados do Azure SQL que contém os dados de saída. 
3.  Criado **conjuntos de dados**, o que descrevem os dados de entrada e dados de saída para tubagens.
4.  Criado uma **pipeline de** com uma **Atividade de cópia** com **BlobSource** como origem e **SqlSink** como sink. 


## <a name="use-server-explorer-to-view-data-factories"></a>Utilizar o Explorador de servidor para ver fábricas de dados

1. No **Visual Studio**, clique em **Ver** no menu e clique em **Explorador do servidor**.
2. Na janela do Server Explorer, expanda **Azure** e expanda **Fábrica de dados**. Se vir a **Iniciar sessão no Visual Studio**, introduza a **conta** associada à sua subscrição Azure e clique em **continuar**. Introduzir **palavra-passe**e clique em **Iniciar sessão**. Tentará Visual Studio obter informações sobre todas as fábricas Azure dados na sua subscrição. Ver o estado desta operação na janela da **Lista de tarefas de fábrica do mesmo de dados** .
    ![Explorador de servidor](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Pode com o botão direito numa fábrica de dados e selecione Exportar fábrica de dados para o novo projeto para criar um projeto do Visual Studio com base numa fábrica de dados existente.
    ![Exportar a fábrica de dados para um projeto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Atualizar as ferramentas de dados fábrica para Visual Studio
Para atualizar as ferramentas de fábrica do Azure dados para o Visual Studio, efetue os seguintes passos:

1. Clique em **Ferramentas** no menu e selecione **Extensions e atualizações**. 
2. Selecione **as atualizações** no painel esquerdo e, em seguida, selecione a **Galeria do Visual Studio**.
4. Selecione **Ferramentas Azure dados fábrica para Visual Studio** e clique em **Atualizar**. Se não vir esta entrada, já tem a versão mais recente das ferramentas. 

Consulte o artigo [Monitor conjuntos de dados e em curso](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para obter instruções sobre como utilizar o portal do Azure para monitorizar as em curso e conjuntos de dados que criou neste tutorial.

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de movimento de dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a atividade de cópia utilizados no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos agendamento e execução de modelo de aplicação do Azure fábrica de dados. |
| [Tubagens](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender tubagens e atividades no Azure fábrica de dados |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender conjuntos de dados no Azure fábrica de dados.
| [Monitorizar e gerir tubagens utilizando a aplicação de monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar tubagens utilizando a monitorização e gestão de aplicação. 

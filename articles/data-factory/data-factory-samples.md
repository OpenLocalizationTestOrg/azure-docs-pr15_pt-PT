<properties     
    pageTitle="Dados Azure Factory - amostras" 
    description="Fornece detalhes sobre exemplos que trabalham com o serviço do Azure fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---samples"></a>Dados Azure Factory - amostras

## <a name="samples-on-github"></a>Amostras no GitHub
O [GitHub Azure-DataFactory repositório](https://github.com/azure/azure-datafactory) contém várias amostras que ajudá-lo rapidamente optimização com o serviço de fábrica do Azure dados (ou) modificar os scripts e utilizá-la na própria aplicação. A pasta Samples\JSON contém fragmentos JSON para cenários comuns.

| Exemplo | Descrição |
| :----- | :---------- | 
| [Tutorial ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | Este exemplo fornece um tutorial de fim para fim para processar ficheiros de registo através do Azure dados fábrica para ligar dados a partir de ficheiros de registo para informações de. <br/><br/>Neste tutorial, pipeline de dados fábrica recolhe registos de exemplo, processos e enriquece os dados a partir de registos com os dados de referência e transformações dos dados para avaliar a eficácia da campanha de marketing que foi iniciada recentemente. |
| [Exemplos JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | Este exemplo fornece exemplos JSON para cenários comuns. | 
| [Exemplo de Downloader HTTP dados](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Este exemplo showcases transferência de dados a partir de um ponto final HTTP ao armazenamento de Blobs do Azure utilizando atividade .NET personalizada. |
| [Exemplo de atividade líquido do ponto de domínio de aplicação de cruz](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Este exemplo permite-lhe para a criação de uma atividade de .NET personalizada que não está restringida a versões assemblagem utilizados no iniciador de ADF (por exemplo, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.). |
| [Executar R script](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |  Este exemplo inclui atividade personalizada a fábrica de dados que pode ser utilizada para invocar RScript.exe. Este exemplo só funciona com o seu próprio cluster de HDInsight (não a pedido) que já tem instalado R no mesmo. |
| [Invocar tarefas motores num cluster de HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | Este exemplo mostra como utilizar MapReduce atividade para invocar um programa de motores. O programa de motores apenas copia os dados a partir de um contentor de Blobs do Azure para outra. |
| [Análise de twitter utilizando a atividade de pontuação Azure máquina aprendizagem Batch](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | Este exemplo mostra como utilizar AzureMLBatchScoringActivity para invocar um modelo de formação de máquina Azure que efetua a análise de sentimento twitter, pontuação, previsão, etc. |
| [Análise de twitter utilizando atividade personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |  Este exemplo mostra como utilizar uma atividade de .NET personalizada para invocar um modelo de formação de máquina Azure que efetua a análise de sentimento twitter, pontuação, previsão, etc. |
| [Tubagens parametrizadas para computador Azure aprendizagem](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | O exemplo fornece um ponto a ponto c# código de para implementar tubagens N para pontuação e reciclagem cada uma com um parâmetro de região diferente onde está a chegar a lista de regiões a partir de um ficheiro de parameters.txt, que está incluído com este exemplo. | 
| [Referência de atualização de dados para trabalhos de análise de sequência do Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |  Este exemplo mostra como utilizar fábrica de dados do Azure e Azure a análise da cadeia em conjunto para executar as consultas com dados de referência e configurar a atualização de dados de referência com base numa agenda. |
| [Híbrido Pipeline com Hadoop de Hortonworks no local](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | O exemplo utiliza um cluster de Hadoop no local como um cluster de destino para executar tarefas na fábrica de dados, tal como adicionaria outros destinos cluster como um HDInsight com base Hadoop cluster na nuvem. |
| [Ferramenta de conversão de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | Esta ferramenta permite-lhe converter JSONs de versão anterior ao 2015-07-01-pré-visualizar para 2015-07-01-pré-visualizar ou mais recente (predefinição). |  
| [Ficheiro de entrada de exemplo U SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |  Este ficheiro é um ficheiro de exemplo utilizado por uma actividade U-SQL. | 

## <a name="azure-resource-manager-templates"></a>Azure modelos de Gestor de recursos
Pode encontrar os seguintes modelos de Gestor de recursos do Azure para fábrica de dados no Github. 

| Modelo | Descrição |
| -------- | ----------- | 
| [Copiar a partir do armazenamento de Blobs do Azure para base de dados Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) | Implementar este modelo cria uma fábrica Azure dados com uma tubagem que copia dados a partir do armazenamento de Blobs do Azure especificado para a base de dados do Azure SQL |    
| [Copie da equipa de vendas ao armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) | Implementar este modelo cria uma fábrica Azure dados com uma tubagem que copia os dados a partir da conta do Salesforce especificada ao armazenamento de Blobs do Azure. |    
| [Transformar dados ao executar o script ramo num cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) | Implementar este modelo cria uma fábrica Azure dados com uma tubagem que transforma dados ao executar o script de ramo de exemplo num cluster Azure HDInsight Hadoop. | 


## <a name="samples-in-azure-portal"></a>Amostras no portal do Azure
Pode utilizar o mosaico **tubagens de exemplo** na home page da sua fábrica de dados para implementar tubagens de exemplo e os respetivos entidades associadas (conjuntos de dados e serviços ligados) no seu fábrica de dados. 

1. Crie uma fábrica de dados ou abra uma fábrica de dados existente. Consulte o artigo [Introdução ao Azure dados fábrica](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory) para obter os passos para criar uma fábrica de dados.
2. No pá a **Fábrica de dados** para a fábrica de dados, clique no mosaico **tubagens de exemplo** .

    ![Mosaico de tubagens de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. Na pá **tubagens de exemplo** , clique em **amostra** que pretende implementar. 
    
    ![Pá tubagens amostra](./media/data-factory-samples/SampleTile.png)

3. Especificar as definições de configuração da amostra. Por exemplo, a chave de nome e a conta da conta Azure armazenamento, nome de servidor do Azure SQL, base de dados, ID de utilizador e palavra-passe, etc. 

    ![Pá de exemplo](./media/data-factory-samples/SampleBlade.png)

4. Depois de terminar com especificar as definições de configuração, clique em **Criar** para criar/implementar a tubagens de exemplo e serviços/tabelas ligadas utilizadas pelas tubagens.
5. Ver o estado de implementação no mosaico do exemplo que anterior clicou na pá **tubagens de exemplo** .

    ![Estado de implementação](./media/data-factory-samples/DeploymentStatus.png)

6. Quando vir a mensagem **foi concluída com êxito implementação** no mosaico da amostra, feche o pá **tubagens de exemplo** .  
5. No pá **Fábrica de dados** , verá que serviços ligados, conjuntos de dados e tubagens são adicionadas à sua fábrica de dados.  

    ![Pá fábrica de dados](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## <a name="samples-in-visual-studio"></a>Amostras no Visual Studio

### <a name="prerequisites"></a>Pré-requisitos

Tem de ter o seguinte instalado no seu computador: 

- Visual Studio 2013 ou o Visual Studio 2015
- Transferir o Azure SDK para Visual Studio 2013 ou o Visual Studio 2015. Navegue até à [Página de transferência do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET** .
- Transferir o plug-in de fábrica do Azure dados mais recente para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se estiver a utilizar o Visual Studio 2013, também pode atualizar o plug-in executando os seguintes passos: no menu, clique em **Ferramentas** -> **atualizações e extensões** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas de fábrica do Microsoft Azure dados para o Visual Studio** -> **Atualizar**.

### <a name="use-data-factory-templates"></a>Utilizar modelos de fábrica do mesmo de dados

1. Clique em **ficheiro** , no menu, aponte para **Novo**e clique em **projeto**. 
2. Na caixa de diálogo **Novo projeto** , efetue os seguintes passos: 
    1. Selecione **DataFactory** em **modelos**. 
    2. Selecione **Os modelos de dados fábrica do mesmo** no painel direito. 
    3. Introduza um **nome** para o projeto. 
    4. Selecione uma **localização** para o projeto. 
    5. Clique em **OK**. 

    ![Caixa de diálogo novo projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Na caixa de diálogo **Fábrica os modelos de dados** , selecione o modelo de exemplo a partir da secção de **Modelos de casos de utilização** e clique em **seguinte**. Os passos seguintes ajudá-lo através de utilizar o modelo de **Criação de perfis de cliente** . Passos são semelhantes para os outros exemplos. 

    ![Caixa de diálogo de fábrica do mesmo modelos de dados](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. Na caixa de diálogo **Configuração da fábrica de dados** , clique em **seguinte** na página **Noções básicas de fábrica do mesmo de dados** .
8. Na página **Configurar dados fábrica do mesmo** , efetue os seguintes passos: 
    1. Selecione **Criar novo fábrica de dados**. Também pode selecionar a **fábrica de dados existente utilização**.
    2. Introduza um **nome** para a fábrica de dados.
    3. Selecione a **subscrição do Azure** na qual pretende que a fábrica de dados seja criada. 
    4. Selecione o **grupo de recursos** para a fábrica de dados.
    5. Selecione o **Ocidental dos EUA**, **Leste dos EUA**ou **Europa Norte** **região**.
    6. Clique em **seguinte**. 
9. Na página **armazena dados de configurar** , especifique uma existente **base de dados Azure SQL** e a **conta de armazenamento Azure** (ou) criar/armazenamento de base de dados e clique em seguinte. 
10. Na página **Configurar calcular** , selecione as predefinições e clique em **seguinte**. 
11. Na página de **Resumo** , reveja todas as definições e, clique em **seguinte**. 
12. Na página **Estado de implementação** do, aguarde até terminar a implementação e clique em **Concluir**.
13. Project no Explorador de solução com o botão direito e clique em **Publicar**. 
19. Se vir a caixa de diálogo **Iniciar sessão sua conta Microsoft** , introduza as suas credenciais da conta que tenha subscrição Azure e clique em **Iniciar sessão**.
20. Deverá visualizar a caixa de diálogo seguintes:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Na página **Configurar dados fábrica do mesmo** , efetue os seguintes passos: 
    1. Confirme essa opção de **utilização fábrica de dados existente** .
    2. Selecione a **fábrica de dados** que tinha Selecione quando utilizar o modelo. 
    6. Clique em **seguinte** para mudar para a página de **Publicar itens** . (Prima **SEPARADOR** para retirar o campo nome a se está desativado no botão **seguinte** ). 
23. Na página de **Publicar itens** , certifique-se de que fábricas dados entidades são selecionadas e clique em **seguinte** para mudar para a página de **Resumo** .     
24. Reveja o resumo e clique em **seguinte** para iniciar o processo de implementação e ver o **Estado da implementação**.
25. Na página de **Estado de implementação** , deverá ver o estado do processo de implementação. Quando concluir o processo de implementação, clique em Concluir. 

Consulte o artigo [criar a primeira fábrica de dados (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre como utilizar o Visual Studio para a criação de entidades fábrica de dados e publicá-los Azure.          
<properties 
    pageTitle="Mover dados - data Management Gateway | Microsoft Azure"
    description="Configure um gateway de dados para mover dados entre no local e na nuvem. Utilize o Data Management Gateway no Azure dados fábrica para mover os seus dados." 
    keywords="gateway de dados, integração de dados, mover os dados, credenciais de gateway"
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="jingwang"/>

# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mover dados entre origens no local e na nuvem com o Data Management Gateway
Este artigo fornece uma descrição geral da integração de dados entre arquivos de dados no local e na nuvem arquivos de dados utilizando a fábrica de dados. Cria o artigo [Dados movimento atividades](data-factory-data-movement-activities.md) e outros artigos sobre core dados fábrica conceitos: [conjuntos de dados](data-factory-create-datasets.md) e [tubagens](data-factory-create-pipelines.md). 

## <a name="data-management-gateway"></a>O Data Management Gateway
Tem de instalar o Data Management Gateway no seu computador no local para ativar a mover dados para a um arquivo de dados no local. O gateway pode ser instalado no mesmo computador, como o arquivo de dados ou num computador diferente, desde que o gateway pode ligar-se para o arquivo de dados. 

> [AZURE.IMPORTANT] Consulte o artigo [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes sobre o Data Management Gateway.   

O seguinte tutorial mostra-lhe como criar uma fábrica de dados com uma tubagem que move dados a partir de uma base de dados do **SQL Server** no local para um armazenamento de Blobs do Azure. Como parte do tutorial, instalar e configurar o Data Management Gateway no seu computador. 

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Tutorial: copiar dados no local à nuvem
  
## <a name="create-data-factory"></a>Criar a fábrica de dados
Neste passo, utilize o Azure portal para criar uma instância de fábrica do Azure dados com o nome **ADFTutorialOnPremDF**. 

1.  Inicie a sessão [portal do Azure](https://portal.azure.com). 
2.  Clique em **+ Novo**, clique em **informações da empresa + analytics**e clique em **Dados fábrica do mesmo**.

    ![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
2. Na pá **Novo fábrica de dados** , introduza **ADFTutorialOnPremDF** para o nome.

    ![Adicionar a Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

    > [AZURE.IMPORTANT] 
    > O nome da fábrica dados Azure tem de ser exclusivo global. Se receber o erro: o **nome da fábrica de dados "ADFTutorialOnPremDF" não está disponível**, altere o nome da fábrica dados (por exemplo, yournameADFTutorialOnPremDF) e tente criar novamente. Utilize este nome em vez de ADFTutorialOnPremDF enquanto executa restantes passos neste tutorial.
    > 
    > O nome da fábrica dados poderá estar registado como um nome **DNS** no futuro e, consequentemente, ficam publicamente visível.
3. Selecione a **subscrição do Azure** onde pretende que a fábrica de dados seja criada. 
4.  Selecione o **grupo de recursos** de existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos com o nome: **ADFTutorialResourceGroup**. 
5.  Clique em **Criar** no pá a **fábrica de dados nova** .

    > [AZURE.IMPORTANT] Para criar instâncias de fábrica de dados, tem de ser um membro da função [Contribuinte fábrica de dados](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) ao nível do grupo de subscrição/recurso. 
11. Após a criação de estiver concluída, verá a **Fábrica de dados** pá conforme apresentado na seguinte imagem:

    ![Dados fábrica Home Page](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Criar o gateway
5. No pá **Fábrica de dados** , clique em **autor e implementar** mosaico para iniciar o **Editor de** para a fábrica de dados.

    ![Criar e implementar em mosaico](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.  No Editor de fábrica do mesmo de dados, clique em **... Mais** na barra de ferramentas e, em seguida, clique em **Novo gateway de dados**. Em alternativa, pode com o botão direito **Gateways de dados** na vista de árvore e clique em **Novo gateway de dados**. 

    ![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Na pá **Criar** , introduza **adftutorialgateway** para o **nome**e clique em **OK**.    

    ![Criar pá do Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)
3. Na pá **Configurar** , clique em **instalar diretamente neste computador**. Esta ação transfere o pacote de instalação do gateway, instalações, configura e regista o gateway no computador.  

    > [AZURE.NOTE] 
    > Utilize o Internet Explorer ou um Microsoft ClickOnce compatíveis browser.
    > 
    > Se estiver a utilizar o Chrome, aceda à [loja na web Chrome](https://chrome.google.com/webstore/), procurar com palavra-chave de "ClickOnce", selecione uma das extensões de ClickOnce e instalá-lo. 
    >  
    > Faça o mesmo para o Firefox (instalar suplemento). Clique em **Abrir Menu** botão na barra de ferramentas (**três linhas horizontais** no canto superior direito), clique em **suplementos**, procurar com palavra-chave de "ClickOnce", selecione uma das extensões de ClickOnce e instalá-lo.    

    ![Gateway - configurar pá](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Desta forma é a forma mais fácil (com um clique) Transferir, instalar, configurar e registar o gateway num único passo. Pode ver que a aplicação **Microsoft configuração Gestor data Management Gateway** está instalada no seu computador. Também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft dados gestão Gateway\2.0\Shared**.

    Também pode transferir e instalar manualmente o gateway utilizando as ligações neste pá e registar-utilizar a chave de mostrados na caixa de texto de **Nova chave** .
    
    Artigo do [Data Management Gateway](data-factory-data-management-gateway.md) Consulte para todos os detalhes sobre o gateway.

    >[AZURE.NOTE] Tem de ser um administrador no computador local para instalar e configurar o Data Management Gateway com êxito. Pode adicionar utilizadores adicionais para **Utilizadores de Gateway de gestão de dados** Windows grupo local. Os membros deste grupo podem utilizar a ferramenta de Gestor de configuração de Gateway de gestão de dados para configurar o gateway. 

5. Aguarde alguns minutos ou esperar até ver a seguinte mensagem de notificação:

    ![Instalação de gateway efetuada com êxito](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png) 
6. Inicie a aplicação do **Gestor de configuração de Gateway de gestão de dados** no seu computador. Na janela de **pesquisa** , escreva **Data Management Gateway** para aceder a este utilitário. Também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft dados gestão Gateway\2.0\Shared** 

    ![Gestor de configuração de gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
6. Confirmar que está a ver `adftutorialgateway is connected to the cloud service` mensagem. A barra da parte inferior de estado apresenta **ligado ao serviço em nuvem** , juntamente com uma **marca de verificação verde**.

    No separador **base** , também pode fazer as seguintes operações: 
    - **Registe-se** um gateway com uma chave a partir do portal Azure utilizando o botão de registo. 
    - **Parar** o serviço de anfitrião do Gateway de gestão de dados em execução no seu computador de gateway. 
    - **Atualizações de agenda** para ser instalado num momento específico do dia. 
    - Vista quando o gateway foi a **última atualização**.
    - Especificar a hora em que pode ser instalada uma atualização para o gateway. 

8. Mudar para o separador **Definições** . O certificado especificado na secção de **certificado** é utilizado para encriptar/desencriptar credenciais para o arquivo de dados no local que especificou no portal. (opcional) Clique em **Alterar** para utilizar em vez disso, o seu próprio certificado. Por predefinição, o gateway utiliza o certificado que seja gerado automaticamente pelo serviço fábrica de dados.

    ![Configuração de certificados do gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Também pode fazer no separador **Definições** , as seguintes ações: 
    - Ver ou exporte o certificado a ser utilizado pelo gateway.
    - Altere o ponto final HTTPS utilizado pelo gateway.    
    - Configure um servidor proxy HTTP para ser utilizado pelo gateway.   
9. (opcional) Mudar para o separador de **diagnóstico** , selecione a opção **Activar registo verboso** se pretende ativar o registo verboso que pode utilizar para resolver quaisquer problemas com o gateway. As informações de registo podem ser encontradas no **Visualizador de eventos** em **registos de serviços e aplicações** -> nó do**Data Management Gateway** . 

    ![Separador Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Também pode executar as seguintes ações no separador de **diagnóstico** : 
    
    - Utilize a secção de **Testar ligação** para uma origem de dados no local com o gateway.
    - Clique em **Ver registos** de início para ver o registo do Data Management Gateway numa janela do Visualizador de eventos. 
    - Clique em **Enviar registos** para carregar um ficheiro zip com registos dos últimos sete dias para a Microsoft para facilitar a resolução de problemas do seu problemas. 
10. No separador de **diagnóstico** , na secção **Testar ligação** , selecione **SqlServer** para o tipo de arquivo de dados, introduza o nome do servidor de base de dados, o nome da base de dados, especifique o tipo de autenticação, introduza o nome de utilizador e palavra-passe e clique em **Testar** para verificar se o gateway pode ligar-se à base de dados. 
11. Mudar para o browser e no **portal do Azure**, clique em **OK** no pá a **Configurar** e, em seguida, no pá **Novo gateway de dados** .
6. Deverá ver **adftutorialgateway** em **Gateways de dados** na vista de árvore à esquerda.  Se clicar no mesmo, deverá ver o JSON associado. 
    

## <a name="create-linked-services"></a>Criar serviços ligados 
Neste passo, pode cria dois serviços ligados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** liga uma base de dados do SQL Server no local e as ligações de serviço ligadas **AzureStorageLinkedService** armazenar um BLOBs do Azure para a fábrica de dados. Criar uma tubagem posteriormente destas instruções que copia os dados da base de dados do SQL Server no local para o arquivo de Blobs do Azure. 

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Adicionar um serviço ligado a uma base de dados do SQL Server no local
1.  No **Editor de fábrica do mesmo de dados**, clique em **arquivo de dados novos** na barra de ferramentas e selecione **Do SQL Server**. 

    ![Novo serviço do SQL Server ligadas](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.  No **editor de JSON** no lado direito, execute os seguintes passos: 
    1. **GatewayName**, especifique **adftutorialgateway**. 
    2. Em **connectionString**, execute os seguintes passos: 
        1. Para **nomedoservidor**, introduza o nome do servidor que aloja a base de dados do SQL Server.
        2. Para **databasename**, introduza o nome da base de dados.
        3. Clique em **encriptar** botão na barra de ferramentas. Isto transferências e inicia a aplicação de Gestor de credenciais.
        
            ![Aplicação do Gestor de credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
        5. Na caixa de diálogo **Definir as credenciais** , especifique o tipo de autenticação, nome de utilizador e palavra-passe e clique em **OK**. Se a ligação ser bem sucedida, as credenciais encriptadas são armazenadas na JSON e fecha a caixa de diálogo. 
        6. Feche o separador de browser vazia iniciada a caixa de diálogo se não for automaticamente fechado e voltar para o separador com o portal do Azure. 
  
            Na máquina de gateway, estas credenciais são **encriptados** utilizando um certificado que é o proprietário o serviço de dados fábrica do mesmo. Se pretender utilizar o certificado associado o Data Management Gateway em vez disso, consulte o artigo [Definir credenciais de forma segura](#set-credentials-and-security).    
    1.  Na barra de comando para implementar o serviço do SQL Server ligadas, clique em **Implementar** . Deverá ver o serviço ligado na vista de árvore. 
        
        ![Serviço do SQL Server associado na vista de árvore](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)  

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adicionar um serviço ligado para uma conta de armazenamento Azure
 
1. No **Editor de fábrica do mesmo de dados**, clique em **arquivo de dados novos** na barra de comandos e clique em **armazenamento Azure**.
2. Introduza o nome da sua conta de armazenamento Azure para o **nome da conta**.
3. Introduza a chave para a sua conta de armazenamento Azure na **chave da conta**.
4. Clique em **Implementar** para implementar o **AzureStorageLinkedService**.
   
 
## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, Criar entrada e saída de conjuntos de dados que representam dados de entrada e saídos a operação de cópia de (base de dados no local SQL Server = > armazenamento de Blobs do Azure). Antes de criar conjuntos de dados, efetue os seguintes passos (passos detalhados sobre segue a lista):

- Criar uma tabela com o nome **emp** da base de dados SQL Server que adicionou como um serviço ligado para a fábrica de dados e inserir apenas alguns exemplos de entradas para a tabela.
- Crie um contentor de BLOBs denominado **adftutorial** na conta de armazenamento de Blobs do Azure que adicionou como um serviço ligado para a fábrica de dados.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparar no local SQL Server para o tutorial

1. Na base de dados que especificou para o SQL Server no local serviço ligadas (**SqlServerLinkedService**), utilize o seguinte script SQL para criar a tabela de **emp** na base de dados.

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL, 
            FirstName varchar(50),
            LastName varchar(50),
            CONSTRAINT PK_emp PRIMARY KEY (ID)
        )
        GO 
2. Inserir alguns exemplo na tabela: 

        INSERT INTO emp VALUES ('John', 'Doe')
        INSERT INTO emp VALUES ('Jane', 'Doe')

### <a name="create-input-dataset"></a>Criar o conjunto de dados de entrada

1. No **Editor de fábrica do mesmo de dados**, clique em **... Mais**, clique em **novo conjunto de dados** na barra de comandos e clique em **tabela do SQL Server**. 
2.  Substitua JSON no painel da direita com o seguinte texto:
        
            {       
                "name": "EmpOnPremSQLTable",
                "properties": {
                    "type": "SqlServerTable",
                    "linkedServiceName": "SqlServerLinkedService",
                    "typeProperties": {
                        "tableName": "emp"
                    },
                    "external": true,
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "externalData": {
                            "retryInterval": "00:01:00",
                            "retryTimeout": "00:10:00",
                            "maximumRetry": 3
                        }
                    }
                }
            }    

    Tenha em atenção os seguintes pontos: 

    - **tipo** está definido para **SqlServerTable**.
    - **tabela** está definido para **emp**.
    - **linkedServiceName** está definido para **SqlServerLinkedService** (tiver criado este serviço ligado anteriormente neste tutorial.).
    - Para um conjunto de dados entrado que não é gerado pelo outra tubagem no Azure fábrica de dados, tem de definir **externos** **Verdadeiro**. -Indica que os dados de entrada são produzidos externa para o serviço de fábrica do Azure dados. Opcionalmente, pode especificar quaisquer políticas de dados externos utilizando o elemento **externalData** na secção **política** .    

    Consulte o artigo [mover os dados para a SQL Server](data-factory-sqlserver-connector.md) para obter mais detalhes acerca das propriedades JSON.
2. Na barra de comando para implementar o conjunto de dados, clique em **Implementar** .  


### <a name="create-output-dataset"></a>Criar o conjunto de dados de saída

1.  No **Editor de fábrica do mesmo de dados**, clique em **novo conjunto de dados** na barra de comandos e clique em **armazenamento de Blobs do Azure**.
2.  Substitua JSON no painel da direita com o seguinte texto: 

            {
                "name": "OutputBlobTable",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "AzureStorageLinkedService",
                    "typeProperties": {
                        "folderPath": "adftutorial/outfromonpremdf",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            }
  
    Tenha em atenção os seguintes pontos: 
    
    - **tipo** está definido para **AzureBlob**.
    - **linkedServiceName** está definido para **AzureStorageLinkedService** (tiver criado este serviço ligado no passo 2).
    - **caminhopasta** está definido para **adftutorial/outfromonpremdf** onde outfromonpremdf é a pasta no contentor adftutorial. Se não já existir, crie o contentor **adftutorial** . 
    - A **disponibilidade** está definido para **cada hora** (**frequência** definir a **hora** e **intervalo** de definido para **1**).  O serviço de dados fábrica gera um setor de dados de saída cada hora na tabela **emp** na base de dados do SQL Azure. 

    Se não especificar um **nome de ficheiro** para uma **tabela de resultados**, os ficheiros gerados a **caminhopasta** são com nome no seguinte formato: dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Para definir **caminhopasta** e **nome de ficheiro** dinamicamente com base na data e hora **SliceStart** , utilize a propriedade partitionedBy. No exemplo seguinte, caminhopasta utiliza ano, mês e dia a partir do SliceStart (hora de início do setor a ser processada) e hora a partir de SliceStart utiliza o nome de ficheiro. Por exemplo, se um setor é endereçados produzido de 2014-10-20T08:00:00, a nomedapasta está definida para wikidatagateway/wikisampledataout/2014/10/20 e o nome de ficheiro está definido para 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 
    Consulte o artigo [mover os dados para a armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md) para obter mais detalhes acerca das propriedades JSON.
2.  Na barra de comando para implementar o conjunto de dados, clique em **Implementar** . Confirme que está a ver ambos os conjuntos de dados na vista de árvore.  

## <a name="create-pipeline"></a>Criar em curso
Neste passo, criar uma **pipeline de** com uma **Cópia atividade** que utiliza **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1.  No Editor de dados de fábrica do mesmo, clique em **... Mais**e clique em **novo em curso**. 
2.  Substitua JSON no painel da direita com o seguinte texto: 
    
            {
                "name": "ADFTutorialPipelineOnPrem",
                "properties": {
                "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
                "activities": [
                {
                    "name": "CopyFromSQLtoBlob",
                    "description": "Copy data from on-prem SQL server to blob",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "EmpOnPremSQLTable"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "OutputBlobTable"
                      }
                    ],
                    "typeProperties": {
                      "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from emp"
                      },
                      "sink": {
                        "type": "BlobSink"
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
                "start": "2016-07-05T00:00:00Z",
                "end": "2016-07-06T00:00:00Z",
                "isPaused": false
              }
            }

    > [AZURE.IMPORTANT]
    > Substitua o valor da propriedade **Iniciar** com o valor de dia e de **fim** atual com o dia seguinte.

    Tenha em atenção os seguintes pontos:
 
    - Na secção de atividades, existe apenas actividade cujo **tipo** está definido para **Copiar**.
    - Modo de **entrada** para a atividade está definido para **EmpOnPremSQLTable** e **saída** da atividade de está definido para **OutputBlobTable**.
    - Na secção **typeProperties** , **SqlSource** é especificada como o **tipo de origem** e **BlobSink **for especificada como o **tipo de sink**.
    - Consulta SQL `select * from emp` especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

     Inicie o ambos e final DataHora tem de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **fim** é opcional, mas utilizamos neste tutorial. 
    
    Se não especificar valor da propriedade de **fim** , é calculada como "**Iniciar + 48 horas**". Para executar a tubagem indefinidamente, especifique a **9/9/9999** como o valor da propriedade de **fim** . 
    
    Estiver a definir a duração de tempo no qual os setores de dados são processados com base nas propriedades de **disponibilidade** que foram definidas para cada conjunto de dados do Azure fábrica de dados.
    
    No exemplo, existem 24 dados os setores do gráfico cada setor de dados é produzido por hora.     
2. Clique em **Implementar** na barra de comando para implementar o conjunto de dados (tabela é um conjunto de dados retangular). Confirme que a tubagem apresentado na vista de árvore em **tubagens** nó.  
5. Agora, clique em **X** duas vezes para fechar as lâminas para regressar ao pá a **Fábrica de dados** para o **ADFTutorialOnPremDF**.

**Parabéns!** Ter criado uma fábrica dados Azure, serviços ligados, conjuntos de dados e uma tubagem e agendada pipeline de com êxito.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver a fábrica de dados numa vista de diagrama 
1. No **portal do Azure**, clique em **diagrama** mosaico na home page para a fábrica de dados **ADFTutorialOnPremDF** . :

    ![Ligação de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Deverá visualizar o diagrama semelhante a imagem seguinte:

    ![Vista de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Pode ampliar, reduzir, zoom a 100%, zoom ajustar automaticamente posicionar tubagens e conjuntos de dados e mostrar informações de linhagem da (realça montante e descendentes itens dos itens seleccionados).  Pode fazer duplo clique de um objeto (conjunto de dados de entrada/saída ou em curso) para ver as propriedades do mesmo. 

## <a name="monitor-pipeline"></a>Em curso do monitor
Neste passo, utilize o portal do Azure para monitorizar o que é o problema numa fábrica dados Azure. Também pode utilizar os cmdlets do PowerShell para monitorizar a conjuntos de dados e tubagens. Para obter detalhes sobre como monitorizar, consulte o artigo [Monitor do computador e tubagens gerir](data-factory-monitor-manage-pipelines.md).

5. No diagrama, faça duplo clique **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable setores](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Repare que todos os dados setores para cima se encontram na estado **preparado** porque a duração de tubagem (hora de início para a hora de fim) é no passado. Também é uma vez que inseriu os dados na base de dados do SQL Server e é apresentada sempre. Confirme que os setores do gráfico não aparecem na secção **problema os setores do gráfico** na parte inferior. Para ver todos os setores, clique em **Ver mais** na parte inferior da lista dos setores do gráfico. 
7. Agora, na pá **conjuntos de dados** , clique em **OutputBlobTable**.

    ![OputputBlobTable setores](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
9. Clique em qualquer setor de dados a partir da lista e deverá ver o pá **Setor de dados** . Consulte a atividade é executado para no setor. Consulte o artigo de apenas uma atividade executar normalmente.  

    ![Dados setor pá](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se não estiver no setor no estado **pronta** , pode ver os montante os setores do gráfico que não estão prontos e que estejam a impedir o setor atual a partir de executar na lista de **montante os setores do gráfico que são ainda não está prontos** .

10. Clique na **atividade de executar** a partir da lista na parte inferior para ver **Detalhes de executar atividade**.

    ![Pá executar detalhes de atividade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

    Verá informações como débito, duração e o gateway utilizado para transferir os dados. 
11. Clique em **X** para fechar todas as lâminas até o utilizador 
12. voltar a pá principal para o **ADFTutorialOnPremDF**.
14. (opcional) Clique em **tubagens**, clique **ADFTutorialOnPremDF**e o nível de detalhe através de tabelas de entrada (**Consumed**) ou conjuntos de dados de saída (**produzidos**).
15. Utilize ferramentas de como utilizar ferramentas de como o [Explorador de armazenamento do Microsoft](http://storageexplorer.com/) para verificar que é criado um ficheiro/blob para cada hora.

    ![Explorador de armazenamento Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Próximos passos

- Artigo do [Data Management Gateway](data-factory-data-management-gateway.md) Consulte para todos os detalhes sobre o Data Management Gateway.
- Consulte o artigo [copiar dados de Blobs do Azure para Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber mais sobre como utilizar cópia atividade para mover os dados a partir de um arquivo de dados de origem para um arquivo de dados sink. 

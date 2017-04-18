<properties 
    pageTitle="Do SQL Server armazenados procedimento atividade" 
    description="Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado numa base de dados do SQL Azure ou armazém de dados do SQL Azure a partir de uma tubagem fábrica de dados." 
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
    ms.date="09/30/2016" 
    ms.author="spelluru"/>

# <a name="sql-server-stored-procedure-activity"></a>Do SQL Server armazenados procedimento atividade
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

Pode utilizar a atividade de procedimento de armazenado do SQL Server numa fábrica de dados [em curso](data-factory-create-pipelines.md) para invocar um procedimento armazenado de uma das seguintes arquivos de dados: 


- Base de dados Azure SQL 
- Armazém de dados do Azure SQL  
- Servidor de base de dados SQL na sua empresa ou um VM Azure. Tem de instalar o Data Management Gateway no mesmo computador que aloja a base de dados ou num computador em separado para evitar a competir para recursos com a base de dados. O Data Management Gateway é um software que liga-se no local origens de dados/origens de dados hosed no Azure VMs aos serviços em nuvem, de forma segura e geridos. Consulte o artigo [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway. 

Este artigo constrói no artigo do [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

## <a name="walkthrough"></a>Instruções passo a passo

### <a name="sample-table-and-stored-procedure"></a>Tabela de exemplo e procedimento armazenado
1. Crie a seguinte **tabela** na sua base de dados de SQL Azure SQL Server Management Studio ou qualquer outra ferramenta que estiver familiarizado com a utilizar. A coluna de datetimestamp é a data e hora quando o ID de correspondente é gerado. 

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    ID é identificada exclusivo e a coluna de datetimestamp é a data e hora quando o ID de correspondente é gerado.
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    > [AZURE.NOTE] Este exemplo utiliza a base de dados do SQL Azure, mas funciona da mesma forma para armazém de dados do SQL Azure e base de dados do SQL Server. 
2. Crie o seguinte **procedimento armazenado** que insere os dados para o **sampletable**.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS
        
        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

    > [AZURE.IMPORTANT] **Nome** e **invólucro** do parâmetro (DateTime neste exemplo) tem de corresponder do parâmetro especificado no pipeline/atividade JSON. Na definição do procedimento armazenado, certifique-se de que **@** é utilizada como um prefixo para o parâmetro.
    
### <a name="create-a-data-factory"></a>Criar uma fábrica de dados  
4. Inicie sessão no [portal do Azure](https://portal.azure.com/). 
5. Clique em **Novo** no menu à esquerda, clique em **informações da empresa + Analytics**e clique em **Dados fábrica do mesmo**.
    
    ![Novo fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory.png)   
4.  Na pá **Novo fábrica de dados** , introduza **SProcDF** para o nome. Nomes de dados fábrica Azure são **globalmente exclusivos**. Tem de prefixo o nome da fábrica dados com o seu nome, para ativar a criação da fábrica com êxito.

    ![Novo fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory-blade.png)      
3.  Selecione a sua **subscrição do Azure**. 
4.  Para o **Grupo de recursos**, efetue um dos seguintes passos: 
    1.  Clique em **Criar novo** e introduza um nome para o grupo de recursos.
    2.  Clique em **utilizar existente** e selecione um grupo de recursos existente.  
5.  Selecione a **localização** para a fábrica de dados.
6.  Selecione **Afixar ao dashboard** para que possa ver a fábrica de dados no dashboard de próxima vez que iniciar sessão. 
6.  Clique em **Criar** no pá a **fábrica de dados nova** .
6.  Consulte a fábrica de dados a ser criada no **dashboard** do Azure portal. Depois da fábrica de dados foi criada com êxito, verá a página de fábrica do mesmo dados, que mostra-lhe os conteúdos da fábrica de dados.
    ![Home page do fábrica dados](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço do SQL Azure ligadas  
Depois de criar a fábrica de dados, criar um serviço do SQL Azure ligadas que liga a base de dados do SQL Azure a fábrica de dados. Esta base de dados contém o sampletable tabela e sp_sample procedimento armazenado.

7.  Clique em **autor e implementar** no pá a **Fábrica de dados** para **SProcDF** iniciar o Editor de fábrica do mesmo de dados.
2.  Clique em **arquivo de dados novos** na barra de comandos e selecione a **Base de dados do SQL Azure**. Deverá visualizar o script JSON para criar um serviço do SQL Azure ligadas no editor. 

    ![Novo arquivo de dados](media/data-factory-stored-proc-activity/new-data-store.png)
4. Em JSON script, efetue as seguintes alterações: 
    1. Substituir ** &lt;nomedoservidor&gt; ** com o nome do seu servidor de base de dados do Azure SQL.
    2. Substituir ** &lt;databasename&gt; ** com a base de dados no qual foi criado a tabela e o procedimento armazenado.
    3. Substituir ** &lt; username@servername ** com a conta de utilizador que tenha acesso à base de dados.
    4. Substituir ** &lt;palavra-passe&gt; ** com a palavra-passe da conta de utilizador. 

    ![Novo arquivo de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
5. Na barra de comando para implementar o serviço ligado, clique em **Implementar** . Confirme que está a ver AzureSqlLinkedService na vista de árvore à esquerda. 

    ![Vista de árvore com o serviço ligadas](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
6. Clique em **... Mais** na barra de ferramentas, clique em **novo conjunto de dados**e clique em **Azure SQL**. **Novo conjunto de dados** na barra de comandos e selecione **Azure SQL**.

    ![Vista de árvore com o serviço ligadas](media/data-factory-stored-proc-activity/new-dataset.png)
7. Copiar/colar o script JSON seguinte para o editor de JSON.

        {               
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
7. Na barra de comando para implementar o conjunto de dados, clique em **Implementar** . Confirme que está a ver o conjunto de dados na vista de árvore. 

    ![Vista de árvore com os serviços ligados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar uma tubagem com SqlServerStoredProcedure atividade
Agora, vamos criar uma tubagem com uma atividade de SqlServerStoredProcedure.
 
9. Clique em **... Mais** no comando barra e clique em **novo em curso**. 
9. Copiar/colar o fragmento de JSON seguinte. Defina o **storedProcedureName** para **sp_sample**. Nome e invólucro do parâmetro **DateTime** tem de corresponder o nome e a maiúsculas e minúsculas do parâmetro na definição do procedimento armazenado.  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                "start": "2016-08-02T00:00:00Z",
                "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    Se precisa de ser efetuada com um valor nulo para um parâmetro, usar esta sintaxe: "parâmetro1": nulo (em minúsculas). 
9. Na barra de ferramentas para implementar a tubagem, clique em **Implementar** .  

### <a name="monitor-the-pipeline"></a>Monitorizar as em curso

6. Clique em **X** para fechar o Editor de fábrica do mesmo dados Pás e navegar de volta para o pá fábrica de dados e clique em **diagrama**.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
7. Na **Vista de diagrama**, pode ver uma descrição geral da tubagens e conjuntos de dados utilizados neste tutorial. 

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
8. Na vista de diagrama, faça duplo clique no conjunto de dados **sprocsampleout**. Consulte os setores no estado pronta. Deverá haver cinco setores uma vez que um setor é produzido para cada hora entre a hora de início e hora de fim a partir do JSON.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png) 
10. Quando um setor está no estado **pronta** , execute uma * *Selecione* sampletable** consulta contra a base de dados do Azure SQL para verificar que os dados foram inseridos à tabela de pelo procedimento armazenado.

    ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

    Consulte o artigo [Monitor pipeline de](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre como monitorizar tubagens Azure fábrica de dados.  

> [AZURE.NOTE] Neste exemplo, não a SprocActivitySample tem quaisquer entradas. Se pretende corrente de bicicleta esta atividade com uma atividade montante (ou seja, processamento de prévia), os resultados da atividade de montante da podem ser utilizados como entradas nesta actividade. Neste caso, esta actividade não executará até a actividade montante estiver concluída e os resultados das atividades montante estão disponíveis (no estado pronta). As entradas que não podem ser utilizadas diretamente como um parâmetro para a atividade de procedimento armazenado

## <a name="json-format"></a>Formato JSON
    {
        "name": "SQLSPROCActivity",
        "description": "description", 
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## <a name="json-properties"></a>Propriedades JSON

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
nome | Nome da atividade | Sim
Descrição | Texto que descreva a atividade para que é utilizada | N
tipo | SqlServerStoredProcedure | Sim
entradas do tipo | Opcional. Se especificar um conjunto de dados de entrada, tem de ser disponível (no estado 'Pronto') para a atividade de procedimento armazenado executar. Não pode ser consumido o conjunto de dados de entrada no procedimento armazenado como um parâmetro. Só é utilizado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. | N
resultados de | Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. Conjunto de dados de saída especifica a **agenda** para a atividade de procedimento armazenado (hora a hora, semanal, mensal, etc.). <br/><br/>O conjunto de dados de saída tem de utilizar um **serviço ligadas** que se refere a uma base de dados do SQL Azure ou um armazém de dados do SQL Azure ou uma base de dados SQL Server na qual pretende que o procedimento armazenado para executar. <br/><br/>O conjunto de dados de saída pode servir de uma forma para passar o resultado do procedimento armazenado para processamento por actividade outra ([interligação de atividades](data-factory-scheduling-and-execution.md#chaining-activities)) no pipeline de subsequentes. No entanto, fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve a uma tabela do SQL que aponta o conjunto de dados de saída para. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que só é utilizado para especificar o agendamento para executar a atividade de procedimento armazenado. | Sim
storedProcedureName | Especifique o nome do procedimento armazenado na base de dados Azure SQL ou armazém de dados de SQL Azure é representado pelo serviço ligado que utiliza a tabela de resultados. | Sim
storedProcedureParameters | Especificar valores para os parâmetros de procedimento armazenado. Se precisar de passar nulo um parâmetro, usar esta sintaxe: "parâmetro1": nulo (todas as minúsculas). Consulte o artigo o exemplo seguinte para saber como utilizar esta propriedade.| N

## <a name="passing-a-static-value"></a>Prisma um valor estático 
Agora, vamos pondere adicionar outra coluna 'Cenário' com o nome da tabela que contém um valor estático denominado exemplo de documentos.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
    
    AS
    
    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

Agora, passe o parâmetro de cenário e o valor da atividade de procedimento armazenado. A secção typeProperties no exemplo anterior aspeto fragmento que se segue:

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": 
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }


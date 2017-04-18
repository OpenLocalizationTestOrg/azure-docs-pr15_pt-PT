<properties
    pageTitle="Mover os dados do SQL Server no local para SQL Azure com Azure dados fábrica | Azure"
    description="Configure um pipeline ADF compõe a mensagem no duas atividades de migração de dados em conjunto mover dados numa base diária entre bases de dados no local e na nuvem."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>Mover os dados do SQL server no local para SQL Azure com fábrica de dados do Azure

Este tópico mostra como mover os dados a partir de uma base de dados no local SQL Server para uma base de dados do SQL Azure através do armazenamento de Blobs do Azure utilizando a fábrica de dados do Azure (ADF).

As ligações seguintes do **menu** para tópicos que descrevem como ingerir esta última dados para o destino ambientes onde os dados podem ser armazenados e processados durante o processo de ciências de dados de equipa.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>Introdução: O que é ADF e quando deve ser utilizada para migrar dados?

Azure fábrica de dados é um serviço de integração de dados totalmente geridas baseado na nuvem que orquestra e automatiza o movimento e transformação de dados. O conceito de chave no modelo de ADF está em curso. Uma tubagem é um agrupamento lógico das atividades, cada um dos quais define as ações a efetuar os dados contidos nos conjuntos de dados. Serviços ligados são utilizados para definir as informações necessárias para fábrica de dados ligar a recursos de dados.

Com ADF, os serviços de processamento de dados existentes podem ser compostos para tubagens de dados que estão disponíveis e geridos altamente na nuvem. Podem ser agendadas tubagens estes dados para ingerir esta última, preparar, transformar, analisar e publicar dados e ADF gere e orquestra os dados complexos e as dependências de processamento. Soluções podem ser rapidamente criadas e implementadas na nuvem, ligar-se um número crescente no local e na nuvem origens de dados.

Considere utilizar ADF:

- Quando precisa de dados automática e continuamente migradas num cenário de híbrido que acede aos recursos no local e na nuvem 
- Quando os dados são efectuados ou necessita de ser modificadas ou ter adicionada ao mesmo quando a ser migrada a lógica de negócio. 

ADF permite o agendamento e monitorização de tarefas utilizando scripts JSON simples que gerir o movimento dos dados numa base periódica. ADF também tem outros capacidades, tais como suporte para operações complexas. Para mais informações sobre ADF, consulte a documentação do [Azure dados fábrica (ADF)](https://azure.microsoft.com/services/data-factory/).


## <a name="scenario"></a>O cenário

Vamos configurar um pipeline ADF compõe a mensagem no duas atividades de migração de dados. Em conjunto sejam movidos dados numa base diária entre uma base de dados do SQL no local e uma base de dados do SQL Azure na nuvem. Duas atividades são:

* copiar dados de uma base de dados do SQL Server no local para uma conta de armazenamento de Blobs do Azure
* copiar dados a partir da conta de armazenamento de Blobs do Azure para uma base de dados do SQL Azure.

>[AZURE.NOTE] Os passos mostrados aqui foram adaptadas a partir do tutorial mais detalhado fornecido pela equipa ADF: [mover dados entre origens no local e na nuvem com o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) referências as secções relevantes do tópico são fornecidas conforme adequado.


## <a name="prereqs"></a>Pré-requisitos
Neste tutorial assume que tem:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure**. Utilizar uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento Azure, consulte o artigo [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) . Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento de. Consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acesso numa **base de dados Azure SQL**. Se tem de configurar uma base de dados do SQL Azure, tpoic [Introdução com base de dados do Microsoft Azure SQL](../sql-database/sql-database-get-started.md) fornece informações sobre como pode aprovisionar uma nova instância de uma base de dados do SQL Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

> [AZURE.NOTE] Este procedimento utiliza o [Azure portal](https://portal.azure.com/).


##<a name="upload-data"></a>Carregar os dados para o SQL Server no local

Utilizamos o [conjunto de dados de veículo pt](http://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O conjunto de dados de veículo pt está disponível, conforme indicado nessa mensagem, no Azure blob armazenamento [Pt veículo dados](http://www.andresmh.com/nyctaxitrips/). Os dados tem dois ficheiros, o ficheiro trip_data.csv, que contém os detalhes de viagem, e o ficheiro trip_far.csv, que contém os detalhes de tarifa paga para cada viagem. Um exemplo e a descrição destes ficheiros são fornecidas no campo Descrição [pt veículo viagens de conjunto de dados](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Pode adaptar o processo fornecido aqui para um conjunto de seus próprios dados ou siga os passos conforme descrito utilizando o conjunto de dados de veículo pt. Para carregar o conjunto de dados de veículo pt para a base de dados do SQL Server no local, siga o procedimento descrito [Em volume importar dados para a base de dados do SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas instruções são para uma Máquina Virtual no Azure SQL Server, mas o procedimento para a carregar para o SQL Server no local é a mesma.


##<a name="create-adf"></a>Criar uma fábrica dados Azure

As instruções para criar uma nova fábrica de dados do Azure e um grupo de recursos no [portal do Azure](https://portal.azure.com/) são fornecidas [criar uma fábrica de dados do Azure](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). O novo ADF instância *adfdsp* e um nome de grupo criado o recurso *adfdsprg*.


## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalar e configurar o Data Management Gateway

Para ativar a sua tubagens numa fábrica Azure dados para trabalhar com o SQL Server no local, tem de adicioná-lo como um serviço ligadas a fábrica de dados. Para criar um serviço ligada para um SQL Server no local, tem de:

- Transfira e instale o Microsoft Data Management Gateway sessão no computador no local. 
- Configure o serviço ligado para a origem de dados no local utilizar o gateway. 

O Data Management Gateway serializes e deserializes os dados de origem e sink no computador onde está alojado.

Para instruções de configuração da compilação e detalhes sobre a data Management Gateway, consulte [mover dados entre origens no local e na nuvem com o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)


## <a name="adflinkedservices"></a>Criar serviços ligados para ligar para os recursos de dados

Um serviço ligado define as informações necessárias para Azure fábrica de dados para se ligar a um recurso de dados. O procedimento passo a passo para criar serviços ligados é fornecido em [criar serviços ligados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services).

Temos três recursos neste cenário para a qual são necessários serviços ligados.

1. [Serviço ligadas para no local SQL Server](#adf-linked-service-onprem-sql)
2. [Serviço ligadas para o armazenamento de Blobs do Azure](#adf-linked-service-blob-store)
3. [Serviço ligadas para base de dados Azure SQL](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Serviço ligadas para base de dados do SQL Server no local

Para criar o serviço ligado para o SQL Server no local:

- Clique no **Arquivo de dados** na página de destino ADF no Portal clássica do Azure 
- Selecione **SQL** e introduza as credenciais de *nome de utilizador* e *palavra-passe* para o SQL Server no local. Tem de introduzir o nome do servidor como um **totalmente qualificado nome da instância nomedoservidor barra invertida (servername\instancename)**. Nome o serviço ligadas *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Serviço ligadas para Blob

Para criar o serviço ligado para a conta de armazenamento de Blobs do Azure:

- Clique no **Arquivo de dados** na página de destino ADF no Portal clássica do Azure
- Selecione a **Conta de armazenamento do Azure** 
- Introduza o nome de chave e contentor da conta de armazenamento de Blobs do Azure. Nome o serviço ligadas *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Serviço ligadas para base de dados Azure SQL

Para criar o serviço ligado para a base de dados do SQL Azure:

- Clique no **Arquivo de dados** na página de destino ADF no Portal clássica do Azure
- Selecione **Azure SQL** e introduza o *nome de utilizador* e *palavra-passe* as credenciais para a base de dados do SQL Azure. O *nome de utilizador* tem de ser especificado como *user@servername*.   


##<a name="adf-tables"></a>Definir e criar tabelas para especificar como aceder aos conjuntos de dados

Crie tabelas que especificam a estrutura, localização e disponibilidade dos conjuntos de dados com os seguintes procedimentos baseada em script. Ficheiros JSON são utilizados para definir as tabelas. Para mais informações sobre a estrutura destes ficheiros, consulte o artigo [conjuntos de dados](../data-factory/data-factory-create-datasets.md).

> [AZURE.NOTE]  Deve ser executado o `Add-AzureAccount` cmdlet antes de executar o cmdlet [AzureDataFactoryTable novo](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que a subscrição para a direita Azure está selecionada para a execução do comando. Para a documentação deste cmdlet, consulte o artigo [Adicionar AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

As definições de acesso baseado em JSON nas tabelas utilizam os nomes dos seguintes procedimentos:

* o **nome da tabela** no SQL server no local é *nyctaxi_data*
* o **nome do contentor** na conta de armazenamento de Blobs do Azure é *containername*  

Três definições da tabela são necessários para este pipeline ADF:

1. [Tabela do SQL no local](#adf-table-onprem-sql)
2. [Tabela de BLOBs](#adf-table-blob-store)
3. [SQL Azure tabela](#adf-table-azure-sql)

> [AZURE.NOTE]  Estes procedimentos utilizam Azure PowerShell para definir e criar as atividades ADF. Mas também podem ser feitas estas tarefas através do portal Azure. Para obter detalhes, consulte o artigo [Criar entrada e saída de conjuntos de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Tabela do SQL no local

A definição da tabela para o SQL Server no local é especificada no ficheiro JSON seguinte:

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Os nomes das colunas não foram incluídos aqui. Pode selecionar sub nos nomes das colunas, incluindo-las aqui (para o tópico de [documentação ADF](../data-factory/data-factory-data-movement-activities.md ) a verificação de detalhes.

Copie a definição de JSON da tabela para um ficheiro denominado *onpremtabledef.json* ficheiro e guarde-o para uma localização conhecida (aqui considerada *C:\temp\onpremtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Tabela de BLOBs
Definição para a tabela para a localização de Blobs do resultado é nas seguintes (mapas os dados aspirados no local para BLOBs do Azure):

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copie a definição de JSON da tabela para um ficheiro denominado *bloboutputtabledef.json* ficheiro e guarde-o para uma localização conhecida (aqui considerada *C:\temp\bloboutputtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure tabela
Definição para a tabela do SQL Azure de saída é o seguinte (este esquema mapas os dados provenientes do blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copie a definição de JSON da tabela para um ficheiro denominado *AzureSqlTable.json* ficheiro e guarde-o para uma localização conhecida (aqui considerada *C:\temp\AzureSqlTable.json*). Crie a tabela no ADF com o seguinte cmdlet do Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>Definir e criar as em curso

Especifique as atividades que pertencem à tubagem e criar pipeline de com os seguintes procedimentos baseada em script. Um ficheiro JSON é utilizado para definir as propriedades de tubagem.

* O script assume que **Gasoduto nome** é *AMLDSProcessPipeline*.
* Tenha em atenção que configurar estamos a periodicidade do pipeline de ser executadas diariamente e utilizar o tempo de execução predefinido para a tarefa (12: 00 UTC).

> [AZURE.NOTE]Os seguintes procedimentos utilizam Azure PowerShell para definir e criar pipeline de ADF. Mas também pode ser feita esta tarefa através do portal Azure. Para obter detalhes, consulte o artigo [criar e executar uma tubagem](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline).

Utilizar as definições de tabela fornecidas anteriormente, a definição de tubagem para o ADF é especificada da seguinte forma:

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copie esta definição JSON do pipeline de para um ficheiro denominado *pipelinedef.json* ficheiro e guarde-o para uma localização conhecida (aqui considerada *C:\temp\pipelinedef.json*). Crie a tubagem ADF com o seguinte cmdlet do Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Confirme que pode ver as em curso no ADF no Portal clássica do Azure aparecem como seguinte (quando clica em diagrama)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>Iniciar a tubagem
Agora pode ser executado pipeline de utilizando o seguinte comando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Os valores de parâmetro *DataDeInício* e *enddate* tem de ser substituído com as datas reais entre os quais pretende que a tubagem para executar.

Quando executa a tubagem, deverá poder ver os dados aparecem no contentor selecionado para o blob, um ficheiro por dia.

Tenha em atenção que recomendamos não utilizadas a funcionalidade fornecida pelo ADF aos dados de pipe incrementada. Para mais informações sobre como fazer isto e outras funcionalidades fornecidas pelo ADF, consulte a [documentação ADF](https://azure.microsoft.com/services/data-factory/).

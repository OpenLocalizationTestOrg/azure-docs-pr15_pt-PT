<properties 
    pageTitle="Mover os dados no local HDFS | Dados Azure fábrica" 
    description="Saiba mais sobre como mover dados de HDFS no local com o Azure fábrica de dados." 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover os dados a partir do HDFS no local com a fábrica de dados do Azure
Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados a partir de um HDFS no local para outro arquivo de dados. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

Atualmente, a fábrica de dados suporta apenas mover os dados a partir de um HDFS no local para noutros arquivos de dados, mas não para mover dados a partir de outros arquivos de dados para um HDFS no local.


## <a name="enabling-connectivity"></a>Activar a conectividade
Serviço de dados de fábrica do mesmo suporta a ligação ao HDFS no local com o Data Management Gateway. Consulte o artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. Utilize o gateway para ligar à HDFS, mesmo se estão alojados numa VM de IaaS Azure. 

Enquanto pode instalar o gateway no mesmo computador no local ou a VM Azure como o HDFS, recomendamos que instale o gateway num máquina/Azure separado IaaS VM. Está com dificuldades gateway num computador separada reduz contenção de recursos e melhora o desempenho. Quando instala o gateway num computador diferente, a máquina deverá conseguir aceder ao computador com o HDFS. 


## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de HDFS no local é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados a partir de um HDFS no local para um armazenamento de Blobs do Azure que sejam. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo: Copiar dados de HDFS no local para BLOBs do Azure

Este exemplo mostra como copiar dados a partir de um HDFS no local ao armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço do tipo [OnPremisesHdfs](#hdfs-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada de tipo de [partilha de ficheiros](#hdfs-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [FileSystemSource](#hdfs-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados a partir de um HDFS no local para uma BLOBs do Azure cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

Como primeiro passo, configure o data management gateway. As instruções no artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) . 

**HDFS ligadas serviço** Este exemplo utiliza a autenticação do Windows. Consulte a secção [HDFS ligadas serviço](#hdfs-linked-service-properties) para diferentes tipos de autenticação pode utilizar. 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }

**Serviço de armazenamento ligado do Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada HDFS** Este conjunto de dados refere-se para a pasta HDFS DataTransfer/UnitTest /. A tubagem copia todos os ficheiros nesta pasta para o destino. 

A definição "externos": "true" informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica dados.
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Gasoduto com atividade de cópia**

A tubagem contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **FileSystemSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Propriedades do serviço ligadas HDFS

A tabela seguinte fornece descrição para elementos JSON específicas HDFS ligadas serviço.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo tem de estar definida: **Hdfs** | Sim | 
| URL | URL para o HDFS | Sim |
| encryptedCredential | Resultado de [Novo AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) da credencial de acesso. | N |
| nome de utilizador | Autenticação do nome de utilizador para Windows. | Sim (para a autenticação do Windows)
| palavra-passe | Palavra-passe para autenticação do Windows. | Sim (para a autenticação do Windows)
| authenticationType | Windows, ou anónimos. | Sim |
| gatewayName | Nome do gateway que o serviço de dados fábrica devo utilizar para ligar para o HDFS. | Sim |   

Para obter detalhes sobre como definir credenciais para HDFS no local, consulte [definir as credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="using-anonymous-authentication"></a>Utilizando a autenticação anónima

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>Utilizando a autenticação do Windows
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>Propriedades do tipo de HDFS conjunto de dados

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties de conjunto de dados do tipo de **partilha de ficheiros** (que inclui o conjunto de dados HDFS) tem as seguintes propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
caminhopasta | Caminho para a pasta. Exemplo:`myfolder`<br/><br/>Utilizar o caráter de escape ' \ ' para carateres especiais na cadeia. Por exemplo: folder\subfolder, especifique pasta\\\\subpasta e d:\samplefolder, especifique d:\\\\pastadeexemplo.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter caminhos de pastas com base no setor tempos de data de início/fim. | Sim
nome de ficheiro | Especifique o nome do ficheiro na **caminhopasta** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar um valor para esta propriedade, a tabela que aponta para todos os ficheiros na pasta.<br/><br/>Quando não é especificado um nome de ficheiro de um conjunto de dados de saída, o nome do ficheiro gerado seria nas seguintes neste formato: <br/><br/>Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N
partitionedBy | partitionedBy pode ser utilizado para especificar uma caminhopasta dinâmica, nome de ficheiro para os dados da série de tempo. Exemplo: caminhopasta parametrizada para cada hora de dados. | N
fileFilter | Especificar um filtro para ser utilizado para selecionar um subconjunto de ficheiros na caminhopasta em vez de todos os ficheiros. <br/><br/>Permitido valores são: `*` (múltiplos carateres) e `?` (caráter individual).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/>**Nota**: fileFilter é aplicável para um conjunto de dados de partilha de ficheiros entrado | N
| Formatar | São suportados os seguintes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**e **ParquetFormat**. Defina a propriedade de **tipo** em Formatar para um destes valores. Consulte as secções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat), [Especificando OrcFormat](#specifying-orcformat)e [Especificando ParquetFormat](#specifying-parquetformat) para obter detalhes. Se pretender copiar os ficheiros como-é entre com base no ficheiro armazena (cópia binária), pode ignorar a secção formato em ambas as definições do conjunto de dados de entrada e saída. | N 
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip**, **Deflate**e **BZip2** e níveis de suportados são: **Optimal** e **mais rápida**. Atualmente, as definições de compressão não são suportadas para dados **AvroFormat** ou **OrcFormat**. Para mais informações, consulte a secção de [suporte de compressão](#compression-support) .  | N |



> [AZURE.NOTE] nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.


### <a name="using-partionedby-property"></a>Utilizar a propriedade partionedBy

Tal como mencionado na secção anterior, pode especificar uma caminhopasta dinâmica, nome de ficheiro de dados da série de tempo com partitionedBy. Pode fazê-lo com as macros de dados fábrica e a variável do sistema SliceStart, SliceEnd indicar o período de tempo lógicos para um setor dados indicada. 

Para saber mais sobre conjuntos de dados de série de tempo, agendamento e os setores do gráfico, consulte os artigos [Criar conjuntos de dados](data-factory-create-datasets.md), [agendamento & execução](data-factory-scheduling-and-execution.md)e [Tubagens criar](data-factory-create-pipelines.md) . 

#### <a name="sample-1"></a>Exemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Neste exemplo {setor} é substituído com o valor da variável do sistema SliceStart fábrica de dados no formato (YYYYMMDDHH) especificado. O SliceStart que se refere a hora de início no setor. A caminhopasta é diferente para cada setor. Por exemplo: wikisampledataout/wikidatagateway/2014100103 ou wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Neste exemplo, ano, mês, dia e hora da SliceStart são extraídos para variáveis separadas que são utilizadas pelo propriedades caminhopasta e nome de ficheiro.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Propriedades do tipo de atividade de cópia de HDFS

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Para copiar atividade, quando é de origem do tipo **FileSystemSource** as seguintes propriedades estão disponíveis na secção typeProperties:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| recursivas | Indica se os dados são lidos modo recursivo das pastas sub ou apenas a partir da pasta especificada. | VERDADEIRO, FALSO (predefinição)| N |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.


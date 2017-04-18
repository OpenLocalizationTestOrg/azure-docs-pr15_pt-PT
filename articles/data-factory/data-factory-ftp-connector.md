<properties 
    pageTitle="Mover os dados a partir do servidor de FTP | Microsoft Azure" 
    description="Saiba mais sobre como mover dados de um servidor FTP utilizando Azure fábrica de dados." 
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
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Mover os dados a partir de um servidor FTP utilizando a fábrica de dados do Azure
Este artigo descreve como utilizar a atividade de cópia no Azure dados fábrica para mover dados a partir de um servidor de FTP para um arquivo de dados suportados sink. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) que apresenta uma descrição geral de movimento de dados com cópia atividade e a lista de arquivos de dados suportadas como origens/sumidouros. 

Atualmente, a fábrica de dados suporta apenas mover os dados a partir de um servidor de FTP outros arquivos de dados, mas não para mover dados a partir de outros arquivos de dados para um servidor de FTP. Suporta ambas no local e na nuvem servidores FTP. 

Se está a mover dados de um servidor **no local** FTP para um arquivo de dados da nuvem (exemplo: armazenamento de Blobs do Azure), instalar e utilizar o Data Management Gateway. O Data Management Gateway é um agente do cliente que está instalado no seu computador no local, que permite que os serviços em nuvem ligar ao recurso no local. Consulte o artigo [Data Management Gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Consulte o artigo de [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway e utilizá-lo. Utilize o gateway para ligar a um servidor de FTP, mesmo se o servidor está numa máquina de virtual Azure IaaS (VM). 

Pode instalar o gateway no mesmo computador no local ou a VM de IaaS Azure como o servidor de FTP. No entanto, recomendamos que instale o gateway numa máquina separada ou uma IaaS VM separada do Azure para evitar contenção de recursos e para um melhor desempenho. Quando instala o gateway num computador diferente, a máquina deverá conseguir aceder ao servidor FTP. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de um servidor de FTP é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo: Copiar dados a partir do servidor de FTP para BLOBs do Azure

Este exemplo mostra como copiar dados a partir de um servidor de FTP para um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

- Um serviço do tipo [ServidorFTP](#ftp-linked-service-properties)ligado.
- Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
- Um [conjunto de dados](data-factory-create-datasets.md) entrada de tipo de [partilha de ficheiros](#fileshare-dataset-type-properties).
- Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [FileSystemSource](#ftp-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados a partir de um servidor de FTP para uma BLOBs do Azure cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

**FTP ligadas serviço** Este exemplo utiliza a autenticação básica com o nome de utilizador e palavra-passe em texto simples. Também pode utilizar uma das seguintes formas: 

- Autenticação anónima 
- Autenticação básica com credenciais encriptadas
- FTP através de SSL/TLS (FTPS)

Consulte a secção [FTP ligadas serviço](#ftp-linked-service-properties) para diferentes tipos de autenticação pode utilizar. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
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

**Conjunto de dados de entrada de FTP** Este conjunto de dados refere-se para a pasta FTP `mysharedfolder` e ficheiro `test.csv`. A tubagem copia o ficheiro para o destino. 

A definição "externos": "true" informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica dados.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **FileSystemSource** e **sink** tipo está definido como **BlobSink**. 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Propriedades do serviço ligadas FTP

A tabela seguinte fornece uma descrição para elementos JSON específicos de FTP serviço ligadas.

| Propriedade | Descrição | Obrigatório | Predefinido |
| -------- | ----------- | -------- | ------- | 
| tipo | A propriedade de tipo tem de estar definida para ServidorFTP | Sim | &nbsp;
| anfitrião | Nome ou endereço IP do servidor de FTP | Sim | &nbsp;
| authenticationType | Especificar o tipo de autenticação | Sim | Básica e anónima |
| nome de utilizador | Utilizador a quem tem acesso ao servidor de FTP | N | &nbsp;
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador) | N | &nbsp;
| encryptedCredential | Encriptada credenciais para aceder ao servidor FTP | N | &nbsp;
| gatewayName | Nome do gateway data Management Gateway para ligar a um servidor no local de FTP | N    | &nbsp;
| porta | Porta no qual o servidor de FTP está a escutar | N | 21 |
| enableSsl | Especifique se pretende utilizar FTP através de canal SSL/TLS | N | VERDADEIRO | 
| enableServerCertificateValidation | Especifique se pretende ativar a validação de certificado SSL do servidor quando utilizando FTP através de canal SSL/TLS | N | VERDADEIRO | 

### <a name="using-anonymous-authentication"></a>Utilizando a autenticação anónima

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Utilizar o nome de utilizador e palavra-passe em texto simples para autenticação básica
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Utilizar a porta, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Utilizar encryptedCredential para autenticação e de gateway

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Para obter detalhes sobre como definir credenciais para uma origem de dados FTP no local, consulte [definir as credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia de FTP

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, as propriedades tipo variam dependendo dos tipos de origens e sumidouros.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.

## <a name="next-steps"></a>Próximos passos
Consulte os artigos seguintes: 

- [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar uma tubagem com uma atividade de cópia. 

<properties 
    pageTitle="Mover os dados para uma base de dados do SQL Azure para Azure máquina aprendizagem | Azure" 
    description="Criar tabela SQL e carregar dados a tabela de SQL" 
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

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover os dados para uma base de dados do SQL Azure para Azure máquina aprendizagem

Este tópico descreve as opções para mover dados a partir de ficheiros simples (formatos TSV ou CSV) ou a partir de dados armazenados num servidor SQL no local para uma base de dados do Azure SQL. Estas tarefas para mover dados para a nuvem são parte do processo de ciências de dados de equipa.

Para um tópico que contorna as opções para mover dados para um SQL Server no local para aprendizagem máquina, consulte o artigo [mover dados do SQL Server num computador virtual Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

As ligações seguintes do **menu** para tópicos que descrevem como ingerir esta última dados para o destino ambientes onde os dados podem ser armazenados e processados durante o processo de ciência do equipa dados (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

A tabela seguinte resume as opções para mover dados para uma base de dados do SQL Azure.

<b>ORIGEM</b> |<b>DESTINO: Base de dados do Azure SQL</b> |
-------------- |--------------------------------|
<b>Ficheiro simples (CSV ou TSV formatado)</b> |<a href="#bulk-insert-sql-query">Consulta SQL de inserir em volume |
<b>SQL Server no local</b> | 1. <a href="#export-flat-file">exportar para ficheiro simples<br> 2. <a href="#insert-tables-bcp">Assistente de migração de base de dados SQL<br> 3. <a href="#db-migration">da base de dados back cópias de segurança e restaurar<br> 4. <a href="#adf">fábrica dados azure |


## <a name="prereqs"></a>Pré-requisitos
Os procedimentos descritos aqui pedir que tenha:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure**. Utilizar uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento Azure, consulte o artigo [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) . Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento de. Consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acesso numa **base de dados Azure SQL**. Se tem de configurar uma base de dados do SQL Azure, [Introdução com base de dados do Microsoft Azure SQL](../sql-database/sql-database-get-started.md) fornece informações sobre como pode aprovisionar uma nova instância de uma base de dados do SQL Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

**Dados**: os processos de migração são demonstrados utilizando o [conjunto de dados de veículo pt](http://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados de veículo pt contém informações sobre os dados de viagem e feiras e está disponível, conforme indicado essa mensagem, sobre o armazenamento de Blobs do Azure: [Pt veículo dados](http://www.andresmh.com/nyctaxitrips/). Um exemplo e a descrição destes ficheiros são fornecidas no campo Descrição [pt veículo viagens de conjunto de dados](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Pode adaptar os procedimentos descritos aqui para um conjunto de seus próprios dados ou siga os passos conforme descrito utilizando o conjunto de dados de veículo pt. Para carregar o conjunto de dados de veículo pt para a base de dados do SQL Server no local, siga o procedimento descrito [Em volume importar dados para a base de dados do SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas instruções são para uma Máquina Virtual no Azure SQL Server, mas o procedimento para a carregar para o SQL Server no local é a mesma.


## <a name="file-to-azure-sql-database"></a>Mover dados de uma origem de ficheiro simples para uma base de dados do Azure SQL

Dados de ficheiros simples (formatado TSV ou CSV) podem ser movidos para uma base de dados do Azure SQL utilizando uma consulta de SQL de inserir em volume.

### <a name="bulk-insert-sql-query"></a>Consulta SQL de inserir em volume

Os passos para o procedimento utilizando a consulta de SQL de inserir em volume são semelhantes das abrangidos nas secções para mover dados de uma origem de ficheiro simples para SQL Server numa VM Azure. Para obter detalhes, consulte o artigo [Consulta SQL de inserir em volume](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Mover dados do SQL Server no local para uma base de dados do Azure SQL

Se a origem de dados estiver armazenada num SQL Server no local, existem várias possibilidades para mover os dados para uma base de dados do Azure SQL:

1. [Exportar para ficheiro simples](#export-flat-file) 
2. [Assistente de migração de base de dados SQL](#insert-tables-bcp)
3. [Base de dados back cópias de segurança e restaurar](#db-migration)
4. [Dados Azure fábrica](#adf)

Os passos para os três primeiros são muito semelhantes a essas secções nos [dados de mover para o SQL Server num computador virtual Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) que abrangem estes mesmos procedimentos. Ligações para as secções adequadas nesse tópico são fornecidas as seguintes instruções.

###<a name="export-flat-file"></a>Exportar para ficheiro simples

Os passos para este exportar para um ficheiro simples são semelhantes das constantes [Exportar para ficheiro simples](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Assistente de migração de base de dados SQL

Os passos para utilizar o Assistente de migração de base de dados do SQL são semelhantes das abrangidos no [Assistente de migração de base de dados SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Base de dados back cópias de segurança e restaurar

Os passos para utilizar a base de dados cópias de segurança e restauro são semelhantes das abrangidos numa [base de dados fazer cópia de segurança e restaurar](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Dados Azure fábrica

O procedimento para mover dados para uma base de dados do Azure SQL com Azure dados fábrica (ADF) é fornecido no tópico de [mover os dados de um servidor SQL no local para SQL Azure com Azure fábrica de dados](machine-learning-data-science-move-sql-azure-adf.md). Este tópico mostra como mover os dados a partir de uma base de dados do SQL Server no local para uma base de dados do Azure SQL através do armazenamento de Blobs do Azure utilizando ADF. 

Considere utilizar ADF quando necessitam de dados automática e continuamente migradas num cenário de híbrido que acede aos recursos no local e na nuvem e, quando os dados são efectuados ou precisa de ser modificadas ou ter adicionada ao mesmo quando a ser migrada a lógica de negócio. ADF permite o agendamento e monitorização de tarefas utilizando scripts JSON simples que gerir o movimento dos dados numa base periódica. ADF também tem outros capacidades, tais como suporte para operações complexas.





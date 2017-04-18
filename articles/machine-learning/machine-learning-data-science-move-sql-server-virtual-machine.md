<properties 
    pageTitle="Mova os dados para o SQL Server numa máquina virtual Azure | Azure" 
    description="Deslocar-se dados a partir de ficheiros simples ou a partir de um SQL Server no local para o SQL Server Azure VM." 
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

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mova os dados para o SQL Server numa máquina virtual Azure

Este tópico descreve as opções para mover dados a partir de ficheiros simples (formatos TSV ou CSV) ou do SQL Server no local para o SQL Server num computador virtual Azure. Estas tarefas para mover dados para a nuvem são parte do processo de ciências de dados de equipa.

Para um tópico que contorna as opções para mover dados para uma base de dados do SQL Azure para aprendizagem máquina, consulte o artigo [mover os dados para uma base de dados do SQL Azure para Azure máquina aprendizagem](machine-learning-data-science-move-sql-azure.md).

No **menu** abaixo ligações para tópicos que descrevem como ingerir esta última dados para outros ambientes de destino, onde os dados podem ser armazenados e processados durante o processo de ciência do equipa dados (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


A tabela seguinte resume as opções para mover dados para o SQL Server num computador virtual Azure.

<b>ORIGEM</b> |<b>DESTINO: SQL Server no Azure VM</b> |
------------------ |-------------------- |
<b>Ficheiro simples</b> |1. <a href="#insert-tables-bcp">utilitário de cópia da linha de comandos em volume (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">em volume a consulta SQL de inserir</a><br> 3. <a href="#sql-builtin-utilities">gráficos utilitários incorporados no SQL Server</a>
<b>No local SQL Server</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Implementar uma base de dados do SQL Server para um assistente do Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">Exportar para um ficheiro simples</a><br> 3. <a href="#sql-migration">Assistente de migração de base de dados SQL</a> <br> 4. <a href="#sql-backup">da base de dados back cópias de segurança e restaurar</a><br>

Tenha em atenção que este documento assume que comandos SQL são executados a partir do SQL Server Management Studio ou no Explorador de base de dados do Visual Studio.

> [AZURE.TIP] Como alternativa, pode utilizar [Azure fábrica de dados](https://azure.microsoft.com/services/data-factory/) para criar e agendar uma tubagem que se irá mover dados para uma VM do SQL Server no Azure. Para mais informações, consulte o artigo [copiar dados com o Azure dados fábrica (actividade de cópia)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Pré-requisitos
Neste tutorial assume que tem:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure**. Irá utilizar uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento Azure, consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) . Depois de ter criado a conta de armazenamento, terá de obter a chave de conta utilizada para aceder ao armazenamento de. Consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Aprovisionada **do SQL Server numa VM Azure**. Para obter instruções, consulte o artigo [Configurar uma máquina de virtual Azure SQL Server como um servidor de IPython bloco de notas para análise avançadas](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Mover dados de uma origem de ficheiro simples para o SQL Server uma VM Azure

Se os dados estiverem num ficheiro simples (disposto num formato de linha/coluna), pode ser movida para SQL Server VM no Azure através dos seguintes métodos:

1. [Utilitário de cópia da linha de comandos em volume (BCP)](#insert-tables-bcp) 
2. [Consulta SQL de inserir em volume](#insert-tables-bulkquery)
3. [Gráficos utilitários incorporados no SQL Server (importar/exportar, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilitário de cópia da linha de comandos em volume (BCP)

BCP é um utilitário da linha de comandos instalado com o SQL Server e uma das formas mais rápidas para mover os dados. Funciona em todas as três variantes de SQL Server (no local SQL Server, SQL Azure e SQL Server VM no Azure). 

> [AZURE.NOTE]**Onde os meus dados deverá para BCP?**  
> Enquanto não for necessário, ter ficheiros que contém os dados de origem localizados no mesmo computador que o SQL server destino permite para transferências mais rápidas (rede velocidade vs. no disco local velocidade es). Pode mover os ficheiros simples que contêm dados para o computador onde o SQL Server está instalado com ficheiro várias ferramentas, como o [AZCopy](../storage/storage-use-azcopy.md)a copiar, [Explorador de armazenamento do Azure](http://storageexplorer.com/) ou windows copiar/colar através do protocolo de ambiente de trabalho remoto (RDP).

1. Certifique-se de que a base de dados e as tabelas são criadas da base de dados do SQL Server de destino. Eis um exemplo de como o fazer essa utilizando o `Create Database` e `Create Table` comandos:

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Gera o ficheiro de formato que descreve o esquema para a tabela ao emitir o comando seguinte a partir da linha de comandos do computador onde está instalado bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Insira os dados na base de dados utilizando o comando bcp da seguinte forma. Isto deve trabalhar a partir da linha de comandos partindo do princípio de que o SQL Server estiver instalado no mesmo computador:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Otimizar os BCP insere** Consulte o seguinte artigo ['Diretrizes para otimizar os importação em volume'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) para otimizar o essas insere.


### <a name="insert-tables-bulkquery-parallel"></a>Parallelizing insere para movimento de dados mais rápido

Se os dados que está a mover forem grandes, pode acelerar coisas através da execução em simultâneo vários comandos BCP em paralelo num PowerShell Script.

> [AZURE.NOTE]**Dados grandes ingestão** Para otimizar o carregamento de conjuntos de dados grandes e muito grandes de dados, partição suas tabelas de base de dados lógica e física utilizar várias tabelas os grupos de ficheiros e partição. Para mais informações sobre como criar e carregar os dados para tabelas de partições, consulte o artigo [Paralelo de duas tabelas de partição de carga SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


O script do PowerShell exemplo abaixo demonstrar, paralelas insere utilizando bcp:
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserir em volume

[Consulta SQL de inserir em volume](https://msdn.microsoft.com/library/ms188365) podem ser utilizados para importar dados para a base de dados a partir de ficheiros de linha/coluna com base (tipos suportados abrange os[Preparar dados para em volume exportar ou importar (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tópico. 

Aqui estão alguns comandos de exemplo para inserir em volume são como abaixo:  

1. Analisar os seus dados e defina as opções personalizadas antes de importar para se certificar de que a base de dados do SQL Server assume o mesmo formato para quaisquer campos especiais, tais como as datas. Eis um exemplo de como definir o formato de data como dia do mês do ano (se os seus dados contêm a data no formato dia do mês do ano):

        SET DATEFORMAT ymd; 
    
2. Importe dados utilizando declarações de importação em volume:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Utilitários incorporados no SQL Server

Pode utilizar o SQL Server integrações Services (SSIS) para importar dados para o SQL Server VM no Azure a partir de um ficheiro simples. SSIS está disponível nos dois ambientes de studio. Para obter detalhes, consulte o artigo [Integration Services (SSIS) e Studio ambientes](https://technet.microsoft.com/library/ms140028.aspx):

- Para obter detalhes sobre as ferramentas de dados do SQL Server, consulte o artigo [Ferramentas de dados do Microsoft SQL Server](https://msdn.microsoft.com/data/tools.aspx)  
- Para obter detalhes sobre o Assistente de importação/exportação, consulte o artigo [Assistente SQL Server importação e exportação](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Mover dados do SQL Server no local para o SQL Server uma VM Azure

Também pode utilizar as seguintes estratégias de migração:

1. [Implementar uma base de dados do SQL Server para um assistente do Microsoft Azure VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar para ficheiro simples](#export-flat-file) 
3. [Assistente de migração de base de dados SQL](#sql-migration)
4. [Base de dados back cópias de segurança e restaurar](#sql-backup)

Descrevemos cada um dos seguintes procedimentos abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implementar uma base de dados do SQL Server para um assistente do Microsoft Azure VM

A **Implementar uma base de dados do SQL Server para um assistente do Microsoft Azure VM** é uma forma simple e recomendada para mover os dados a partir de uma instância do SQL Server no local para o SQL Server numa VM Azure. Para obter passos detalhados, bem como um debate de outras alternativas, consulte o artigo [migrar uma base de dados para o SQL Server numa VM Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportar para ficheiro simples

Vários métodos podem ser utilizados para em volume exportar dados do SQL Server no local, conforme é documentado no tópico [em volume de importação e exportação de dados (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Este documento vai abordar o programa de cópia em volume (BCP) como um exemplo. Depois de exportados dados para um ficheiro simples, podem ser importado para outro SQL server utilizando a importação em volume. 

1. Exportar os dados do SQL Server no local para um ficheiro com o utilitário bcp da seguinte forma

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Criar a base de dados e a tabela no SQL Server VM sobre a utilização do Azure a `create database` e `create table` para o esquema da tabela exportados no passo 1.

3. Crie um ficheiro de formato para descrever o esquema da tabela de dados a ser exportados/importados. Obter detalhes sobre o ficheiro de formato é descritos no [artigo criar um ficheiro de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatar a geração ficheiro quando a ser executado BCP a partir do computador do SQL Server 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Formatar a geração ficheiro quando a ser executado BCP remotamente contra SQL Server 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Utilize qualquer um dos métodos descritos na secção [Mover dados a partir de ficheiro de origem](#filesource_to_sqlonazurevm) para mover os dados nos ficheiros simples para SQL Server.

### <a name="sql-migration"></a>Assistente de migração de base de dados SQL

[Assistente de migração de base de dados do SQL Server](http://sqlazuremw.codeplex.com/) fornece uma forma fácil de utilizar para mover dados entre duas instâncias do SQL server. -Permite ao utilizador mapear o esquema de dados entre origens e tabelas de destino, selecione tipos de coluna e várias outras funcionalidades. Utiliza a cópia de em volume (BCP) nas folhas de rosto. Uma captura de ecrã do ecrã de boas-vindas para o Assistente de migração de base de dados do SQL é apresentada abaixo.  

![Assistente de migração do SQL Server][2]

### <a name="sql-backup"></a>Base de dados back cópias de segurança e restaurar

SQL Server suporta: 

1. [Base de dados back para cima e restaure a funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (ambos para um ficheiro local ou bacpac exportar para um blob) e [As aplicações de camadas de dados](https://msdn.microsoft.com/library/ee210546.aspx) (utilizando bacpac). 
2. Capacidade de criar diretamente VMs do SQL Server no Azure com uma base de dados copiado ou copiar para uma base de dados do SQL Azure existente. Para obter mais detalhes, consulte o artigo [utilizar o Assistente de base de dados de cópia](https://msdn.microsoft.com/library/ms188664.aspx). 

Captura de ecrã da parte detrás da base de dados para cima/restaurar opções a partir do SQL Server Management Studio é apresentado abaixo.

![Ferramenta de importação do SQL Server][1]

## <a name="resources"></a>Recursos

[Migrar uma base de dados para o SQL Server numa VM Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server em máquinas virtuais do Azure panorâmica](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

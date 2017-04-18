<properties
    pageTitle="Tutorial de fábrica do mesmo de dados: primeiro pipeline de dados | Microsoft Azure"
    description="Neste tutorial de fábrica do Azure dados mostra-lhe como criar e agendar uma fábrica de dados que processa dados através de script de ramo num Hadoop cluster."
    services="data-factory"
    keywords="dados Azure fábrica tutorial, hadoop cluster, hadoop ramo"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>Tutorial: Criar o seu pipeline primeiro aos dados de processo utilizando Hadoop cluster 
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo de Gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste tutorial, construa a fábrica de dados Azure primeira com uma tubagem de dados que processa dados ao executar o script ramo num cluster Azure HDInsight (Hadoop). 

> [AZURE.NOTE] Este artigo não fornece uma descrição geral conceptual da fábrica de dados do Azure. Para obter uma descrição geral conceptual do serviço, consulte o artigo [Introdução ao Azure fábrica de dados](data-factory-introduction.md). Consulte o [caminho de formação: fábrica de dados](https://azure.microsoft.com/documentation/learning-paths/data-factory/) para uma forma recomendada para navegar pelos nosso conteúdo para saber mais sobre a fábrica de dados.

## <a name="whats-covered-in-this-tutorial"></a>O que está incluído neste tutorial? 
**Azure dados fábrica** permite-lhe redigir de dados de **movimento** e tarefas de **processamento** de dados como fluxos de trabalho condicionados por dados (também denominados dados tubagens). Saiba como construir a sua primeira atividade de tubagem com uma transformação de dados (ou transformação de dados) de dados. Esta actividade utiliza um cluster de HDInsight Hadoop para transformar e analisar os registos de web de exemplo.  

Neste tutorial, execute os seguintes passos:

1.  Crie uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais dados tubagens esses dados mover e processo. 
2.  Crie **Serviços ligados**. Criar um serviço ligado para ligar um arquivo de dados ou de um serviço de cluster para a fábrica de dados. Um arquivo de dados como o armazenamento do Windows Azure contém os dados de entrada/saída de atividades no pipeline de. Um serviço de cluster como HDInsight Hadoop cluster processos/transformações dados.    
3.  Criar entrada e saída de **conjuntos de dados**. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline de e um conjunto de dados de saída representa a exportação para a atividade.
3.  Crie a **em curso**. Uma tubagem pode ter uma ou mais actividades (exemplos: copiar atividade, HDInsight Hive atividade). Este exemplo utiliza a atividade de HDInsight ramo que executa um script ramo num cluster HDInsight Hadoop. O script pela primeira vez cria uma tabela que referencia os dados do registo web não processado armazenados no armazenamento de Blobs do Azure e, em seguida, partições os dados não processados por ano e mês.

    Existem dois tipos de atividades suportados pelo Azure fábrica de dados. São: [atividades de movimento de dados](data-factory-data-movement-activities.md) e [as atividades de transformação de dados](data-factory-data-transformation-activities.md). Existe apenas uma dados movimento actividade, que é a atividade de cópia. Neste tutorial, não é utilizar a atividade de cópia. Para obter um tutorial sobre como utilizar a atividade de cópia, consulte o artigo [Tutorial: copiar dados a partir de um Azure blob para Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Ramo de HDInsight atividade que utilizar neste tutorial é um das atividades de transformação de dados suportadas pelo fábrica de dados.  
 
Eis a **vista de diagrama** da fábrica de dados de exemplo que construir neste tutorial. 

![Vista de diagrama do tutorial fábrica de dados](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

Neste tutorial, a pasta de **inputdata** do contentor de Blobs do Azure **adfgetstarted** contém um ficheiro com o nome input.log. Este ficheiro de registo tem entradas dos três meses: Janeiro, Fevereiro e Março de 2016. Seguem-se as linhas de exemplo para cada mês no ficheiro de entrada. 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Quando o ficheiro é processado por canalização com HDInsight Hive atividade, a atividade é executado um script ramo no HDInsight cluster se a partições introduzidos dados por ano e mês. O script cria três pastas de saída que contêm um ficheiro com entradas de cada mês.  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

A partir de linhas de exemplo apresentadas acima, a primeira parte (com 2016-01-01) destina-se para o ficheiro 000000_0 no mês = 1 pasta. Da mesma forma, a segunda opção destina-se para o ficheiro no mês = 2 pasta e o terceiro um destina-se para o ficheiro no mês = pasta 3.  


## <a name="pre-requisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os pré-requisitos seguintes:

1.  **Subscrição do azure** - se não tiver uma subscrição do Azure, pode criar uma conta de avaliação gratuita apenas de duas minutos. Consulte o artigo de [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre como pode obter uma conta de avaliação gratuita.

2.  **Armazenamento do Windows azure** – utilizar uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento Azure, consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) . Depois de ter criado a conta de armazenamento, anote o **nome da conta** e a **chave de acesso**. Consulte o artigo [Ver, copiar e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys). 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>Carregar ficheiros para o armazenamento do Windows Azure para o tutorial
Antes de iniciar o tutorial, tem de preparar a sua conta de armazenamento do Windows Azure com ficheiros de exemplo para o tutorial.

1. Carregue o ficheiro de consulta de ramo (HQL) para a pasta de **scripts** do contentor de BLOBs **adfgetstarted** .
2. Carregar ficheiro de entrada para a pasta **inputdata** do contentor de BLOBs **adfgetstarted** . 

#### <a name="create-hql-script-file"></a>Criar o ficheiro de script HQL 

1. Inicie o **Bloco de notas** e cole o seguinte script HQL. Este script ramo cria duas tabelas: **WebLogsRaw** e **WebLogsPartitioned**. Clique em **ficheiro** , no menu e selecione **Guardar como**. Mudar para a pasta **C:\adfgetstarted** no seu disco rígido. Selecione * *todos os ficheiros (*.*) **para o** Guardar como escreva** campo. Introduza **partitionweblogs.hql** para o ****o nome do ficheiro. Confirme que o **codificação de** campo na parte inferior da caixa de diálogo está definido para **ANSI**. Caso não esteja, configurá-lo a **ANSI * *.  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

O tempo de execução, a atividade Hive no pipeline de dados fábrica transmite valores o **inputtable** e parâmetros **partitionedtable** conforme mostrado no seguinte fragmento:  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

O **storageaccountname** é o nome da sua conta de armazenamento Azure.
 
#### <a name="create-a-sample-input-file"></a>Criar um ficheiro de entrada de exemplo
Utilizar o bloco de notas, crie um ficheiro denominado **input.log** na **c:\adfgetstarted** com o seguinte conteúdo: 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>Carregar ficheiro de entrada e ficheiro HQL para o seu armazenamento de Blobs do Azure

Esta secção fornece instruções sobre como utilizar a ferramenta de **AzCopy** para copiar ficheiros input.log e partitionweblogs.hql ao armazenamento de Blobs do Azure. Pode utilizar qualquer ferramenta da sua escolha (por exemplo: [Microsoft Azure armazenamento Explorer](http://storageexplorer.com/), [CloudXPlorer por ClumsyLeaf Software](http://clumsyleaf.com/products/cloudxplorer) para executar esta tarefa.   
     
1. Transfira a [versão mais recente do **AzCopy**](http://aka.ms/downloadazcopy)ou a [versão mais recente de pré-visualização](http://aka.ms/downloadazcopypr). Consulte o artigo [como utilizar AzCopy](../storage/storage-use-azcopy.md) artigo para obter instruções sobre como utilizar o utilitário.
2. Navegue para a pasta c:\adfgetstarted e execute o seguinte comando: 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    Este comando carrega o ficheiro **input.log** para a conta de armazenamento (**adfgetstarted** contentor e **inputdata** pasta). Substitua **storageaccountname** com o nome da sua conta de armazenamento e **storageaccesskey** a tecla de acesso de armazenamento.

    > [AZURE.NOTE] Este comando cria um contentor denominado **adfgetstarted** no seu armazenamento de Blobs do Azure e copia o ficheiro de **input.log** da sua unidade local para a pasta **inputdata** no contentor. 
    
3. Depois do ficheiro foi carregado com êxito, pode ver o resultado semelhante ao seguinte a partir do AzCopy.
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. Execute o seguinte comando para carregar o ficheiro **partitionweblogs.hql** para a pasta de **script** do contentor **adfgetstarted** . Eis o comando: 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

Concluiu os pré-requisitos. Pode criar uma fábrica de dados utilizando uma das seguintes formas. Clique dos separadores na parte superior ou as ligações seguintes para executar o tutorial. 

- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo de Gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
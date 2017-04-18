<properties
    pageTitle="Utilizar Hadoop Sqoop no HDInsight | Microsoft Azure"
    description="Saiba como utilizar HDInsight .NET SDK para executar Sqoop importar e exportar entre um cluster de Hadoop e uma base de dados do Azure SQL."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="09/14/2016"
    ms.author="jgao"/>

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Executar tarefas Sqoop com .NET SDK para Hadoop no HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar HDInsight .NET SDK para executar tarefas Sqoop no HDInsight para importar e exportar entre HDInsight cluster e base de dados Azure SQL ou base de dados do SQL Server.

> [AZURE.NOTE] Os passos neste artigo podem ser utilizados com um de um baseado no Windows ou Linux HDInsight cluster; No entanto, estes passos funcionam apenas a partir de um cliente do Windows. Utilize o Seletor de tabulação na parte superior deste artigo para escolher outros métodos.

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Cluster de respostas Hadoop em HDInsight**. Consulte o artigo [Criar cluster e base de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Executar Sqoop utilizando .NET SDK

O HDInsight .NET SDK fornece bibliotecas do cliente .NET, que facilita a trabalhar com clusters HDInsight a partir do .NET. Nesta secção, irá criar uma aplicação de consola c# para exportar o hivesampletable para a tabela de base de dados SQL que criou anteriormente neste tutoriais.

**Para submeter uma tarefa Sqoop**

1. Crie uma aplicação de consola do c# no Visual Studio.
2. A partir da consola de Gestor de pacotes do Visual Studio, execute o seguinte comando Nuget para importar o pacote.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Utilize o seguinte código no ficheiro Program.cs:

        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
        
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Prima **F5** para executar o programa. 

##<a name="limitations"></a>Limitações

* Em volume export - baseado em com Linux HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou base de dados do SQL Azure atualmente não suporta insere em volume.

* De lotes - com baseado em Linux HDInsight, ao utilizar o `-batch` mudar quando efetuar inserções, Sqoop irá efetuar várias insere em vez de lotes as operações de inserir.

##<a name="next-steps"></a>Próximos passos

Agora que tem aprendeu como utilizar Sqoop. Para obter mais informações, consulte o artigo:

- [Utilizar Oozie com HDInsight](hdinsight-use-oozie.md): utilizar Sqoop ação num fluxo de trabalho Oozie.
- [Analisar os dados de atrasos em voos utilizando HDInsight](hdinsight-analyze-flight-delay-data.md): utilizar Hive para analisar voo atrasar dados e, em seguida, utilize Sqoop para exportar dados para uma base de dados do Azure SQL.
- [Transferir dados do HDInsight](hdinsight-upload-data.md): encontrar outros métodos para carregar os dados ao armazenamento Blob HDInsight/Azure.



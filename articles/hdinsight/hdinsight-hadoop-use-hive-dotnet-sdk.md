<properties
    pageTitle="Executar consultas de ramo utilizando HDInsight .NET SDK | Microsoft Azure"
    description="Saiba como submeter tarefas de Hadoop para Azure HDInsight Hadoop utilizando HDInsight .NET SDK."
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

# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Executar consultas de ramo utilizando HDInsight .NET SDK

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Saiba como submeter ramo consultas utilizando HDInsight .NET SDK.

> [AZURE.NOTE] Os passos neste artigo tem de ser efetuados a partir de um cliente do Windows. Para obter informações sobre como utilizar um cliente de Unix, OS X ou Linux para trabalhar com ramo, utilize o Seletor de tabulação apresentado na parte superior o artigo.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Cluster de respostas Hadoop em HDInsight**. Consulte o artigo [começar a utilizar com base em Linux Hadoop no HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Submeter ramo consultas utilizando HDInsight .NET SDK

O HDInsight .NET SDK fornece bibliotecas do cliente .NET, que facilita a trabalhar com clusters HDInsight a partir do .NET. 

**Para submeter tarefas**

1. Crie uma aplicação de consola do c# no Visual Studio.
2. A partir da consola do Gestor de pacotes Nuget, execute o seguinte comando.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Utilize o seguinte código:

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
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

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Prima **F5** para executar a aplicação.


## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu várias formas para criar um cluster de HDInsight. Para saber mais, consulte os artigos seguintes:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Criar Hadoop clusters no HDInsight][hdinsight-provision]
* [Gerir clusters de Hadoop HDInsight utilizando o Portal do Azure](hdinsight-administer-use-management-portal.md)
* [Referência de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
* [Utilizar Sqoop com HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Criar autenticação não interativa .NET HDInsight aplicações](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md



<properties
   pageTitle="Utilizar Hadoop porco com .NET no HDInsight | Microsoft Azure"
   description="Saiba como utilizar o SDK .NET para Hadoop para submeter as tarefas de porco para Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Executar tarefas porco com o .NET SDK para Hadoop no HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este artigo fornece um exemplo de como utilizar o SDK .NET para Hadoop para submeter porco tarefas para um Hadoop num cluster de HDInsight.

O HDInsight .NET SDK fornece bibliotecas do cliente .NET que o torna mais fácil de trabalhar com clusters HDInsight a partir do .NET. Porco permite-lhe criar MapReduce operações através da modelação uma série de transformações de dados. Vai aprender a utilizar uma aplicação do c# básica para submeter uma tarefa porco para um cluster de HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte.

* Um cluster de Azure HDInsight (Hadoop no HDInsight) (o Windows ou Linux-based).
* Visual Studio 2012http 2013 ou o 2015.

## <a name="create-the-application"></a>Criar a aplicação

O HDInsight .NET SDK fornece bibliotecas do cliente .NET, que facilita a trabalhar com clusters HDInsight a partir do .NET. 


1. Abrir o Visual Studio 2012http ou 2013
2. A partir do menu **ficheiro** , selecione **Novo** e, em seguida, selecione o **projeto**.
3. Para o novo projeto, escreva ou selecione os seguintes valores.

    <table>
    <tr>
    <th>Propriedade</th>
    <th>Valor</th>
    </tr>
    <tr>
    <th>Categoria</th>
    <th>Modelos/Visual c# / Windows</th>
    </tr>
    <tr>
    <th>Modelo</th>
    <th>Aplicação de consola</th>
    </tr>
    <tr>
    <th>Nome</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Clique em **OK** para criar o projeto.
5. No menu **Ferramentas** , selecione **Gestor de pacote de biblioteca** ou **Gestor de pacote de Nuget**e, em seguida, selecione **Consola do Gestor de pacote**.
6. Execute o seguinte comando na consola para instalar os pacotes de .NET SDK.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. A partir do Explorador de solução, faça duplo clique **Program.cs** para o abrir. Substitua o código existente com o seguinte.

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. Prima **F5** para iniciar a aplicação.
8. Prima **ENTER** para sair da aplicação.

## <a name="summary"></a>Resumo

Como pode ver, o SDK .NET para Hadoop permite-lhe criar aplicações de .NET submeter porco tarefas a um cluster de HDInsight e monitorizar o estado da tarefa.

## <a name="next-steps"></a>Próximos passos

Para obter informações gerais sobre porco no HDInsight.

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight.

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md) [portal de pré-visualização]: https://portal.azure.com/

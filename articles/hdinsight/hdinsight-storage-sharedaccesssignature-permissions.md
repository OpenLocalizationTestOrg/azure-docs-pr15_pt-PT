<properties
pageTitle="Restringir o acesso de HDInsight a dados utilizar assinaturas de acesso partilhado"
description="Saiba como utilizar assinaturas de acesso partilhado para restringir o acesso de HDInsight a dados armazenados num blobs do Azure armazenamento."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Utilizar assinaturas de acesso partilhadas do Azure armazenamento para restringir o acesso a dados com HDInsight

HDInsight utiliza Azure armazenamento Blobs do armazenamento de dados. Enquanto o HDInsight tem de ter acesso total a blob utilizado como armazenamento predefinida para o cluster, pode restringir a permissões para dados armazenados num outras blobs utilizadas pelo cluster. Por exemplo, poderá pretende tornar alguns dados só de leitura. Pode fazê-lo utilizar assinaturas de acesso partilhado.

Partilhada acesso assinaturas (SA) são uma funcionalidade de contas de armazenamento Azure que permite-lhe limitar o acesso aos dados. Por exemplo, fornecer acesso só de leitura aos dados. Neste documento, irá obter informações sobre como utilizar associações de segurança para ativar o acesso de lista só de leitura e a um contentor de Blobs do HDInsight.

##<a name="requirements"></a>Requisitos de

* Uma subscrição do Azure

* C# ou Python. Código de exemplo c# é fornecido como uma solução do Visual Studio.

    * Visual Studio tem de ser versão 2013 ou 2015
    
    * Python tem de ser versão 2.7 ou superior

* Um HDInsight baseado em Linux cluster ou [Azure PowerShell] [ powershell] -se tiver um cluster de baseado em Linux existente, pode utilizar Ambari para adicionar uma assinatura de acesso partilhado ao cluster. Caso contrário, pode utilizar o Azure PowerShell para criar um novo cluster e adicionar uma assinatura de acesso partilhado durante a criação de cluster.

* Os ficheiros de exemplo a partir do [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositório tem o seguinte procedimento:

    * Um projeto do Visual Studio que pode criar um contentor de armazenamento, política armazenada e SA para utilização com o HDInsight
    
    * Um script de Python que pode criar um contentor de armazenamento, política armazenada e SA para utilização com o HDInsight
    
    * Um script PowerShell que pode criar um novo cluster de HDInsight e configure-o para utilizar as associações de segurança.

##<a name="shared-access-signatures"></a>Acesso partilhado assinaturas

Existem duas formas de assinaturas de acesso partilhada:

* Ad hoc: A hora de início, hora de expiração e permissões para as associações de segurança são todas as especificado no URI SA (ou implícitas, no caso onde for omitida hora de início).

* A política de acesso armazenada: uma política de acesso armazenado é definida num contentor recurso - um contentor de BLOBs, tabela, fila ou partilha de ficheiros - e podem ser utilizada para gerir restrições para uma ou mais assinaturas acesso partilhado. Ao associar uma SA uma política de acesso armazenado, as associações de segurança herda as restrições - a hora de início, hora de expiração e permissões - definidas para a política de acesso armazenado.

A diferença entre as duas formas é importante para um cenário de chave: revogação. Uma SA são um URL, para que qualquer pessoa que obtém as SA pode utilizá-la, independentemente de quem solicitou para começar. Se um SA for publicada publicamente, pode ser utilizado por qualquer pessoa no mundo. Uma SA que são distribuída são válida até uma das quatro coisas situações:

1. O tempo de expiração especificado nas associações de segurança é atingido.

2. O tempo de expiração especificado na política de acesso armazenado referenciada pelas associações de segurança é atingido (se uma política de acesso armazenado é referenciada, e se especifica um tempo de expiração). Quer Isto pode ocorrer porque o intervalo de decorrida ou porque que tenha modificado a política de acesso armazenados para ter um tempo de expiração no passado, que é uma forma de revogar as associações de segurança.

3. A política de acesso armazenado referenciada pelas associações de segurança é eliminada, o que é outra forma de revogar as associações de segurança. Tenha em atenção que se recriar a política de acesso armazenados com exatamente o mesmo nome, todos os tokens SA existentes novamente será válidos de acordo com as permissões associadas essa política de acesso armazenado (partindo do princípio de que o tempo de validade as associações de segurança não passou). Se são planeia revogar as associações de segurança, certifique-se de que utilize um nome diferente se está a criar a política de acesso com um tempo de expiração no futuro.

4. A chave de conta que foi utilizada para criar as associações de segurança é geradas novamente. Tenha em atenção que este procedimento causará todos os componentes de aplicação utilizar esse chave da conta a falha autenticar até que as mesmas são atualizadas para utilizar a outros chave da conta válido ou a chave de conta recuperada recentemente.

> [AZURE.IMPORTANT] Uma assinatura de acesso partilhado URI está associada a chave de conta utilizada para criar a assinatura e, a associados armazenados política de acesso (se existirem). Se não for especificada nenhum política de acesso armazenados, é a única forma de revogar uma assinatura de acesso partilhado alterar a chave de conta. 

É recomendável que utilize sempre as políticas de acesso armazenados, para que possa revogar assinaturas ou prolongar a data de expiração, conforme necessário. Este documento, utilize os passos armazenados políticas de acesso para gerar SA.

Para mais informações sobre assinaturas de acesso partilhadas, consulte o artigo [Compreender o modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Criar uma política armazenada e gerar uma SA

Atualmente tem de criar uma política armazenada através de programação. Pode encontrar c# tanto o exemplo de Python de criar uma política armazenada e SA no [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Criar uma política armazenada e SA utilizando C\#

1. Abra a solução no Visual Studio.

2. No Explorador de solução, com o botão direito no projeto __SASToken__ e selecione __Propriedades__.

3. Selecione __Definições__ e adicionar os valores para as seguintes entradas:

    * StorageConnectionString: A cadeia de ligação para a conta de armazenamento à qual pretende criar uma política armazenada e SA para. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde `myaccount` é o nome da sua conta de armazenamento e `mykey` é a chave da conta de armazenamento.
    
    * ContainerName: O contentor na conta de armazenamento que pretende restringir o acesso a.
    
    * SASPolicyName: O nome a utilizar para a política armazenada que será criada.
    
    * FileToUpload: O caminho para um ficheiro que vai ser carregado para o contentor.
    
4. Execute o projecto. Será apresentada uma janela de consola e informações semelhantes ao seguinte serão apresentadas assim que tem sido gerada as associações de segurança:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Guarde o token de política SA, terá de isto quando associar o seu cluster HDInsight a conta de armazenamento. Também terá do nome de conta de armazenamento e o nome do contentor.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Criar uma política armazenada e SA utilizando Python

1. Abra o ficheiro SASToken.py e alterar os seguintes valores:

    * política\_nome: O nome a utilizar para a política armazenada que será criada.
    
    * armazenamento\_conta\_nome: O nome da sua conta de armazenamento.
    
    * armazenamento\_conta\_chave: A chave da conta de armazenamento.
    
    * armazenamento\_contentor\_nome: O contentor na conta de armazenamento que pretende restringir o acesso a.
    
    * exemplo\_ficheiro\_caminho: O caminho para um ficheiro que vai ser carregado para o contentor

2. Execute o script. Será apresentado o token de SA semelhante ao seguinte quando concluir o script:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Guarde o token de política SA, terá de isto quando associar o seu cluster HDInsight a conta de armazenamento. Também terá do nome de conta de armazenamento e o nome do contentor.
    
##<a name="use-the-sas-with-hdinsight"></a>Utilizar as associações de segurança com HDInsight

Ao criar um cluster de HDInsight, tem de especificar uma conta de armazenamento principal e, opcionalmente, pode especificar contas de armazenamento adicional. Ambos os métodos de adição de armazenamento requerem acesso total às contas de armazenamento e contentores são utilizadas.

Para que possa utilizar uma assinatura de acesso partilhado para limitar o acesso a um contentor, tem de adicionar uma entrada de personalizado para a configuração de __sites principais__ para cluster.

* Para clusters __baseados no Windows__ ou __baseado em Linux__ HDInsight, pode fazê-lo durante a criação de cluster através do PowerShell.

* __Baseado em Linux__ clusters do HDInsight, altere a configuração após a criação de cluster utilizando Ambari.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Criar um novo cluster que utiliza as associações de segurança

Um exemplo de criação de um cluster de HDInsight que utiliza as associações de segurança é incluído na `CreateCluster` directório do repositório. Para utilizá-lo, utilize os passos seguintes:

1. Abrir o `CreateCluster\HDInsightSAS.ps1` ficheiro num editor de texto e modificar os seguintes valores no início do documento.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Por exemplo, alterar `'mycluster'` para o nome do cluster que pretende criar. Os valores de SA devem corresponder aos valores a partir os passos anteriores ao criar uma conta de armazenamento e token de SA.
    
    Assim que tiver alterado os valores, guarde o ficheiro.

1. Abra uma nova linha de comandos do PowerShell de Azure. Se não estiver familiarizado com o Azure PowerShell, ou se não tiver instalado, consulte o artigo [instalar e configurar o Azure PowerShell][powershell].

2. A partir da linha de comandos, utilize o seguinte para autenticar à sua subscrição do Azure:

        Login-AzureRmAccount
    
    Quando lhe for pedido, inicie sessão com a conta para a sua subscrição Azure.
    
    Se o início de sessão está associado a múltiplas subscrições Azure, poderá ter de utilizar `Select-AzureRmSubscription` para selecionar a subscrição que pretende utilizar.

2. A partir da linha de comandos, alterar directórios para o `CreateCluster` directory que contém o ficheiro HDInsightSAS.ps1. Em seguida, utilize o seguinte procedimento para executar o script
        
        .\HDInsightSAS.ps1
    
    Como o script é executado, iniciará sessão saída para a linha de comandos do PowerShell à medida que cria o recurso de contas de grupo e de armazenamento. Vai, em seguida, apresentar-lhe para introduzir o utilizador HTTP para HDInsight cluster. Esta é a conta de utilizador utilizada para HTTP/s acesso seguro a cluster.
    
    Se estiver a criar um cluster baseado em Linux, também lhe ser pedido para um SSH nome da conta de utilizador e palavra-passe. Isto é utilizado para remotamente início de sessão para cluster.
    
    > [AZURE.IMPORTANT] Quando lhe for pedido para HTTP/s ou SSH nome de utilizador e palavra-passe, tem de fornecer uma palavra-passe que cumpre os seguintes critérios:
    >
    > - Tem de ser pelo menos de 10 carateres de comprimento
    > - Tem de conter pelo menos um dígito
    > - Tem de conter, pelo menos, um caráter que não sejam alfanuméricos
    > - Tem de conter pelo menos um canto superior ou letra minúscula


Irá demorar algum tempo para este script concluir, normalmente cerca de 15 minutos. Quando concluir o script sem erros, cluster foi criado.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Atualizar um cluster existente para utilizar as associações de segurança

Se tiver um cluster de baseado em Linux existente, pode adicionar as associações de segurança para a configuração do __core site__ através dos seguintes passos:

1. Abra a web Ambari da IU para o seu cluster. O endereço para esta página é https://YOURCLUSTERNAME.azurehdinsight.net. Quando lhe for pedido, autenticar-se para a utilizando o nome de administrador (admin) e a palavra-passe que utilizou quando criar o cluster de computação.

2. A partir do lado esquerdo da web Ambari IU, selecione __HDFS__ e, em seguida, selecione o separador de __Configs__ no meio da página.

3. Selecione o separador __Avançadas__ e, em seguida, desloque-se até encontrar a secção __personalizada core de sites__ .

4. Expandir a secção de __personalizada core de sites__ , em seguida, desloque-se para o fim e selecione a ligação __Adicionar propriedade...__ . Utilize os seguintes valores para os campos de __chave__ e __valor__ :

    * __Chave__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Valor__: O SA devolvido pela aplicação c# ou Python executou anteriormente
    
    Substitua o nome do contentor utilizado com o c# ou aplicação SA __CONTAINERNAME__ . Substitua o nome de conta de armazenamento que utilizou __STORAGEACCOUNTNAME__ .

5. Clique no botão __Adicionar__ para guardar esta chave e valor e, em seguida, clique no botão __Guardar__ para guardar as alterações à configuração. Quando lhe for pedido, adicione uma descrição da alteração ("adicionar acesso ao armazenamento SA" por exemplo) e, em seguida, clique em __Guardar__.

    Clique em __OK__ quando tem sido concluídas as alterações.

    > [AZURE.IMPORTANT] Este procedimento guarda as alterações à configuração, mas tem de reiniciar o vários serviços antes da alteração entra em vigor.

6. No web Ambari IU, selecione __HDFS__ a partir da lista no lado esquerdo e, em seguida, selecione __Reiniciar todas as__ __Ações de serviço de__ lista pendente, à direita. Quando lhe for pedido, selecione __Ativar o modo de manutenção__ e, em seguida, selecione __Conform reinicie todos".

    Repita este processo para as entradas de MapReduce2 e FIO a partir da lista no lado esquerdo da página.

7. Assim que estes tem reiniciado, selecione cada uma delas e desativem o modo de manutenção das __Ações de serviço de__ lista pendente.

##<a name="test-restricted-access"></a>Testar o acesso restrito

Para verificar que têm o acesso restrito, utilize os seguintes métodos:

* Para clusters de HDInsight __baseados no Windows__ , utilize o ambiente de trabalho remoto para ligar ao cluster. Consulte o artigo [ligar ao HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) para obter mais informações.

    Quando estabelecida, utilize o ícone de __linha de comandos Hadoop__ no ambiente de trabalho para abrir uma linha de comandos.

* Para __baseado em Linux__ HDInsight clusters, utilize SSH para ligar ao cluster. Consulte um dos seguintes para obter informações sobre como utilizar SSH com baseado em Linux clusters:

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Quando ligado ao cluster, utilize os passos seguintes para verificar que poderá só de leitura e lista de itens na conta de armazenamento SA:

1. A partir da linha de comandos, escreva o seguinte. Substitua o nome do contentor criado para a conta de armazenamento SA __SASCONTAINER__ . Substitua __SASACCOUNTNAME__ com o nome da conta de armazenamento utilizado para as associações de segurança:

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Isto irá listar o conteúdo do contentor, deve incluir o ficheiro que foi carregado quando o contentor e SA foi criado.
    
2. Utilize o seguinte procedimento para verificar se pode ler os conteúdos do ficheiro. Substitua o __SASCONTAINER__ e __SASACCOUNTNAME__ tal como no passo anterior. Substitua o __nome de ficheiro__ com o nome do ficheiro apresentado no comando anterior:

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Isto irá listar o conteúdo do ficheiro.
    
3. Utilize o seguinte para transferir o ficheiro para o sistema de ficheiros local:

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Isto irá transferir o ficheiro para um ficheiro local chamado __fichteste. txt__.

4. Utilize o seguinte para carregar o ficheiro local para um novo ficheiro chamado __testupload.txt__ no armazenamento SA:

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Receberá uma mensagem semelhante ao seguinte:
    
        put: java.io.IOException
        
    Este erro ocorre porque a localização de armazenamento é leitura + apenas para a lista. Utilize o seguinte procedimento para colocar os dados no armazenamento predefinida para o cluster, que é gravável:
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    Desta vez, a operação deve concluída com êxito.
    
##<a name="troubleshooting"></a>Resolução de problemas

###<a name="a-task-was-canceled"></a>Uma tarefa foi cancelada

__Sintomas__: ao criar um cluster utilizando o script do PowerShell, poderá receber a seguinte mensagem de erro:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Problema__: Este erro pode ocorrer se utilizar uma palavra-passe para o utilizador de administração/HTTP ao cluster ou (baseado em Linux clusters,) o utilizador SSH.

__Resolução__: utilizar uma palavra-passe que cumpre os seguintes critérios:

- Tem de ser pelo menos de 10 carateres de comprimento
- Tem de conter pelo menos um dígito
- Tem de conter, pelo menos, um caráter que não sejam alfanuméricos
- Tem de conter pelo menos um canto superior ou letra minúscula

##<a name="next-steps"></a>Próximos passos

Agora que aprendeu como adicionar armazenamento de acesso limitado ao seu cluster HDInsight, saber outras formas de trabalhar com dados no seu cluster:

* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

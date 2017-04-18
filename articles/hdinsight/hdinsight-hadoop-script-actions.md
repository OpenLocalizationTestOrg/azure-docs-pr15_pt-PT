<properties
    pageTitle="Script de desenvolvimento de ação com HDInsight | Microsoft Azure"
    description="Saiba como personalizar Hadoop clusters com Script ação. Acção de script pode ser utilizada para instalar o software adicional em execução num Hadoop cluster ou para alterar a configuração de aplicações instaladas num cluster."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Desenvolver scripts de acção de Script para clusters baseados no HDInsight Windows

Saiba como escrever scripts de acção de Script para HDInsight. Para obter informações sobre a utilização de scripts de acção de Script, consulte o artigo [Personalizar HDInsight clusters utilizando Script ação](hdinsight-hadoop-customize-cluster.md). Para o mesmo artigo escrito para clusters baseado em Linux HDInsight, consulte o artigo [scripts de desenvolver ação de Script para HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] As informações neste documento são específicas a clusters HDInsight baseados no Windows. Para obter informações sobre como utilizar ações de script com clusters baseados no Windows, consulte o artigo [desenvolvimento de ação de Script com HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Acção de script pode ser utilizada para instalar o software adicional em execução num Hadoop cluster ou para alterar a configuração de aplicações instaladas num cluster. Acções de script estão scripts que são executados em nós de cluster quando são implementados HDInsight clusters e estas são executadas assim que nós do cluster de concluir a configuração de HDInsight. Uma ação de script é executada em privilégios de conta de administrador do sistema e fornece os direitos de acesso completo a nós de cluster. Cada cluster pode ser fornecido com uma lista de ações de script para ser executada pela ordem em que estão especificados.

> [AZURE.NOTE] Se tiver a seguinte mensagem de erro:
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> É uma vez que não inclui os métodos de ajuda.  Consulte o artigo [métodos de Helper para scripts personalizados](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Scripts de exemplo

Para criar clusters de HDInsight num sistema operativo Windows, a ação de Script é script Azure PowerShell. Segue-se um exemplo script para configurar os ficheiros de configuração do site:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

O script leva-o até quatro parâmetros, o nome de ficheiro de configuração, a propriedade que pretende modificar, o valor que pretende definir, e uma descrição. Por exemplo:

    hive-site.xml hive.metastore.client.socket.timeout 90

Estes parâmetros serão defina o valor de hive.metastore.client.socket.timeout a 90 no ficheiro ramo site.xml.  O valor predefinido é 60 segundos.

Este exemplo de script também pode ser encontrado em [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight fornece vários scripts de instalar componentes adicionais sobre HDInsight clusters:

Nome | Script
----- | -----
**Instalar motores** | https://hdiconfigactions.blob.Core.Windows.NET/sparkconfigactionv03/spark-Installer-v03.ps1. Consulte o artigo [instalar e utilizar gerar sobre clusters de HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalação e utilização R sobre clusters de HDInsight][hdinsight-r-scripts].
**Instalar Solr** | https://hdiconfigactions.blob.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Consulte o artigo [instalar e utilizar clusters de Solr no HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar Giraph** | https://hdiconfigactions.blob.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Consulte o artigo [instalar e utilizar clusters de Giraph no HDInsight](hdinsight-hadoop-giraph-install.md).

Acção de script pode ser implementada a partir do portal do Azure, Azure PowerShell ou ao utilizar o .NET SDK HDInsight.  Para mais informações, consulte o artigo [Personalizar HDInsight clusters utilizando Script acção][hdinsight-cluster-customize].

> [AZURE.NOTE] Os scripts de exemplo funcionam apenas com a versão de cluster de HDInsight 3.1 ou acima. Para mais informações sobre as versões de cluster HDInsight, consulte o artigo [versões de cluster HDInsight](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Métodos de Helper para scripts personalizados

Métodos de helper de ação de script são utilitários que pode utilizar ao escrever scripts personalizados. Estas são definidas em [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)e podem ser incluídas no seu scripts utilizando o seguinte procedimento:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Aqui estão os métodos de helper que são fornecidos por este script:

Método de ajuda | Descrição
-------------- | -----------
**HDIFile guardar** | Transferir um ficheiro a partir do especificado URI Uniform Resource Identifier () para uma localização no disco local que está associado com o nó do Azure VM atribuído ao cluster.
**HDIZippedFile expandir** | Deszipe um ficheiro compactado.
**HDICmdScript invocar** | Execute um script do cmd.exe.
**HDILog de escrita** | Escreva os resultados de scripts personalizados utilizados para uma acção de script.
**Obter serviços** | Obter uma lista de serviços em execução no computador onde o script é executado.
**Get-Service** | Com o nome de serviço específico como entrada, obter informações detalhadas de um serviço específico (nome do serviço, processar ID, estado, etc.) no computador onde o script é executado.
**Get-HDIServices** | Obter uma lista dos serviços de HDInsight em execução no computador onde o script é executado.
**Get-HDIService** | Com o nome do serviço HDInsight específico como entrada, obter informações detalhadas de um serviço específico (nome do serviço, processar ID, estado, etc.) no computador onde o script é executado.
**Get-ServicesRunning** | Obter uma lista dos serviços que estão a ser executados no computador onde o script é executado.
**Get-ServiceRunning** | Verificar se um serviço específico (por nome) está em execução no computador onde o script é executado.
**Get-HDIServicesRunning** | Obter uma lista dos serviços de HDInsight em execução no computador onde o script é executado.
**Get-HDIServiceRunning** | Verificar se um serviço de HDInsight específico (por nome) está em execução no computador onde o script é executado.
**Get-HDIHadoopVersion** | Obter a versão do Hadoop instalada no computador onde o script é executado.
**Teste IsHDIHeadNode** | Verifique se o computador onde o script executa é um nó cabeça.
**Teste IsActiveHDIHeadNode** | Verifique se o computador onde o script executa é um nó de cabeça activo.
**Teste IsHDIDataNode** | Verifique se o computador onde o script executa é um nó de dados.
**Editar HDIConfigFile** | Edite o config ficheiros ramo-site.xml, core site.xml, hdfs site.xml, mapred site.xml ou fio site.xml.


## <a name="best-practices-for-script-development"></a>Práticas recomendadas para o desenvolvimento de script

Quando desenvolver um script personalizado para um cluster de HDInsight, existem várias melhores práticas a ter em conta:

- Procurar a versão de Hadoop

    HDInsight apenas a versão 3.1 (Hadoop 2.4) e acima de utilizar a ação de Script para instalar componentes personalizados num cluster de suporte. No seu script personalizado, tem de utilizar o método de helper **Get-HDIHadoopVersion** para verificar a versão de Hadoop antes de prosseguir com executar outras tarefas no script.


- Fornecer estáveis ligações para recursos de script

    Os utilizadores devem certificar-se de que todos os scripts e outros artefactos utilizados na personalização de um cluster de permanecem disponíveis em todo o tempo de vida do cluster e que as versões destes ficheiros não mudarem para a duração. Estes recursos são necessários se imagens novamente de nós do cluster for necessária. É a melhor prática transferir e arquivar tudo numa conta de armazenamento que o utilizador controla. Isto pode ser a conta de armazenamento predefinida ou em qualquer uma das contas adicionais de armazenamento especificadas no momento da implementação para um cluster de personalizada.
    Na motores e R personalizados amostras cluster fornecidos na documentação, por exemplo, vamos tenha efetuado uma cópia local dos recursos nesta conta de armazenamento: https://hdiconfigactions.blob.core.windows.net/.


- Certifique-se de que o script de personalização de cluster é idempotent

    Que deve esperar que os nós de um cluster de HDInsight será novamente imagens durante o tempo de vida de cluster. O script de personalização cluster é executado sempre que um cluster é imagens novamente. Este script deve ser concebido para ser idempotent no sentido em que relativamente a voltar a imagens, o script deve garantir que o cluster é devolvido para o mesmo valor personalizado Estado que estava imediatamente após o script executou pela primeira vez quando o cluster inicialmente foi criado. Por exemplo, se um script personalizado instalado uma aplicação do D:\AppLocation no seu próprio executar, em seguida, em cada execução subsequente, relativamente a voltar a imagens, o script deve verificar se existe a aplicação na localização D:\AppLocation antes de prosseguir com os outros passos no script.


- Instalar componentes personalizados na localização ideal

    Quando nós de cluster forem novamente imagens, a unidade de recurso C:\ e a unidade de sistema D:\ podem ser formatados novamente, que resulta na perda de dados e as aplicações que tinham sido instaladas nessas unidades. Isto também pode acontecer se um nó Azure máquina virtual (VM) que faz parte de cluster vai para baixo e é substituído por um novo nó. Pode instalar componentes na unidade de D:\ ou na localização C:\apps num cluster. Todas as outras localizações na unidade de C:\ são reservadas. Especifique a localização onde estão aplicações ou as bibliotecas para ser instalada no script de personalização de cluster.


- Certifique-se de elevada disponibilidade da arquitetura de cluster

    O HDInsight tem uma arquitetura de activo passivo para elevada disponibilidade, na qual um nó cabeça está no modo activo (que os serviços de HDInsight estão a ser executado) e o outro nó cabeça está em modo de espera (na qual HDInsight serviços não estiver a executar). Os nós alternar modos ativos e passivos se HDInsight serviços estão interrompidos. Se uma acção de script é utilizada para instale os serviços em ambos os nós de cabeça para elevada disponibilidade, tenha em atenção que o mecanismo de activação pós-falha HDInsight não poderão automaticamente falhar sobre estes serviços instalados pelo utilizador. Serviços instalados pelo utilizador em nós de cabeça HDInsight que sejam espera que esteja disponível altamente tem de ter os seus próprios mecanismo activação pós-falha se no modo de activo passivo ou estar no modo de activo activo.

    Um comando de acção de Script HDInsight é executado em ambos os nós de cabeça quando a função de cabeça nó especificada como um valor no parâmetro *ClusterRoleCollection* . Por isso, ao estruturar um script personalizado, certifique-se de que o script está em mente esta configuração. Não se deve deparado com problemas, onde os mesmos serviços estão instalados e iniciados em ambos os nós de cabeça e estas terminam competir com os outros. Além disso, tenha em atenção que dados serão perdidos durante o processamento de imagens novamente, para que o software instalado através da ação de Script tem de estar e são a esses eventos. Aplicações devem ser concebidas para trabalhar com dados altamente disponíveis que são distribuídos em muitos nós. Tenha em atenção que até 1/5 de nós num cluster pode ser novamente criada uma imagem ao mesmo tempo.


- Configurar os componentes personalizados para utilizar o armazenamento de Blobs do Azure

    Os componentes personalizados que instala em nós de cluster poderão ter uma configuração predefinida para utilizar o armazenamento de distribuído ficheiro Hadoop sistema (HDFS). Deverá alterar a configuração para utilizar em vez disso, o armazenamento de Blobs do Azure. Numa cluster novamente imagem, o sistema de ficheiros HDFS obtém formatado e seria perder quaisquer dados que estão armazenados aí. Em vez disso, utilizando o armazenamento de Blobs do Azure assegura que os seus dados serão retidos.

## <a name="common-usage-patterns"></a>Padrões de utilização comum

Esta secção fornece orientações sobre como implementar a algumas dos padrões de utilização comuns que poderão ocorrer ao escrever o seu próprio script personalizado.

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

Muitas vezes no desenvolvimento de ação de script, irá sentir-a necessidade de definir variáveis de ambiente. Por exemplo, um cenário mais provável é quando transferir um ficheiro binário a partir de um site externo, instalá-lo no cluster e adicionar a localização do onde está instalado para a variável de ambiente de 'PATH'. O fragmento que se segue mostra-lhe como configurar as variáveis de ambiente no script personalizado.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Esta declaração define a variável de ambiente **MDS_RUNNER_CUSTOM_CLUSTER** para o valor 'true' e também define o âmbito desta variável para ser globais. Por vezes é importante que variáveis de ambiente estão definidas no âmbito apropriado – máquina ou utilizador. Consulte [aqui] [ 1] para obter mais informações sobre a definição de variáveis de ambiente.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a localizações onde estão armazenados os scripts personalizados

Scripts utilizadas para personalizar um cluster de precisa de estar quer na conta de armazenamento predefinida para o cluster ou num contentor só de leitura público no qualquer outra conta de armazenamento. Se o script acede recursos localizados noutro local estes necessitam de ser um acessível publicamente (público, pelo menos, só de leitura). Por exemplo poderá pretender aceder a um ficheiro e guardá-la utilizando o comando SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Neste exemplo, tem de garantir que o contentor somecontainer na conta de armazenamento 'somestorageaccount' está acessível publicamente. Caso contrário, o script irá gerar uma exceção 'Não encontrado' e falhar.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Passar parâmetros para o cmdlet AzureRmHDInsightScriptAction adicionar

Para passar vários parâmetros para o cmdlet adicionar AzureRmHDInsightScriptAction, é necessário formatar o valor de cadeia para incluir todos os parâmetros para o script. Por exemplo:

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

ou

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Gerar exceção para implementação cluster falha

Se quiser ser notificado com exatidão do facto de personalização de cluster não teve êxito como esperado, é importante gerar uma exceção e falhar a criação de cluster. Por exemplo, poderá pretender processam um ficheiro, se existir e as maiúsculas/minúsculas de erro onde o ficheiro não existe. Isto seria Certifique-se de que o script sai correctamente e o estado do cluster corretamente é conhecido. O fragmento de seguinte dá um exemplo de como alcançar isto:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Neste fragmento, se o ficheiro não existia,-seria conduzir a um Estado onde o script realmente sai correctamente depois de a imprimir a mensagem de erro e o cluster atinge o estado de execução partindo do princípio que-concluído "com êxito" o processo de personalização cluster. Se quiser ser notificado com exatidão o facto de personalização de cluster essencialmente não teve êxito como esperado devido a um ficheiro em falta, é mais adequado gerar uma exceção e falhar o passo de personalização cluster. Para realizar esta tem de utilizar em vez disso, o fragmento de código de exemplo seguintes.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista de verificação para implementar uma acção de script
Eis os passos que seguiu quando a preparar para implementar estes scripts:

1. Coloca os ficheiros que contêm os scripts personalizados num local que esteja acessível por nós do cluster durante a implementação. Isto pode ser qualquer um dos predefinido ou contas adicionais de armazenamento especificadas no momento da implementação de cluster ou qualquer outro contentor de armazenamento acessível publicamente.
2. Adicione controlos para scripts para se certificar de que executam idempotently, para que o script pode ser executado várias vezes no mesmo nó.
3. Utilize o cmdlet do Azure PowerShell de **Saída de escrita** para imprimir STDOUT, bem como STDERR. Não utilize **Escrita anfitrião**.
4. Utilizar uma pasta de ficheiros temporários, tal como $env: TEMP, para manter o ficheiro transferido utilizado pelos scripts e, em seguida, limpá-los depois de tem executado scripts.
5. Instale o software personalizado apenas na D:\ ou C:\apps. Outras localizações na unidade c: não devem ser utilizadas como estão reservadas. Tenha em atenção que instalar os ficheiros na unidade c: fora da pasta C:\apps pode resultar em falhas de configuração durante novamente imagens do nó.
6. Na eventualidade de ficheiros de configuração de serviço de Hadoop ou definições ao nível do SO foram alterados, poderá pretender reiniciar HDInsight serviços para que pode atende a qualquer definições ao nível do sistema operativo, tais como as variáveis de ambiente definir os scripts.

## <a name="debug-custom-scripts"></a>Depurar scripts personalizados

Os registos de erros de script são armazenados, juntamente com outros saída, na conta de armazenamento predefinido que especificou para o cluster na sua criação. Os registos são armazenados numa tabela com o nome *u < \cluster-name-fragment >< \time-stamp > setuplog*. Estes são registos agregados que têm registos de todos os nós (nó cabeça e nós de trabalho) no qual o script é executado no cluster.
Uma forma fácil de verificar os registos de início é utilizar ferramentas de HDInsight para Visual Studio. Para instalar as ferramentas, consulte o artigo [começar a utilizar o Visual Studio Hadoop ferramentas para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Para verificar o registo utilizando o Visual Studio**

1. Abrir o Visual Studio.
2. Clique em **Ver**e, em seguida, clique em **Explorador de servidor**.
3. Botão direito do rato "Azure", clique em ligar ao **Microsoft Azure subscrições**e, em seguida, introduza as suas credenciais.
4. Expandir **capacidade de armazenamento**, expanda a conta de armazenamento Azure utilizada como o sistema de ficheiro predefinido, expanda **tabelas**e, em seguida, faça duplo clique no nome da tabela.


Pode também remoto para os nós de cluster para ver a ambos STDOUT e STDERR scripts personalizados. Os registos em cada nó são específicos apenas para esse nó e sessão iniciados no **C:\HDInsightLogs\DeploymentAgent.log**. Estes ficheiros de registo registam todos os resultados a partir do script personalizado. Um fragmento de registo de exemplo para uma acção de script motores tem o seguinte aspeto:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Este registo é limpar que a ação de script motores tiver sido executada no VM denominada HEADNODE0 e que foram iniciadas sem exceções durante a execução.

Se que ocorre uma falha de execução, o resultado que descrevem-lo também irão ser contido neste ficheiro de registo. As informações fornecidas nestes registos devem ser úteis no depurar script problemas que podem surgir.


## <a name="see-also"></a>Consulte também

- [Personalizar clusters HDInsight utilizando a ação de Script][hdinsight-cluster-customize]
- [Instalar e utilizar motores no HDInsight clusters][hdinsight-install-spark]
- [Instalar e utilizar R no HDInsight clusters][hdinsight-r-scripts]
- [Instalar e utilizar clusters de Solr no HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar e utilizar clusters de Giraph no HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx

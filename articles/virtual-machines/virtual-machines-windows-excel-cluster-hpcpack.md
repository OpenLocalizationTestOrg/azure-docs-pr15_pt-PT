<properties
 pageTitle="Cluster aos Pack para o Excel e SOA | Microsoft Azure"
 description="Introdução ao ser executada em grande escala Excel e SOA das cargas de trabalho num cluster de aos Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Introdução ao executar o das cargas de trabalho do Excel e SOA num cluster aos Pack no Azure

Este artigo mostra-lhe como implementar um cluster de Microsoft aos Pack em máquinas virtuais Azure utilizando um modelo de guia de introdução Azure ou opcionalmente um script de implementação do Azure PowerShell. O cluster utiliza imagens Azure Marketplace VM concebidas para executar o Microsoft Excel ou das cargas de trabalho de arquitectura orientados para serviços (SOA) com aos Pack. Pode utilizar o cluster para executar aos simples do Excel e nos serviços do SOA a partir de um computador de cliente no local. Os serviços do Excel aos incluem descarregar de livro do Excel e Excel funções definidas pelo utilizador ou UDFs.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

De alto nível, o diagrama seguinte mostra o cluster aos Pack que cria.

![Cluster HPC connosco em execução das cargas de trabalho do Excel][scenario]

## <a name="prerequisites"></a>Pré-requisitos

*   **Computador cliente** - necessita de um computador cliente baseados no Windows submeter tarefas de Excel e SOA de exemplo para o cluster. Também precisa de um computador Windows para executar o script de implementação do Azure PowerShell cluster (se optar por esse método de implementação) e

*   **Subscrição do azure** - se não tiver uma subscrição do Azure, pode criar um [livre conta](https://azure.microsoft.com/pricing/free-trial/) apenas de duas minutos.

*   **Quota núcleos** - é possível que tenha aumentar a quota de núcleos, especialmente se implementar vários nós de cluster com tamanhos de VM multicore. Se estiver a utilizar um modelo de guia de introdução Azure, é a quota de núcleos no Gestor de recursos por região Azure. Nesse caso, poderá ter aumentar a quota numa região específico. Consulte o artigo [limites de subscrição Azure, quotas e restrições](../azure-subscription-service-limits.md). Para aumentar uma quota, [Abra um pedido de suporte online do cliente](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem encargos.

*   **Licença do Microsoft Office** - se implementar nós de cluster utilizando uma imagem de Marketplace aos Pack VM com o Microsoft Excel, uma versão de avaliação de 30 dias do Microsoft Excel Professional Plus 2013 está instalado. Depois do período de avaliação, tem de fornecer uma licença válida do Microsoft Office para ativar o Excel para continuar a executar das cargas de trabalho. Consulte o artigo de [ativação do Excel](#excel-activation) neste artigo. 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Passo 1. Configurar um cluster de aos Pack no Azure

Mostramos-duas opções para configurar o cluster: primeiro, utilizando um modelo de guia de introdução Azure e o Azure portal; e em segundo, utilizando um script de implementação do Azure PowerShell.


### <a name="option-1-use-a-quickstart-template"></a>Opção 1. Utilizar um modelo de guia de introdução
Utilize um modelo de guia de introdução Azure de forma rápida e fácil implementar um cluster de aos Pack no portal do Azure. Quando abrir o modelo no portal do, irá obter uma simple IU onde introduzir as definições para o seu cluster. Eis os passos. 

>[AZURE.TIP]Se pretender, utilize um [modelo do Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que cria um cluster semelhante especificamente para das cargas de trabalho do Excel. Os passos diferem ligeiramente a partir do seguinte.

1.  Visite a [página de modelo de criar Cluster HPC GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se pretender, reveja as informações sobre o modelo e o código de origem.

2.  Clique em **implementar para Azure** para iniciar uma implementação com o modelo no portal do Azure.

    ![Implementar o modelo em Azure][github]

3.  No portal do, siga estes passos para introduzir os parâmetros para o modelo de cluster HPC.

    um. Na página **parâmetros** , introduza ou modifique os valores para os parâmetros de modelo. (Clique no ícone junto a cada definição de informações de ajuda). Valores de exemplo são apresentadas no ecrã seguinte. Este exemplo cria um cluster denominado *hpc01* no domínio de *hpc.local* que consiste num nó cabeça e 2 calcular nós. Os nós de cluster são criados a partir de uma imagem aos Pack VM que inclui o Microsoft Excel.

    ![Introduza os parâmetros][parameters]

    >[AZURE.NOTE]O nó cabeça VM é criado automaticamente a partir da [imagem de Marketplace mais recente](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) aos Pack 2012 R2 no Windows Server 2012 R2. Atualmente, a imagem é baseada aos Pack 2012 R2 atualização 3.
    >
    >Cluster nó VMs são criados a partir da imagem mais recente da família nó cluster selecionado. Selecione a opção **ComputeNodeWithExcel** para a mais recente aos Pack cluster nó imagem que inclui uma versão de avaliação do Microsoft Excel Professional Plus 2013. Para implementar um cluster para sessões SOA gerais ou para o Excel UDF descarregar, selecione a opção de **ComputeNode** (sem o Excel instalado).

    b. Selecione a subscrição.

    c. Crie um grupo de recursos para cluster, tal como *hpc01RG*.

    d. Selecione uma localização para o grupo de recursos, tal como Central (EUA).

    "e". Na página **termos Legal** , reveja os termos. Se concordar, clique em **comprar**. Em seguida, quando tiver terminado de definir os valores para o modelo, clique em **Criar**.

4.  Quando a implementação está concluída (normalmente demorar cerca de 30 minutos), exportar o ficheiro de certificado cluster o nó do cabeça cluster. Um passo posterior, o utilizador importa este certificado público no computador cliente para fornecer a autenticação do lado do servidor para enlace HTTP seguro.

    um. Ligue para o nó cabeça ao ambiente de trabalho remoto do portal do Azure.

     ![Ligar para o nó cabeça][connect]

    b. Utilize padrão procedimentos no Gestor de certificados para exportar o certificado de cabeça nó (localizado em orientação do diapositivo notas: \LocalMachine\My) sem a chave privada. Neste exemplo, exportar *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exporte o certificado][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opção 2. Utilizar o script de implementação de IaaS aos Pack

O script de implementação aos Pack IaaS fornece outra forma versátil para implementar um cluster de aos Pack. Cria um cluster no modelo clássico de implementação, Considerando que o modelo utiliza o modelo de implementação do Azure o Gestor de recursos. Além disso, o script é compatível com uma subscrição no serviço Azure Global ou Azure China.

**Pré-requisitos adicionais**

* **Azure PowerShell** - [instalar e configurar o Azure PowerShell](../powershell-install-configure.md) (versão 0.8.10 ou posterior) no seu computador cliente.

* **Script de implementação aos Pack IaaS** - transferir e descompactar a versão mais recente do script a partir do [Centro de transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verificar a versão do script ao executar `New-HPCIaaSCluster.ps1 –Version`. Este artigo é baseado na versão 4.5.0 ou posterior do script.

**Criar o ficheiro de configuração**

 O script de implementação de aos Pack IaaS utiliza um ficheiro de configuração de XML como entrada que descreva a infraestrutura do HPC cluster. Para implementar um cluster que consiste num nó cabeça e 18 cluster nós criados da imagem de nó de cluster que inclui o Microsoft Excel, substitua valores para o seu ambiente para o ficheiro de configuração de exemplo seguintes. Para mais informações sobre o ficheiro de configuração, consulte o ficheiro Manual.rtf na pasta de scripts e [criar um cluster HPC com o script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas sobre o ficheiro de configuração**

* O **VMName** do nó cabeça **tem** de ser igual a **ServiceName**ou executar as tarefas SOA falhar.

* Certifique-se de que especificar **EnableWebPortal** para que o certificado de cabeça nó é gerado e exportado.

* O ficheiro Especifica um script do PowerShell pós-configuração PostConfig.ps1 que é executada no nó cabeça. O seguinte script do exemplo configura a cadeia de ligação do Azure armazenamento, remove a função de nó cluster do nó cabeça e coloca todos os nós online quando estão implementados. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Executar o script**

1.  Abra a consola do PowerShell no computador cliente como administrador.

2.  Alterar directório para a pasta de script (E:\IaaSClusterScript neste exemplo).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Para implementar o cluster aos Pack, execute o seguinte comando. Este exemplo assume que o ficheiro de configuração está localizado na E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

O script de implementação aos Pack é executado há algum tempo. É um critério é que o script exportar e transferir o certificado de cluster e guardá-lo na pasta de documentos do utilizador actual no computador cliente. O script gera uma mensagem semelhante ao seguinte. Um passo seguinte, o utilizador importa o certificado no arquivo de certificados apropriado.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Passo 2. Descarregar livros do Excel e executar UDFs a partir de um cliente no local

### <a name="excel-activation"></a>Ativação do Excel

Quando utilizar a imagem ComputeNodeWithExcel VM para cargas de trabalho de produção, tem de fornecer uma chave de licença válida do Microsoft Office para ativar o Excel em nós cluster. Caso contrário, a versão de avaliação do Excel expira após 30 dias e, em execução livros do Excel irá falhar com o COMException (0x800AC472). 

Pode rearmar Excel para outro 30 dias após a hora de avaliação: iniciar sessão no nó cabeça e clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` em todo o Excel calcular nós através do Gestor de clusters de HPC. Pode rearmar um máximo de duas vezes. Após esta ação, tem de fornecer uma chave de licença do Office válida.

O Office Professional Plus 2013 instalados na imagem VM é uma edição de volume com uma chave de licença do genérico Volume (GVLK). Pode ativá-la através do Key Management Service (KMS) / Active Directory-Based ativação (AD BA) ou Multiple Activation Key (MAK). 

    * Para utilizar o AD/KMS-BA, utilizar um servidor KMS existente ou configurar um novo utilizando o pacote de licença de Volume do Microsoft Office 2013. (Se pretender, configure o servidor no nó cabeça.) Em seguida, ative a chave do anfitrião KMS através da Internet ou o telefone. Em seguida, clusrun `ospp.vbs` para definir o KMS servidor e porta e ativar o Office em todos os nós de calcular o Excel. 
    
    * Para utilizar MAK, primeiro clusrun `ospp.vbs` para introduzir a chave e, em seguida, ativar todos do Excel calcular nós através da Internet ou o telefone. 

>[AZURE.NOTE]Chaves de produto de revenda para o Office Professional Plus 2013 não podem ser utilizadas com esta imagem VM. Se tiver chaves válidas e suporte de instalação de edições do Office ou o Excel que não seja nesta edição de volume do Office Professional Plus 2013, pode utilizá-los em vez disso. Em primeiro lugar, desinstale nesta edição de volume e instalar a edição que possui. Pode ser capturado o voltar a instalar cluster nó do Excel como uma imagem VM personalizada para utilizar numa implementação na escala.

### <a name="offload-excel-workbooks"></a>Descarregar livros do Excel

Siga estes passos para descarregar um livro do Excel para que seja executada no cluster aos Pack no Azure. Para executar esta tarefa, tem de ter o Excel 2010 ou 2013 já instalada no computador cliente.

1. Utilizar uma das opções no passo 1 para implementar um cluster de aos Pack com o Excel calcular imagem nó. Obtenha o ficheiro de certificado de cluster (. cer) e cluster nome de utilizador e palavra-passe.

2. No computador cliente, importe o certificado de cluster em orientação do diapositivo notas: \CurrentUser\Root.

3. Certifique-se de que está instalado o Excel. Crie um ficheiro de Excel.exe.config com o seguinte conteúdo na mesma pasta como Excel.exe no computador cliente. Este passo garante que aos Pack 2012 R2 Excel suplemento é carregado com êxito.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Configure o cliente de estação de submeter tarefas para cluster aos Pack. Uma das opções é a completo [aos Pack 2012 R2 atualização 3 instalação](http://www.microsoft.com/download/details.aspx?id=49922) de transferir e instalar o cliente aos Pack. Em alternativa, transfira e instale os [utilitários de cliente aos Pack 2012 R2 atualização 3](https://www.microsoft.com/download/details.aspx?id=49923) e o adequado Visual C++ 2010 redistributable para o seu computador ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  Neste exemplo, vamos utilizar um livro do Excel de exemplo com o nome ConvertiblePricing_Complete.xlsb. Pode transferi-lo [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copie o livro do Excel para uma pasta de trabalho como D:\Excel\Run.

7.  Abra o livro do Excel. No friso **desenvolver** , clique em **Suplementos COM** e confirme que aos Pack Excel suplemento é carregado com êxito.

    ![Suplemento para o pacote de de Excel][addin]

8.  Edite a macro do VBA HPCControlMacros no Excel alterando as remova as linhas, conforme mostrado no seguinte script. Substituir valores adequados para o seu ambiente.

    ![Macro do Excel para o pacote de de][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Copie o livro do Excel para um diretório de carregamento como D:\Excel\Upload. Este diretório é especificado na constante de HPC_DependsFiles na VBA macro.

10. Para executar o livro num cluster no Azure, clique no botão de **Cluster** na folha de cálculo.

### <a name="run-excel-udfs"></a>Executar UDFs do Excel

Para executar o Excel UDFs, siga os passos anteriores 1-3 para configurar o computador cliente. Para o Excel UDFs, não tem de ter a aplicação do Excel instalada no cluster nós. Por isso, quando criar o seu cluster calcular nós, pode escolher um normal calcular imagem nó em vez da imagem de nó cluster com o Excel.

>[AZURE.NOTE] Existe um limite de 34 carateres no Excel 2010 e caixa de diálogo do conector de cluster de 2013. Utilize esta caixa de diálogo para especificar o cluster que é executada as UDFs. Se o nome de cluster completa é mais longo (por exemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com), não se ajusta na caixa de diálogo. A solução é definir uma variável de toda a máquina como *CCP_IAASHN* com o valor do nome do cluster longo. Em seguida, introduza *CCP_IAASHN %* na caixa de diálogo como o nome do nó cabeça cluster. 

Depois do cluster com êxito for implementado, continuar com os seguintes passos para executar uma amostra incorporada UDF do Excel. Para UDFs personalizada do Excel, consulte estes [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para construir o XLL e implementá-los no IaaS cluster.

1.  Abra um novo livro do Excel. No friso **desenvolver** , clique em **Suplementos**. Em seguida, na caixa de diálogo, clique em **Procurar**, navegue para a pasta %CCP_HOME%Bin\XLL32 e selecione o exemplo ClusterUDF32.xll. Se o ClusterUDF32 não existe no computador do cliente, copie-o partir da pasta %CCP_HOME%Bin\XLL32 no nó cabeça.

    ![Selecione UDF][udf]

2.  Clique em **ficheiro** > **Opções** > **Avançadas**. Em **fórmulas**, selecione **Permitir funções definidas pelo utilizador XLL para executar um cluster de computação**. Em seguida, clique em **Opções** e introduza o nome completo cluster no **nome de cabeça nó Cluster**. (Conforme indicado anteriormente esta caixa de entrada está limitada a 34 caracteres, para que um nome de cluster longo poderá não se ajusta. Pode utilizar uma variável de toda a máquina aqui para um nome de cluster longo.)

    ![Configurar o UDF][options]

3.  Para executar o cálculo UDF no cluster, clique na célula com valor =XllGetComputerNameC() e prima Enter. A função simplesmente obtém o nome do nó cluster no qual o UDF é executado. Para a primeira execução, uma caixa de diálogo credenciais solicita-lhe o nome de utilizador e palavra-passe para ligar ao IaaS cluster.

    ![Executar UDF][run]

    Quando existem células muitas para calcular, prima Alt-Shift-Ctrl + F9 para executar cálculos em todas as células.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Passo 3. Executar uma carga de trabalho SOA a partir de um cliente no local

Para executar aplicações SOA gerais no cluster aos Pack IaaS, em primeiro lugar utilize um dos métodos no passo 1 para implementar o cluster. Especifique um genérico calcular imagem nó neste caso, uma vez que não necessita de Excel em nós cluster. Em seguida, siga estes passos.

1. Depois de obter o certificado de cluster, importá-lo no computador cliente em orientação do diapositivo notas: \CurrentUser\Root.

2. Instale [Aos Pack 2012 R2 atualização 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) e os [utilitários de cliente aos Pack 2012 R2 atualização 3](https://www.microsoft.com/download/details.aspx?id=49923). Estas ferramentas permitem-lhe desenvolver e executar SOA aplicações de cliente.

3. Transferir o HelloWorldR2 [código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633). Abra o HelloWorldR2.sln no Visual Studio 2010http ou 2012.

4. Construa o projeto EchoService pela primeira vez. Em seguida, implemente o serviço de IaaS cluster da mesma forma que implementar a um cluster no local. Para obter passos detalhados, consulte o artigo Leiame no HelloWordR2. Modificar e crie a HellWorldR2 e outros projetos, tal como descrito na secção seguinte para gerar as aplicações de cliente SOA que são executados num cluster Azure IaaS.

### <a name="use-http-binding-with-azure-storage-queue"></a>Utilizar enlace Http com fila de armazenamento Azure

Para utilizar enlace Http com uma fila de armazenamento Azure, altere alguns o código de exemplo.

* Atualize o nome do cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Opcionalmente, utilize a predefinição TransportScheme no SessionStartInfo ou explicitamente configurá-lo a Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Utilize encadernação predefinida para o BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Ou definidos explicitamente utilizando o basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Opcionalmente, defina o sinalizador de UseAzureQueue como verdadeiro no SessionStartInfo. Se não definir,-lo será definido como verdadeiro por predefinição, quando o nome do cluster tiver sufixos de domínio Azure e o TransportScheme é Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Utilizar enlace Http sem fila de armazenamento Azure

Para utilizar enlace Http sem uma fila de armazenamento Azure, defina explicitamente o sinalizador de UseAzureQueue falso na SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utilizar NetTcp encadernação

Para utilizar NetTcp encadernação, a configuração é semelhante ao ligar a um cluster no local. Tem de abrir alguns pontos finais no nó cabeça VM. Se tiver utilizado o script de implementação aos Pack IaaS para criar o cluster, por exemplo, defina os pontos finais no portal do Azure clássico da seguinte forma.


1. Pare a VM.

2. Adicionar as portas TCP 9090, 9087, 9091, 9094 para a sessão, Broker, Broker trabalho e os serviços de dados, respetivamente

    ![Configurar os pontos finais][endpoint]

3. Comece a VM.

A aplicação de cliente SOA não requer alterações exceto alterando o nome de cabeça para o nome completo do cluster IaaS.

## <a name="next-steps"></a>Próximos passos

* Consulte [estes recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para obter mais informações sobre a execução das cargas de trabalho do Excel com aos Pack.

* Consulte o artigo [Gerir serviços de SOA no Microsoft aos Pack](https://technet.microsoft.com/library/ff919412.aspx) para obter mais informações sobre como implementar e gerir os serviços SOA com aos Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png

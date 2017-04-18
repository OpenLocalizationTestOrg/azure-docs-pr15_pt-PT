<properties
   pageTitle="Actualizar um cluster de ferro de serviço autónomo no Windows Server | Microsoft Azure"
   description="Atualizar o código de serviço ferro e/ou configuração que executa um cluster de ferro serviço autónomo, incluindo a definição de modo de actualização de cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Atualizar o seu cluster de ferro serviço autónomo no Windows Server

> [AZURE.SELECTOR]
- [Azure Cluster](service-fabric-cluster-upgrade.md)
- [Cluster autónomo](service-fabric-cluster-upgrade-windows-server.md)

Para qualquer sistema operativo, estruturar para possibilidades é fundamental para alcançar a longo prazo sucesso do seu produto. Um cluster de serviço ferro é um recurso que é o proprietário. Este artigo descreve como pode certificar-se de que o cluster sempre é executada versões do ferro serviço suportadas código e configurações.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão de ferro que é executada no seu Cluster

Pode definir o seu cluster para transferir atualizações de ferro de serviço, quando a Microsoft disponibiliza uma nova versão ou escolher selecionar uma versão suportada ferro que pretende que o seu cluster de estar ligado. 

Pode fazê-lo ao definir a configuração do cluster "fabricClusterAutoupgradeEnabled" true ou false.


>[AZURE.NOTE] Certifique-se manter o seu cluster sempre a executar uma versão suportada do serviço ferro. Como e quando anunciar estamos a versão de uma versão nova do ferro de serviço, a versão anterior está marcada para o fim de suporte após um mínimo de 60 dias a partir dessa data. As versões novas são anunciada [no blogue de equipa do serviço ferro](https://blogs.msdn.microsoft.com/azureservicefabric/ ). A nova versão está disponível para, em seguida, selecione. 


Pode atualizar o seu cluster para a nova versão apenas se estiver a utilizar uma configuração de nó de estilo de produção, onde cada nó ferro de serviço que é atribuído num física separada ou máquina virtual. Se tiver um cluster de desenvolvimento, sempre que existam mais do que um serviço ferro nós num única física ou máquina virtual, tem de suprimir seu cluster e recrie-lo com a nova versão.


Existem duas fluxos de trabalho distintos para atualizar o seu cluster para a mais recente ou uma versão do serviço de suportado ferro. Para clusters que tenha conectividade para transferir a versão mais recente automaticamente e a segunda para clusters que não estão nenhuma conectividade para transferir a versão mais recente do serviço ferro.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com a conectividade para transferir o código e a configuração mais recentes 

Utilize estes passos para atualizar o seu cluster para uma versão suportada, se os nós de cluster tem ligação à internet para [http://download.microsoft.com](http://download.microsoft.com) 

Para clusters que tenham conectividade para [http://download.microsoft.com](http://download.microsoft.com), podemos verificar periodicamente a disponibilidade de novas versões de ferro de serviço.


Quando uma versão nova do serviço ferro estiver disponível, o pacote é transferido localmente para cluster e aprovisionado para a atualização. Para além para informar o cliente desta nova versão, o sistema coloca um aviso do Estado de funcionamento cluster explícitas semelhante ao seguinte:

"A cluster versão atual [versão #] suporte termina [data].", assim que o cluster estiver em execução a versão mais recente, o aviso desaparece.


#### <a name="cluster-upgrade-workflow"></a>Cluster fluxo de trabalho para a atualização.
 
Quando vir o aviso de estado de funcionamento de cluster, terá de fazer o seguinte procedimento:

1. Ligar ao cluster a partir de qualquer computador que tenha acesso de administrador para todos os computadores que são listados como nós do cluster. Que este script é executado no computador não tem de ser parte do cluster

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obter a lista de ferro serviço versões que pode atualizar para o

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    deve obter um resultado semelhante ao seguinte:

    ![obter ferro versões][getfabversions]

3. Iniciar uma atualização de cluster para uma das versões que esteja disponível utilizando o [PowerShell iniciar ServiceFabricClusterUpgrade cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Pode monitorizar o progresso da atualização do Explorador de ferro serviço ou ao executar o seguinte comando da shell de power

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Depois de ter corrigido os problemas que resultou na anulação, tem de iniciar a atualização novamente, seguindo os mesmos passos.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com <U>nenhuma conectividade</u> para transferir o código e a configuração mais recentes

Utilize estes passos para atualizar o seu cluster para uma versão suportada, se a cluster nós **não tem** ligação à internet para [http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Se estiver a executar um cluster de que não seja internet ligado, terá de monitorizar o blogue da equipa ferro serviço para receber uma notificação de uma nova versão. Sistema **não** coloque qualquer aviso de estado de funcionamento de cluster para alertá-lo do mesmo.  

1. Modificar a configuração do cluster para definir a propriedade seguinte FALSO.

        "fabricClusterAutoupgradeEnabled": false,

e iniciar uma atualização de configuração. consulte [início ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) para detalhes de utilização. A versão manifesto cluster é a versão que tem na clusterConfig.JSON. Certifique-se atualizar antes de início desativar a atualização de configuração.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Cluster fluxo de trabalho para a atualização.
 


1. Transferir a versão mais recente do pacote do documento [Criar cluster de ferro de serviço para o windows server](service-fabric-cluster-creation-for-windows-server.md) 


1. Ligar ao cluster a partir de qualquer computador que tenha acesso de administrador para todos os computadores que são listados como nós do cluster. Que este script é executado no computador não tem de ser parte do cluster 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Copie o pacote transferido para o arquivo de cluster da imagem.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Registe-se o pacote copiado 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Iniciar uma atualização de cluster para uma das versões que está disponível. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Pode monitorizar o progresso da atualização do Explorador de ferro serviço ou ao executar o seguinte comando da shell de power

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Depois de ter corrigido os problemas que resultou na anulação, tem de iniciar a atualização novamente, seguindo os mesmos passos.



## <a name="next-steps"></a>Próximos passos
- Saiba como personalizar algumas das [definições do serviço de ferro cluster ferro](service-fabric-cluster-fabric-settings.md)
- Saiba como [Dimensionar e reduzir o seu cluster](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [actualizações de aplicação](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

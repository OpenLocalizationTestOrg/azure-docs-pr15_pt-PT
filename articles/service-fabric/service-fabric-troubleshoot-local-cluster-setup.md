<properties
   pageTitle="Resolver problemas de configuração do seu cluster serviço ferro local | Microsoft Azure"
   description="Este artigo aborda um conjunto de sugestões de resolução de problemas seu cluster de desenvolvimento local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Resolver problemas de configuração do seu cluster de desenvolvimento local

Se ocorrer um problema ao interagir com o seu cluster de desenvolvimento do Azure Service ferro local, reveja as seguintes sugestões para potenciais soluções.

## <a name="cluster-setup-failures"></a>Cluster falhas de configuração

### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar o serviço ferro registos

#### <a name="problem"></a>Problema

Ao executar o script DevClusterSetup, verá um erro como esta:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução

Feche a janela atual do PowerShell e abrir uma nova janela do PowerShell como administrador. Agora deverá conseguir executar com êxito o script.

## <a name="cluster-connection-failures"></a>Cluster falhas de ligação

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Os cmdlets do PowerShell ferro de serviço não são reconhecidos no Azure PowerShell

#### <a name="problem"></a>Problema

Se tentar executar qualquer uma dos cmdlets do PowerShell ferro de serviço, tal como `Connect-ServiceFabricCluster` numa janela do Azure PowerShell, falhar, a dizer que não é reconhecido o cmdlet. O motivo para isto é que o Azure PowerShell utiliza a versão de 32 bits do Windows PowerShell (mesmo em versões de sistema operativo de 64 bits), Considerando que apenas trabalho os cmdlets do serviço ferro nos ambientes de 64 bits.

#### <a name="solution"></a>Solução

Executar sempre o serviço ferro cmdlets diretamente a partir do Windows PowerShell.

>[AZURE.NOTE] A versão mais recente do Azure PowerShell não criar um atalho especial, para que esta já não deve ocorrer.

### <a name="type-initialization-exception"></a>Escreva exceção de inicialização

#### <a name="problem"></a>Problema

Quando estiver a ligar ao cluster no PowerShell, consulte o erro TypeInitializationException para System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solução

A variável de path não foi definida corretamente durante a instalação. Fórum terminar sessão no Windows e inicie sessão novamente. Isto totalmente atualizará o caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Ocorre uma falha na ligação cluster com "Objecto estiver fechado"

#### <a name="problem"></a>Problema

Uma chamada para ligar ServiceFabricCluster falha com um erro como esta:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solução

Feche a janela atual do PowerShell e abrir uma nova janela do PowerShell como administrador. Agora deverá conseguir ligar com êxito.

### <a name="fabric-connection-denied-exception"></a>Exceção da ligação negado ferro

#### <a name="problem"></a>Problema

Quando depuração do Visual Studio, obterá um erro de FabricConnectionDeniedException.

#### <a name="solution"></a>Solução

Este erro ocorre normalmente quando experimentar para tentar iniciar um anfitrião do serviço processa manualmente, em vez de permitir que o tempo de execução do serviço ferro iniciá-lo por si.

Certifique-se de que não tem qualquer projetos de serviço definir como projetos de arranque na sua solução. Apenas os projetos de aplicação do serviço ferro devem ser definidos como projectos de arranque.

>[AZURE.TIP] Se, após a configuração, o seu cluster local começa a se comportam de forma anormal, pode redefini-lo com a aplicação de tabuleiro de sistema de Gestor de local cluster. Isto removerá o cluster existente e configurar um novo. Tenha em atenção que implementado todas as aplicações e dados associados serão removidos.

## <a name="next-steps"></a>Próximos passos

- [Compreender e resolver problemas de seu cluster com relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizar o seu cluster com o Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md)

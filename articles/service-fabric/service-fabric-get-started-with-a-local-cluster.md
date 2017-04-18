<properties
   pageTitle="Introdução ao implementar e actualizar aplicações no seu cluster local | Microsoft Azure"
   description="Configurar um cluster de serviço ferro local, implementar uma aplicação existente para o mesmo e, em seguida, atualizar a aplicação."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introdução ao implementar e actualizar aplicações no seu cluster local
O SDK do Azure Service ferro inclui um ambiente de desenvolvimento local completo que pode utilizar rapidamente começar a implementar e gerir aplicações num local cluster. Neste artigo, crie um cluster local, implementar uma aplicação existente para o mesmo e, em seguida, atualizar dessa aplicação para uma nova versão, tudo a partir do Windows PowerShell.

> [AZURE.NOTE] Este artigo assume que já [configurar o seu ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Criar um cluster local
Um cluster de serviço ferro representa um conjunto de recursos de hardware que pode implementar aplicações. Normalmente, um cluster é constituído por qualquer parte de cinco para muitos milhares de máquinas. No entanto, o SDK do serviço ferro inclui uma configuração de cluster que pode executar num único computador.

É importante compreender que o cluster local ferro de serviço não é uma emulador ou simulator. É executado o mesmo código de plataforma que se encontra no clusters de múltiplos máquinas. A única diferença é que é executada os processos de plataforma são normalmente distribuir por cinco máquinas num computador de um.

O SDK fornece duas formas de configurar um cluster local: um script do Windows PowerShell e a aplicação de tabuleiro de sistema do Gestor de Cluster Local. Neste tutorial, utilizamos o script do PowerShell.

> [AZURE.NOTE] Se já tiver criado um cluster local ao implementar uma aplicação a partir do Visual Studio, pode ignorar esta secção.


1. Inicie uma nova janela do PowerShell como administrador.

2. Execute o script de configuração de cluster a partir da pasta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    Cluster configuração demorar alguns minutos. Depois de configuração está concluída, deverá ver resultados semelhantes:

    ![Cluster configuração saída][cluster-setup-success]

    Agora está pronto para experimentar a implementar uma aplicação para o seu cluster.

## <a name="deploy-an-application"></a>Implementar uma aplicação
O SDK do serviço ferro inclui um conjunto avançado de quadros e programador ferramenta para criação de aplicações. Se estiver interessado em aprender a criar aplicações no Visual Studio, consulte o artigo [criar a aplicação de serviço ferro primeira no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Neste tutorial, irá utilizamos uma aplicação de exemplo existente (denominada WordCount) para que recomendamos pode concentrar-se a aspetos de gestão da plataforma – incluindo implementação, monitorização e atualização.


1. Inicie uma nova janela do PowerShell como administrador.

2. Importe o módulo serviço ferro SDK PowerShell.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Crie um diretório para armazenar a aplicação que pode transferir e implementar, tal como C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Transferir a aplicação WordCount](http://aka.ms/servicefabric-wordcountapp) para a localização que criou.  Nota: o browser Microsoft Edge guarda o ficheiro com uma extensão *. zip* .  Altere a extensão de ficheiro para *.sfpkg*.

5. Ligar ao local cluster:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Crie uma nova aplicação utilizando o comando de implementação do SDK com um nome e um caminho para o pacote de aplicação.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Se todas as vai bem, deverá ver saída semelhante ao seguinte:

    ![Implementar uma aplicação para o cluster local][deploy-app-to-local-cluster]

7. Para ver a aplicação em ação, execute o browser e navegue para [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Deverá visualizar:

    ![Aplicação implementada IU][deployed-app-ui]

    A aplicação de WordCount é muito simple. Inclui o código para gerar aleatórias cinco carateres "palavras", o que são reencaminhadas, em seguida, à aplicação através do ASP.NET Web API JavaScript do lado do cliente. Um serviço com estado controla o número de palavras contados. Estes são divididos com base no primeiro caráter da palavra. Pode encontrar o código de origem para a aplicação de WordCount na [Introdução amostras](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    A aplicação que são implementados contém a quatro partições. Para que as palavras que comecem com respostas através de G são armazenadas na primeira partição, palavras que comecem com H através de N são armazenadas na segunda partição e assim sucessivamente.

## <a name="view-application-details-and-status"></a>Ver detalhes de aplicação e de estado
Agora que recomendamos tiver implementado a aplicação, vamos ver algumas dos detalhes da aplicação no PowerShell.

1. Implementado todas as aplicações no cluster de consulta:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Partindo do princípio que apenas tiver implementado a aplicação de WordCount, verá algo semelhante ao:

    ![Consulta implementadas todas as aplicações no PowerShell][ps-getsfapp]

2. Vá para o próximo nível consultando o conjunto de serviços que estão incluídos na aplicação WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Listar os serviços para a aplicação no PowerShell][ps-getsfsvc]

    A aplicação é constituída dois serviços – front-end web e o serviço de estado que gere as palavras.

3. Por fim, veja a lista de partições para WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Ver as partições de serviço no PowerShell][ps-getsfpartitions]

    O conjunto de comandos que tiver utilizado, como todos os comandos do PowerShell de ferro de serviço, estão disponíveis para qualquer cluster que também pode ligar ao local ou remota.

    Para saber uma maneira mais visual interagir com o cluster, pode utilizar a ferramenta de serviço ferro Explorer baseada na web ao navegar para [Http://localhost:19080/Explorador](http://localhost:19080/Explorer) no browser.

    ![Ver detalhes de aplicação no serviço ferro Explorer][sfx-service-overview]

    > [AZURE.NOTE] Para saber mais sobre o serviço ferro Explorer, consulte o artigo [visualizar o seu cluster com o Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Atualizar uma aplicação
Serviço ferro fornece actualizações de tempo de inatividade não através da monitorização o estado de funcionamento da aplicação, tal como-descer ao longo do cluster. Vamos executar uma atualização da aplicação WordCount simple.

A nova versão da aplicação agora conta apenas as palavras que começam com uma vogal. Tal como a atualização descer, vemos duas alterações de comportamento da aplicação. Em primeiro lugar, a taxa no qual a contagem de cresce deve tornar mais lento, uma vez que estão a ser contadas palavras menos. Em segundo lugar, dado que a primeira partição tem dois vogais (A e "e") e todas as outras partições contenham apenas um cada, respectiva contagem eventualmente deverá começar a outpace a outras pessoas.

1. [Transferir o pacote de v2 WordCount](http://aka.ms/servicefabric-wordcountappv2) na mesma localização onde transferiu o pacote de v1.

2. Regressar à janela do PowerShell e utilize o comando de atualização do SDK para registar a nova versão do cluster. Em seguida, começar a actualizar o ferro: / WordCount aplicação.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Deverá ver saída no PowerShell começa o seguinte aspeto semelhante a como a atualização.

    ![Atualizar o progresso no PowerShell][ps-appupgradeprogress]

3. Enquanto a atualização é continuar, poderá ser mais fácil monitorizar o respetivo estado a partir do serviço ferro Explorador. Iniciar uma janela do browser e navegue para [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expandir **aplicações** na árvore do lado esquerdo, em seguida, selecione **WordCount**e, por último **ferro: / WordCount**. No separador essentials, irá ver o estado de actualização à medida que avança através de domínios do cluster de atualização.

    ![Atualizar o progresso no serviço ferro Explorer][sfx-upgradeprogress]

    À medida que avança a atualização através de cada domínio, verificações de integridade são executadas para se certificar de que a aplicação estiver a funcionar corretamente.

4. Se voltar a executar a consulta anterior para o conjunto de serviços no ferro: / WordCount aplicação, repare que a versão do WordCountService alterada, mas a versão do WordCountWebService não:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Serviços de aplicação de consulta após atualização][ps-getsfsvc-postupgrade]

    Desta forma, realça como serviço ferro gere actualizações de aplicação. Tocar apenas o conjunto de serviços (ou pacotes de código/configuração dentro desses serviços) que foram alteradas, que torna o processo de atualização de forma mais rápida e mais fiável.

5. Por fim, regresse ao browser para observar o comportamento da nova versão da aplicação. Como esperado, a contagem de decorre mais lentamente e a primeira partição termina com um pouco mais do volume.

    ![Ver a nova versão da aplicação no browser][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Limpar

Antes de concluir, é importante lembrar-se de que o cluster local é real. Aplicações continuam a ser executado em segundo plano até que removê-los.  Dependendo da natureza das suas aplicações, uma aplicação a execução pode demorar até recursos significativos no seu computador. Tem várias opções para gerir aplicações e cluster:

1. Para remover uma aplicação individual e todos os seus dados, execute o seguinte:

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    Em alternativa, elimine a aplicação a partir do menu **AÇÕES** do serviço ferro Explorer ou menu de contexto na vista de lista de aplicações do painel do lado esquerdo.

    ![Eliminar uma aplicação é serviço ferro Explorer][sfe-delete-application]

2. Depois de eliminar a aplicação do cluster, pode anular o registo versões 1.0.0 e 2.0.0 do tipo de aplicação WordCount. A eliminação remove os pacotes da aplicação, incluindo o código de configuração e, a partir do arquivo do cluster da imagem.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    Em alternativa, no Explorador de ferro do serviço, selecione o **Tipo de anular aprovisionamento** para a aplicação.

3. Para encerrar o cluster, mas manter os dados da aplicação e rastreios, clique em **Parar Cluster Local** na aplicação do tabuleiro do sistema.

4. Para eliminar o cluster completamente, clique em **Remover Cluster Local** na aplicação do tabuleiro do sistema. Esta opção irá resultar em outra implementação lenta da próxima vez que prima F5 no Visual Studio. Remova o cluster local apenas se não pretender utilizá-lo para algum tempo ou se for necessário recuperar recursos.

## <a name="1-node-and-5-node-cluster-mode"></a>1 nó e o modo de cluster de nó 5

Quando trabalha com o cluster local para desenvolver aplicações, geralmente encontrá-se a efetuar iterações rápidas de escrever o código, depuração, alterar código, etc depuração. Para ajudar a otimizar este processo, pode executar o cluster local em dois modos: 1 nó ou nó 5. Ambos os modos de cluster têm os seus benefícios.
Modo de cluster de nó 5 permite-lhe trabalhar com um cluster real. Pode testar activação pós-falha cenários, trabalhar com mais instâncias e réplicas dos seus serviços.
Modo de cluster de nó 1 está optimizado para fazer de implementação rápida e registo dos serviços, para o ajudar a validar rapidamente código utilizando o tempo de execução do serviço ferro.

1 nó cluster modo tanto o modo de cluster nó 5 não seja uma emulador ou simulator. É executado o mesmo código de plataforma que se encontra no clusters de múltiplos máquinas.

> [AZURE.NOTE] Esta funcionalidade está disponível no SDK versão 5.2 e acima.

Para alterar o modo de cluster para um cluster de nó 1, utilize o Gestor de Cluster do serviço ferro Local ou utilizar o PowerShell da seguinte forma:

1. Inicie uma nova janela do PowerShell como administrador.

2. Execute o script de configuração de cluster a partir da pasta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    Cluster configuração demorar alguns minutos. Depois de configuração está concluída, deverá ver resultados semelhantes:
    
    ![Cluster configuração saída][cluster-setup-success-1-node]

Se estiver a utilizar o Gestor de Cluster do serviço ferro Local:

![Modo de cluster de parâmetro][switch-cluster-mode]

> [AZURE.WARNING] Ao alterar o modo de cluster, cluster atual é a ser removido do seu sistema e está a ser criado um novo cluster. Os dados que tem de ter armazenados na cluster, serão eliminados quando alterar o modo de cluster.

## <a name="next-steps"></a>Próximos passos
- Agora que tiver implementado e atualizado algumas aplicações previamente concebidas, pode [tentar construir a sua própria no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Todas as ações executadas no cluster local neste artigo podem ser efetuadas num [Azure cluster](service-fabric-cluster-creation-via-portal.md) também.
- A atualização do que são executadas neste artigo foi básica. Consulte a [documentação de atualização](service-fabric-application-upgrade.md) para saber mais sobre a power e flexibilidade de actualizações de serviço ferro.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png

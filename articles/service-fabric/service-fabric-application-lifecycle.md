<properties
   pageTitle="Ciclo de vida de aplicação no serviço ferro | Microsoft Azure"
   description="Descreve desenvolver, implementar, testes, atualizar, manter e remover aplicações de serviço ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida de aplicação de serviço ferro
Como com outras plataformas, uma aplicação no Azure Service ferro normalmente analise as seguintes fases: estrutura, desenvolvimento, testes, implementação, atualizar, manutenção e remoção. Serviço ferro fornece suporte primeira classe para o ciclo de vida de aplicação completo de aplicações na nuvem, a partir do desenvolvimento através de implementação, gestão diária e manutenção para desativar o eventual. O modelo de serviço permite várias funções diferentes participar de forma independente no ciclo de vida de aplicação. Este artigo fornece uma descrição geral das APIs e como são utilizadas por diferentes funções ao longo as fases do ciclo de vida de aplicação do serviço ferro.

## <a name="service-model-roles"></a>Funções de modelo de serviço
São as funções de modelo de serviço:

- **Programador do serviço**: desenvolva modulares e genéricos serviços que podem ser novamente dossier e utilizados em várias aplicações do mesmo tipo ou tipos diferentes. Por exemplo, um serviço de fila pode ser utilizado para criar uma aplicação de bilheteira (suporte técnico) ou uma aplicação de comércio electrónico (carrinho de compras).

- **Programador de aplicação**: Cria aplicações ao integrar uma coleção de serviços que satisfazem determinados requisitos específicos ou cenários. Por exemplo, um site de comércio electrónico poderá integrar "JSON sem estado front-end serviço", "Leilão com estado serviços" e "fila com estado" para criar uma solução auctioning.

- **Administrador da aplicação**: faz com que as decisões sobre a configuração da aplicação (preencher os parâmetros de modelo de configuração), a implementação (mapeamento para recursos disponíveis) e a qualidade do serviço. Por exemplo, um administrador de aplicação decide a região do idioma (em inglês para os Estados Unidos) ou japonês para o Japão, por exemplo da aplicação. Uma aplicação diferente implementada pode ter diferentes definições.

- **Operador**: implementar requisitos especificados pelo administrador da aplicação e de aplicações com base na configuração da aplicação. Por exemplo, um operador disposições e implementa a aplicação e garante que está em execução no Azure. Os operadores monitorizar as informações de estado de funcionamento e o desempenho da aplicação e mantêm a infraestrutura de física conforme necessário.


## <a name="develop"></a>Desenvolver
1. Um *Programador de serviço* desenvolva diferentes tipos de serviços utilizando o modelo de programação [Intervenientes fiáveis](service-fabric-reliable-actors-introduction.md) ou [Serviços fiável](service-fabric-reliable-services-introduction.md) .
2. Um *Programador de serviço* de forma declarativa descreve os tipos de serviço desenvolvidos num ficheiro manifesto de serviço que consiste pacotes de código, configuração e dados de uma ou mais.
3. Um *Programador de aplicação* , em seguida, cria uma aplicação utilizando tipos de serviços diferente.
4. Um *Programador de aplicação* forma declarativa descreve o tipo de aplicação no manifesto de uma aplicação referenciar manifestos serviço dos serviços de constituintes e corretamente substituir e parametrizar diferentes definições de configuração e implementação dos serviços constituintes.

Consulte o artigo [Introdução ao intervenientes fiável](service-fabric-reliable-actors-get-started.md) e [comece a trabalhar com os serviços fiável](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## <a name="deploy"></a>Implementar
1. Um *administrador da aplicação* personaliza o tipo de aplicação a uma aplicação específica seja implementado a um cluster de serviço ferro ao especificar os parâmetros adequados do elemento **ApplicationType** no manifesto da aplicação.

2. Um *operador* carrega o pacote de aplicação para o arquivo de imagem cluster utilizando o [método de **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [cmdlet **ServiceFabricApplicationPackage cópia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicação contém manifesto da aplicação e do conjunto de pacotes de serviço. Serviço ferro implementa aplicações a partir do pacote de aplicação armazenados no arquivo de imagem, pode ser uma loja de Blobs do Azure ou o serviço do sistema ferro de serviço.

3. O *operador* Aprovisiona, em seguida, o tipo de aplicação do cluster de destino a partir do pacote de aplicação carregados utilizando o [método de **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o [cmdlet **Register ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou a [operação de resto **aprovisionar uma aplicação** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Depois de aprovisionar a aplicação, um *operador* inicia a aplicação com os parâmetros fornecidos pelo *administrador da aplicação* utilizando o [método de **CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), o [ **Novo ServiceFabricApplication** cmdlet](https://msdn.microsoft.com/library/azure/mt125913.aspx)ou a [operação de **Criar aplicação** restantes](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Depois de ter sido implementada a aplicação, um *operador* utiliza o [método de **CreateServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), o [ **Novo ServiceFabricService** cmdlet](https://msdn.microsoft.com/library/azure/mt125874.aspx)ou a [operação do resto do **Serviço criar** ](https://msdn.microsoft.com/library/azure/dn707657.aspx) para criar novas instâncias de serviço para a aplicação de baseados nos tipos de serviço disponível.

6. A aplicação agora está em execução no cluster ferro de serviço.

Consulte o artigo [Implementar uma aplicação](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="test"></a>Teste
1. Após a implementar o cluster de desenvolvimento local ou num cluster de teste, um *Programador de serviço* é executado o cenário de teste activação pós-falha incorporados utilizando as classes [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) e [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) ou o [cmdlet **Invocar ServiceFabricFailoverTestScenario** ](https://msdn.microsoft.com/library/azure/mt644783.aspx). O cenário de teste activação pós-falha é executado um serviço especificado através de transições importantes e activações pós-falha para se certificar de que está disponível ainda e a funcionar.

2. O *Programador de serviço* , em seguida, é executado o cenário de teste caos incorporados utilizando as classes [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) e [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) ou o [cmdlet **Invocar ServiceFabricChaosTestScenario** ](https://msdn.microsoft.com/library/azure/mt644774.aspx). O cenário de teste caos originar aleatoriamente vários nó, pacote de código e falhas de réplica para cluster.

3. *Programador do serviço* [comunicação de serviço de serviço de testes](service-fabric-testability-scenarios-service-communication.md) por cenários de teste mover réplicas primárias à volta do cluster de criação.

Para mais informações, consulte [Introdução ao serviço de análise de falhas](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Atualização
1. Um *Programador de serviço* atualiza os serviços constituintes da aplicação com instâncias criadas e/ou correções de erros e fornece uma nova versão do manifesto serviço.

2. Um *Programador de aplicação* substitui e parameterizes as definições de configuração e implementação dos serviços consistentes e fornece uma nova versão do manifesto da aplicação. O programador da aplicação, em seguida, incorpora as novas versões dos manifestos serviço em da aplicação e fornece uma nova versão do tipo de aplicação um pacote de aplicação atualizada.

3. Um *administrador da aplicação* incorpora a nova versão do tipo de aplicação a aplicação de destino ao atualizar os parâmetros adequados.

5. Um *operador* carrega o pacote de aplicação atualizada para o arquivo de imagem cluster utilizando o [método de **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [cmdlet **ServiceFabricApplicationPackage cópia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicação contém manifesto da aplicação e do conjunto de pacotes de serviço.

6. Um *operador* Aprovisiona a nova versão da aplicação do cluster de destino utilizando o [método de **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o [cmdlet **Register ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou a [operação de resto **aprovisionar uma aplicação** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Um *operador* atualiza os a aplicação de destino para a nova versão utilizando o [método de **UpgradeApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), o [cmdlet **ServiceFabricApplicationUpgrade de início** ](https://msdn.microsoft.com/library/azure/mt125975.aspx)ou a [operação de resto **actualizar uma aplicação** ](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Um *operador* verifica o progresso da atualização utilizando o [método de **GetApplicationUpgradeProgressAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), o [cmdlet **Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx)ou a [operação de **Obter aplicação atualizar o progresso da** restantes](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Se for necessário, o *operador* modifica e reaplica os parâmetros da atualização de aplicação atual utilizando o [método de **UpdateApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), o [cmdlet **ServiceFabricApplicationUpgrade de atualização** ](https://msdn.microsoft.com/library/azure/mt126030.aspx)ou a [operação de **Atualizar a aplicação de atualização** restantes](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Se for necessário, o *operador* reverte a atualização de aplicação atual utilizando o [método de **RollbackApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), o [cmdlet **ServiceFabricApplicationRollback de início** ](https://msdn.microsoft.com/library/azure/mt125833.aspx)ou a [operação de **Atualizar a aplicação anulação** restantes](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Serviço ferro atualiza os a aplicação de destino em execução no cluster sem perder a disponibilidade de qualquer um dos seus serviços de constituintes.

Consulte o artigo a [aplicação tutorial de atualização](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## <a name="maintain"></a>Manter
1. Para atualizações do sistema operativo e patches, serviço ferro interfaces com infraestrutura do Azure para garantir a disponibilidade de todas as aplicações executadas no cluster.

2. Para atualizações e patches para a plataforma ferro de serviço, o serviço ferro actualiza propriamente dito sem perder a disponibilidade de qualquer uma das aplicações em execução no cluster.

3. Um *administrador da aplicação* aprova a adição ou remoção de nós de um cluster após analisar dados de utilização da capacidade históricas e prevista procura futura.

4. Um *operador* adiciona e remove nós especificado pelo *administrador da aplicação*.

5. Quando novos nós são adicionados à ou nós existentes são removidas do cluster, serviço ferro automaticamente carga-saldos de executar as aplicações em todos os nós num cluster para alcançar um desempenho ideal.

## <a name="remove"></a>Remover
1. Um *operador* pode eliminar uma instância específica de um serviço em execução no cluster sem remover a aplicação completa utilizando o [método de **DeleteServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), o [cmdlet **Remover ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt126033.aspx)ou a [operação de **Serviço eliminar** restantes](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Também pode eliminar um *operador de* uma instância da aplicação e todos os seus serviços utilizando o [método de **DeleteApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), o [cmdlet **Remover ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125914.aspx)ou a [operação de **Eliminar aplicação** resto](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Assim que a aplicação e serviços foram interrompidas, o *operador* pode anular o aprovisionamento o tipo de aplicação utilizando o [método de **UnprovisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), o [cmdlet **Unregister ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125885.aspx)ou a [operação de **Anular uma aplicação de aprovisionamento** restantes](https://msdn.microsoft.com/library/azure/dn707671.aspx). O tipo de aplicação a anular o aprovisionamento não remove o pacote de aplicação do ImageStore. Tem de remover manualmente o pacote de aplicação.

4. Um *operador* remove o pacote de aplicação de ImageStore utilizando o [método de **RemoveApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) ou o [cmdlet **ServiceFabricApplicationPackage remover** ](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Consulte o artigo [Implementar uma aplicação](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como desenvolver, testar e gerir aplicações de serviço ferro e serviços, consulte o artigo:

- [Fiáveis intervenientes](service-fabric-reliable-actors-introduction.md)
- [Serviços fiáveis](service-fabric-reliable-services-introduction.md)
- [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)
- [Atualização de aplicação](service-fabric-application-upgrade.md)
- [Descrição geral de Testability](service-fabric-testability-overview.md)
- [Exemplo de ciclo de vida de aplicação baseada no resto](service-fabric-rest-based-application-lifecycle-sample.md)

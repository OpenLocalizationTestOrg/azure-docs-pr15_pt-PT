<properties
   pageTitle="Resolução de problemas com o rastreio de eventos | Microsoft Azure"
   description="Os problemas mais comuns que encontrou ao implementar os serviços no Microsoft Azure Service ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Resolver problemas comuns quando implementar serviços no ferro de serviço do Azure

Quando estiver a executar serviços no seu computador de programador, é fácil de utilizar [Ferramentas de depuração de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Para clusters remotos, [relatórios de estado de funcionamento](service-fabric-view-entities-aggregated-health.md) são sempre um bom local para começar. São as formas mais fácil de aceder a estes relatórios através do PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Este artigo assume que está a depurar um cluster remoto e tenha noções básicas sobre como utilizar um destas ferramentas.

##<a name="application-crash"></a>Falha de aplicação
O "partição for inferior a contagem de réplica ou instância de destino" relatório é uma boa indicação de que o seu serviço está a falhar. Para saber onde o serviço está a falhar leva um pouco mais inquérito. Quando o seu serviço está a ser executado em escala, o melhor amigo será um conjunto de bem pensado rastreios.  Sugerimos tenta [Azure diagnósticos](service-fabric-diagnostics-how-to-setup-wad.md) para esses rastreios de recolha e utilização de uma solução como [Procurar flexível](service-fabric-diagnostic-how-to-use-elasticsearch.md) para visualizar e procurar os traços.

![Estado de funcionamento do SFX partição](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Durante a inicialização do serviço ou ator
Quaisquer exceções antes do tipo de serviço está inicializado irão provocar o processo para falha de sistema. Para estes tipos de falhas, o registo de eventos de aplicação irá mostrar o erro do seu serviço.
Estas são as exceções mais comuns para ver antes do serviço é inicializado.

***System.IO.FileNotFoundException***

Este erro é frequentemente devido a assemblagem dependências em falta. Verifique a propriedade CopyLocal no Visual Studio ou da cache de assemblagem global para o nó.

***System.Runtime.InteropServices.COMException***
 *na System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Isto indica que o nome do tipo de serviço registado não corresponder o manifesto de serviço.

[Diagnósticos do Azure](service-fabric-diagnostics-how-to-setup-wad.md) pode ser configurado para carregar o registo de eventos de aplicação para todos os seus nós automaticamente.

###<a name="runasync-or-onactivateasync"></a>RunAsync() ou OnActivateAsync()
Se a falha acontece durante a inicialização ou em execução do seu tipo de serviço registados ou ator, a exceção irá capturada por Azure Service ferro. Pode ver estas dos fornecedores Origemdoevento detalhados na secção "Passos seguintes".

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre diagnósticos existentes fornecidos pela ferro de serviço:

* [Diagnósticos do fiáveis intervenientes](service-fabric-reliable-actors-diagnostics.md)
* [Fiáveis diagnósticos de serviços](service-fabric-reliable-services-diagnostics.md)

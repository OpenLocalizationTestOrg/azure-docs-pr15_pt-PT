<properties
   pageTitle="Configurar o seu ambiente de desenvolvimento | Microsoft Azure"
   description="Instale o tempo de execução, SDK e ferramentas e criar um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto para criar aplicações."
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
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Preparar o seu ambiente de desenvolvimento

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [X](service-fabric-get-started-mac.md)

 Para criar e executar [as aplicações do Azure Service ferro] [ 1] no seu computador de desenvolvimento, instale o runtime, SDK e ferramentas. Também precisa permitir a execução dos scripts do Windows PowerShell incluído no SDK.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistemas operativos suportados
As seguintes versões de sistemas operativos são suportadas para o desenvolvimento:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 inclui apenas o Windows PowerShell 2.0 por predefinição. Os cmdlets do PowerShell do serviço ferro requer PowerShell 3.0 ou superior. Pode [Transferir o Windows PowerShell 5.0] [ powershell5-download] a partir do Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Instalar as ferramentas, SDK e runtime

O instalador do plataforma Web oferece duas configurações para o desenvolvimento de ferro de serviço:

- [Instalar o tempo de execução do serviço ferro, SDK e ferramentas para Visual Studio 2015 (requer o Visual Studio 2015 Update 2 ou posterior)][full-bundle-vs2015]
- [Instalar o tempo de execução do serviço ferro e apenas SDK (ferramentas de Visual Studio)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Permitir a execução de script do PowerShell

Serviço ferro utiliza scripts do Windows PowerShell para criar um cluster de desenvolvimento local e para a implementação de aplicações a partir do Visual Studio. Por predefinição, o Windows bloqueia estes scripts a execução. Para ativá-las, tem de modificar a política de execução do PowerShell. Abra o PowerShell como administrador e introduza o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Próximos passos
Agora que tiver terminado de definição do seu ambiente de desenvolvimento, começar a criação e execução de aplicações.

- [Criar uma aplicação de serviço ferro primeira no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Saiba como implementar e gerir aplicações no seu cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [Saiba mais sobre os modelos de programação: serviços fiáveis e intervenientes fiável](service-fabric-choose-framework.md)
- [Consulte os exemplos de código do serviço ferro no GitHub](https://aka.ms/servicefabricsamples)
- [Visualizar o seu cluster através do Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md)
- [Siga o caminho de formação do serviço ferro para obter uma introdução abrangente para a plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página de campanha ferro de serviço"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ligação VS WebPI de 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ligação Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Ligação de SDK WebPI Core"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

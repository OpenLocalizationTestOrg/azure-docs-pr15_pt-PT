<properties 
   pageTitle="Depuração de serviços em nuvem Azure | Microsoft Azure"
   description="Depuração de serviços em nuvem Azure"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>Depuração de serviços em nuvem

Pode utilizar abordagens diferentes para depurar uma aplicação do Azure utilizando as ferramentas do Azure para o Microsoft Visual Studio e o SDK do Azure:

- Pode depurar uma aplicação do Azure a partir do Visual Studio quando está a desenvolvê-lo, tal como faria com qualquer aplicação Visual c# ou do Visual Basic. Para mais informações, consulte o artigo [depurar o seu serviço de nuvem no seu computador local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Pode utilizar o Azure diagnóstico para registar informações detalhadas de execução de código num funções, se estiver a executar as funções no ambiente de desenvolvimento ou no Azure. Para mais informações, consulte o artigo [recolher dados utilizando os diagnósticos do Azure de registo](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Se estiver a utilizar o Visual Studio Enterprise para escrever funções direcionadas para o .NET Framework 4 ou o 4,5 Framework .NET, pode ativar a IntelliTrace ao tempo que implementar um serviço em nuvem Azure a partir do Visual Studio. IntelliTrace fornece um registo que pode utilizar com o Visual Studio para depurar a sua aplicação como se estivesse em execução no Azure. Para mais informações, consulte o artigo [depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Pode ativar a depuração remota no seu serviços em nuvem ao tempo quando implementar o serviço de nuvem do Visual Studio. Se optar por Ativar depuração remota para uma implementação, serviços de depuração remoto estão instalados nas máquinas virtuais que executar cada instância de função. Estes serviços, tais como msvsmon.exe, não vai afetar o desempenho ou resultar em custos extra. Para mais informações, consulte o artigo [depuração de um serviço na nuvem no Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).




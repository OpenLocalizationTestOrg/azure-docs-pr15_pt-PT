<properties 
pageTitle="Processar eventos de ciclo de vida do serviço na nuvem | Microsoft Azure" 
description="Saiba como os métodos de ciclo de vida de uma função de serviço em nuvem podem ser utilizados no .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o ciclo de vida de uma função Web ou de trabalho no .NET

Quando cria uma função de trabalho, pode expande a classe de [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) que fornece métodos que pode substituir que lhe permitem respondem a eventos de ciclo de vida. Para funções de web esta classe é opcional, para tem utilizá-la para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Expandir a classe de RoleEntryPoint

A classe de [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) inclui métodos que são chamados pelo Azure quando- **é iniciado**, **é executado**ou **deixa de** uma função web ou de trabalho. Opcionalmente, pode substituir estes métodos para gerir inicialização de função, sequências de encerramento de funções ou o tópico da execução da função. 

Quando expandir **RoleEntryPoint**, deve ter em atenção os seguintes comportamentos dos métodos:

-   Os métodos de [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) e [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) devolvem um valor booleano, para que fique possível devolver **Falso** destes métodos.

     Se o seu código devolve **Falso**, o processo de função é terminado inesperadamente, sem executar qualquer sequência de encerramento que tiver no local. Em geral, deverá evitar devolver **Falso** do método **OnStart** .
     
-   Qualquer exceção não identificada dentro de uma sobrecarga de um método de **RoleEntryPoint** é tratada como uma exceção não processada.

     Caso ocorra uma exceção dentro de um dos métodos ciclo de vida, Azure irá elevar o evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) e, em seguida, o processo é terminado. Após ter sido tomado offline ao seu cargo, vai ser reiniciado por Azure. Quando ocorre uma exceção não processada, o evento [Parar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) não é elevado e o método de **OnStop** não denomina-se.

Se ao seu cargo não for iniciado ou é Reciclagem entre o inicialização, ocupado e os Estados de paragem, poderá deitar seu código uma exceção não processada dentro de um dos eventos do ciclo de vida reinicia a função de cada vez. Neste caso, utilize o evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) para determinar a causa da exceção e processar-corretamente. Também poderá ser devolver ao seu cargo a partir do método [Executar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , o que faz com que a função reiniciar. Para mais informações sobre os Estados de implementação, consulte o artigo [Comuns problemas que causam funções de administrador a Reciclagem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Se estiver a utilizar as **Ferramentas do Azure para o Microsoft Visual Studio** para desenvolver a sua aplicação, os modelos do project função Expandir automaticamente a classe de **RoleEntryPoint** por si, nos ficheiros *WebRole.cs* e *WorkerRole.cs* .

## <a name="onstart-method"></a>Método de OnStart

O método de **OnStart** chama-se-á quando a instância de função estiver online ao Azure. Enquanto o código de OnStart está em execução, a instância de função está marcada como **ocupado** e não tráfego externo será direcionado para o-pelo balanceador de carga. Pode substituir este método para executar o trabalho de inicialização, tal como implementar processadores de eventos e iniciar [Diagnósticos do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** devolve **true**, a instância é inicializada com êxito e Azure chama o método de **RoleEntryPoint.Run** . Se **OnStart** devolve **Falso**, a função termina imediatamente, sem executar qualquer sequências encerramento planeado.

Exemplo de código que se segue mostra como substituir o método de **OnStart** . Este método configura e inicia um monitor de diagnóstico quando a instância da função é iniciado e configura uma transferência de registo de dados para uma conta de armazenamento:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Método OnStop

O método de **OnStop** é chamado após uma instância de função foi colocada em offline ao Azure e antes do processo de sai. Pode substituir este método para ligar código necessário para a instância de função para encerrada.

> [AZURE.IMPORTANT] Código em execução no método **OnStop** tem um período de tempo limitado para concluir quando chama-se por razões que não seja um encerramento iniciado pelo utilizador. Após este período de tempo decorrido, o processo for terminado, para que deve certificar-se de que código no podem método **OnStop** executar rapidamente ou tolera não a ser executado para a conclusão. O método de **OnStop** é chamado depois do evento **Parar** é elevado.


## <a name="run-method"></a>Executar método

Pode substituir o método **Executar** para implementar um tópico de execução longa para a instância de função.

Substituir o método **Executar** não é necessário; a implementação predefinida é iniciado um tópico que está suspenso uma eternidade. Se a substituir o método de **Executar** , o seu código deve bloquear indefinidamente. Se o método **Executar** devolve, a função é automaticamente correctamente reciclada; por outras palavras, Azure eleva o evento **Parar** e chama o método de **OnStop** para que as sequências de encerramento podem ser executadas antes da função é disponibilizada offline.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementar os métodos de ciclo de vida do ASP.NET para uma função da web

Pode utilizar os métodos de ciclo de vida do ASP.NET, para além das fornecidas pela classe **RoleEntryPoint** , para gerir inicialização e sequências de encerramento para uma função de web. Isto pode ser útil para fins de compatibilidade se são migrar uma aplicação do ASP.NET existente para Azure. Os métodos de ciclo de vida do ASP.NET são chamados a partir dos métodos **RoleEntryPoint** . O **aplicação\_iniciar** método é chamado após o método de **RoleEntryPoint.OnStart** termina. O **aplicação\_fim** método denomina-se antes do método **RoleEntryPoint.OnStop** é chamado.

## <a name="next-steps"></a>Próximos passos
Saiba como [criar um pacote de serviço de nuvem](cloud-services-model-and-package.md).
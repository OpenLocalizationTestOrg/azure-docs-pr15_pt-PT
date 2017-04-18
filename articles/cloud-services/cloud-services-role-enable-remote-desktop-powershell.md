<properties
pageTitle="Ativar a ligação de ambiente de trabalho remoto para uma função no Azure serviços em nuvem através do PowerShell"
description="Como configurar a sua aplicação de serviço de nuvem azure através do PowerShell para permitir ligações de ambiente de trabalho remotas"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Ativar a ligação de ambiente de trabalho remoto para uma função no Azure serviços em nuvem através do PowerShell

>[AZURE.SELECTOR]
- [Azure portal clássico](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Ambiente de trabalho remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a aplicação enquanto estiver em execução.

Este artigo descreve como ativar o ambiente de trabalho remoto no seu funções de serviço de nuvem através do PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para deste artigo. PowerShell utiliza a extensão de ambiente de trabalho remoto para que pode ativar o ambiente de trabalho remoto após a aplicação é implementada.


## <a name="configure-remote-desktop-from-powershell"></a>Configurar o ambiente de trabalho remoto do PowerShell

O cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) permite-lhe ativar o ambiente de trabalho remoto em funções especificadas ou todas as funções da sua implementação do serviço de nuvem. O cmdlet permite-lhe especificar o nome de utilizador e palavra-passe para o utilizador de ambiente de trabalho remoto através do parâmetro de *credencial* aceita um objeto de PSCredential.

Se estiver a utilizar o PowerShell forma interativa, pode definir o objeto PSCredential facilmente ao contactar o cmdlet [Get-credenciais](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Este comando apresenta uma caixa de diálogo, permitindo-lhe introduzir o nome de utilizador e palavra-passe para o utilizador remoto de uma forma segura.

Uma vez que o ajuda a PowerShell em cenários de automatização, também pode configurar o objeto **PSCredential** de uma forma que não necessita de interação do utilizador. Em primeiro lugar, tem de configurar uma palavra-passe segura. Comece por especificar uma palavra-passe de texto simples convertê-la para uma cadeia segura utilizando [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx). A seguir tem converter uma cadeia de padrão encriptada utilizando [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx)esta cadeia segura. Agora pode guardar esta cadeia padrão encriptada para um ficheiro com [Conteúdo do conjunto](https://technet.microsoft.com/library/ee176959.aspx).

Também pode criar um ficheiro de palavra-passe segura para que não tem de escrever a palavra-passe cada vez. Além disso, um ficheiro de palavra-passe segura é melhor do que um ficheiro de texto simples. Utilize o PowerShell seguinte para criar um ficheiro de palavra-passe segura:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Quando definir a palavra-passe, certifique-se de que cumpre os [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).

Para criar o objeto de credenciais do ficheiro de palavra-passe segura, tem de ler o conteúdo do ficheiro e convertê-los novamente para uma cadeia segura utilizando [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) também aceita um parâmetro de *expiração* , o que especifica uma **data/hora** na qual a conta de utilizador expira. Por exemplo, pode definir a conta a expirar alguns dias desde a data e hora atuais.

Este exemplo PowerShell mostra-lhe como configurar a extensão de ambiente de trabalho remoto num serviço na nuvem:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcionalmente, também pode especificar os ranhura de implementação e as funções que pretende ativar o ambiente de trabalho remoto. Se não forem especificados estes parâmetros, o cmdlet permite ambiente de trabalho remoto todas as funções na ranhura de implementação de **produção** .

A extensão de ambiente de trabalho remoto está associada uma implementação. Se criar uma nova implementação do serviço, tem de ativar o ambiente de trabalho remoto esse implementação. Se pretender sempre para ambiente de trabalho remoto ativado, em seguida, deverá considerar integrar o seu fluxo de trabalho de implementação os scripts de PowerShell.


## <a name="remote-desktop-into-a-role-instance"></a>Ambiente de trabalho remoto para uma instância de função
O cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) é utilizada para ambiente de trabalho remoto para uma instância de função específica do seu serviço de nuvem. Pode utilizar o parâmetro *LocalPath* para transferir o ficheiro RDP localmente. Ou pode utilizar o parâmetro de *Iniciação* à iniciação diretamente a caixa de diálogo ligação de ambiente de trabalho remoto para aceder a instância de função do serviço de nuvem.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão de ambiente de trabalho remoto está ativado num serviço
Apresenta o cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) que ambiente de trabalho remoto está activada ou desactivada numa implementação do serviço. O cmdlet devolve o nome de utilizador para o utilizador de ambiente de trabalho remoto e as funções de que a extensão de ambiente de trabalho remoto está ativada para. Por predefinição, isto acontece no ranhura implementação e pode optar por utilizar a transição ranhura em vez disso.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover a extensão de ambiente de trabalho remoto de um serviço
Se já tiver ativado a extensão de ambiente de trabalho remoto numa implementação e precisar de atualizar as definições de ambiente de trabalho remoto, primeiro, remova a extensão. E voltar a ativá-la com as novas definições. Por exemplo, se pretender definir uma nova palavra-passe para a conta de utilizador remoto ou conta expirou. Este procedimento é necessária no implementações existentes, que tenham a extensão de ambiente de trabalho remoto ativada. Para implementações novas, pode simplesmente aplicar a extensão diretamente.

Para remover a extensão de ambiente de trabalho remoto de implementação, pode utilizar o cmdlet [AzureServiceRemoteDesktopExtension remover](https://msdn.microsoft.com/library/azure/dn495280.aspx) . Opcionalmente, também pode especificar a ranhura de implementação e a função a partir do qual pretende remover a extensão de ambiente de trabalho remoto.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] Para remover completamente a configuração de extensão, deverá contactar o cmdlet *Remover* com o parâmetro **UninstallConfiguration** .
>
>O parâmetro **UninstallConfiguration** desinstala qualquer configuração de extensão que é aplicada ao serviço. Cada configuração de extensão está associada com a configuração do serviço. Chamar o cmdlet *Remover* sem **UninstallConfiguration** dissocia de <mark>implementação</mark> da configuração da extensão, assim eficazmente a remover a extensão. No entanto, a configuração de extensão permanece associada com o serviço.



## <a name="additional-resources"></a>Recursos adicionais

[Como configurar os serviços em nuvem](cloud-services-how-to-configure.md)

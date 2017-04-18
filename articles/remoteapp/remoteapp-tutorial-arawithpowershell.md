<properties
   pageTitle="Utilizar cmdlets do PowerShell com o Azure RemoteApp | Microsoft Azure"
   description="Saiba como utilizar cmdlets do Windows PowerShell no Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Utilizar cmdlets do Windows PowerShell com o RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

 Pode utilizar os cmdlets do PowerShell do Azure RemoteApp para administrar e manter as suas coleções de. Utilize as seguintes informações para começar a utilizar.

## <a name="get-the-cmdlets"></a>Obter os cmdlets 
-------------
Transferir pela primeira vez os cmdlets do Azure Powershell [aqui](http://go.microsoft.com/?linkid=9811175), RemoteApp cmdlets estão incluídos no mesmo. 

Consulte o artigo [Ajuda do cmdlet RemoteApp do Azure](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurar os cmdlets do Azure para utilizar a sua subscrição
------------------
Siga [Este guia](../powershell-install-configure.md) para poder utilizar os cmdlets contra a sua subscrição do Azure.

Pode utilizar estes passos para começar a trabalhar rapidamente:

1.  Transfira e instale os [cmdlets do Azure PowerShell](http://go.microsoft.com/?linkid=9811175).
2.  Inicie o Microsoft Azure PowerShell.
3.  Execute **Adicionar AzureAccount** para autenticar à sua subscrição do Azure. Quando lhe for pedido, introduza o mesmo nome de utilizador e palavra-passe que utiliza para iniciar sessão no Azure portal.  
4.  Execute **Get-AzureSubscription** para as subscrições associadas à sua conta de utilizador da lista. 
5.  Execute **Selecione AzureSubscription** e especifique o nome da subscrição ou o ID para utilizar na consola do PowerShell.

Parabéns, a sua consola do Azure PowerShell estiver configurado e pronto a utilizar. Tenha em atenção que terá os passos 2 a 5 para repeate sempre que inicia a consola do Azure PowerShell.  

## <a name="create-a-cloud-collection"></a>Criar uma coleção de nuvem
--------------------
É muito simple, execute o seguinte comando:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

O comando acima publica automaticamente aplicações do Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio e Word).

Criação de coleções de sites pode demorar 30 minutos ou mais tempo a concluir. Por conseguinte, este comando devolver um ID de rastreio que pode utilizar da seguinte forma:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Depois de concluir o processo de coleções de sites, pode adicionar utilizadores à coleção de com o seguinte comando:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

E já está! Esse utilizador deverá conseguir ligar à aplicação utilizando o cliente do Azure RemoteApp encontrado [aqui](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Cmdlets disponíveis
Existem muitas outras comandos que temos, a documentação para os mesmos vai ser chegar pouco tempo:

Cmdlets de coleções de RemoteApp básicos: 

- Novo AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Definir AzureRemoteAppCollection
- AzureRemoteAppCollection de actualização
- Remover AzureRemoteAppCollection
- AzureRemoteAppUser adicionar
- Get-AzureRemoteAppUser
- Remover AzureRemoteAppUser
- Get-AzureRemoteAppSession
- AzureRemoteAppSession desligar
- AzureRemoteAppSessionLogoff invocar
- Enviar AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- AzureRemoteAppProgram publicar
- AzureRemoteAppProgram anular a publicação
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Cmdlets de rede virtual RemoteApp:

- Novo AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Definir AzureRemoteAppVNet
- Remover AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get - AzureRemoteAppVpnDeviceConfigScript
- Repor AzureRemoteAppVpnSharedKey

Cmdlets de imagem do modelo de RemoteApp:

- Novo AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Mudar o nome AzureRemoteAppTemplateImage
- Remover AzureRemoteAppTemplateImage

Cmdlets outras RemoteApp:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Definir AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 

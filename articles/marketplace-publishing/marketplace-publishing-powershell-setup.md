<properties
   pageTitle="Configurar o PowerShell para criar uma VM do Marketplace | Microsoft Azure"
   description="Instruções para configurar o PowerShell do Azure e utilizá-lo como um processo opcional fluam para criar VM imagens para implementar e vendem no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar o Azure PowerShell para criar uma oferta para Azure Marketplace
Para obter informações detalhadas sobre como configurar o PowerShell no Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Uma abordagem simples é utilizar o método de certificado, transferências e importa um certificado necessário para autenticação. Para obter o certificado conforme seja necessário, utilize o cmdlet **Get-AzurePublishSettingsFile** . Quando lhe for pedido, guarde o ficheiro. Para importar o certificado para uma sessão do PowerShell, utilize o cmdlet **AzurePublishSettingsFile importar** .

Para configurar e armazenar as definições de subscrição do Microsoft Azure comuns para a sessão do PowerShell, utilize os cmdlets **AzureSubscription conjunto** e **Selecione AzureSubscription** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

O primeiro comando associa uma conta de armazenamento predefinida com a subscrição (necessária para algumas operações de aprovisionamento VM).  O segundo faz com que a subscrição a que atual (reconhecida pelo outras cmdlets).

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
- [Criar uma imagem de máquina virtual do Marketplace](marketplace-publishing-vm-image-creation.md)

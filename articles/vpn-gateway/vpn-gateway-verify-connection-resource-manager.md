<properties
   pageTitle="Certifique-se de uma ligação de gateway | Microsoft Azure"
   description="Este artigo mostra-lhe como verificar a ligação do gateway no modelo de implementação de Gestor de recursos"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Certifique-se de uma ligação de gateway

Pode verificar a sua ligação de gateway no algumas maneiras diferentes. Este artigo mostrar-lhe como verificar o estado de uma ligação de gateway Gestor de recursos utilizando o portal do Azure e através do PowerShell.


## <a name="verify-using-powershell"></a>Verifique a utilizar o PowerShell

Terá de instalar a versão mais recente dos cmdlets do PowerShell do Azure Gestor de recursos. Para obter informações sobre como instalar os cmdlets do PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Para mais informações sobre como utilizar cmdlets do Gestor de recursos, consulte o artigo [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Passo 1: Inicie sessão na sua conta Azure

1. Abra o seu consola do PowerShell com privilégios elevados e ligar à sua conta.

        Login-AzureRmAccount

2. Verifique as subscrições para a conta.

        Get-AzureRmSubscription 

3. Especifique a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Passo 2: Verifique a sua ligação


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Verifique a utilizar o portal do Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Próximos passos

- Pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.


<properties
   pageTitle="Começar a criar uma Internet opostas Balanceador de carga no modo clássico através do PowerShell | Microsoft Azure"
   description="Saiba como criar um opostas Balanceador de carga no modo clássico através do PowerShell da Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Começar a criar um opostas Balanceador de carga (clássico) no PowerShell da Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Pode também [saber como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure da Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Configurar o Balanceador de carga através do PowerShell

Para configurar um balanceador de carga através do powershell, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../../articles/powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.


2. Depois de criar uma máquina virtual, pode utilizar os cmdlets do PowerShell para adicionar um balanceador de carga para uma máquina de virtual dentro do serviço na nuvem mesmo.

No exemplo seguinte irá adicionar que um balanceador de carga definir denominado "webfarm" para o cloud serviço "mytestcloud" (ou myctestcloud.cloudapp.net), adicionar os pontos finais para o Balanceador de carga a máquinas virtuais com o nome "web1" e "web2". O Balanceador de carga recebe o tráfego de rede na porta 80 e carregar saldos entre as máquinas virtuais definidas pelo ponto final local (no porta maiúsculas e minúsculas 80) utilizando TCP.


### <a name="step-1"></a>Passo 1
Criar um ponto final de balanceamento de carga para a primeira VM "web1"

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Passo 2

Criar outro ponto final para a segunda VM "web2" utilizando o mesmo nome de conjunto de Balanceador de carga

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Remover uma máquina virtual do Balanceador de carga

Pode utilizar AzureEndpoint remover para remover um ponto final de máquina virtual do Balanceador de carga

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Próximos passos

Também pode [começar a criar um balanceador de carga interno](load-balancer-get-started-ilb-classic-ps.md) e qual o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede do especific carregamento Balanceador de configurar.

Se a aplicação necessitar manter as ligações vivo para servidores atrás de um balanceador de carga, é possível compreender mais sobre [as definições de tempo limite TCP idle para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Irá ajudar a para saber mais sobre o comportamento de ligação idle quando estiver a utilizar o Azure Balanceador de carga.


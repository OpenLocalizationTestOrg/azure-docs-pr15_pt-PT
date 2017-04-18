<properties
   pageTitle="Configurar o Balanceador de carga para SQL sempre no | Microsoft Azure"
   description="Configurar Balanceador de carga para trabalhar com SQL sempre e como tirar partido do powershell para criar Balanceador de carga para a implementação de SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-load-balancer-for-sql-always-on"></a>Configurar o Balanceador de carga para SQL sempre no

Grupos de disponibilidade do SQL Server AlwaysOn agora pode ser executados com ILB. Grupo de disponibilidade é solução de principal programa de protecção do SQL Server para recuperação de disponibilidade e falhas alta. O serviço de grupo de disponibilidade escuta permite o aplicações cliente para forma totalmente integrada ligar à réplica principal, independentemente do número de réplicas na configuração.

O nome de escuta (DNS) é mapeado para um endereço IP balanceamento de carga e Balanceador de carga do Azure encaminha o tráfego recebido apenas o servidor principal no conjunto de réplicas.

Pode utilizar o ILB suporte para os pontos finais do SQL Server AlwaysOn (escuta). Agora que tenha controlo sobre a acessibilidade da escuta e pode escolher o endereço IP balanceamento de carga a partir de uma sub-rede específica na sua rede Virtual (VNet).

Ao utilizar ILB no escuta, o ponto final do SQL server (por exemplo, servidor = tcp:ListenerName, 1433; base de dados = DatabaseName) está acessível apenas por:

- Serviços e VMs na mesma rede Virtual
- Serviços e VMs da rede ligada no local
- Serviços e VMs a partir do VNets interligados

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 - AlwaysOn SQL configurados com Balanceador de carga de acesso à Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Adicionar balanceador de carga interna para o serviço

1. O exemplo seguinte, irá configurar uma rede Virtual que contém uma sub-rede designado por sub-rede-1:

        Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

2. Adicionar pontos finais de balanceamento de carga para ILB no cada VM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
        DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    No exemplo acima, tem de 2 VM denominados "sqlsvc1" e "sqlsvc2" executar "Sqlsvc" do serviço na nuvem. Depois de criar o ILB com parâmetro "DirectServerReturn", adicione os pontos finais de balanceamento de carga para ILB para permitir que SQL configurar listeners para os grupos de disponibilidade.

Para mais informações sobre SQL AlwaysOn, consulte o artigo [configurar um balanceador de carga interna para um grupo de Disponibilidade AlwaysOn no Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Consulte também

[Começar a configurar uma opostas Balanceador de carga da Internet](load-balancer-get-started-internet-arm-ps.md)

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)

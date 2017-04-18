<properties
   pageTitle="Como gerir listas de controlo de acesso (ACL) para os pontos finais ao utilizar o PowerShell"
   description="Saiba como gerir juntamente com o PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Como gerir listas de controlo de acesso (ACL) para os pontos finais ao utilizar o PowerShell

Pode criar e gerir o controlo de acesso de rede listas (ACL) para os pontos finais ao utilizar o Azure PowerShell ou no Portal de gestão. Neste tópico, encontrará procedimentos para tarefas comuns de ACL que pode concluir através do PowerShell. Para a lista dos cmdlets do Azure PowerShell consulte [Cmdlets de gestão de Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Para mais informações sobre ACL, consulte o artigo [o que é uma lista de controlo de acesso de rede (ACL)?](virtual-networks-acl.md). Se pretender gerir o seu ACL utilizando o Portal de gestão, consulte o artigo [como definir o pontos finais para uma Máquina Virtual](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Faça a gestão de ACL de rede ao através do PowerShell do Azure

Pode utilizar os cmdlets do Azure PowerShell para criar, remover e configurar (definido) controlo de acesso de rede listas (ACL). Vamos incluiu alguns exemplos de algumas formas do que pode configurar uma ACL através do PowerShell.

Para obter uma lista completa dos cmdlets do ACL PowerShell, pode utilizar qualquer um dos seguintes procedimentos:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Criar uma ACL de rede com regras que permitam acesso a partir de uma sub-rede remota

O exemplo abaixo ilustra uma forma de criar uma nova ACL que contém regras. Este ACL, em seguida, é aplicado a um ponto final de máquina virtual. As regras ACL no exemplo abaixo irão permitir o acesso a partir de uma sub-rede remota. Para criar uma nova ACL de rede com regras de licença para uma sub-rede remota, abra uma Azure o ISE do PowerShell. Copiar e colar o script abaixo, configurar o script com os seus próprios valores e, em seguida, execute o script.

1. Crie o novo objecto ACL de rede.

        $acl1 = New-AzureAclConfig

1. Defina uma regra que permita o acesso a partir de uma sub-rede remota. No exemplo abaixo, defina regra *100* (que tem a prioridade alta sobre regra 200 e superior) para permitir o acesso de *10.0.0.0/8* sub-rede remota para o ponto final de máquina virtual. Substitua os valores com os seus próprios requisitos de configuração. O nome "SharePoint ACL config" deve ser substituído com o nome amigável ao qual pretende ligar esta regra.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. Para regras adicionais, repita o cmdlet, substituir os valores com os seus requisitos de configuração. Certifique-se de que altere a regra número ordem para refletir a ordem pela qual pretende que as regras de ser aplicada. O número de regra inferior tem precedência sobre o número mais alto.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. Em seguida, pode criar um novo ponto final (adicionar) ou configurar a ACL para um ponto final existente (definido). Neste exemplo, iremos adicionar um novo ponto final máquina virtual denominado "web" e actualizar o ponto final de máquina virtual com as definições de ACL.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. Em seguida, combinar os cmdlets e executar o script. Neste exemplo, os cmdlets combinados teria o seguinte aspeto:

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Remover uma regra de ACL de rede que permita o acesso a partir de uma sub-rede remota

O exemplo abaixo ilustra uma forma para remover uma regra ACL de rede.  Para remover uma regra de ACL de rede com regras de licença para uma sub-rede remota, abra uma Azure o ISE do PowerShell. Copiar e colar o script abaixo, configurar o script com os seus próprios valores e, em seguida, execute o script.

1. Primeiro passo é obter o objeto de ACL de rede para o ponto final de máquina virtual. Em seguida, irá remover a regra ACL. Neste caso, podemos são remoção do mesmo por ID da regra. Isto vai remover apenas o ID da regra 0 da ACL. Não-remove o objeto ACL do ponto final de máquina virtual.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Em seguida, tem de aplicar o objeto de ACL de rede para o ponto final de máquina virtual e atualizar a máquina virtual.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Remover uma ACL de rede de um ponto final de máquina virtual

Em certos cenários, poderá pretender remover um objeto de ACL de rede de um ponto final de máquina virtual. Para fazê-lo, abra uma Azure o ISE do PowerShell. Copiar e colar o script abaixo, configurar o script com os seus próprios valores e, em seguida, execute o script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Próximos passos

[O que é uma lista de controlo de acesso de rede (ACL)?](virtual-networks-acl.md)

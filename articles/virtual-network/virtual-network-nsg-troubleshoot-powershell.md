<properties 
   pageTitle="Resolver problemas de grupos de segurança de rede - PowerShell | Microsoft Azure"
   description="Saiba como resolver problemas de rede grupos de segurança no modelo de implementação de Gestor de recursos do Azure através do Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Resolver problemas de grupos de segurança de rede através do PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Se configurou grupos de segurança de rede (NSGs) no seu máquina virtual (VM) e estiver a ter problemas de conectividade VM, este artigo fornece uma descrição geral das capacidades de diagnóstico para NSGs ajudar a resolver ainda mais.

NSGs permitem-lhe para controlar os tipos de tráfego esse fluxo e terminar a sua máquinas virtuais (VMs). NSGs podem ser aplicadas a sub-redes numa rede Virtual Azure (VNet), interfaces de rede (NIC) ou ambas. As regras eficazes aplicadas a uma NIC são uma agregação das regras que existam no NSGs aplicados a uma imagem e de sub-rede que está ligado ao. Regras através destes NSGs, por vezes, podem entrar em conflito com os outros e afetar a conectividade da rede uma VM.  

Pode ver todas as regras de segurança eficaz a partir do seu NSGs, tal como aplicada no NIC seu VM. Este artigo mostra como resolver problemas de conectividade VM utilizar estas regras no modelo de implementação de Gestor de recursos do Azure. Se não estiver familiarizado com os conceitos VNet e NSG, leia os artigos descrição geral [da rede Virtual](virtual-networks-overview.md) e [grupos de segurança de rede](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Utilizar regras de segurança eficaz para resolver problemas de fluxo de tráfego VM

O cenário que se segue é um exemplo de um problema de ligação comuns:

Uma VM denominada *VM1* faz parte de uma sub-rede denominada *Sub-rede1* dentro de uma VNet *WestUS VNet1*com o nome. Uma tentativa de ligação para a VM utilizando o RDP através da porta TCP 3389 falha. NSGs são aplicados à NIC *VM1 NIC1* e de sub-rede *Sub-rede1*. O tráfego para a porta TCP 3389 é permitido no NSG associada a interface de rede *VM1 NIC1*, no entanto, TCP ping para falha de porta 3389 do VM1.

Enquanto este exemplo utiliza a porta TCP 3389, os passos seguintes podem ser utilizados para determinar falhas de ligação de entrada e saída sobre qualquer porta.

## <a name="detailed-troubleshooting-steps"></a>Detalhadas passos de resolução de problemas
Conclua os passos seguintes para resolver problemas de NSGs para uma VM:

1. Inicie uma sessão do PowerShell do Azure e iniciar sessão no Azure. Se não estiver familiarizado com através do PowerShell Azure, leia o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .

2. Introduza o seguinte comando para devolver todas as regras de NSG aplicadas a uma imagem com o nome *VM1 NIC1* no grupo de recursos *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Se não souber o nome de uma imagem, introduza o seguinte comando para obter os nomes de todos os NIC num grupo de recursos: 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    O texto que se segue é um exemplo do resultado regras eficaz devolvido para *VM1 NIC1* NIC:

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Tenha em atenção as seguintes informações no resultado:

    - Existem duas secções **NetworkSecurityGroup** : uma é associada a uma sub-rede (*Sub-rede1*) e um está associado uma NIC (*VM1 NIC1*). Neste exemplo, um NSG foi aplicado a cada um.
    - **Associação** mostra o recurso (sub-rede ou NIC) um determinado NSG está associado. Se o recurso NSG for movida/associação terminada imediatamente antes de executar este comando, poderá ter de esperar alguns segundos para que a alteração para refletir na saída do comando. 
    - Os nomes de regra que são precedidos com *defaultSecurityRules*: é criado quando NSG uma, várias regras de segurança predefinidos são criadas dentro da mesma. Regras de predefinidas não podem ser removidas, mas pode ser substituídos com regras de prioridade superiores.
     Leia o artigo [Descrição geral NSG](virtual-networks-nsg.md#default-rules) para saber mais sobre regras de segurança do NSG predefinidas.
    - **ExpandedAddressPrefix** expande os prefixos de endereço para NSG predefinido etiquetas. Etiquetas representam vários prefixos de endereço. Expansão as etiquetas pode ser útil quando resolução de problemas de conectividade VM para a prefixos de endereço específico. Por exemplo, com VNET efectuado, etiqueta VIRTUAL_NETWORK expande para mostrar dispõe VNet prefixos no resultado anterior.

        >[AZURE.NOTE] As comando apenas mostra eficaz regras se uma NSG está associada a uma sub-rede, uma imagem ou ambas. Uma VM poderá ter várias NIC com NSGs diferentes aplicados. Quando a resolução de problemas, execute o comando para cada NIC.
        
3. Para facilitar a filtragem através do número maior de regras NSG, introduza os comandos seguintes para resolver mais problemas: 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Um filtro para o tráfego de RDP (portas TCP 3389), é aplicada à vista de grelha, conforme apresentado na seguinte imagem:

    ![Lista de regras](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Como pode ver na vista de grelha, são ambos permitem e negar regras para RDP. O resultado do passo 2 mostra que a regra *DenyRDP* no NSG aplicado à sub-rede. Para regras de entrada, NSGs aplicados à sub-rede são processados pela primeira vez. Caso uma correspondência for encontrada, não é processada NSG aplicado para a interface de rede. Neste caso, a regra *DenyRDP* da sub-rede bloqueia RDP para a VM (**VM1**).

    >[AZURE.NOTE] Uma VM poderá ter várias NIC anexada. Cada poderá estar ligada ao sub-rede diferente. Uma vez que os comandos nos passos anteriores são executados contra uma NIC, é importante para se certificar de que especificar NIC está a ter a falha de conectividade ao. Se não tiver a certeza, pode executar os comandos sempre contra cada NIC anexado a VM.

5. Para RDP para VM1, altere a regra *Negar RDP (3389)* para *Permitir RDP(3389)* no **Sub-rede1 NSG** NSG. Confirme que é aberta ao abrir uma ligação de RDP para a VM ou ao utilizar a ferramenta de PsPing porta TCP 3389. Pode obter mais informações sobre o PsPing lendo o [PsPing a página de transferência](https://technet.microsoft.com/sysinternals/psping.aspx)

    Pode ou remover regras a partir de um NSG utilizando as informações na saída a partir do seguinte comando:

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Considerações sobre

Considere os seguintes pontos quando problemas de conectividade de resolução de problemas:

- Regras NSG predefinidas serão bloquear o acesso de entrada da internet e permitir apenas VNet tráfego de entrada. Regras devem ser adicionadas explicitamente para permitir o acesso de entrada de Internet, conforme necessário.
- Se não existem NSG segurança regras a causar a falha a conectividade da rede uma VM, o problema poderá ser devido a:
    - Software de firewall em execução no interior do sistema operativo da VM
    - Encaminha configurada para eletrodomésticos virtuais ou tráfego no local. Tráfego da Internet pode ser redirecionado para no local através de túnel forçada. Uma ligação de RDP/SSH a partir da Internet para o seu VM poderá não funcionar com esta definição, dependendo de como o hardware de rede no local trata este tráfego. Leia o artigo [Resolução de problemas rotas](virtual-network-routes-troubleshoot-powershell.md) para aprender a diagnosticar os problemas de encaminhar poderão estar a impedir o fluxo de tráfego e terminar a VM. 
- Se lhe tiver dispõe VNets, por predefinição, a etiqueta VIRTUAL_NETWORK irá expandir automaticamente para incluir prefixos para VNets dispõe. Pode ver estes prefixos na lista **ExpandedAddressPrefix** , para resolver quaisquer problemas relacionados com a conectividade de efectuado VNet. 
- Regras de segurança eficaz apenas são apresentadas se existe uma NSG associada a VM NIC e ou sub-rede. 
- Se existirem sem NSGs associadas a NIC ou sub-rede e têm um endereço IP público atribuído ao seu VM, todas as portas será abertas para acesso entrada e saída. Se a VM tem um endereço IP público, aplicar NSGs a NIC ou sub-rede é vivamente recomendado.  

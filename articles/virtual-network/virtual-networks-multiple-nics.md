<properties
   pageTitle="Criar uma VM com vários NIC"
   description="Saiba como criar e configurar vms com vários NIC"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com vários NIC

Pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NIC) a cada um dos seus VMs. Multi NIC é um requisito para muitos eletrodomésticos de virtual rede, tal como a entrega da aplicação e soluções de otimização WAN. Multi NIC também fornece mais funcionalidades de gestão de tráfego de rede, incluindo isolamento de tráfego entre uma parte da frente terminar NIC e novamente fim NIC (s), ou a separação de tráfego de plano de dados a partir de tráfego de plano de gestão.

![NIC multi para VM](./media/virtual-networks-multiple-nics/IC757773.png)

A figura acima mostra uma VM com três NIC, que cada ligado a uma sub-rede diferente.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- Acesso à Internet VIP (implementações clássicas) só é suportada no NIC "predefinido". Existe apenas uma VIP ao IP do NIC predefinido.
- Neste momento, os endereços instância nível público IP (LPIP) (implementações clássicos) não são suportados para multi NIC VMs.
- A ordem da NIC a partir de dentro da VM será aleatória e também pode ser alteradas ao longo do Azure infraestrutura atualizações. No entanto, os endereços IP e o correspondente ethernet MAC endereços irá permanecem inalteradas. Por exemplo, suponha **que eth1** tem o endereço IP 10.1.0.100 e endereço de MAC 00-0D-3A-B0-39-0D; Depois de uma atualização de infraestrutura Azure e reinicie o computador, pode ser alterado para **Eth2**, mas o período de inquérito e MAC emparelhar permanecerá igual. Quando for um reinício iniciados por cliente, a ordem NIC permanecerá igual.
- O endereço para cada NIC no cada VM têm de estar localizado numa sub-rede, vários NIC de uma única VM cada possibilidade de endereços que estão na mesma sub-rede.
- O tamanho da memória virtual determina o número de NIC que pode criar para uma VM. Referência do [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) e artigos de tamanhos VM [Linux](../virtual-machines/virtual-machines-linux-sizes.md) para determinar NIC quantos suporta cada tamanho da memória virtual. 

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSGs)
Na implementação de Gestor de recursos, qualquer NIC numa VM pode ser associada com uma rede segurança grupo (NSG), incluindo quaisquer NIC de uma VM que tem vários NIC ativado. Se uma NIC é atribuída um endereço dentro de uma sub-rede onde a sub-rede está associada a uma NSG, em seguida, as regras NSG da sub-rede também aplicáveis para esse NIC. Para além de associar sub-redes NSGs, também pode associar uma imagem a uma NSG.

Se uma sub-rede está associada um NSG e uma NIC dentro desse sub-rede individualmente associadas a um NSG, as regras NSG associadas são aplicadas na **ordem de fluxo** de acordo com a direcção de tráfego que está a ser transmitida ou desaparecer NIC:

- **Tráfego de receção** cujo destino é NIC em questão flua pela primeira vez através da sub-rede acionar regras NSG a sub-rede, antes de passar para a imagem, em seguida, acionar regras NSG o NIC.
- **Tráfego de saída** cuja origem é NIC em questão flua pela primeira vez saída de NIC, acionar regras NSG o NIC, antes de prisma sub-rede, em seguida, acionar regras NSG a sub-rede.

Saiba mais sobre [Os grupos de segurança de rede](virtual-networks-nsg.md) e como são aplicados com base em associações a sub-redes, VMs e NIC...

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Como configurar um multi NIC VM numa implementação clássica

As instruções abaixo irão ajudá-lo a criar um multi VM NIC que contém 3 NIC: uma imagem predefinida e duas NIC adicional. Os passos de configuração criará uma VM que irão ser configurada de acordo com o fragmento de ficheiro de configuração de serviço abaixo:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Tem os seguintes pré-requisitos antes de tentar executar os comandos do PowerShell no exemplo.

- Uma subscrição do Azure.
- Uma rede virtual configurada. Consulte o artigo [Descrição geral da rede Virtual](virtual-networks-overview.md) para mais informações sobre VNets.
- A versão mais recente do Azure PowerShell transferiu e instalou. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Para criar uma VM com vários NIC, siga os passos abaixo:

1. Selecione uma imagem VM a partir da Galeria de imagens do Azure VM. Tenha em atenção que imagens alteram com frequência e estão disponíveis por região. Imagem do especificada no exemplo abaixo pode alterar ou poderá não ser na sua região, por isso, certifique-se de que especifique que precisa na imagem.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Crie uma configuração VM.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Crie o início de sessão do administrador predefinido.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Adicione NIC adicional à configuração VM.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Especifique o sub-rede e o endereço IP para o NIC predefinido.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Crie a VM na sua rede virtual.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] O VNet especificadas aqui tem de existir (tal como mencionado nos pré-requisitos). O exemplo abaixo Especifica uma rede virtual denominada **MultiNIC VNet**.

## <a name="limitations"></a>Limitações

As seguintes limitações são aplicáveis ao utilizar a funcionalidade NIC multi:

- Multi NIC VMs têm de ser criados no Azure redes virtuais (VNets). Não VNet VMs não podem ser configurados com NIC Multi.
- Todos os VMs num conjunto de disponibilidade tem de utilizar em multi NIC ou NIC único. Não pode ser uma mistura de multi NIC VMs e único NIC VMs dentro de um conjunto de disponibilidade. Aplicam as mesmas regras para VMs num serviço de nuvem.
- Uma VM com NIC única não pode ser configurada com multi NIC (e vice versa) assim que é implementado, sem eliminar e voltar a criar.


## <a name="secondary-nics-access-to-other-subnets"></a>Acesso NIC secundário para outras sub-redes

Por predefinição NIC secundário não será configurada com um gateway predefinido, devido ao qual o fluxo de tráfego de NIC secundário será limitado estar dentro da mesma sub-rede. Se pretenderem que os utilizadores ativar NIC secundário falar fora da sua própria sub-rede, precisam de adicionar uma entrada na tabela de encaminhamento para configurar o gateway, conforme descrito abaixo.

>[AZURE.NOTE] VMs criados antes de Julho de 2015 poderá ter um gateway predefinido configurado para todos os NIC. O gateway predefinido para NIC secundário não será removido até que estas VMs são reiniciados. Em sistemas operativos que utilizam o modelo de encaminhamento de anfitrião fracos, tal como Linux, a ligação à Internet pode interromper se utiliza o tráfego de penetração e saída NIC diferente.

### <a name="configure-windows-vms"></a>Configurar o Windows VMs

Suponha que tem uma VM Windows com duas NIC da seguinte forma:

- Endereço NIC IP primário: 192.168.1.4
- Endereço NIC IP secundário: 192.168.2.5

A tabela de rota IPv4 para este VM teria o seguinte aspeto:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Repare que a rota de predefinida (0.0.0.0) só está disponível para NIC principal. Não será possível aceder à recursos fora da sub-rede para NIC secundário, conforme visto abaixo:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Para adicionar uma rota predefinida no NIC secundário, siga os passos abaixo:

1. A partir de uma linha de comandos, execute o comando abaixo para identificar o número de índice remissivo para o NIC secundário:

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Repare que a segunda entrada na tabela, com um índice de 27 (neste exemplo).
3. A partir de comandos, execute o comando **Adicionar rota** conforme apresentado abaixo. Neste exemplo, que está a especificar 192.168.2.1 como o gateway predefinido para o NIC secundário:

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Para testar a conectividade, regresse à linha de comandos e tentar executar um ping sub-rede diferente da NIC secundário como mostrado int e longo exemplo abaixo:

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Também pode verificar tabela encaminhar para verificar a rota recentemente adicionada, conforme apresentado abaixo:

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurar Linux VMs

Para Linux VMs, uma vez que o comportamento predefinido utiliza anfitrião fraco encaminhamento, recomendamos que o NIC secundário está restrito a fluxos de tráfego apenas dentro da mesma sub-rede. No entanto se determinados cenários de procura da conectividade fora da sub-rede, os utilizadores devem activar baseados em política de encaminhamento para se certificar de que o tráfego de entrada e saída utiliza NIC mesmo.

## <a name="next-steps"></a>Próximos passos

- Implemente [MultiNIC VMs num cenário de aplicação de 2 camadas numa implementação Gestor de recursos](virtual-network-deploy-multinic-arm-template.md).
- Implemente [MultiNIC VMs num cenário de aplicação de 2 camadas numa implementação clássica](virtual-network-deploy-multinic-classic-ps.md).

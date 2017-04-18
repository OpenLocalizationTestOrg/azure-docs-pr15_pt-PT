<properties 
   pageTitle="Aceleração de funcionamento em rede para uma máquina de virtual - PowerShell | Microsoft Azure"
   description="Saiba como configurar acelerada de rede para uma máquina virtual Azure através do PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
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
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Redes acelerada para uma máquina virtual

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Redes acelerada permite única raiz e/s virtualização (SR IOV) para uma máquina de virtual (VM), significativamente a melhorar o desempenho de rede. Este caminho de alto desempenho ignora o anfitrião do datapath reduzir a latência, interferências de duração dos e a utilização da CPU para utilização com as mais excessiva rede das cargas de trabalho no tipos VM suportados. Este artigo explica como utilizar o Azure PowerShell para configurar acelerada à rede no modelo de implementação de Gestor de recursos do Azure. Também pode criar uma VM com acelerada de funcionamento em rede utilizando o Portal do Azure. Para saber como, clique na caixa de Portal do Azure na parte superior deste artigo.

A imagem seguinte mostra a comunicação entre as duas máquinas virtuais (VM) com e sem acelerada de rede:

![Comparação](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sem acelerada funcionamento em rede, todo o tráfego de rede e terminar a VM tem percorrer o anfitrião e o parâmetro virtual. O parâmetro virtual fornece todas as imposição de política, tal como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços de rede foi virtualizado para o tráfego de rede. Para saber mais, leia o artigo [Hyper-V rede Virtualização e mudar Virtual](https://technet.microsoft.com/library/jj945275.aspx) .

Com acelerada de rede, o tráfego de rede chega ao cartão de rede (NIC) e, em seguida, é encaminhado para a VM. Todas as políticas de rede que o parâmetro virtual aplica-se sem acelerada de rede são descarregadas e aplicadas no hardware. Aplicar a política de hardware permite NIC reencaminhe o tráfego de rede diretamente para a VM, ignore o anfitrião e o parâmetro virtual, enquanto mantém todos os a política-aplicada no anfitrião do.

Os benefícios da acelerada de rede só se aplicam a VM que está ativado no. Para obter melhores resultados, é ideal ativar esta funcionalidade no VMs, pelo menos, duas ligado para a mesma VNet.  Quando comunicar ao longo do VNets ou conexão no local, esta funcionalidade tem um impacto mínimo para latência geral.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Benefícios

- **Inferior latência / superiores pacotes por segundo (pps):** Remover o parâmetro virtual da datapath remove o tempo gastam pacotes no anfitrião do para processamento de política e aumenta o número de pacotes que podem ser processadas dentro da VM.
- **Reduzido interferências de duração dos:** Processamento de parâmetro virtual depende da quantidade de política de que precisa de ser aplicada e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política do hardware remove esse variabilidade por entregar pacotes diretamente para a VM, removendo o anfitrião para comunicação VM e todas as interrupções de software e parâmetros de contexto.
- **a utilização da CPU diminuiu:** Ignorar o parâmetro virtual no anfitrião do orienta para menos a utilização da CPU para processamento de tráfego de rede.

## <a name="limitations"></a>Limitações

Existem as seguintes limitações ao utilizar esta funcionalidade:
 
- **Criação de interface de rede:** Só pode ser ativado acelerado funcionamento em rede para uma nova interface de rede.  Não pode ser ativado numa interface de rede existente.
- **Criação VM:** Só pode ser anexada uma interface de rede com a rede acelerada ativada para uma VM quando a VM é criada. Não pode ser anexada a interface de rede para um VM existente.
- **Regiões:** Oferecida no apenas as regiões oeste Central-nos e oeste Europe Azure. O conjunto de regiões irá expandir no futuro.
- **Sistema operativo de suportados:** Microsoft Windows Server 2012 R2 e Windows Server 2016 pré-visualização técnica 5. Suporte de Linux e Windows Server 2012 será adicionado mais rapidamente.
- **Tamanho da memória virtual:** Standard_D15_v2 e Standard_DS15_v2 são suportados apenas tamanhos de instância VM. Para mais informações, consulte o artigo de [tamanhos de VM do Windows](../virtual-machines/virtual-machines-windows-sizes.md) . O conjunto de tamanhos de instância VM suportados irá expandir no futuro.

Alterações para estas limitações vai ser anunciadas através da página de [Rede Virtual do Azure atualizações](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Criar um VM do Windows com a rede acelerada

1. Abra uma linha de comandos do PowerShell e conclua os restantes passos nesta secção dentro de uma única sessão PowerShell. Se ainda não tiver instalado e configurado o PowerShell, execute os passos no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .
2. Para registar a pré-visualização, enviar uma mensagem de e-mail para [Acelerada subscrições de rede](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com o seu ID de subscrição e se destina a utilizar. Não conclua os passos restantes até depois de receber um e-mail a notificar lhe tenha sido aceites para a pré-visualização.
3. Registe-se a funcionalidade com a sua subscrição introduzindo os seguintes comandos:

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Substitua *westcentralus* com o nome de outra localização suportado por esta capacidade listada na secção [limitações](#limitations) deste artigo (se desejar). Introduza o seguinte comando para configurar uma variável para a localização:

        $locName = "westcentralus"

5. Substitua *RG1* com um nome para o grupo de recursos que irá conter a nova interface de rede e introduza os seguintes comandos criá-lo:

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Alterar *VM1 NIC1* para o que pretende atribuir um nome a interface de rede e, em seguida, introduza o seguinte comando:

        $NICName = "VM1-NIC1"

7. A interface de rede tem de estar ligada a uma sub-rede dentro de uma Azure Virtual rede existente (VNet) na mesma localização e [subscrição](../azure-glossary-cloud-terminology.md#subscription) como a interface de rede. Saiba mais sobre VNets lendo o artigo [Descrição geral de rede Virtual](virtual-networks-overview.md) se não estiver familiarizado com os mesmos. Para criar um VNet, conclua os passos no artigo [criar um VNet](virtual-networks-create-vnet-arm-ps.md) . Altere os "valores" dos $Variables seguinte para o nome da VNet e sub-rede que pretende ligar-se para a interface de rede.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Se não souber o nome de um VNet existente na localização que escolheu no passo 3, introduza os seguintes comandos:
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Se a lista de devolvida estiver vazia, tem de criar um VNet na localização. Para criar um VNet, conclua os passos no artigo [criar uma rede virtual](virtual-networks-create-vnet-arm-ps.md) .

    Para obter o nome das sub-redes dentro de VNet, escreva os seguintes comandos e substitua *Sub-rede1* acima com o nome de uma sub-rede:
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Introduza os seguintes comandos para obter a VNet e sub-rede e atribua-lhes variáveis.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identifica um recurso de endereço IP de público existente que pode ser associado à interface de rede para que se pode ligar ao mesmo através da Internet. Se não pretender aceder a VM com a interface de rede através da Internet, pode ignorar este passo. Sem um endereço IP público, tem de ligar para a VM a partir de outro VM ligado para a mesma VNet. 

    Alterar *PIP1* para o nome de um recurso de endereço IP de público existente que existe na localização que está a criar a interface de rede no e que não está atualmente associada a outra interface de rede. Se for necessário, altere $rgName para o nome do grupo de recursos o recurso de endereço IP público existir no e introduza o seguinte comando:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Se não souber o nome de um recurso de endereço IP público existente, introduza os seguintes comandos:

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Se a coluna **IPConfiguration** não tem um valor no resultado devolvido, o recurso de endereço IP público não está associado uma interface de rede existente e pode ser utilizado. Se a lista estiver em branco ou não existem não recursos disponíveis de endereço IP públicos, pode criar um utilizando o comando novo AzureRmPublicIPAddress.

    >[AZURE.NOTE] Endereços IP públicos tem uma taxa nominal. Saiba mais sobre preços lendo a página de [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Se tiver optado por não adicionar um recurso de endereço IP público para a interface, remover *- PublicIPAddress $PIP1* no final do comando que se segue. Crie a interface de rede com a rede acelerada introduzindo o seguinte comando:

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Atribua a interface de rede para uma VM quando criar a VM ao seguir as instruções nos passos 3 e 6 do artigo [criar uma VM](../virtual-machines/virtual-machines-windows-ps-create.md) . No passo 6-2, substitua *Standard_A1* um dos tamanhos VM listados na secção [limitações](#limitations) deste artigo.

    >[AZURE.NOTE] Se tiver alterado o *nome* do $locName, $rgName ou $nic variáveis neste artigo, o passo 6 para criar um artigo VM falhará. No entanto, pode alterar os *valores* das variáveis.

12. Quando a VM estiver criada, transfira o [controlador acelerada de rede](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), ligar-se para a VM e executar o instalador do controlador dentro da VM.

13. Botão direito do rato no botão do Windows e clique em **Gestor de dispositivos**. Verifique se o **Mellanox ConnectX-3 Virtual função Ethernet adaptador** aparece na opção de **rede** quando expandido, conforme apresentado na seguinte imagem:

    ![Gestor de dispositivos](./media/virtual-network-accelerated-networking-powershell/image2.png)
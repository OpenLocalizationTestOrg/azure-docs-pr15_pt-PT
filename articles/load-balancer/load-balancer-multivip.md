<properties
   pageTitle="Várias VIP para um serviço na nuvem"
   description="Descrição geral de multiVIP e como definir várias VIP um serviço na nuvem"
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

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurar vários VIP para um serviço na nuvem

Pode aceder a serviços em nuvem Azure através da Internet pública utilizando um endereço IP fornecido pelo Azure. Este endereço IP público é referido como um VIP (virtual IP) uma vez que está ligada ao balanceador de carga Azure e não Máquina Virtual (VM) instâncias dentro do serviço de nuvem. Pode aceder a qualquer instância VM dentro de um serviço na nuvem ao utilizar um único VIP.

No entanto, existem cenários em que podem ter mais do que um VIP como uma entrada de aponte para o mesmo serviço na nuvem. Por exemplo, o seu serviço de nuvem poderá alojar vários Web sites que necessitam de conectividade SSL utilizando a porta predefinida do 443, tal como cada site está alojado por um cliente diferente, ou de inquilinos. Neste cenário, tem de ter um público oposto endereço IP diferente para cada site. O diagrama abaixo ilustra um típica alojamento com uma necessidade de vários certificados SSL na mesma porta público na web com várias inquilino.

![Cenário de multi VIP SSL](./media/load-balancer-multivip/Figure1.png)

No exemplo acima, todos os VIP utilizam a mesma porta pública (443) e o tráfego é redirecionado para uma ou VMs numa porta privada exclusiva para o endereço IP interno do serviço na nuvem que aloja todos os Web sites de balanceamento de carga mais.

>[AZURE.NOTE] Outra situação que exigem a utilização de vários VIP está a alojar vários escutas de grupo de disponibilidade de SQL AlwaysOn no mesmo conjunto de máquinas virtuais.

VIP é dinâmico por predefinição, o que significa que o endereço IP real atribuído para o serviço de nuvem podem ser alteradas ao longo do tempo. Para impedir que aconteça, pode reservar um VIP do seu serviço. Para saber mais sobre VIP reservada, consulte o artigo [Reservadas IP público](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Consulte o artigo [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/) para obter informações sobre preços no VIP e IPs reservadas.

Pode utilizar o PowerShell para verificar VIP utilizado pelo seu serviços em nuvem, bem como adicionar e remover VIP, associar um VIP para um ponto final e configurar balanceamento de carga num VIP específico.

## <a name="limitations"></a>Limitações

Neste momento, a funcionalidade de Multi VIP está limitada a cenários seguintes:

- **IaaS apenas**. Apenas pode ativar a Multi VIP para serviços em nuvem que contêm VMs. Não é possível utilizar Multi VIP PaaS cenários com instâncias de função.
- **Apenas PowerShell**. Apenas pode gerir Multi VIP utilizando o PowerShell.

Estas limitações são temporárias e podem ser alteradas em qualquer altura. Certifique-se para voltar a visitar esta página para confirmar as alterações futuras.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Como adicionar um VIP num serviço na nuvem

Para adicionar um VIP no seu serviço, execute o seguinte comando do PowerShell:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Este comando apresenta um resultado semelhante ao seguinte exemplo:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Como remover uma VIP a partir de um serviço na nuvem

Para remover VIP adicionado ao seu serviço no exemplo acima, execute o seguinte comando do PowerShell:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] Só pode remover uma VIP se não tiver sem pontos finais associados à mesma.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Como obter as informações de VIP a partir de um serviço na nuvem

Para obter VIP associado a um serviço na nuvem, execute o seguinte script do PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

O script apresenta um resultado semelhante ao seguinte exemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Neste exemplo, o serviço de nuvem tem 3 VIP:

- **Vip1** VIP o predefinido, sabe que porque o valor para IsDnsProgrammedName está definido como verdadeiro.
- **Vip2** e **Vip3** não são utilizados como não possui quaisquer endereços IP. Só irão ser utilizados se associar um ponto final para o VIP.

>[AZURE.NOTE] A sua subscrição só será cobrada pelo VIP extra assim que estão associadas um ponto final. Para mais informações sobre preços, consulte o artigo [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Como associar um VIP para um ponto final

Para associar um VIP num serviço na nuvem para um ponto final, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

O comando cria um ponto final ligado a VIP designado por *Vip2* portas *80*e ligações para a VM denominada *myVM1* num serviço de nuvem denominado *Omeuserviço* com *TCP* no porta *8080*.

Para verificar a configuração, execute o seguinte comando do PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

O resultado é semelhante ao exemplo seguinte:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Como ativar balanceamento de carga num VIP específico

Pode associar uma única VIP a várias máquinas virtuais para fins de balanceamento de carga. Por exemplo, tem de um serviço na nuvem com o nome *Omeuserviço*e duas máquinas virtuais com o nome *myVM1* e *myVM2*. E o serviço de nuvem tem vários VIP, uma delas denominada *Vip2*. Se quiser certificar-se de que todo o tráfego para porta *81* no *Vip2* é distribuído entre *myVM1* e *myVM2* no porta *8181*, execute o seguinte script do PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

Também pode atualizar o seu Balanceador de carga para utilizar um VIP diferente. Por exemplo, se executar o comando do PowerShell, irá alterar o conjunto para utilizar um VIP denominada Vip1 de balanceamento de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Próximos passos

[Análise de registo de balanceamento de carga do Azure](load-balancer-monitor-log.md)

[Opostas carga balanceador descrição geral da Internet](load-balancer-internet-overview.md)

[Começar a utilizar em opostas Balanceador de carga de Internet](load-balancer-get-started-internet-arm-ps.md)

[Descrição geral de rede virtual](../virtual-network/virtual-networks-overview.md)

[IP reservado REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)
<properties
   pageTitle="Reservada IP | Microsoft Azure"
   description="Compreender IPs reservadas e como geri-los"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="reserved-ip-overview"></a>Descrição geral IP reservado
Endereços IP no Azure abrange duas categorias: dinâmicos e reservadas. Endereços IP públicos geridos pelo Azure são dinâmicos por predefinição. Isto significa que o endereço IP utilizados para um serviço em nuvem determinado (VIP) ou para aceder a uma função ou VM instância diretamente (ILPIP) pode alterar a frequentemente, quando os recursos são encerramento ou desatribuído.

Para impedir que os endereços IP alterar, é possível reservar um endereço IP. IPs reservadas pode ser utilizado apenas como um VIP, garantindo que o endereço IP do serviço em nuvem serão os mesmos mesmo como recursos são encerramento ou desatribuído. Além disso, pode converter IPs dinâmico existente utilizada como um VIP para um endereço IP reservado.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como reservar um endereço IP público estático, utilizando o [modelo de implementação do Gestor de recursos](virtual-network-ip-addresses-overview-arm.md).

Certifique-se de que compreende como [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Quando precisa de um IP reservado?
- **Pretende certificar-se de que o PI está reservado na sua subscrição**. Se pretender reservar um endereço IP que não será disponibilizado da sua subscrição em qualquer circunstâncias, deve utilizar um endereço IP público reservado.  
- **Pretende que o seu IP para se manter com o seu serviço de nuvem até mesmo em estado parado ou desatribuído (VMs)**. Se pretender que o serviço de ser acedido utilizando um endereço de IP que não será alterada mesmo quando VMs no serviço de nuvem são parar ou desatribuído.
- **Pretende certificar-se de que o tráfego de saída do Azure utiliza um endereço IP previsível**. Poderá ter a firewall no local configurada para permitir o tráfego apenas a partir de endereços IP específicos. Por reserva um IP, ficará a saber o endereço IP de origem e não terá de atualizar as suas regras de firewall devido a uma alteração de IP.

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES
1. Pode utilizar um IP reservado para todos os serviços Azure?  
  - IPs reservadas só pode ser utilizado para VMs e funções de instância de serviço de nuvem expostas através de um VIP.
1. Quantos IPs reservadas posso ter?  
  - Neste momento, todas as subscrições do Azure estão autorizadas para utilizar IPs reservadas 20. No entanto, pode pedir IPs reservadas adicionais. Ver a página de [subscrição e limites de serviço](../azure-subscription-service-limits.md) para obter mais informações.
1. Existe um encargo para IPs reservadas?
  - Consulte [Detalhes de preços de endereço de IP reservadas](http://go.microsoft.com/fwlink/?LinkID=398482) para informação de preços.
1. Como posso reservar um endereço IP?
  - Pode utilizar o PowerShell ou a [Azure gestão REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) para reservar um endereço IP numa região específico. Este endereço IP reservado está associado à sua subscrição. Não é possível reservar um endereço IP utilizando o Portal de gestão.
1. Posso utilizar este com afinidade grupo com base VNets?
  - IPs reservadas só são suportados no VNets regionais. Não é suportada para VNets que estão associadas a afinidade grupos. Para mais informações sobre como associar um VNet uma região ou de um grupo de afinidade, consulte o artigo [sobre VNets regionais e de grupos de afinidade](virtual-networks-migrate-to-regional-vnet.md).

## <a name="how-to-manage-reserved-vips"></a>Como gerir VIP reservada

Antes de poder utilizar IPs reservadas, tem de o adicionar à sua subscrição. Para criar um IP reservado do conjunto de endereços IP públicos disponíveis na localização *Central (EUA)* , execute o seguinte comando do PowerShell:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

No entanto, repare que não é possível especificar quais os IP que está a ser reservada. Para ver que endereços IP são reservados na sua subscrição, execute o seguinte comando PowerShell e repare os valores para *ReservedIPName* e o *endereço*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Assim que um IP reservado, permanece associado à sua subscrição até eliminá-lo. Para eliminar o IP reservado mostrado acima, execute o seguinte comando do PowerShell:

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Como reservar o endereço IP de um serviço de nuvem existente

É possível reservar o endereço IP de um serviço de nuvem existente ao adicionar o parâmetro *- ServiceName* . Para reservar o endereço IP de um serviço na nuvem *TestService* na localização *Central (EUA)* , execute o seguinte comando do PowerShell:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Como associar um IP reservado num serviço na nuvem novo
O script abaixo cria um novo IP reservado, em seguida, associa-o num serviço na nuvem nova com o nome *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Quando cria um IP reservado para utilizar com um serviço na nuvem, irá necessitar consultar a VM utilizando *VIP:&lt;número da porta >* para comunicação de entrada. Reserva um IP não significa que pode ligar-se para a VM diretamente. O IP reservado é atribuído ao serviço de nuvem que tenha sido implementada a VM. Se pretender estabelecer ligação com uma VM por IP diretamente, tem de configurar um endereço de IP público instância nível. Um endereço IP público de nível da instância é um tipo de endereço IP público (denominado um ILPIP) que é atribuído diretamente para o seu VM. Não pode ser reservado. Para mais informações, consulte [IP público ao nível da instância (ILPIP)](virtual-networks-instance-level-public-ip.md) .

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Como remover um IP reservado a partir de uma implementação em execução
Para remover o IP reservado adicionado ao serviço de novo criado no script acima, execute o seguinte comando do PowerShell:

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Remover um IP reservado a partir de uma implementação em execução não remove a reserva da sua subscrição. -Simplesmente liberta IP para ser utilizado por outro recurso na sua subscrição.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Como associar um IP reservado para uma implementação em execução
O script abaixo cria um novo serviço de nuvem denominado *TestService2* com uma nova VM *TestVM2*com o nome e, em seguida, associa o IP reservado existente com o nome *MyReservedIP* para o serviço de nuvem.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Como associar um IP reservado num serviço na nuvem ao utilizar um ficheiro de configuração do serviço
Também pode associar um IP reservado num serviço na nuvem ao utilizar um ficheiro de configuração (CSCFG) de serviço. O xml de exemplo abaixo mostra como configurar um serviço na nuvem para utilizar um VIP reservada denominada *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Próximos passos

- Compreenda como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implementação clássica.

- Saiba mais sobre [os endereços IP privados reservados](virtual-networks-reserved-private-ip.md).

- Saiba mais sobre [os endereços de instância nível público IP (ILPIP)](virtual-networks-instance-level-public-ip.md).

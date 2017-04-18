<properties 
   pageTitle="Nível de IP público (ILPIP) da instância | Microsoft Azure"
   description="Noções sobre ILPIP (PIP) e como geri-los"
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

# <a name="instance-level-public-ip-overview"></a>Instância nível público IP descrição geral
Uma instância IP público nível (ILPIP) é um endereço IP público que pode atribuir diretamente para a instância VM ou uma função, em vez do serviço de nuvem a instância VM ou função residem na. Isto não demora do local de VIP (virtual IP) que é atribuído ao seu serviço de nuvem. Em vez disso, é um endereço IP adicional que pode utilizar para ligar diretamente à sua instância VM ou função.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](virtual-network-ip-addresses-overview-arm.md). 

Certifique-se de que compreende como [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

>[AZURE.NOTE] No passado, um ILPIP era designado por um PIP que designa endereço IP público. 

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como apresentado na figura 1, o serviço de nuvem é acedido utilizando um VIP, enquanto os VMs individuais estão normalmente aceder às mesmas através de VIP:&lt;número da porta&gt;. Ao atribuir uma ILPIP a uma VM específica, que pode ser acedida VM diretamente utilizar esse endereço IP.

Quando cria um serviço na nuvem no Azure, os registos de DNS de correspondentes são criados automaticamente para permitir o acesso ao serviço através de um nome de domínio completamente qualificado (FQDN) em vez de utilizar o VIP real. O mesmo processo acontece para ILPIP, permissão do acesso à instância VM ou função por FQDN em vez do ILPIP. Por exemplo, se criar um serviço na nuvem com o nome *contosoadservice*e configurar uma função de web *contosoweb* com duas instâncias com o nome, o Azure registar os seguintes registos a para as instâncias:

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Pode atribuir ILPIP apenas uma para cada instância VM ou função. Pode utilizar até 5 ILPIP por subscrição. Neste momento, ILPIP não é suportada para multi-NIC VMs.

## <a name="why-should-i-request-an-ilpip"></a>Por que motivo devo a pedir um ILPIP?
Se quiser ser conseguir ligar à sua instância VM ou função por um endereço IP atribuído diretamente à mesma, em vez de utilizar a nuvem do serviço VIP:&lt;número da porta&gt;, pedir um ILPIP para o seu VM ou a instância de função.
- **FTP passivo** - por ter um ILPIP na sua VM, pode receber o tráfego em praticamente qualquer porta, não terá de abrir um ponto final para receber tráfego. Isto permite cenários como FTP passivo onde as portas são escolhidas dinamicamente.
- **IP de saída** - o tráfego de saída com origem a VM vai com ILPIP como a origem e este procedimento identifica VM para entidades externas.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Saiba como pedir uma ILPIP durante a criação de VM
O script do PowerShell cria um novo serviço de nuvem *FTPService*, com o nome, em seguida, obtém uma imagem a partir do Azure e cria uma VM denominada *FTPInstance* utilizando a imagem obtida, define a VM para utilizar uma ILPIP e adiciona a VM para o novo serviço:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Como obter ILPIP informações para uma VM
Para ver as informações de ILPIP de VM criado com o script acima, execute o seguinte comando PowerShell e observar os valores para *PublicIPAddress* e *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Como remover uma ILPIP a partir de uma VM
Para remover ILPIP adicionado à VM no script acima, execute o seguinte comando do PowerShell:
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Como adicionar um ILPIP para uma VM existente
Para adicionar um ILPIP VM criada utilizando o script acima, execute o seguinte comando:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Como associar um ILPIP para uma VM utilizando um ficheiro de configuração do serviço
Também pode associar uma ILPIP para uma VM utilizando um ficheiro de configuração (CSCFG) de serviço. O xml de exemplo abaixo mostra como configurar um serviço na nuvem para utilizar uma ILPIP denominada *MyPublicIP* para uma instância de função: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Próximos passos

- Compreenda como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implementação clássica.

- Saiba mais sobre [IPs reservadas](virtual-networks-reserved-public-ip.md).
 
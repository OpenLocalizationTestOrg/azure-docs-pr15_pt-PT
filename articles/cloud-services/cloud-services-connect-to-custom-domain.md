<properties
  pageTitle="Ligar um serviço na nuvem para um controlador de domínio personalizado | Microsoft Azure"
  description="Saiba como ligar o seu funções web/trabalho para um domínio personalizado do AD utilizando o PowerShell e extensão do domínio de AD"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Conexão Azure Cloud Services funções de administrador a um controlador de domínio do AD alojado no Azure personalizado

Vamos irá configure primeiro uma rede Virtual (VNet) no Azure. Em seguida, vamos adicionar um controlador de domínio Active Directory (hospedados numa máquina de Virtual do Azure) para o VNet. Em seguida, iremos adicionar existentes funções de serviço de nuvem para o VNet previamente criado e subsequentemente ligá-los para o controlador de domínio.

Antes de Vamos começar a utilizar, algumas das coisas a ter em conta:

1.  Neste tutorial utiliza PowerShell, pelo que pedimos que certifique-se de que tem o Azure PowerShell instalado e pronta a ser enviada. Para obter ajuda a configurar o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

2.  Instâncias de controlador de domínio do AD e função Web/trabalhador necessitam de ser o VNet.

Siga este guia passo a passo e se ocorrerem problemas, deixe-num comentário abaixo. Alguém obter novamente para si (Sim, podemos ler comentários).

3. A rede que é referenciada por nuvem <mark>tem de ser</mark> uma **rede virtual clássico**de serviço.

## <a name="create-a-virtual-network"></a>Criar uma rede Virtual

Pode criar uma rede Virtual no Azure utilizando o Azure portal clássico ou o PowerShell. Para este tutorial, vamos utilizar o PowerShell. Para criar uma rede Virtual através do portal clássico Azure, consulte o artigo [Criar rede Virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual

Assim que tiver concluído como configurar a rede Virtual, terá de criar um controlador de domínio do AD. Para este tutorial, podemos irá estar a configurar um controlador de domínio do AD numa máquina de Virtual do Azure.

Para executar esta tarefa, crie uma máquina de virtual através do PowerShell utilizando os comandos abaixo:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promover o seu Máquina Virtual para um controlador de domínio
Para configurar a Máquina Virtual como um controlador de domínio do AD, terá de iniciar sessão na VM e configure-o.

Para iniciar sessão VM, pode obter o ficheiro RDP através do PowerShell, utilize os comandos abaixo.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Assim que tiver sessão iniciada para a VM, a configuração Máquina Virtual como um controlador de domínio do AD seguindo o guia passo a passo sobre [como configurar o seu cliente controlador de domínio do AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicionar o seu serviço de nuvem à rede Virtual

Em seguida, tem de adicionar a sua implementação do serviço de nuvem para VNet que acabou de criar. Para fazer isto, modifique o cscfg de serviço de nuvem adicionando as secções relevantes para a sua cscfg utilizando o Visual Studio ou no editor da sua escolha.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Em seguida, criar o seu projeto de serviços na nuvem e implemente-o Azure. Para obter ajuda com a implementar o seu pacote de serviços na nuvem para Azure, consulte o artigo [como criar e implementar um serviço na nuvem](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ligar o seu funções web/trabalho ao domínio

Assim que o seu projeto do serviço de nuvem é implementado no Azure, ligue o seu instâncias de função para o domínio personalizado do AD utilizando a extensão de domínio do AD. Para adicionar a extensão de domínio do AD a sua implementação de serviços na nuvem existente e aderir o domínio personalizado, execute os seguintes comandos no PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

E já está.

Nuvem serviços agora devem ser aderidos ao seu controlador de domínio personalizado. Se quiser saber mais sobre as diferentes opções disponíveis como configurar a extensão do AD domínio, utilize a ajuda de PowerShell, conforme apresentado abaixo.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

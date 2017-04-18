<properties
    pageTitle="Azure Active Directory Domain Services: Guia de administração | Microsoft Azure"
    description="Aderir a uma máquina de virtual do Windows para um domínio gerido utilizando o PowerShell do Azure e o modelo de implementação clássica."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Associar uma máquina de virtual do Windows Server a um domínio gerido através do PowerShell

> [AZURE.SELECTOR]
- [Portal clássica Azure - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos e clássica](../resource-manager-deployment-model.md). Este artigo aborda a utilizar o modelo de implementação clássica. Serviços de domínio do Azure AD atualmente não suporta o modelo de Gestor de recursos.

Estes passos mostram como personalizar um conjunto de comandos do PowerShell do Azure que criam e pré-configurar uma máquina de virtual baseados no Windows Azure utilizando uma abordagem de bloco modular. Estes passos ajudá-lo a criar uma máquina de virtual baseados no Windows Azure e associá-lo para um domínio gerido de serviços de domínio do Azure AD.

Estes passos, siga uma abordagem de preencher-em-de-células em branco para criar conjuntos de comandos do Azure PowerShell. Esta abordagem pode ser útil se estiver familiarizado com o PowerShell ou que pretender saber quais os valores para especificar para a configuração de efetuada com êxito. Os utilizadores avançados do PowerShell podem colocar os comandos e substituir os seus próprios valores para as variáveis (linhas começados por "$").

Se ainda não o tiver feito, utilize as instruções no [artigo como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar o Azure PowerShell no seu computador local. Em seguida, abra uma linha de comandos do Windows PowerShell.

## <a name="step-1-add-your-account"></a>Passo 1: Adicionar a sua conta

1. Na linha de comandos do PowerShell, escreva **AzureAccount adicionar** e clique em **Enter**.
2. Escreva o endereço de e-mail associado à sua subscrição do Azure e clique em **continuar**.
3. Escreva a palavra-passe para a sua conta.
4. Clique em **Iniciar sessão**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Passo 2: Configurar a sua subscrição e a conta de armazenamento

Defina a sua subscrição do Azure e a conta de armazenamento ao executar estes comandos na linha de comandos do Windows PowerShell. Substituir tudo dentro de aspas, incluindo o < e > carateres, com os nomes corretos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Pode obter o nome da subscrição correto da propriedade SubscriptionName do resultado do comando **Get-AzureSubscription** . Pode obter o nome da conta de armazenamento correto da propriedade etiqueta do resultado do comando **Get-AzureStorageAccount** depois de executar o comando **Selecionar AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Passo 3: Instruções passo a passo sobre - aprovisionar a máquina virtual e associá-lo para o domínio gerido
Aqui está o comando Azure PowerShell correspondente definido para criar esta máquina virtual, com linhas em branco entre cada bloco de para legibilidade.

Especificar informações sobre a máquina de virtual do Windows para ser aprovisionado.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Para os valores de InstanceSize para D-, DS ou máquinas virtuais de série G, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Fornecem informações sobre o domínio gerido.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Especifique o nome do serviço na nuvem.

    $svcname="Contoso100-test"

Especifique o nome da rede virtual ao qual deve ser associada a VM. Certifique-se de que o domínio gerido AAD DS está disponível na rede virtual.

    $vnetname="MyPreviewVnet"

Selecione a imagem VM para ser utilizado para aprovisionar a VM.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configure a VM - nome do conjunto de VM, o tamanho da instância e imagem para ser utilizado.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obter as credenciais de administrador local para a VM. Selecione uma palavra-passe de administrador local segura.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obter as credenciais para uma conta de utilizador que pertencem a grupo 'Administradores de CC AAD' para associar VM ao domínio gerido. Não especificar o nome de domínio - por exemplo, no nosso exemplo, vamos especificar 'Rodrigo' como o nome de utilizador.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurar a VM - especificar requisito de associação de domínio e credenciais necessárias.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Defina uma sub-rede para a VM.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Opcional: Aponte para o endereço IP do domínio. Se definir os endereços IP do domínio gerido dos serviços de domínio do Azure AD para ser os servidores DNS para a rede virtual, este passo não é necessário.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Agora, pode Aprovisione o Windows VM façam parte de um domínio.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script para aprovisionar um VM do Windows e aderir automaticamente a um domínio gerido AAD serviços de domínio
Este conjunto de comandos do PowerShell cria uma máquina virtual para um servidor de linha de negócio que:

- Utiliza a imagem do Centro de dados do Windows Server 2012 R2.
- É uma máquina virtual extra pequena.
- Tem o nome contoso-teste.
- É automaticamente domínio associado ao domínio gerido contoso100.
- É adicionado à mesma rede virtual que o domínio gerido.

Eis o script completo de exemplo para criar a máquina virtual do Windows e associá-lo automaticamente para o domínio gerido de serviços de domínio do Azure AD.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Conteúdo relacionado
- [Serviços de domínio Azure AD - guia de introdução](./active-directory-ds-getting-started.md)

- [Administrar um domínio gerido de serviços de domínio do Azure AD](./active-directory-ds-admin-guide-administer-domain.md)

<properties 
   pageTitle="Como configurar um endereço IP estático privado no modo clássico ausing o clip | Microsoft Azure"
   description="Noções sobre IPs privado estático (diminuições) e como geri-los no modo clássico utilizando o clip"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Como configurar um endereço IP estático privado (clássico) no clip do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Também pode [Gerir um endereço IP estático privado no modelo de implementação de Gestor de recursos](virtual-networks-static-private-ip-arm-cli.md).

Os comandos do Azure clip de exemplo abaixo esperam um ambiente simple já criado. Se pretender executar os comandos, tal como estes são apresentados neste documento, primeiro a criar o ambiente de teste descrito na [criar um vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP estático privado quando criar uma VM
Para criar uma nova VM denominada *DNS01* num novo serviço de nuvem denominado *TestService* com base no cenário acima, siga estes passos:

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
1. Execute o comando **azure service criar** para criar o serviço de nuvem.

        azure service create TestService --location uscentral

    Saída esperada:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. Execute o comando **azure criar vm** para criar a VM. Repare que o valor para um endereço IP estático privado. A lista apresentada depois da saída explica os parâmetros utilizados.

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    Saída esperada:

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (ou – localização)**. Azure região onde a VM será criada. Para o cenário, *centralus*.
    - **-n (ou nome – vm)**. Nome da VM seja criada.
    - **-w (ou – nome de rede virtual)**. Nome da VNet onde a VM será criada. 
    - **-S (ou – ip estático)**. Privado endereço IP estático a VM.
    - **TestService**. Nome do serviço em nuvem onde a VM será criada.
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Imagem utilizada para criar a VM.
    - **adminuser**. Administrador local para a VM do Windows.
    - **AdminP@ssw0rd**. Palavra-passe de administrador local para a VM do Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para ver as estáticas privadas IP informações de endereço a VM criada com o script acima, execute o seguinte comando clip do Azure e observe o valor para *StaticIP de rede*:

    azure vm static-ip show DNS01

Saída esperada:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP estático privado de uma VM
Para remover o endereço IP estático privado adicionada à VM no script acima, execute o seguinte comando Azure clip:
    
    azure vm static-ip remove DNS01

Saída esperada:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Como adicionar um endereço IP estático privado a um VM existente
Para adicionar um estático privado endereço IP de VM criada utilizando o script acima, execução ele seguinte comando:

    azure vm static-ip set DNS01 192.168.1.101

Saída esperada:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre endereços [IP público reservada](virtual-networks-reserved-public-ip.md) .
- Saiba mais sobre os endereços de [nível da instância IP público (ILPIP)](virtual-networks-instance-level-public-ip.md) .
- Consulte as [IP reservado REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx).

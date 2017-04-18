## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Como criar um VNet utilizando o clip do Azure

Pode utilizar o clip do Azure para gerir os recursos do Azure a partir da linha de comandos a partir de qualquer computador a executar o Windows, Linux ou os x. Para criar um VNet utilizando o clip do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado o clip do Azure, consulte o artigo [instalar e configurar o clip do Azure](../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
2. Execute o comando de **modo azure config** para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Eis o resultado esperado para o comando acima:

        info:    New mode is arm

3. Se for necessário, execute o **grupo azure criar** para criar um novo grupo de recursos, conforme apresentado abaixo. Repare que a saída do comando. A lista apresentada depois da saída explica os parâmetros utilizados. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

        azure group create -n TestRG -l centralus

    Eis o resultado esperado para o comando acima:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (ou – nome)**. Nome para o novo grupo de recursos. Para o cenário, *TestRG*.
    - **-l (ou – localização)**. Azure região onde será criado o novo grupo de recursos. Para o cenário, *centralus*.

4. Execute o comando **criar rede azure vnet** para criar um VNet e uma sub-rede, conforme apresentado abaixo. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Eis o resultado esperado para o comando acima:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (ou - grupo de recursos)**. Nome do grupo de recursos onde o VNet será criado. Para o cenário, *TestRG*.
    - **-n (ou – nome)**. Nome da VNet seja criada. Para o cenário, *TestVNet*
    - **-a (ou – prefixos de endereço)**. Lista de blocos CIDR utilizados para o espaço de endereços VNet. Para o cenário, *192.168.0.0/16*
    - **-l (ou – localização)**. Azure região onde o VNet será criado. Para o cenário, *centralus*.

5. Execute o comando **criar rede azure vnet sub-rede** para criar uma sub-rede conforme apresentado abaixo. Repare que a saída do comando. A lista apresentada depois da saída explica os parâmetros utilizados.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Eis o resultado esperado para o comando acima:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-"e" (ou nome – vnet**. Nome da VNet onde a sub-rede será criada. Para o cenário, *TestVNet*.
    - **-n (ou – nome)**. Nome da nova sub-rede. Para o cenário, *front-end*.
    - **-a (ou – prefixo de endereço)**. Bloco CIDR sub-rede. Quatro nosso cenário, *192.168.1.0/24*.

6. Repita o passo 5 acima para criar outras sub-redes, se necessário. Para o cenário, execute o comando abaixo para criar a sub-rede *back-end* .

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Execute o comando **Mostrar rede azure vnet** para ver as propriedades do novo vnet, conforme apresentado abaixo.

        azure network vnet show -g TestRG -n TestVNet

    Eis o resultado esperado para o comando acima:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK

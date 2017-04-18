## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Implementar o modelo de processador utilizando o clip do Azure

Para implementar o modelo de processador que transferiu utilizando Azure clip, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
2. Executar o **`azure config mode`** comando para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Eis o resultado esperado para o comando acima:

        info:    New mode is arm

3. Se for necessário, execute o **`azure group create`** para criar um novo grupo de recursos, conforme apresentado abaixo. Repare que a saída do comando. A lista apresentada depois da saída explica os parâmetros utilizados. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](../articles/resource-group-overview.md).

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

4. Executar o **`azure group deployment create`** cmdlet para implementar o novo VNet utilizando o modelo e o parâmetro de ficheiros que transferiu e modificação acima. A lista apresentada depois da saída explica os parâmetros utilizados.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Eis o resultado esperado para o comando acima:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (ou - grupo de recursos)**. Nome do grupo de recursos a VNet novo será criado na.
    - **-f (ou - ficheiro de modelo)**. Caminho para o seu ficheiro de modelo de processador.
    - **-"e" (ou - ficheiro de parâmetros)**. Caminho para o seu ficheiro de parâmetros de processador.

5. Executar o **`azure network vnet show`** comando para ver as propriedades do novo vnet, conforme apresentado abaixo.

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

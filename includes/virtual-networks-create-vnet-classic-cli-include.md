## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Como criar um VNet clássica utilizando o clip do Azure

Pode utilizar o clip do Azure para gerir os recursos do Azure a partir da linha de comandos a partir de qualquer computador a executar o Windows, Linux ou os x. Para criar um VNet utilizando o clip do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
2. Execute o comando **criar rede azure vnet** para criar um VNet e uma sub-rede, conforme apresentado abaixo. A lista apresentada depois da saída explica os parâmetros utilizados.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Saída esperada:

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **– vnet**. Nome da VNet seja criada. Para o cenário, *TestVNet*
    - **-"e" (ou – espaço de endereços)**. Espaço de endereços VNet. Para o cenário, *192.168.0.0*
    - **-i (ou - cidr)**. Máscara de rede no formato CIDR. Para o cenário, *16*.
    - **- n (ou --nome de sub-rede**). Nome da primeira sub-rede. Para o cenário, *front-end*.
    - **-p (ou – sub-rede-início-ip)**. Iniciar o endereço IP do espaço de endereços de sub-rede ou sub-rede. Para o cenário, *192.168.1.0*.
    - **-r (ou – sub-rede cidr)**. Máscara de rede no formato CIDR para sub-rede. Para o cenário, *24*.
    - **-l (ou – localização)**. Azure região onde o VNet será criado. Para o cenário, *Central (EUA)*.

3. Execute o comando **criar rede azure vnet sub-rede** para criar uma sub-rede conforme apresentado abaixo. A lista apresentada depois da saída explica os parâmetros utilizados.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Eis o resultado esperado para o comando acima:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (ou nome – vnet**. Nome da VNet onde a sub-rede será criada. Para o cenário, *TestVNet*.
    - **-n (ou – nome)**. Nome da nova sub-rede. Para o cenário, *back-end*.
    - **-a (ou – prefixo de endereço)**. Bloco CIDR sub-rede. Quatro nosso cenário, *192.168.2.0/24*.

4. Execute o comando **Mostrar rede azure vnet** para ver as propriedades do novo vnet, conforme apresentado abaixo.

            azure network vnet show

    Eis o resultado esperado para o comando acima:

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

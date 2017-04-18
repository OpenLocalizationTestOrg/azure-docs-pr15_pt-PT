## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Como criar um VNet com um ficheiro de configuração de rede do PowerShell

Azure utiliza um ficheiro xml para definir todas as VNets disponíveis para uma subscrição. Pode transferir este ficheiro e editá-la para modificar ou eliminar VNets existente e criar novos. Neste documento, irá aprender a transferir este ficheiro, designado de ficheiro de configuração (ou netcgf) de rede e editá-lo para criar um novo VNet. Verificar o [esquema de configuração de rede virtual Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para saber mais sobre o ficheiro de configuração de rede.

Para criar um VNet com um ficheiro de netcfg através do PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.
2. A partir da consola do Azure PowerShell, utilize o cmdlet **Get-AzureVnetConfig** para transferir o ficheiro de configuração de rede, executando o comando abaixo. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Saída esperada:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Abra o ficheiro que guardou no passo 2 acima utilizando qualquer aplicação de editor de XML ou texto e procure o **<VirtualNetworkSites>** elemento. Se tiver quaisquer redes já criadas, todas as redes serão apresentada como a sua própria **<VirtualNetworkSite>** elemento.
4. Para criar a rede virtual descrita neste cenário, adicione o XML seguinte em apenas a **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Guarde o ficheiro de configuração de rede.
10. A partir da consola do Azure PowerShell, utilize o cmdlet **Set-AzureVnetConfig** para carregar o ficheiro de configuração de rede ao executar o comando abaixo. Repare que a saída no comando, deverá ver **bem sucedida** em **OperationStatus**. Se isto é não as maiúsculas/minúsculas, verifique o existência de erros de ficheiro xml.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Eis o resultado esperado para o comando acima:

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. A partir da consola do Azure PowerShell, utilize o cmdlet **Get-AzureVnetSite** para verificar que a nova rede foi adicionada ao executar o comando abaixo. 

        Get-AzureVNetSite -VNetName TestVNet

    Eis o resultado esperado para o comando acima:

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded
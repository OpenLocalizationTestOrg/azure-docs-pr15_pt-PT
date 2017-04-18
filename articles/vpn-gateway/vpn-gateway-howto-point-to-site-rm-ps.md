<properties 
   pageTitle="Configurar uma ligação de gateway VPN ponto-para-Site à rede virtual utilizando o modelo de implementação do Gestor de recursos | Microsoft Azure"
   description="Em segurança ligar à sua rede Virtual Azure através da criação de uma ligação de gateway VPN de ponto-para-Site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar uma ligação ponto-para-Site a uma VNet através do PowerShell

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configuração de um ponto Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual a uma rede virtual. Uma ligação de P2S é útil quando pretende ligar à sua VNet a partir de uma localização remota, tais como a partir de casa ou numa conferência, ou quando tiver apenas alguns clientes que precisam para se ligar a uma rede virtual. 

Ligações ponto-para-Site não necessitam de um dispositivo VPN ou um endereço IP público para trabalhar. Uma ligação VPN é estabelecida iniciando a ligação a partir do computador cliente. Para mais informações sobre as ligações ao ponto para o Site, consulte as [FAQ de Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [planeamento e estrutura](vpn-gateway-plan-design.md). 

Este artigo explica o criar um VNet com uma ligação ponto-para-Site no modelo de implementação de Gestor de recursos através do PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementação e métodos para P2S ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os dois modelos de implementação e os métodos de implementação disponíveis para configurações P2S. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Fluxo de trabalho básico 

![Ponto-para-Site-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "site de ponto")

Neste cenário, irá criar uma rede virtual com uma ligação ponto-para-Site. As instruções também irão ajudá-lo gerar certificados, que são necessários para esta configuração. Uma ligação de P2S é composta pelos seguintes itens: A VNet com um gateway VPN, um ficheiro. cer de certificado de raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Utilizamos os seguintes valores para esta configuração. Vamos defina as variáveis na secção [1](#declare) do artigo. Pode utilizar os passos como uma guia passo a passo e utilize os valores sem alterá-los ou alterá-las para refletir o seu ambiente. 

### <a name="example"></a>Valores de exemplo

- **Nome: VNet1**
- **Espaço de endereços: 192.168.0.0/16** e **10.254.0.0/16**<br>Neste exemplo, utilizamos mais do que um espaço de endereços para ilustrar a que esta configuração funcionam com vários espaços de endereço. No entanto, vários espaços de endereço não são necessários para esta configuração.
- **Nome de sub-rede: front-end**
    - **Intervalo de endereços de sub-rede: 192.168.1.0/24**
- **Nome de sub-rede: back-end**
    - **Intervalo de endereços de sub-rede: 10.254.1.0/24**
- **Nome de sub-rede: GatewaySubnet**<br>O nome de sub-rede *GatewaySubnet* é obrigatório para o gateway VPN trabalhar.
    - **Intervalo de endereços de sub-rede: 192.168.200.0/24** 
- **Conjunto de endereços do cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se ligam ao VNet utilizando esta ligação de ponto-para-Site receber um endereço IP do conjunto de endereços do cliente VPN.
- **Subscrição:** Se tiver mais do que uma subscrição, certifique-se de que está a utilizar o correto.
- **Grupo de recursos: TestRG**
- **Localização: Leste dos e.u.a.**
- **Servidor DNS: endereço IP** do servidor DNS que pretende utilizar para resolução de nomes.
- **Nome GW: Vnet1GW**
- **Nome IP público: VNet1GWPIP**
- **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de começar

- Certifique-se de que tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Instale a versão mais recente dos cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Quando trabalha com o PowerShell para esta configuração, certifique-se de que está a executar como administrador. 

## <a name="declare"></a>Parte 1 - registo na e conjunto de variáveis

Nesta secção, inicie sessão e declarar os valores utilizados para esta configuração. Os valores declarados são utilizados nos scripts de exemplo. Altere os valores para refletir o seu ambiente. Em alternativa, pode utilizar os valores declarados e aceda através dos passos como um exercício.

1. Na consola do PowerShell, inicie sessão na sua conta Azure. Este cmdlet pede-lhe as credenciais de início de sessão para a sua conta Azure. Depois de início de sessão, transfere as definições de conta para que fiquem disponíveis para Azure PowerShell.

        Login-AzureRmAccount 

2. Obter uma lista das suas subscrições Azure.

        Get-AzureRmSubscription

3. Especifique a subscrição que pretende utilizar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Declare as variáveis que pretende utilizar. Utilize o exemplo seguinte, com os valores para o seu próprio quando for necessário.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Parte 2 - configurar um VNet 

1. Crie um grupo de recursos.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Crie as configurações de sub-rede para a rede virtual nomeá-los *front-end*, *back-end*e *GatewaySubnet*. Estes prefixos devem fazer parte do espaço de endereços VNet declarados.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Crie a rede virtual. O servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes para os recursos que está a ligar. Neste exemplo, utilizámos um endereço IP público. Certifique-se de que utilizar os seus próprios valores.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Especificar as variáveis para a rede virtual que criou.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Pedir um endereço IP público atribuído dinamicamente. Este endereço IP é necessário para o gateway funcione corretamente. Mais tarde, ligar o gateway para a configuração de IP do gateway.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>Parte 3 - certificados

Certificados são utilizados pelo Azure para autenticar os clientes VPN para VPNs ponto-para-Site. Exportar dados de certificado pública (não a chave privada) como uma Base-64 codificado x. 509. cer ficheiro a partir de um certificado de raiz gerados por uma solução de certificado de empresa ou um certificado autoassinado raiz. Importar, em seguida, os dados de certificados público do certificado de raiz para Azure. Para além disso, tem de gerar um certificado de cliente a partir do certificado de raiz para clientes. Cada cliente que pretende ligar à rede virtual utilizando uma ligação de P2S tem de ter instalado um certificado de cliente que foi criado a partir do certificado de raiz.

### <a name="cer"></a>1. a obter o ficheiro. cer para o certificado de raiz

Terá de obter os dados de certificados público para o certificado de raiz que pretende utilizar.

- Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro. cer para o certificado de raiz que pretende utilizar. 

- Se não estiver a utilizar uma solução de certificado empresarial, é necessário gerar um certificado autoassinado raiz. Para obter os passos do Windows 10, pode consultar a [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md).


1. Para obter um ficheiro. cer a partir de um certificado, abra **certmgr** e localize o certificado de raiz. O certificado autoassinado raiz com o botão direito, clique em **todas as tarefas**e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para exportar certificados**.

2. No assistente, clique em **seguinte**, selecione **não, não exportar a chave privada**e, em seguida, clique em **seguinte**.

3. Na página do **Formato de ficheiro de exportação** , selecione **Base-64 codificado x. 509 (. CER).** Em seguida, clique em **seguinte**. 

4. No **ficheiro para exportar**, **procure** a localização para o qual pretende exportar o certificado. Para **nome de ficheiro**, nome do ficheiro de certificado. Em seguida, clique em **seguinte**.

5. Clique em **Concluir** para exportar o certificado.

### <a name="generate"></a>2. gerar o certificado de cliente

Em seguida, gere os certificados de cliente. Quer pode gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado no vários clientes. A vantagem para gerar certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos os utilizadores está a utilizar o mesmo certificado de cliente e localizar o que precisa de revogar o certificado para um cliente, terá de gerar e instale novos certificados para todos os clientes que utilizam o certificado para autenticar. Os certificados de cliente são instalados em cada computador cliente mais tarde neste exercício.

- Se estiver a utilizar uma solução de certificado empresarial, gerar um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com', em vez do NetBIOS 'Domínio \ nomedeutilizador' Formatar. 

- Se estiver a utilizar um certificado autoassinado, consulte o artigo [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="exportclientcert"></a>3. exporte o certificado de cliente

Um certificado de cliente é necessário para autenticação. Depois de gerar o certificado de cliente, exportá-lo. O certificado de cliente que exportar será instalado mais tarde em cada computador cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr*. Botão direito do rato o certificado de cliente que pretende exportar, clique em **todas as tarefas**e, em seguida, clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *. pfx* . Certifique-se gravar ou lembre-se a palavra-passe (tecla) que definiu para este certificado.

### <a name="upload"></a>4. carregar o ficheiro. cer de certificado de raiz

Declare a variável para o seu nome de certificado, substituindo o valor pela sua própria:

        $P2SRootCertName = "Mycertificatename.cer"

Adicione os dados de certificado público para o certificado de raiz para Azure. Pode carregar ficheiros até 20 para certificados de raiz. Não carregar a chave privada para o certificado de raiz para Azure. Assim que o ficheiro. cer é carregado, Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual. 

Substitua o caminho do ficheiro pelas suas próprias e, em seguida, execute os cmdlets.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Parte 4 - criar um gateway VPN

Configure e criar o gateway de rede virtual para o seu VNet. O *-GatewayType* tem de ser **Vpn** e o *-VpnType* tem de ser **RouteBased**. Isto pode demorar até 45 minutos a concluir.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Parte 5 – transfira o pacote de configuração do cliente VPN

Os clientes estabelecer ligação ao Azure utilizando P2S tem de ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração do cliente VPN estão disponíveis para clientes do Windows. O pacote de cliente VPN contém informações para configurar o software de cliente VPN que incorporada no Windows e é específico de VPN ao qual pretende ligar. O pacote não instalar software adicional. Consulte as [FAQ de Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Depois do gateway foi criado, pode transferir o pacote de configuração do cliente. Este exemplo transfere o pacote para clientes de 64 bits. Se pretende transferir o cliente de 32 bits, substitua 'Amd64' 'x86'. Também pode transferir o cliente VPN utilizando o portal do Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. O cmdlet do PowerShell devolve uma ligação de URL. Este é um exemplo de que o devolvida URL parece:

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copie e cole a ligação que é devolvida para um browser para transferir o pacote. Em seguida, instale o pacote no computador cliente. Se obtiver um menu de contexto SmartScreen, clique em **obter mais informações**, em seguida, **mesmo assim executar** para poder instalar o pacote.

4. No computador cliente, navegue para **As definições de rede** e clique em **VPN**. Irá ver a ligação listada. -Apresentará o nome da rede virtual que irá ligar e o seguinte aspeto semelhante a este exemplo: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Cliente VPN")


## <a name="clientcertificate"></a>Parte 6 - instale o certificado de cliente

Cada computador cliente tem de ter um certificado de cliente para autenticar. Ao instalar o certificado de cliente, terá a palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro. pfx para o computador cliente.
2. Faça duplo clique sobre o ficheiro. pfx para o instalar. Não modifique a localização da instalação.

## <a name="connect"></a>Parte 7 - ligar ao Azure

1. Para ligar à sua VNet, no computador cliente, navegue para ligações VPN e localize a ligação VPN que criou. -Chama-se o mesmo nome como a sua rede virtual. Clique em **Ligar**. Poderá ser apresentada uma mensagem de pop-up que se refira a utilizar o certificado. Se tal acontecer, clique em **continuar** para utilizar privilégios elevados. 

2. Na página de estado da **ligação** , clique em **Ligar** para iniciar a ligação. Se vir um ecrã de **Selecionar certificado** , certifique-se de que o certificado de cliente a apresentar é aquele que pretende utilizar para ligar. Se não for, utilize a seta pendente para selecionar o certificado correto e, em seguida, clique em **OK**.

    ![Ligação do cliente VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Ligação do cliente VPN")

3. Agora deve ser estabelecida a ligação.

    ![Estabelecer ligação] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Estabelecer ligação")

## <a name="verify"></a>Parte 8 - Verifique se a sua ligação

1. Para verificar se a sua ligação VPN está ativa, abra uma linha de comandos elevada e executar *ipconfig/tudo*.

2. Ver os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do conjunto de endereços de cliente do ponto-para-Site VPN, especificado na configuração. Os resultados deverão ser algo semelhante ao seguinte:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>Para adicionar ou remover um certificado de raiz confiável

Certificados são utilizados para autenticar os clientes VPN para VPNs ponto-para-Site. Os passos seguintes orientá-lo na adicionar e remover certificados de raiz. Quando adiciona um ficheiro codificado em Base64 x. 509 (. cer) para Azure, são informar Azure para confiar no certificado de raiz que representa o ficheiro. 

Pode adicionar ou remover certificados raiz fidedignos utilizando o PowerShell ou no portal do Azure. Se pretender executar este procedimento utilizando o portal do Azure, aceda ao seu **gateway de rede virtual > Definições > Configuração do ponto-para-site > certificados de raiz**. Os passos seguintes orientam estas tarefas através do PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Adicionar um certificado de raiz confiável

Pode adicionar até 20 ficheiros de. cer de certificados de raiz confiável para Azure. Siga os passos abaixo para adicionar um certificado de raiz.

1. Criar e preparar o novo certificado de raiz que irá adicionar a Azure. Exportar a chave pública, tal como uma Base-64 codificado x. 509 (. CER) e abri-lo com um editor de texto. Em seguida, copie apenas a secção apresentada abaixo. 
 
    Copie os valores, conforme mostrado no exemplo seguinte:

    ![certificado] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificado")
    
2. Especifique o nome do certificado e informações da chave como uma variável. Substitua as informações pelo seu próprio, tal como mostrado no seguinte exemplo:

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Adicione o novo certificado de raiz. Só pode adicionar um certificado de cada vez.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Pode verificar se o novo certificado foi adicionado corretamente ao utilizar o cmdlet seguinte.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz confiável

Pode remover o certificado de raiz confiável a partir do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ligar ao Azure através do Site de ponto. Se pretender que os clientes se liguem, precisa de instalar um novo certificado de cliente é gerado a partir de um certificado que é considerado fidedigno no Azure.

1. Para remover um certificado de raiz confiável, modifique o exemplo seguinte:

    Declare as variáveis.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Remova o certificado.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Utilize o cmdlet seguinte para verificar se o certificado foi removido com êxito. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Para gerir a lista de certificados de cliente revogado

Pode revogar certificados de cliente. A lista de revogação de certificados permite-lhe seletivamente negar conectividade de ponto-para-Site com base em certificados de cliente individuais. Se remover um certificado de raiz. cer a partir do Azure,-revoga o acesso para todos os certificados de cliente gerado/assinado pelo certificado de raiz revogado. Se pretende revogar um certificado de cliente em particular, não a raiz, pode fazê-lo. Desta forma os certificados que foram gerados a partir do certificado de raiz irão ainda ser válido.

Exercícios práticos comuns são utilizar o certificado de raiz para gerir o acesso a níveis de equipa ou organização, ao usar o cliente revogado certificados para controlo de acesso extensivamente em que os utilizadores individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

1. Obter a impressão digital do certificado do cliente para revogar.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Adicione a impressão digital à lista de impressão digital revogado.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Certifique-se de que a impressão digital foi adicionada à lista de revogação de certificados. Tem de adicionar uma impressão digital cada vez.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Restaurar um certificado de cliente

Pode restaurar um certificado de cliente ao remover a impressão digital a partir da lista de certificados de cliente revogado.

1.  Remova a impressão digital a partir da lista de impressão digital do certificado de cliente revogado.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Verifique se a impressão digital é removida da lista de revogado.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Próximos passos

Pode adicionar uma máquina virtual à sua rede virtual. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.
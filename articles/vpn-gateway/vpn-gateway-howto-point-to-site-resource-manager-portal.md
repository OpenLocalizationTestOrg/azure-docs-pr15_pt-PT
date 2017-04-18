<properties 
   pageTitle="Configurar uma ligação de gateway VPN de ponto-para-Site à rede virtual utilizando o modelo de implementação do Gestor de recursos e o portal do Azure | Microsoft Azure"
   description="Em segurança ligar à sua rede Virtual Azure através da criação de uma ligação de gateway VPN ponto-para-Site utilizando o Gestor de recursos e o portal do Azure."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma ligação ponto-para-Site a uma VNet utilizando o Portal do Azure

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configuração de um ponto Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual a uma rede virtual. Uma ligação de P2S é útil quando pretende ligar à sua VNet a partir de uma localização remota, tais como a partir de casa ou numa conferência, ou quando tiver apenas alguns clientes que precisam para se ligar a uma rede virtual. 

Ligações ponto-para-Site não necessitam de um dispositivo VPN ou um endereço IP público para trabalhar. Uma ligação VPN é estabelecida iniciando a ligação a partir do computador cliente. Para mais informações sobre as ligações ao ponto para o Site, consulte as [FAQ de Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [planeamento e estrutura](vpn-gateway-plan-design.md).

Este artigo explica o criar um VNet com uma ligação ponto-para-Site no modelo de implementação de Gestor de recursos utilizando o portal Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementação e métodos para P2S ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os dois modelos de implementação e os métodos de implementação disponíveis para configurações P2S. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Fluxo de trabalho básico 

![Ponto-para-Site-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "site de ponto")

### <a name="example"></a>Valores de exemplo

- **Nome: VNet1**
- **Espaço de endereços: 192.168.0.0/16**<br>Neste exemplo, utilizamos espaço de endereços apenas uma. Pode ter mais do que um espaço de endereços para sua VNet.
- **Nome de sub-rede: front-end**
- **Intervalo de endereços de sub-rede: 192.168.1.0/24**
- **Subscrição:** Se tiver mais do que uma subscrição, certifique-se de que está a utilizar o correto.
- **Grupo de recursos: TestRG**
- **Localização: Leste dos e.u.a.**
- **GatewaySubnet: 192.168.200.0/24**
- **Nome do gateway rede virtual: VNet1GW**
- **Tipo de gateway: VPN**
- **Tipo de VPN: com base em encaminhar**
- **Endereço IP público: VNet1GWpip**
- **Tipo de ligação: ponto-para-site**
- **Conjunto de endereços de cliente: 172.16.201.0/24**<br>Os clientes VPN que se ligam ao VNet utilizando esta ligação de ponto-para-Site receber um endereço IP do conjunto de endereços do cliente.

## <a name="before-beginning"></a>Antes de começar

- Certifique-se de que tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Parte 1 - criar uma rede virtual

Se estiver a criar esta configuração como um exercício, pode referir-se para os [valores de exemplo](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. Adicionar espaço de endereços adicionais e sub-redes

Pode adicionar espaço de endereços adicionais e sub-redes a sua VNet depois de ter sido criada.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. criar uma sub-rede do gateway

Antes de ligar à rede virtual para um gateway, primeiro tem de criar a sub-rede de gateway para a rede virtual ao qual pretende ligar. Se possível, é melhor criar uma sub-rede de gateway utilizando CIDR ou um bloco de /28 /27 para fornecer suficiente endereços IP para acomodar os requisitos de configuração de futuros adicionais.

Capturas de ecrã nesta secção são fornecidas como um exemplo de referência. Certifique-se de que utilizar o intervalo de endereços GatewaySubnet que corresponde com os valores necessários para a configuração.

#### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. Especifique um servidor de DNS (opcional)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Parte 2 - criar um gateway de rede virtual

As ligações de ponto-para-site requerem as seguintes definições:

- Tipo de gateway: VPN
- Tipo de VPN: com base em encaminhar

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Parte 3 - gerar certificados

Certificados são utilizados por Azure para autenticar os clientes VPN para VPNs ponto-para-Site. Exportar dados de certificado pública (não a chave privada) como uma Base-64 codificado x. 509. cer ficheiro a partir de um certificado de raiz gerados por uma solução de certificado de empresa ou um certificado autoassinado de raiz. Importar, em seguida, os dados de certificados público do certificado de raiz para Azure. Para além disso, tem de gerar um certificado de cliente a partir do certificado de raiz para clientes. Cada cliente que pretende ligar à rede virtual utilizando uma ligação de P2S tem de ter instalado um certificado de cliente que foi criado a partir do certificado de raiz.

### <a name="getcer"></a>1. a obter o ficheiro. cer para o certificado de raiz

Se estiver a utilizar uma solução empresarial, pode utilizar a sua cadeia de certificado existente. Se não estiver a utilizar uma solução AC de empresa, pode criar um certificado autoassinado raiz. Um método para criar um certificado autoassinado é makecert.

- Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro. cer para o certificado de raiz que pretende utilizar. 

- Se não estiver a utilizar uma solução de certificado empresarial, é necessário gerar um certificado autoassinado raiz. Para obter os passos do Windows 10, pode consultar a [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um ficheiro. cer a partir de um certificado, abra **certmgr** e localize o certificado de raiz. O certificado autoassinado raiz de contexto, clique em **todas as tarefas**e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para exportar certificados**.

2. No assistente, clique em **seguinte**, selecione **não, não exportar a chave privada**e, em seguida, clique em **seguinte**.

3. Na página do **Formato de ficheiro de exportação** , selecione **Base-64 codificado x. 509 (. CER).** Em seguida, clique em **seguinte**. 

4. No **ficheiro para exportar**, **procure** a localização para o qual pretende exportar o certificado. Para **nome de ficheiro**, nome do ficheiro de certificado. Em seguida, clique em **seguinte**.

5. Clique em **Concluir** para exportar o certificado.

### <a name="generateclientcert"></a>2. gerar um certificado de cliente

Quer pode gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado no vários clientes. A vantagem para gerar certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos os utilizadores está a utilizar o mesmo certificado de cliente e localizar o que precisa de revogar o certificado para um cliente, terá de gerar e instale novos certificados para todos os clientes que utilizam esse certificado para autenticar.

- Se estiver a utilizar uma solução de certificado empresarial, gerar um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com', em vez do formato de 'nomededomínio'. 

- Se estiver a utilizar um certificado autoassinado, consulte o artigo [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="exportclientcert"></a>3. exporte o certificado de cliente

Um certificado de cliente é necessário para autenticação. Depois de gerar o certificado de cliente, exportá-lo. O certificado de cliente que exportar será instalado mais tarde em cada computador cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr*. Botão direito do rato o certificado de cliente que pretende exportar, clique em **todas as tarefas**e, em seguida, clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *. pfx* . Certifique-se gravar ou lembre-se a palavra-passe (tecla) que definiu para este certificado.

## <a name="addresspool"></a>Parte 4 - adicionar o conjunto de endereços do cliente

1. Assim que o gateway de rede virtual ter sido criado, navegue para a secção **Definições** pá de gateway a rede virtual. Na secção **Definições** , clique em configuração de **ponto-para-site** para abrir o pá de **configuração** .

    ![aponte para pá de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "aponte para pá de site")

2. **Conjunto de endereços** é o conjunto de endereços IP a partir do qual clientes que ligam irão receber um endereço IP. Adicione o conjunto de endereços e, em seguida, clique em **Guardar**.

    ![conjunto de endereços do cliente] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "conjunto de endereços do cliente")

## <a name="uploadfile"></a>Parte 5: carregue o ficheiro. cer de certificado de raiz

Depois do gateway foi criado, pode carregar o ficheiro. cer para um certificado de raiz confiável para Azure. Pode carregar ficheiros até 20 para certificados de raiz. Não carregar a chave privada para o certificado de raiz para Azure. Assim que o ficheiro. cer é carregado, Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Navegue para a configuração de **ponto-para-site** pá. Irá adicionar os ficheiros. cer na secção de **certificado de raiz** deste pá.

    ![aponte para pá de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "aponte para pá de site")

2. Certifique-se de que exportou o certificado de raiz, tal como uma Base-64 codificado ficheiro x. 509 (. cer). Tem de exportá-lo neste formato, para que pode abrir o certificado com o editor de texto.
3. Abra o certificado com um editor de texto, como o bloco de notas. Copie apenas a secção seguinte:

    ![dados de certificado] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "dados de certificado")

4. Cole os dados de certificado a secção de **Dados públicos de certificado** do portal. Coloque o nome do certificado no espaço de **nomes** e, em seguida, clique em **Guardar**. Pode adicionar até 20 certificados de raiz fidedigna.

    ![Carregar certificado] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "Carregar certificado")

## <a name="clientconfig"></a>Peça 6 - transfira e instale o pacote de configuração do cliente VPN

Os clientes estabelecer ligação ao Azure utilizando P2S tem de ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração do cliente VPN estão disponíveis para clientes do Windows. 

O pacote de cliente VPN contém informações para configurar o software de cliente VPN incorporada no Windows. A configuração é específica da VPN ao qual pretende ligar. O pacote não instalar software adicional. Consulte as [FAQ de Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Sobre a configuração de **ponto-para-site** pá, clique em **Transferir VPN cliente** para abrir o pá **VPN transferir o cliente** .

    ![Transferir o cliente VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Transferir o cliente VPN")

2. Selecione o pacote correto para o seu cliente e, em seguida, clique em **Transferir**. Para os clientes de 64 bits, selecione **AMD64**. Para clientes da versão de 32 bits, selecione **x86**.

3. Instale o pacote no computador cliente. Se obtiver um menu de contexto SmartScreen, clique em **obter mais informações**, em seguida, **mesmo assim executar** para poder instalar o pacote.

4. No computador cliente, navegue para **As definições de rede** e clique em **VPN**. Irá ver a ligação listada. -Apresentará o nome da rede virtual que irá ligar e o seguinte aspeto semelhante a este exemplo: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "Cliente VPN")

## <a name="installclientcert"></a>Parte 7 - instalar o certificado de cliente

Cada computador cliente tem de ter um certificado de cliente para autenticar. Ao instalar o certificado de cliente, terá a palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro. pfx para o computador cliente.
2. Faça duplo clique sobre o ficheiro. pfx para o instalar. Não modifique a localização da instalação.

## <a name="connect"></a>Parte 8 - ligar ao Azure

1. Para ligar à sua VNet, no computador cliente, navegue para ligações VPN e localize a ligação VPN que criou. -Chama-se o mesmo nome como a sua rede virtual. Clique em **Ligar**. Poderá ser apresentada uma mensagem de pop-up que se refira a utilizar o certificado. Se tal acontecer, clique em **continuar** para utilizar privilégios elevados. 

2. Na página de estado da **ligação** , clique em **Ligar** para iniciar a ligação. Se vir um ecrã de **Selecionar certificado** , certifique-se de que o certificado de cliente a apresentar é aquele que pretende utilizar para ligar. Se não for, utilize a seta pendente para selecionar o certificado correto e, em seguida, clique em **OK**.

    ![Cliente VPN 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Ligação do cliente VPN")

3. Agora deve ser estabelecida a ligação.

    ![Cliente VPN 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Ligação do cliente VPN 2")

## <a name="verify"></a>Parte 9 - Verifique se a sua ligação

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

## <a name="add"></a>Para adicionar ou remover certificados de raiz fidedigna

Pode remover o certificado de raiz confiável a partir do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ligar ao Azure através do Site de ponto. Se pretender que os clientes se liguem, precisa de instalar um novo certificado de cliente é gerado a partir de um certificado que é considerado fidedigno no Azure.

Pode gerir a lista de certificados de cliente revogado na configuração **ponto-para-site** pá. Este é o pá que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).

## <a name="revokeclient"></a>Para gerir a lista de certificados de cliente revogado

Pode revogar certificados de cliente. A lista de revogação de certificados permite-lhe seletivamente negar conectividade de ponto-para-Site com base em certificados de cliente individuais. Se remover um certificado de raiz. cer a partir do Azure,-revoga o acesso para todos os certificados de cliente gerado/assinado pelo certificado de raiz revogado. Se pretende revogar um certificado de cliente em particular, não a raiz, pode fazê-lo. Desta forma os certificados que foram gerados a partir do certificado de raiz irão ainda ser válido. 

Exercícios práticos comuns são utilizar o certificado de raiz para gerir o acesso a níveis de equipa ou organização, ao usar o cliente revogado certificados para controlo de acesso extensivamente em que os utilizadores individuais.

Pode gerir a lista de certificados de cliente revogado na configuração **ponto-para-site** pá. Este é o pá que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).


## <a name="next-steps"></a>Próximos passos

Pode adicionar uma máquina virtual à sua rede virtual. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.
<properties
   pageTitle="Configurar uma ligação de gateway VPN ponto-para-Site a uma rede Virtual do Azure através do portal Azure | Microsoft Azure"
   description="Em segurança ligar à sua rede Virtual Azure através da criação de uma ligação de gateway VPN ponto-para-Site através do portal Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma ligação ponto-para-Site a uma VNet através do portal Azure

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Este artigo explica o criar um VNet com uma ligação ponto-para-Site no modelo de implementação clássica através do portal Azure. Configuração de um ponto Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual a uma rede virtual. Uma ligação de P2S é útil quando pretende ligar à sua VNet a partir de uma localização remota, tais como a partir de casa ou numa conferência. Em alternativa, quando tiver apenas alguns clientes que precisam para se ligar a uma rede virtual.

Ligações ponto-para-Site não necessitam de um dispositivo VPN ou um endereço IP público para trabalhar. Uma ligação VPN é estabelecida iniciando a ligação a partir do computador cliente. Para mais informações sobre as ligações ao ponto para o Site, consulte as [FAQ de Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementação e métodos para P2S ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os dois modelos de implementação e os métodos de implementação disponíveis para configurações P2S. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Fluxo de trabalho básico 

![Ponto-para-Site-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "site de ponto")


As secções seguintes guiá-lo ao longo dos passos para criar uma ligação segura do ponto-para-Site a uma rede virtual. 

1. Criar uma rede virtual e VPN gateway
2. Gerar certificados
3. Carregar o ficheiro. cer
4. Gerar o pacote de configuração do cliente VPN
5. Configurar o computador cliente
6. Ligar ao Azure

### <a name="example-settings"></a>Definições de exemplo

Pode utilizar as seguintes definições de exemplo:

- **Nome: VNet1**
- **Espaço de endereços: 192.168.0.0/16**
- **Nome de sub-rede: front-end**
- **Intervalo de endereços de sub-rede: 192.168.1.0/24**
- **Subscrição:** Se tiver mais do que uma subscrição, certifique-se de que está a utilizar o correto.
- **Grupo de recursos: TestRG**
- **Localização: Leste dos e.u.a.**
- **Tipo de ligação: ponto-para-site**
- **Espaço de endereços de cliente: 172.16.201.0/24**. Os clientes VPN que se ligam ao VNet utilizando esta ligação de ponto-para-Site receber um endereço IP do conjunto de especificado.
- **GatewaySubnet: 192.168.200.0/24**. A sub-rede Gateway tem de utilizar o nome "GatewaySubnet".
- **Tamanho:** Selecione o gateway SKU que pretende utilizar.
- **Tipo de encaminhamento: dinâmicos**

## <a name="vnetvpn"></a>Secção 1 - criar uma rede virtual e um gateway VPN

### <a name="createvnet"></a>Parte 1: Criar uma rede virtual

Se ainda não tiver uma rede virtual, crie uma. Capturas de ecrã são fornecidas como exemplos. Certifique-se de que substitua os valores com o seu próprio. Para criar um VNet utilizando o portal do Azure, utilize os passos seguintes: 

1. A partir de um browser, navegue para o [portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta Azure.

2. Clique em **Novo**. No campo de **pesquisa de mercado** , escreva "Rede Virtual". Localize a **Rede Virtual** a partir da lista devolvida e clique para abrir o pá **Rede Virtual** .

    ![Procurar pá de rede virtual] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Procurar pá de rede virtual")

3. Perto da parte inferior da pá rede Virtual, na lista **Selecione um modelo de implementação** , selecione **clássica**e, em seguida, clique em **Criar**.

    ![Selecione o modelo de implementação] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Selecione o modelo de implementação")

4. No pá **criar rede virtual** , configure as definições de VNet. Neste pá, vai adicionar a sua primeira espaço de endereços e um intervalo de endereços de sub-rede única. Depois de criar o VNet, pode voltar atrás e adicionar sub-redes adicionais e espaços de endereços.

    ![Criar pá de rede virtual] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Criar pá de rede virtual")

5. Certifique-se de que a **subscrição** é o correto. Pode alterar as subscrições ao utilizar o menu pendente.

6. Clique em **grupo de recursos** e quer selecione um grupo de recursos existente ou crie um novo ao escrever um nome para o novo grupo de recursos. Se estiver a criar um novo grupo, dê um nome de grupo de recursos de acordo com os valores de configuração planeada. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

7. Em seguida, selecione as definições de **localização** para a sua VNet. A localização vai determinar onde os recursos que implementar para este VNet residirão.

8. Selecione **Afixar ao dashboard** se pretender que possam localizar o seu VNet facilmente no dashboard de e, em seguida, clique em **Criar**.
    
    ![Afixar ao dashboard] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Afixar ao dashboard")


9. Depois de clicar em criar, irá ver um mosaico no seu dashboard que vai refletir as o progresso da sua VNet. O mosaico alterações à medida que o VNet está a ser criado.

    ![Rede virtual criar dispor em mosaico] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Rede virtual criar dispor em mosaico")

10. Depois de criar a sua rede virtual, pode adicionar o endereço IP do servidor de DNS para processar resolução do nome. Abrir as definições para a sua rede virtual, clique em DNS servers e adicione o endereço IP do servidor DNS que pretende utilizar. Esta definição não cria um novo servidor DNS. Certifique-se de que adicionar um servidor DNS que os recursos podem comunicar com.

Assim que a sua rede virtual ter sido criada, irá ver **que criado** listado em **Estado** , na página de redes no portal do Azure clássica.

### <a name="gateway"></a>Parte 2: Criar sub-rede de gateway e um gateway encaminhamento dinâmico

Neste passo, irá criar uma sub-rede de gateway e um gateway encaminhamento dinâmico. No portal do Azure para o modelo clássico de implementação, pode ser feito criar sub-rede gateway e o gateway através das mesmas lâminas de configuração.

1. No portal do, navegue para a rede virtual para o qual pretende criar um gateway.

2. No pá para a sua rede virtual, no pá **Descrição geral** , na secção de ligações VPN, clique em **Gateway**.

    ![Clique aqui para criar um gateway] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Clique aqui para criar um gateway")


3. No pá **Nova ligação VPN** , selecione **ponto-para-site**.

    ![Tipo de ligação P2S] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "Tipo de ligação P2S")

4. Para o **Espaço de endereços do cliente**, adicione o intervalo de endereços IP. Este é o intervalo a partir do qual os clientes VPN receberão um endereço IP ao ligar. Eliminar o intervalo de preenchimento automático e adicione a sua própria.

    ![Espaço de endereços do cliente] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Espaço de endereços do cliente")

5. Selecione a caixa de verificação **Criar gateway imediatamente** .

    ![Criar gateway imediatamente] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Criar gateway imediatamente")

6. Clique em **configuração de gateway opcional** para abrir o pá de **configuração de Gateway** .

    ![Clique em configuração de gateway opcional] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Clique em configuração de gateway opcional")

7. Clique em **Configurar sub-rede obrigatório definições** para adicionar a **sub-rede de gateway**. Enquanto é possível criar uma sub-rede de gateway tão pequenas quanto /29, recomendamos que cria uma sub-rede maior que inclui mais endereços ao selecionar, pelo menos, /28 ou /27. Isto permitirá para endereços suficientes acomodar possíveis configurações adicionais que pode querer no futuro.

    >[AZURE.IMPORTANT] Quando trabalha com sub-redes de gateway, evite associando um grupo de segurança de rede (NSG) à sub-rede de gateway. Associar um grupo de segurança de rede para esta sub-rede pode fazer com que o gateway de VPN parar a funcionar como esperado. Para obter mais informações sobre os grupos de segurança de rede, consulte o artigo [o que é um grupo de segurança da rede?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Adicionar GatewaySubnet] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Adicionar GatewaySubnet")

8. Selecione o **tamanho**de gateway. Este é o gateway SKU que irá utilizar para criar o seu gateway de rede virtual. No portal do, a SKU predefinido é **básica**. Para mais informações sobre o gateway SKUs, consulte o artigo [Sobre VPN as definições de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Selecione o **Tipo de encaminhamento** para o gateway. Configurações de P2S necessitam de um tipo de encaminhamento **dinâmicos** . Clique em **OK** quando terminar de configurar este pá.

    ![Configurar encaminhamento tipo] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configurar encaminhamento tipo")

10. No pá **Nova ligação VPN** , clique em **OK** na parte inferior da pá para começar a criar o seu gateway rede virtual. Isto pode demorar até 45 minutos a concluir. 


## <a name="generatecerts"></a>Secção 2 - gerar certificados

Certificados são utilizados por Azure para autenticar os clientes VPN para VPNs ponto-para-Site. Exportar dados de certificado pública (não a chave privada) como uma Base-64 codificado x. 509. cer ficheiro a partir de um certificado de raiz gerados por uma solução de certificado de empresa ou um certificado autoassinado de raiz. Importar, em seguida, os dados de certificados público do certificado de raiz para Azure. Para além disso, tem de gerar um certificado de cliente a partir do certificado de raiz para clientes. Cada cliente que pretende ligar à rede virtual utilizando uma ligação de P2S tem de ter instalado um certificado de cliente que foi criado a partir do certificado de raiz.

### <a name="cer"></a>Parte 1: Obter o ficheiro. cer para o certificado de raiz


Se estiver a utilizar uma solução empresarial, pode utilizar a sua cadeia de certificado existente. Se não estiver a utilizar uma solução AC de empresa, pode criar um certificado autoassinado raiz. Um método para criar um certificado autoassinado é makecert.

- Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro. cer para o certificado de raiz que pretende utilizar. 

- Se não estiver a utilizar uma solução de certificado empresarial, é necessário gerar um certificado autoassinado raiz. Para obter os passos do Windows 10, pode consultar a [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um ficheiro. cer a partir de um certificado, abra **certmgr** e localize o certificado de raiz. O certificado autoassinado raiz de contexto, clique em **todas as tarefas**e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para exportar certificados**.

2. No assistente, clique em **seguinte**, selecione **não, não exportar a chave privada**e, em seguida, clique em **seguinte**.

3. Na página do **Formato de ficheiro de exportação** , selecione **Base-64 codificado x. 509 (. CER).** Em seguida, clique em **seguinte**. 

4. No **ficheiro para exportar**, **procure** a localização para o qual pretende exportar o certificado. Para **nome de ficheiro**, nome do ficheiro de certificado. Em seguida, clique em **seguinte**.

5. Clique em **Concluir** para exportar o certificado.


### <a name="genclientcert"></a>Parte 2: Gerar um certificado de cliente

Quer pode gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado no vários clientes. A vantagem para gerar certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos os utilizadores está a utilizar o mesmo certificado de cliente e localizar o que precisa de revogar o certificado para um cliente, terá de gerar e instale novos certificados para todos os clientes que utilizam esse certificado para autenticar.

- Se estiver a utilizar uma solução de certificado empresarial, gerar um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com', em vez do formato de 'nomededomínio'. 

- Se estiver a utilizar um certificado autoassinado, consulte o artigo [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="exportclientcert"></a>Parte 3: Exportar o certificado de cliente

Instale um certificado de cliente em cada computador que pretende ligar à rede virtual. Um certificado de cliente é necessário para autenticação. Pode automatizar instalar o certificado de cliente ou pode instalá-lo manualmente. Os passos seguintes orientá-lo na exportar e instalar manualmente o certificado de cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr*. Botão direito do rato o certificado de cliente que pretende exportar, clique em **todas as tarefas**e, em seguida, clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *. pfx* . Certifique-se gravar ou lembre-se a palavra-passe (tecla) que definiu para este certificado.

## <a name="upload"></a>Secção 3 - carregar o ficheiro. cer de certificado de raiz

Depois do gateway foi criado, pode carregar o ficheiro. cer para um certificado de raiz confiável para Azure. Pode carregar ficheiros até 20 para certificados de raiz. Não carregar a chave privada para o certificado de raiz para Azure. Assim que o ficheiro. cer é carregado, Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Na secção de **ligações VPN** da pá para sua VNet, clique no gráfico de **clientes** para abrir o **ponto-para-site VPN ligação** pá.

    ![Clientes] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clientes")

2. Na ligação **ponto-para-site** pá, clique em **Gerir certificados** para abrir o pá **certificados** .<br>

    ![Pá de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. No pá **certificados** , clique em **carregar** para abrir o pá **Carregar certificado** .<br>

    ![Carregar pá de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Clique no gráfico de pasta para procurar o ficheiro. cer. Selecione o ficheiro e, em seguida, clique em **OK**. Atualize a página para ver o certificado carregado no pá **certificados** .

    ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Secção 4 - gerar o pacote de configuração do cliente VPN

Para se ligar à rede virtual, também tem de configurar um cliente VPN. O computador cliente requer um certificado de cliente e o pacote de configuração de cliente VPN inicial para poder ligar.

O pacote de cliente VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instalar software adicional. As definições são específicas para a rede virtual ao qual pretende ligar. Para a lista dos sistemas operativos cliente que são suportadas, consulte as ligações de [ponto-para-Site](vpn-gateway-vpn-faq.md#point-to-site-connections) secção das perguntas mais frequentes do VPN Gateway. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para gerar o pacote de configuração do cliente VPN

1. No portal do Azure, no pá a **Descrição geral** do seu VNet, em **ligações VPN**, clique no gráfico de cliente para abrir o **ponto-para-site VPN ligação** pá.
2. Na parte superior do **ponto-para-site VPN ligação** pá, clique no transferir o pacote que corresponde ao sistema operativo de cliente no qual será instalado:

 - Para os clientes de 64 bits, selecione **O cliente de VPN (64 bits)**.
 - Para clientes da versão de 32 bits, selecione **O cliente de VPN (32 bits)**.

    ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Verá uma mensagem que Azure está a gerar o pacote de configuração do cliente VPN para a rede virtual. Depois de alguns minutos, é gerado o pacote e verá uma mensagem no seu computador local que tenha sido transferido o pacote. Guarde o ficheiro de pacote de configuração. Instalará isto em cada computador cliente que irá ligar à rede virtual utilizando P2S.


## <a name="clientconfiguration"></a>Secção 5 - Configure o computador cliente

### <a name="part-1-install-the-client-certificate"></a>Parte 1: Instalar o certificado de cliente

Cada computador cliente tem de ter um certificado de cliente para autenticar. Ao instalar o certificado de cliente, terá a palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro. pfx para o computador cliente.
2. Faça duplo clique sobre o ficheiro. pfx para o instalar. Não modifique a localização da instalação.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Instalar o pacote de configuração do cliente VPN

Pode utilizar o pacote de configuração de cliente VPN mesmo em cada computador cliente, desde que a versão corresponde a arquitetura do cliente.

1. Copie o ficheiro de configuração localmente para o computador ao qual pretende ligar à sua rede virtual e faça duplo clique no ficheiro .exe. 

2. Assim que tiver instalado o pacote, pode começar a ligação VPN. O pacote de configuração não tiver sessão iniciado pela Microsoft. Poderá pretende assinar o pacote utilizando o serviço de assinatura da sua organização ou inicie o seu próprio utilizando [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). É para utilizar o pacote sem iniciar sessão. No entanto, se o pacote não está a sessão iniciado, aparece um aviso quando instalar o pacote.

3. No computador cliente, navegue para **As definições de rede** e clique em **VPN**. Irá ver a ligação listada. -Mostra o nome da rede virtual que irá estabelecer ligação e terá uma aspeto semelhante ao seguinte: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "Cliente VNet VPN")

## <a name="connect"></a>Secção 6 - ligar ao Azure

### <a name="connect-to-your-vnet"></a>Ligar a sua VNet

1. Para ligar à sua VNet, no computador cliente, navegue para ligações VPN e localize a ligação VPN que criou. -Chama-se o mesmo nome como a sua rede virtual. Clique em **Ligar**. Poderá ser apresentada uma mensagem de pop-up que se refira a utilizar o certificado. Se tal acontecer, clique em **continuar** para utilizar privilégios elevados. 

2. Na página de estado da **ligação** , clique em **Ligar** para iniciar a ligação. Se vir um ecrã de **Selecionar certificado** , certifique-se de que o certificado de cliente a apresentar é aquele que pretende utilizar para ligar. Se não for, utilize a seta pendente para selecionar o certificado correto e, em seguida, clique em **OK**.

    ![Ligar] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "Ligação do cliente VPN")

3. Agora deve ser estabelecida a ligação.

    ![Ligação ESTABLISHED] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Estabelecer ligação")

### <a name="verify-the-vpn-connection"></a>Verifique a ligação VPN

1. Para verificar se a sua ligação VPN está ativa, abra uma linha de comandos elevada e executar *ipconfig/tudo*.
2. Ver os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereço de conectividade do ponto-para-Site que especificou quando criou o seu VNet. Os resultados deverão ser algo semelhante ao seguinte:

Exemplo:



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Próximos passos

Pode adicionar máquinas virtuais à sua rede virtual. Veja [como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).
<properties
   pageTitle="Configurar uma ligação de gateway VPN ponto-para-Site a uma rede Virtual do Azure através do portal clássico | Microsoft Azure"
   description="Em segurança ligar à sua rede Virtual Azure através da criação de uma ligação de gateway VPN de ponto-para-Site."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurar uma ligação ponto-para-Site a uma VNet através do portal clássico

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Clássico - clássico de Portal](vpn-gateway-point-to-site-create.md)

Configuração de um ponto Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual a uma rede virtual. Uma ligação de P2S é útil quando pretende ligar à sua VNet a partir de uma localização remota, tais como a partir de casa ou numa conferência, ou quando tiver apenas alguns clientes que precisam para se ligar a uma rede virtual.

Este artigo explica o criar um VNet com uma ligação ponto-para-Site no **modelo de implementação clássica** utilizando o **portal clássica**.

Ligações ponto-para-Site não necessitam de um dispositivo VPN ou um endereço IP público para trabalhar. Uma ligação VPN é estabelecida iniciando a ligação a partir do computador cliente. Para mais informações sobre as ligações ao ponto para o Site, consulte as [FAQ de Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [planeamento e estrutura](vpn-gateway-plan-design.md).


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementação e métodos para P2S ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os dois modelos de implementação e os métodos de implementação disponíveis para configurações P2S. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Fluxo de trabalho básico

![Ponto-para-Site-diagrama] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "site de ponto")
 
Os passos seguintes guiá-lo ao longo dos passos para criar uma ligação segura do ponto-para-Site a uma rede virtual. 

A configuração de uma ligação ponto-para-Site é dividida em quatro secções. A ordem pela qual configura cada uma das seguintes secções é importante. Não ignore os passos ou ir para a frente.

- **Secção 1** Crie um VPN gateway e de rede virtual.
- **Secção 2** Criar os certificados utilizados para autenticação e carregue-os.
- **Secção 3** Exportar e instale os certificados de cliente.
- **Secção 4** Configure o seu cliente de VPN.

## <a name="vnetvpn"></a>Secção 1 - criar uma rede virtual e um gateway VPN

### <a name="part-1-create-a-virtual-network"></a>Parte 1: Criar uma rede virtual

1. Inicie a sessão [portal clássica Azure](https://manage.windowsazure.com/). Estes passos que utilizam o portal clássico, não o portal do Azure. Atualmente, não é possível criar uma ligação de P2S utilizando o portal do Azure.

2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de rede**e, em seguida, clique em **Rede Virtual**. Clique em **Criar personalizado** para iniciar o Assistente de configuração.

3. Na página **Detalhes da rede Virtual** , introduza as seguintes informações e, em seguida, clique na seta seguinte na parte inferior direita.
    - **Nome**: atribuir um nome a sua rede virtual. Por exemplo, 'VNet1'. Este é o nome que irá se referem à quando implementar VMs este VNet.
    - **Localização**: A localização diretamente está relacionada com a localização física (região) onde pretende que os recursos (VMs) para residem. Por exemplo, se pretender que o VMs que implementar a esta rede virtual física localizados no Leste dos EUA, selecione essa localização. Não é possível alterar a região associada com a sua rede virtual depois de o criar.

4. Na página **DNS Servers e grupo análise** , introduza as seguintes informações e, em seguida, clique na seta seguinte na parte inferior direita.
    - **Os servidores DNS**: introduza o nome de servidor DNS e o endereço IP ou selecione um servidor DNS registado anteriormente no menu de atalho. Esta definição não cria um servidor de DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução do nome para esta rede virtual. Se pretender utilizar o serviço de resolução do nome predefinido Azure, deixe esta secção em branco.
    - **Configurar ponto-para-Site VPN**: selecione a caixa de verificação.

5. Na página de **Conectividade do ponto de sites** , especifique o intervalo de endereços IP a partir do qual os seus clientes VPN irão receber um endereço IP quando ligado. Existem algumas regras relativas aos intervalos de endereços que pode especificar. É importante confirmar que o intervalo que especificar não se sobrepõe com qualquer um dos intervalos localizados na sua rede no local.

6. Introduza as seguintes informações e, em seguida, clique na seta seguinte.
 - **Espaço de endereços**: incluem as IP inicial e CIDR (contagem de endereço).
 - **Adicionar espaço de endereços**: adicione espaço de endereços apenas se for necessário para a estrutura da rede.

7. Na página **Virtual espaços de endereços de rede** , especifique o intervalo de endereços que pretende utilizar para a sua rede virtual. Estes são os endereços IP dinâmicos (DIMINUIÇÕES) serão atribuídos para os VMs e outras instâncias de função implementar a esta rede virtual.<br><br>É importante especialmente selecionar um intervalo que não se sobrepõe com qualquer um dos intervalos que são utilizados para a sua rede no local. Terá de coordenar com o seu administrador de rede, que poderá ter de excepção saída de um intervalo de endereços IP a partir do seu espaço de endereços de rede no local para utilizar para a sua rede virtual.

8. Introduza as seguintes informações e, em seguida, clique em marca de verificação para começar a criar a sua rede virtual.
 - **Espaço de endereços**: adicionar o intervalo de endereços IP interno que pretende utilizar para esta rede virtual, incluindo IP inicial e contar. É importante selecionar um intervalo que não se sobrepõe com qualquer um dos intervalos que são utilizados para a sua rede no local. 
 - **Adicionar sub-rede**: sub-redes adicionais não são obrigatórios, mas pretende criar uma sub-rede separada para VMs terão DIMINUIÇÕES estáticas. Ou poderá ter os VMs no sub-rede que está separada do seu outras instâncias de função.
 - **Adicionar sub-rede de gateway**: sub-rede gateway é necessária para um ponto-para-Site VPN. Clique para adicionar a sub-rede de gateway. A sub-rede gateway é utilizada apenas para o gateway de rede virtual.

9. Assim que a sua rede virtual ter sido criada, irá ver **que criado** listado em **Estado** , na página de redes no portal do Azure clássica. Assim que a sua rede virtual ter sido criada, pode criar o gateway encaminhamento dinâmico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: Criar um gateway encaminhamento dinâmico

O tipo de gateway tem de ser configurado tão dinâmico. Estáticos gateways encaminhamento não funcionam com esta funcionalidade.

1. No portal clássico Azure, na página de **redes** , clique na rede virtual que criou e navegue para a página do **Dashboard** .

2. Clique em **Criar Gateway**, localizada na parte inferior da página do **Dashboard** . Aparece uma mensagem a perguntar **que pretende criar um gateway para a rede virtual "VNet1"**. Clique em **Sim** para começar a criar o gateway. Pode demorar cerca de 15 minutos para que o gateway criar.

## <a name="generate"></a>Secção 2 - gerar e carregue os certificados

Certificados são utilizados para autenticar os clientes VPN para VPNs ponto-para-Site. Pode utilizar um certificado de raiz gerado por uma solução de certificado empresarial ou pode utilizar um certificado autoassinado. Pode carregar até 20 certificados de raiz para o Azure. Assim que o ficheiro. cer é carregado, Azure pode utilizar as informações contidas na mesma para autenticar os clientes que tenham um certificado de cliente instalado. O certificado de cliente tem de ser gerado a partir do mesmo certificado que representa o ficheiro. cer.

Nesta secção irá faça o seguinte:

- Obtenha o ficheiro. cer para um certificado de raiz. Isto pode ser um certificado autoassinado ou pode utilizar o seu sistema de certificado de empresa.
- Carregue o ficheiro. cer para Azure.
- Gere certificados de cliente.

### <a name="root"></a>Parte 1: Obter o ficheiro. cer para o certificado de raiz

Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro. cer para o certificado de raiz que pretende utilizar. No [parte 3](#createclientcert), gerar os certificados de cliente a partir do certificado de raiz.

Se não estiver a utilizar uma solução de certificado empresarial, terá de gerar um certificado autoassinado raiz. Para obter os passos do Windows 10, pode consultar a [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md). O artigo explica o utilizar makecert para gerar um certificado autoassinado e, em seguida, exporte o ficheiro. cer.

### <a name="upload"></a>Parte 2: Carregar o ficheiro. cer certificado de raiz portal clássica do Azure

Adicione um certificado fidedigno para Azure. Quando adiciona um ficheiro codificado em Base64 x. 509 (. cer) para Azure, são informar Azure para confiar no certificado de raiz que representa o ficheiro.

1. No portal clássico Azure, na página de **certificados** para a sua rede virtual, clique em **carregar um certificado de raiz**.

2. Na página **Carregar certificado** , procure o certificado de raiz. cer e, em seguida, clique na marca de verificação.

### <a name="createclientcert"></a>Parte 3: Gerar um certificado de cliente

Em seguida, gere os certificados de cliente. Quer pode gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado no vários clientes. A vantagem para gerar certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos os utilizadores está a utilizar o mesmo certificado de cliente e localizar o que precisa de revogar o certificado para um cliente, terá de gerar e instale novos certificados para todos os clientes que utilizam o certificado para autenticar.

- Se estiver a utilizar uma solução de certificado empresarial, gerar um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com', em vez do NetBIOS 'Domínio \ nomedeutilizador' Formatar. 

- Se estiver a utilizar um certificado autoassinado, consulte o artigo [trabalhar com os certificados de raiz autoassinado para configurações de ponto-para-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

## <a name="installclientcert"></a>Secção 3 - exportar e instale o certificado de cliente

Instale um certificado de cliente em cada computador que pretende ligar à rede virtual. Um certificado de cliente é necessário para autenticação. Pode automatizar instalar o certificado de cliente ou pode instalar manualmente. Os passos seguintes orientá-lo na exportar e instalar manualmente o certificado de cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr*. Botão direito do rato o certificado de cliente que pretende exportar, clique em **todas as tarefas**e, em seguida, clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *. pfx* . Certifique-se gravar ou lembre-se a palavra-passe (tecla) que definiu para este certificado.
3. Copie o ficheiro *. pfx* para o computador cliente. No computador cliente, faça duplo clique sobre o ficheiro *. pfx* para o instalar. Introduza a palavra-passe quando solicitado. Não modifique a localização da instalação.

## <a name="vpnclientconfig"></a>Secção 4 - configurar o seu cliente de VPN

Para se ligar à rede virtual, também tem de configurar um cliente VPN. O cliente requer um certificado de cliente e a configuração inicial do cliente VPN para poder ligar. Para configurar um cliente VPN, execute os passos seguintes, ordem.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: Criar o pacote de configuração do cliente VPN

1. No portal clássico Azure, na página **Dashboard** para a sua rede virtual, navegue para o menu Vista rápida no canto direito. Para a lista dos sistemas operativos cliente que são suportadas, consulte as ligações de [ponto-para-Site](vpn-gateway-vpn-faq.md#point-to-site-connections) secção das perguntas mais frequentes do VPN Gateway. O pacote de cliente VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instalar software adicional. As definições são específicas para a rede virtual ao qual pretende ligar.<br><br>Selecione o pacote de transferência que corresponde ao sistema operativo de cliente no qual será instalado:
 - Para clientes da versão de 32 bits, selecione **Transferir o pacote de VPN do cliente de 32 bits**.
 - Para os clientes de 64 bits, selecione **Transferir o pacote de VPN do cliente de 64 bits**.

2. Bastam alguns minutos para criar o seu pacote de cliente. Assim que tiver sido concluído o pacote, pode transferir o ficheiro. O ficheiro de *.exe* que transferir pode ser armazenado em segurança no seu computador local.

3. Depois de gerar e transferir o pacote de cliente VPN a partir do portal clássico Azure, pode instalar o pacote de cliente no computador cliente a partir do qual pretende ligar à sua rede virtual. Se planear instalar o pacote de cliente VPN para vários computadores de cliente, certifique-se de que cada também têm um certificado de cliente instalado.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: Instalar o pacote de configuração de VPN no cliente

1. Copie o ficheiro de configuração localmente para o computador ao qual pretende ligar à sua rede virtual e faça duplo clique no ficheiro .exe. 

2. Assim que tiver instalado o pacote, pode começar a ligação VPN. O pacote de configuração não tiver sessão iniciado pela Microsoft. Poderá pretende assinar o pacote utilizando o serviço de assinatura da sua organização ou inicie o seu próprio utilizando [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). É para utilizar o pacote sem iniciar sessão. No entanto, se o pacote não está a sessão iniciado, aparece um aviso quando instalar o pacote.

3. No computador cliente, navegue para **As definições de rede** e clique em **VPN**. Irá ver a ligação listada. -Apresentará o nome da rede virtual que irá estabelecer ligação e terá uma aspeto semelhante ao seguinte: 

    ![Cliente VPN] (./media/vpn-gateway-point-to-site-create/vpn.png "Cliente VPN")


### <a name="part-3-connect-to-azure"></a>Parte 3: Ligar a Azure

1. Para ligar à sua VNet, no computador cliente, navegue para ligações VPN e localize a ligação VPN que criou. -Chama-se o mesmo nome como a sua rede virtual. Clique em **Ligar**. Poderá ser apresentada uma mensagem de pop-up que se refira a utilizar o certificado. Se tal acontecer, clique em **continuar** para utilizar privilégios elevados. 

2. Na página de estado da **ligação** , clique em **Ligar** para iniciar a ligação. Se vir um ecrã de **Selecionar certificado** , certifique-se de que o certificado de cliente a apresentar é aquele que pretende utilizar para ligar. Se não for, utilize a seta pendente para selecionar o certificado correto e, em seguida, clique em **OK**.

    ![Cliente VPN 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "Ligação do cliente VPN")

3. Agora deve ser estabelecida a ligação.

    ![Cliente VPN 3] (./media/vpn-gateway-point-to-site-create/connected.png "Ligação do cliente VPN 2")

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Verifique a ligação VPN

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

Se pretender obter mais informações sobre redes virtuais, consulte a página de [Documentação da rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) .

<properties
   pageTitle="Configurar uma ligação de VNet para VNet para o modelo de implementação clássica | Microsoft Azure"
   description="Como ligar Azure redes virtuais em conjunto com o PowerShell e o portal de clássico Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurar uma ligação de VNet para VNet para o modelo clássico de implementação

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clássico - clássico de Portal](virtual-networks-configure-vnet-to-vnet-connection.md)



Este artigo explica os passos para criar e ligar as redes virtuais em conjunto com o modelo de implementação clássico (também conhecido como o serviço de gestão). Os passos seguintes utilizam o portal clássico Azure para criar as VNets e gateways e PowerShell para configurar a ligação VNet para VNet. Não é possível configurar a ligação no portal.

![VNet a VNet conectividade diagrama](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementação e métodos para VNet para VNet ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos de implementação atualmente disponíveis e os métodos para VNet para VNet configurações. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações de VNet para VNet

(VNet para VNet) que liga uma rede virtual a outra rede virtual é semelhante a estabelecer ligação uma rede virtual para uma localização do site no local. Ambos os tipos de conectividade utilizam um gateway VPN para fornecer um túnel seguro através do IPsec/IKE. 

VNets que ligar pode estar em diferentes subscrições e regiões diferentes. Pode combinar VNet para comunicação de VNet com configurações de vários sites. Permite-lhe estabelecer topologias de rede que combinam conectividade de publicação em local com a conectividade de rede entre virtual.


### <a name="why-connect-virtual-networks"></a>Por que motivo ligar redes virtuais?

Pretende ligar redes virtuais pelos seguintes motivos:

- **Cruzada região geo-redundância e geo presença**
    - Pode configurar o seu próprio geo replicação ou a sincronização com a conectividade segura sem ultrapassar pontos finais de acesso à Internet.
    - Com Balanceador de carga Azure e Microsoft ou tecnologia clusters de terceiros, pode configurar o altamente disponível carga de trabalho com geo redundância entre as regiões Azure múltiplos. Um exemplo importante é configurar o SQL sempre com grupos de disponibilidade propagação entre as regiões Azure múltiplos.

- **Aplicações de várias camadas regionais com o limite de isolamento fortes**
    - Dentro da mesma região, pode configurar as aplicações com várias camadas com vários VNets ligado juntamente com o forte isolamento e comunicação dependências entre camada segura.

- **Cruzada subscrição, comunicação de organização dependências entre no Azure**
    - Se tiver múltiplas subscrições Azure, pode ligar das cargas de trabalho de subscrições diferentes em conjunto em segurança entre redes virtuais.
    - Para empresas ou fornecedores de serviço, pode ativar a publicação em organização comunicação com tecnologia VPN segura dentro Azure.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>FAQ de VNet para VNet para VNets clássico

- As redes virtuais podem estar nas subscrições do mesmo ou diferentes.

- As redes virtuais podem ser na mesmas ou em diferentes Azure regiões (localizações).

- Um serviço na nuvem ou um ponto final de balanceamento de carga não é possível do intervalo através das redes virtuais, mesmo se estiverem ligados em conjunto.

- Ligar-se em conjunto múltiplas redes virtuais não necessita de todos os dispositivos VPN.

- VNet para VNet suporta a ligação do Azure de redes Virtual. Não suporta a ligação máquinas virtuais ou na nuvem serviços que não são implementados a uma rede virtual.

- VNet para VNet requer o encaminhamento de gateways dinâmicos. Azure gateways encaminhamento estáticos não são suportadas.

- A conectividade da rede virtual pode ser utilizada em simultâneo com VPNs múltiplos sites. Existe um máximo de 10 túneis VPN para um gateway VPN rede virtual estabelecer ligação a outras redes virtuais ou sites no local.

- Os espaços de endereço dos sites de rede local de redes e no local virtuais não sobrepor. Sobreposição de espaços de endereços causará a criação de carregamento dos ficheiros de configuração de netcfg falha ou redes virtuais.

- Túneis redundantes entre um par de redes virtuais não são suportados.

- Todos os túneis VPN para VNet, incluindo P2S VPNs, partilham a largura de banda disponível para o gateway VPN e o mesmo VPN gateway tempo de utilização SLA no Azure.

- Tráfego VNet para VNet percorre a estrutura principal Azure.


## <a name="step1"></a>Passo 1 - Planear a sua intervalos de endereços IP

É importante decidir os intervalos que irá utilizar para configurar redes virtuais. Para esta configuração, deve certificar-se de que nenhuma das intervalos VNet sobrepor-se entre si, ou com qualquer uma das redes locais que se ligam a.

A tabela seguinte mostra um exemplo de como definir o seu VNets. Utilize os intervalos como apenas uma orientação. Anote os intervalos de redes virtuais. Precisar desta informação para obter os passos posteriores.

**Definições de exemplo**

|Rede virtual  |Espaço de endereços               |Região      |Se liga ao site de rede local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |Oeste dos e.u.a.     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Japão leste  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Passo 2 - Criar VNet1

Neste passo, criamos VNet1. Ao utilizar qualquer um dos exemplos, certifique-se de que substituir os seus próprios valores. Se o seu VNet já existir, não precisa de efetuar este passo. No entanto, necessita de confirmar que os intervalos de endereços IP não sobreponha-a com os intervalos para sua segunda VNet ou com qualquer um da outros VNets ao qual pretende ligar.

1. Inicie a sessão [portal clássica Azure](https://manage.windowsazure.com). Neste artigo, vamos utilizar o portal do clássico porque algumas das definições de configuração requeridas ainda não estão disponíveis no portal do Azure.

2. No canto inferior esquerdo do ecrã, clique em **Novo** > **Serviços de rede** > **Rede Virtual** > **Personalizada criar** para iniciar o Assistente de configuração. Como navegar através do assistente, adicione os valores especificados para cada página.

### <a name="virtual-network-details"></a>Detalhes de rede virtual

Na página Detalhes da rede Virtual, introduza as seguintes informações:

  ![Detalhes de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nome** - nome da sua rede virtual. Por exemplo, VNet1.
  - **Localização** – quando cria uma rede virtual, associá-lo a uma localização Azure (região). Por exemplo, se pretender que o seu VMs que são implementadas à sua rede virtual física localizados no ocidental dos EUA, selecione essa localização. Não é possível alterar a localização associada com a sua rede virtual depois de o criar.

### <a name="dns-servers-and-vpn-connectivity"></a>Os servidores DNS e grupo análise

Na página DNS Servers e grupo análise, introduza as seguintes informações e, em seguida, clique na seta seguinte na parte inferior direita.

  ![Os servidores DNS e grupo análise](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **Os servidores DNS** - introduza o nome de servidor DNS e o endereço IP ou selecione um servidor DNS registado anteriormente na lista pendente. Esta definição não cria um servidor de DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução do nome para esta rede virtual. Se pretender que a resolução do nome entre as redes virtuais, tem de configurar o seu servidor de DNS, em vez de utilizar a resolução de nomes que é fornecida pela Azure.
- Não selecione qualquer uma das caixas de verificação para P2S ou S2S conectividade. Clique na seta na parte inferior direita para mover para o ecrã seguinte.

### <a name="virtual-network-address-spaces"></a>Espaços de endereços de rede virtual

Na página Virtual espaços de endereços de rede, especifique o intervalo de endereços que pretende utilizar para a sua rede virtual. Estes são os endereços IP dinâmicos (DIMINUIÇÕES) serão atribuídos para os VMs e outras instâncias de função implementar a esta rede virtual. 

Se estiver a criar um VNet que também terá uma ligação à sua rede no local, é importante especialmente selecionar um intervalo que não se sobrepõe com qualquer um dos intervalos que são utilizados para a sua rede no local. Nesse caso, tem de estão coordenados com o seu administrador de rede. O administrador de rede poderá ter de excepção saída de um intervalo de endereços IP a partir do seu espaço de endereços de rede no local para utilizar para o seu VNet.

  ![Página de rede endereço espaços virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espaço de endereços** - incluindo IP inicial e contagem de endereço. Certifique-se de que os espaços de endereço que especificou não se sobrepõe com qualquer um dos espaços de endereço tem na sua rede no local. Neste exemplo, utilizamos 10.1.0.0/16 para VNet1.
  - **Adicionar sub-rede** - incluindo IP inicial e contagem de endereço. Sub-redes adicionais não são obrigatórios, mas pretende criar uma sub-rede separada para VMs terão DIMINUIÇÕES estáticas. Ou poderá ter os VMs no sub-rede que está separada do seu outras instâncias de função.
 
**Clique na marca de verificação** no canto inferior direito da página e a sua rede virtual começará a criar. Quando estiver concluído, verá "Criada" listado em estado, na página de redes.

## <a name="step-3---create-vnet2"></a>Passo 3 - criar VNet2

Em seguida, repita os passos anteriores para criar outra VNet. Passos posterior, irá ligar as duas VNets. Pode referir-se para as [definições de exemplo](#step1) no passo 1. Se o seu VNet já existir, não precisa de efetuar este passo. No entanto, tem de verificar que os intervalos de endereços IP não se sobrepõe com qualquer uma das outras VNets ou no local redes ao qual pretende ligar.

## <a name="step-4---add-the-local-network-sites"></a>Passo 4 - adicione os sites de rede local

Quando cria uma configuração VNet para VNet, tem de configurar sites de rede local, que são apresentadas na página de **Redes locais** do portal. Azure utiliza as definições de especificado em cada site de rede local para determinar como deve encaminhar o tráfego entre o VNets. Determinar o nome que pretende utilizar para se referir a cada site de rede local. É melhor utilizar um nome descritivo, à medida que seleciona o valor de uma lista pendente no passos posteriores.

Por exemplo, VNet1 liga-se a um site de rede local que criar com o nome "VNet2Local". As definições de VNet2Local contêm prefixos de endereço para VNet2 e um endereço IP público para o gateway VNet2. VNet2 liga-se a um site de rede local que cria com o nome "VNet1Local" que contém os prefixos de endereço para VNet1 e o endereço IP público para o gateway VNet1.

### <a name="localnet"></a>Adicionar o site de rede local VNet1Local

1. No canto inferior esquerdo do ecrã, clique em **Novo** > **Serviços de rede** > **Rede Virtual** > **Adicionar Rede Local**.

2. Na página **especificar os detalhes do seu local de rede** , para **nome**, introduza um nome que pretende utilizar para representar a rede à qual pretende ligar. Neste exemplo, pode utilizar "VNet1Local" para consultar os intervalos de endereços IP e o gateway para VNet1.

3. Em **endereço IP do dispositivo de VPN (opcional)**, especifique qualquer endereço IP público válido. Normalmente, utilizaria o endereço IP externo real para um dispositivo VPN. Para configurações VNet para VNet, utilize o endereço IP público que é atribuído ao gateway para a sua VNet. No entanto, dado que ainda não tiver criado o gateway, pode especificar qualquer endereço IP público válido como um marcador de posição. Não deixe em branco - não é opcional para esta configuração. Um passo posterior, regresse para estas definições e configurá-los com os endereços IP de gateway correspondentes assim Azure gera-lo. Clique na seta para avançar para o ecrã seguinte.

4. Na **especificar a página de endereço**, introduza a contagem de intervalo e endereço endereço IP para VNet1. Tem de corresponder exatamente ao intervalo que esteja configurado para VNet1. Azure utiliza os intervalos de endereços IP que especificar para encaminhar o tráfego destinado VNet1. Clique em marca de verificação para criar a rede local.

### <a name="add-the-local-network-site-vnet2local"></a>Adicionar o site de rede local VNet2Local

Utilize os passos acima para criar o site de rede local "VNet2Local". Pode fazer referência para os valores nas [definições de exemplo](#step1) no passo 1, se necessário.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurar cada VNet para apontar para uma rede local

Cada VNet têm de apontar para a rede local respetivas que pretende para encaminhar o tráfego para. 

#### <a name="for-vnet1"></a>Para VNet1

1. Navegue para a página de **Configurar** para a rede virtual **VNet1**. 
2. Em conetividade de site para o site, selecione "Ligar à rede local" e, em seguida, selecione **VNet2Local** como rede local na lista pendente. 
3. Guarde as suas definições.

#### <a name="for-vnet2"></a>Para VNet2

1. Navegue para a página de **Configurar** para a rede virtual **VNet2**. 
2. Em conetividade de site para o site, selecione "Ligar à rede local", em seguida, selecione **VNet1Local** na lista pendente como rede local. 
3. Guarde as suas definições.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Passo 5 - configurar um gateway para cada VNet

Configure um gateway encaminhamento dinâmico para cada rede virtual. Esta configuração não suporta gateways encaminhamento estático. Se estiver a utilizar VNets que tenham sido configurados anteriormente e que já tem o encaminhamento dinâmico gateways, não terá de efetuar este passo. Se o seu gateways estão encaminhamento estático, tem de eliminá-los e recrie-as como encaminhamento dinâmico gateways. Se eliminar um gateway, o endereço IP público atribuído à mesma obtém disponibilizado e tem de voltar atrás e reconfigurar qualquer uma das suas redes locais e dispositivos VPN com o endereço IP público novo para o novo gateway.

1. Na página de **redes** , certifique-se de que a coluna de estado para a sua rede virtual é **criado**.

2. Na coluna **nome** , clique no nome da sua rede virtual. Neste exemplo, utilizamos "VNet1".

3. Na página do **Dashboard** , repare que este VNet não tem um gateway ainda configurado. Verá este estado alterar à medida que avança através dos passos para configurar o gateway.

4. Na parte inferior da página, clique em **Criar Gateway** e **Encaminhamento dinâmico**. Quando o sistema pede para confirmar que pretende que o gateway criado, clique em Sim.

    ![Tipo de gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Quando estiver a criar o gateway, repare o gráfico de gateway na página muda para a amarelo e diz "Criar Gateway". Bastam normalmente cerca de 30 minutos para o gateway criar.

6. Repita os mesmos passos para VNet2. Não precisa do primeiro gateway VNet para concluir antes de começar a criar o gateway para a sua outras VNet.

7. Quando muda o estado do gateway para "Para ligar", o endereço IP público para cada gateway está visível no Dashboard. Escreva o endereço IP que corresponde a cada VNet, tomar cuidado para não misturá-los para cima. Estes são os endereços IP que são utilizados quando edita os endereços IP do marcador de posição para o dispositivo de VPN para cada rede local.

## <a name="step-6---edit-the-local-network"></a>Passo 6 - Editar da rede local

1. Na página de **Redes locais** , clique no nome do nome de rede Local que pretende editar, em seguida, clique em **Editar** na parte inferior da página. Em **endereço IP do dispositivo de VPN**, o endereço IP do gateway que corresponde à VNet de entrada. Por exemplo, para VNet1Local, coloque o endereço de IP de gateway atribuídos a VNet1. Em seguida, clique na seta na parte inferior da página.

2. Na página **especificar o espaço de endereços** , clique em marca de verificação no canto inferior direito sem fazer as alterações.

## <a name="step-7---create-the-vpn-connection"></a>Passo 7 - criar a ligação VPN

Quando tem sido concluídos os passos anteriores, defina as teclas de pré-partilhadas IPsec/IKE e criar a ligação. Este conjunto de passos utiliza PowerShell e não pode ser configurado no portal. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do Azure PowerShell. Certifique-se transferir a versão mais recente dos cmdlets de gestão de serviço (s). 

1. Abra o Windows PowerShell e inicie sessão.

        Add-AzureAccount

2. Selecione a subscrição que seu VNets residem na.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Crie as ligações. Nos exemplos, repare que a chave partilhada é exatamente o mesmo. A chave partilhada tem de corresponder sempre.


    VNet1 VNet2 ligação

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 VNet1 ligação

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Espere que as ligações ao iniciar. Assim que o gateway tem inicializado, o gateway aspeto o gráfico seguinte.

    ![Estado do gateway - ligado](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Próximos passos

Pode adicionar máquinas virtuais às suas redes virtuais. Consulte a [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais informações.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

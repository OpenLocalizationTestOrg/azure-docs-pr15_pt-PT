<properties 
   pageTitle="Configurar ligação VPN entre duas redes virtuais | Microsoft Azure" 
   description="Saiba como configurar ligações de VPN e resolução do nome de domínio entre duas redes virtuais Azure e como configurar a replicação de geo HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Configurar uma ligação VPN entre duas redes virtuais Azure  

> [AZURE.SELECTOR]
- [Configurar a conectividade VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar o DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar a replicação HBase](hdinsight-hbase-geo-replication.md) 

Conectividade de site para o site de rede virtual Azure utiliza um gateway VPN para fornecer um túnel seguro através do Ipsec/IKE. O VNets pode estar em diferentes subscrições e regiões diferentes. Ainda pode combinar VNet para comunicação de VNet com configurações de vários sites. Existem várias razões para VNet a conectividade de VNet:

- Cruzada região geo-redundância e geo presença 
- Aplicações de várias camadas regionais com o limite de isolamento fortes 
- Cruzada subscrição, comunicação de organização dependências entre no Azure

Para mais informações, consulte o artigo [configurar um VNet VNet ligação](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). 

Para vê-los no vídeo:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Neste tutorial é uma parte da [série] [ hdinsight-hbase-replication] sobre a criação de replicação de geo HBase. 

- Configurar uma ligação VPN entre duas redes virtuais (Este tutorial)
- [Configurar o DNS para as redes virtuais][hdinsight-hbase-geo-replication-dns]
- [Configurar a HBase geo replicação][hdinsight-hbase-geo-replication]

O diagrama seguinte ilustra as duas redes virtuais que irá criar neste tutorial:

![Diagrama de rede virtual HDInsight HBase replicação][img-vnet-diagram]
 

##<a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o Azure PowerShell**.

    Antes de executar scripts de PowerShell, certifique-se de que está ligado à sua subscrição do Azure utilizando o cmdlet seguinte:

        Add-AzureAccount

    Se tiver múltiplas subscrições Azure, utilize o cmdlet seguinte para definir a subscrição atual:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Nomes de serviço Azure e nomes de máquina virtual tem de ser exclusivos. O nome utilizado neste tutorial é Contoso-[nome do Azure Service/VM]-[UE / EUA]. Por exemplo, Contoso-VNet-UE é a rede virtual Azure no Centro de dados do Norte Europa; Contoso-DNS-US são o servidor DNS VM no Centro de dados do Leste dos EUA. Tem de vir com o seus próprio nomes.
 

##<a name="create-two-azure-vnets"></a>Criar duas VNets do Azure



**Para criar uma rede virtual denominado Contoso-VNet-UE na Europa América do Norte**

1.  Inicie sessão no [Portal clássica Azure][azure-portal].
2.  Clique em **Novo**, **Serviços de rede**, **da rede VIRTUAL**, **Criar PERSONALIZADO**.
3.  Introduza:

    - **Nome**: Contoso-VNet-UE
    - **Localização**: Europa Norte

        Neste tutorial, utiliza Europa Norte e Leste dos EUA centros de dados. Pode escolher o seus próprio centros de dados.
4.  Introduza:

    - **Servidor DNS**: (deixá-la em branco) 
    
        Terá do seu servidor de DNS para resolução de nomes no prazo de redes virtuais. Para mais informações sobre quando a resolução do nome de Azure fornecido pela utilização e quando deve utilizar o seu servidor de DNS, consulte o artigo [Resolução de nomes (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Para obter instruções configurar a resolução de nomes entre VNets, consulte o artigo [Configurar o DNS entre duas redes virtuais Azure][hdinsight-hbase-dns].
  
    - **Configurar um site de ponto VPN**: (desmarcada)

        Ponto-para-site não se aplica a este cenário.

    - **Configurar um site para o site VPN**: (desmarcada)
    
        Irá configurar a ligação VPN do site para o site para a rede virtual Azure no Centro de dados do Leste dos EUA.
5.  Introduza:

    -   **IP de partida do espaço de endereços**: 10.1.0.0
    -   **Endereço espaço CIDR**: / 16
    -   **Inicial de 1 de sub-rede IP**: 10.1.0.0
    -   **CIDR sub-rede 1**: / 24

    Não pode ser sobreposto o espaço de endereços com a rede virtual dos EUA.  

**Para criar uma rede virtual denominado Contoso-VNet-UE na Europa Oeste**

- Repita o procedimento último com os seguintes valores:

    - **Nome**: Contoso-VNet-pt
    - **Localização**: Leste dos EUA
     
    - **Servidor DNS**: (deixá-la em branco)
    - **Configurar um site de ponto VPN**: (desmarcada)
    - **Configurar um site para o site VPN**: (desmarcada)
     
    - **IP de partida do espaço de endereços**: 10.2.0.0
    - **Endereço espaço CIDR**: / 16
    - **Inicial de 1 de sub-rede IP**: 10.2.0.0
    - **CIDR sub-rede 1**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Configurar uma ligação VPN entre as duas VNets

###<a name="create-local-networks"></a>Criar redes locais

Quando cria uma VNet VNet configuração, tem de configurar cada VNet para identificar os outros como um site de rede local. Nesta secção, irá configurar cada VNet como uma rede local. As redes locais partilham os mesma espaços de endereços IP com o VNet correspondente.

![Configurar a configuração do site para o site do Azure VPN - azure redes locais][img-vnet-lnet-diagram]


**Para criar uma rede local denominado Contoso-LNet-UE correspondência o espaço de endereços de rede Contoso-VNet-UE**

1. A partir do Portal do Azure clássico, clique em **Novo**, **Serviços de rede**, **Rede VIRTUAL**, **Adicionar rede LOCAL**.
3. Introduza:

    - **Nome**: Contoso-LNet-UE
    - **Endereço de IP do dispositivo de VPN**: 192.168.0.1 (este endereço será atualizado mais tarde)

        Normalmente, utilizaria o endereço IP externo real para um dispositivo VPN. Para VNet para as configurações de VNet, irá utilizar o endereço IP de gateway VPN. Dado que não criou os gateways VPN para as duas VNets ainda, introduza um endereço IP arbitrário e voltar atrás corrigi-lo.
4.  Introduza:

    - **IP de partida do espaço de endereços:** 10.1.0.0
    - **CIDR do espaço de endereços:** /16
    
    Tem de corresponder exatamente ao intervalo que especificou anteriormente Contoso-VNet-UE.

**Para criar uma rede local denominado Contoso-LNet-US correspondência o espaço de endereços de rede Contoso-VNet-pt**

- Repita o procedimento último com os seguintes parâmetros:

    - **Nome**: Contoso-LNet-pt
    - **Endereço de IP do dispositivo de VPN**: 192.168.0.1 (este endereço será atualizado mais tarde)
     
    - **IP de partida do espaço de endereços**: 10.2.0.0
    - **Endereço espaço CIDR**: / 16


###<a name="create-vpn-gateways"></a>Criar VPN gateways

Existem duas partes nesta configuração. Configurar primeiro uma ligação de site para o site VNet a uma rede local e, em seguida, criar uma VPN encaminhamento dinâmica. VNet para VNet requer gateways Azure VPN com VPNs encaminhamento dinâmicas. Azure VPNs encaminhamento estáticas não são suportadas.

**Para configurar a ligação de site para o site da Contoso-VNet-UE para Contoso-LNet-US**

1.  A partir do Portal do Azure clássico, clique em **redes** no painel da esquerda
2.  Clique em **Contoso-VNet-UE**.
3.  Clique no separador **CONFIGUE** .
4.  Verifique se **ligar à rede local**.
5.  Na **Rede LOCAL**, selecione **Contoso-LNet-US**.
6.  Clique em **Adicionar sub-rede de gateway** na secção rede virtual endereço espaços.
7.  Clique em **Guardar**.
8.  Clique em **OK** para confirmar.


**Para criar um gateway VPN para Contoso-VNet-UE**

1.  A partir do Portal do Azure clássico, clique no separador **DASHBOARD** .
4.  Clique em **Criar GATEWAY** na parte inferior da página e, em seguida, clique em **Encaminhamento dinâmico**.
5.  Clique em **Sim** para confirmar. Repare no gráfico de gateway na página muda para a amarelo e indica a criação de Gateway. Bastam normalmente cerca de 15 minutos para o gateway criar.

    Quando muda o estado do gateway para ligar, o endereço IP para cada Gateway estarão visível no Dashboard. Escreva o endereço IP que corresponde a cada VNet, tomar cuidado para não misturá-los para cima. Estes são os endereços IP que serão utilizados quando edita os endereços IP do marcador de posição para o dispositivo de VPN em redes Local.

6.  Fazer uma cópia do **Endereço IP do GATEWAY**. Irá utilizá-lo para configurar o endereço IP de gateway VPN para Contoso-VNet-UE na secção seguinte.

**Para criar um gateway VPN para Contoso-VNet-UE**

- Repita o procedimento dois últimos para configurar a conectividade de site para o site da Contoso-VNet-US para Contoso-LNet-UE e a criar um gateway VPN para Contoso-Vnet-US. Quando tiver terminado, terá do endereço IP de gateway VPN para Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Configurar o dispositivo VPN endereços IP para redes locais
Na secção última, crie um gateway VPN para cada um da VNets. Ter tem os endereços IP dos VPN gateways. Agora pode aceder novamente ao configurar os endereços IP de dispositivo VPN de rede local.

**Para configurar o endereço IP do dispositivo VPN para Contoso-LNet-UE** 

1.  A partir do Portal do Azure clássico, clique em **redes** no painel da esquerda.
2.  Clique em **Redes locais** situado na parte superior.
3.  Clique **Contoso-LNet-UE**e, em seguida, clique em **Editar** na parte inferior.
4.  Atualize **o endereço IP do VPN dispositivo**.  Este é o endereço que obtém a partir do separador DASHBOARD da Contoso-VNET-UE.
5.  Clique no botão à direita.
6.  Clique no botão de verificação.

**Para configurar o endereço IP do dispositivo VPN para Contoso-LNet-pt** 

- Repita o procedimento último para configurar o endereço IP do dispositivo VPN para Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Definir VNet gateway chaves

Os gateways Vnet utilizam uma chave partilhada para autenticar ligações entre as redes virtuais. A tecla não pode ser configurada a partir do Portal de clássica Azure. Tem de utilizar o PowerShell ou .NET SDK.

**Para definir as teclas**

1. A partir de estação de trabalho, abra **O Windows PowerShell ISE** ou consola do Windows PowerShell.
2. Actualizar os parâmetros neste script de seguir e executá-la:

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Verifique a ligação VPN 

Sem qualquer VMs implementados a VNets, pode utilizar o diagrama visual de rede virtual a página do VNet Dashboard no Portal clássica do Azure para verificar o estado de ligação:

![Rede de virtual HDInsight HBase replicação estado da ligação VPN][img-vpn-status]
  



##<a name="next-steps"></a>Próximos passos

Neste tutorial aprendeu como configurar uma ligação VPN entre duas redes virtuais Azure. Outros duas artigos na série abrangem:

- [Configurar o DNS entre duas redes virtuais Azure][hdinsight-hbase-geo-replication-dns]
- [Configurar a HBase geo replicação][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 

<properties
   pageTitle="Como adicionar várias ligações de gateway Site para o Site VPN para uma rede virtual para o modelo de implementação do Gestor de recursos utilizando o portal Azure | Microsoft Azure"
   description="Adicionar múltiplos sites S2S ligações um gateway VPN que tem uma ligação existente"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma ligação de Site para o Site a uma VNet com uma ligação de gateway VPN existente

> [AZURE.SELECTOR]
- [Gestor de recursos - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Clássico - PowerShell](vpn-gateway-multi-site.md)

Este artigo explica o utilizando o portal Azure para adicionar ligações do Site para Site (S2S) para um gateway VPN que tem uma ligação existente. Este tipo de ligação é muitas vezes como uma configuração "com várias site". 

Pode utilizar este artigo para adicionar uma ligação de S2S para um VNet que já tem uma ligação de S2S, ligação de ponto-para-Site ou VNet para VNet ligação. Existem algumas limitações ao adicionar ligações. Verifique a secção [antes de começar](#before) neste artigo para verificar antes de iniciar a configuração. 

Este artigo aplica-se ao VNets criada utilizando o modelo de implementação do Gestor de recursos que têm um gateway RouteBased VPN. Estes passos não são aplicadas às configurações de ligação coexistentes ExpressRoute/Site-para-Site. Consulte o artigo [ExpressRoute/S2S coexistentes ligações](../expressroute/expressroute-howto-coexist-resource-manager.md) para informações sobre ligações coexistentes.

### <a name="deployment-models-and-methods"></a>Modelos de implementação e métodos de

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Vamos atualizar esta tabela como novos artigos e ferramentas adicionais disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>Antes de começar

Verifique os seguintes itens:

- Não está a criar uma ligação de coexistentes ExpressRoute/S2S.
- Tem uma rede virtual que foi criada utilizando o modelo de implementação do Gestor de recursos com uma ligação existente.
- O gateway de rede virtual para sua VNet é RouteBased. Se tiver um gateway PolicyBased VPN, tem de eliminar o gateway de rede virtual e criar um novo gateway VPN como RoutBased.
- Nenhum dos intervalos de endereços sobrepor-se para qualquer uma da VNets que este VNet está a ligar.
- Tiver compatível dispositivo VPN e alguém que consiga configurá-lo. Consulte o artigo [sobre os dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do seu dispositivo VPN ou se não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu no local, é necessário estão coordenados com alguém que pode fornecer essas detalhes por si.
- Tem um endereço IP público externamente oposto para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.


## <a name="part1"></a>Parte 1 - configurar uma ligação

1. A partir de um browser, navegue para o [portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **todos os recursos** e localize o **gateway de rede virtual** a partir da lista de recursos e clique na mesma.
3. No pá **Virtual gateway de rede** , clique em **ligações**.

    ![Pá ligações](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. No pá **ligações** , clique em **+ Add**.

    ![Adicionar botão de ligação](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. No pá **Adicionar ligação** , preencha os seguintes campos:
    - **Nome:** O nome que pretende atribuir para o site que está a criar a ligação.
    - **Tipo de ligação:** Selecione o **Site para site (IPsec)**.

    ![Adicionar pá de ligação](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Parte 2 - Adicionar um gateway de rede local

1. Clique em **gateway de rede Local** ***Escolher um gateway de rede local***. Isto vai abrir o pá **Escolher gateway de rede local** .

    ![Selecione gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Clique em **Criar novo** para abrir o pá **Criar gateway de rede local** .

    ![Criar pá de gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. No pá **Criar gateway de rede local** , preencha os seguintes campos:
    - **Nome:** O nome que pretende atribuir o recurso de gateway de rede local.
    - **Endereço IP:** O endereço IP público do dispositivo VPN no site ao qual pretende ligar.
    - **Espaço de endereços:** O espaço de endereços que pretende que a ser encaminhado para o novo site de rede local.
4. Clique em **OK** no pá a **Criar gateway de rede local** para guardar as alterações.

## <a name="part3"></a>Parte 3 - adicionar a chave partilhada e criar a ligação

1. No pá **Adicionar ligação** , adicione a chave partilhada que pretende utilizar para criar a sua ligação. Pode obter a chave partilhada a partir do seu dispositivo VPN, ou fazer uma cópia aqui e, em seguida, configurar o seu dispositivo VPN para utilizar a mesma chave partilhada. O importante é que as teclas são exatamente o mesmo.

    ![Chave partilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. Na parte inferior da pá, clique em **OK** para criar a ligação.

## <a name="part4"></a>Parte 4 - Verifique a ligação VPN

Pode verificar a ligação VPN no portal do ou ao utilizar o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>Próximos passos

- Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo o máquinas virtuais [caminho de formação](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) para obter mais informações.

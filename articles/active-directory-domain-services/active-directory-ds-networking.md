<properties
    pageTitle="Serviços de domínio do Azure AD: Diretrizes de funcionamento em rede | Microsoft Azure"
    description="Considerações de funcionamento em rede para o Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerações de funcionamento em rede para serviços de domínio do Azure AD

## <a name="how-to-select-an-azure-virtual-network"></a>Como selecionar uma rede virtual Azure
As diretrizes seguintes ajudá-lo selecionar uma rede virtual para utilizar com os serviços de domínio do Azure AD.

### <a name="type-of-azure-virtual-network"></a>Tipo de rede virtual Azure

- Pode ativar serviços de domínio do Azure AD numa rede virtual Azure clássica.

- Azure AD serviços de domínio **não pode ser ativado no redes virtuais criadas utilizando o Gestor de recursos do Azure**.

- Pode ligar uma rede virtual com base no Gestor de recursos a uma rede virtual clássica na qual os serviços de domínio do Azure AD está ativado. A partir daí, pode utilizar os serviços de domínio do Azure AD na rede virtual com base no Gestor de recursos. Para mais informações, consulte a secção de [conectividade de rede](active-directory-ds-networking.md#network-connectivity) .

- **Redes virtuais regionais**: Se planeia utilizar uma rede virtual existente, certifique-se de que é uma rede virtual regional.

    - Redes virtuais que utilizam o mecanismo legado afinidade grupos não podem ser utilizados com os serviços de domínio do Azure AD.

    - Para utilizar os serviços de domínio do Azure AD, [Migrar legados redes virtuais para redes virtuais regionais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Azure região para a rede virtual

- Os serviços de domínio do Azure AD domínio gerido for implementado na mesma região Azure como a rede virtual optar por ativar o serviço no.

- Selecione uma rede virtual numa região Azure suportada pelos serviços de domínio do Azure AD.

- Ver a página de [Serviços Azure por região](https://azure.microsoft.com/regions/#services/) saber as regiões Azure na qual dos serviços de domínio do Azure AD está disponível.


### <a name="requirements-for-the-virtual-network"></a>Requisitos para a rede virtual

- **Proximidade as Azure das cargas de trabalho**: selecione a rede virtual que atualmente aloja/irá alojar máquinas virtuais que precisam de aceder a serviços de domínio do Azure AD.

- **Os servidores DNS personalizados/trazer-your-proprietário**: Certifique-se de que não existem nenhum dos servidores DNS personalizados configurados para a rede virtual.

- **Domínios existente com o mesmo nome de domínio**: Certifique-se de que não possui um domínio existente com o mesmo nome de domínio disponível nessa rede virtual. Por exemplo, partem do princípio que tenha um domínio denominado 'contoso.com' já disponíveis na rede virtual selecionada. Mais tarde, experimente ativar um domínio gerido de serviços de domínio do Azure AD com o mesmo nome de domínio (isto é 'contoso.com') nessa rede virtual. Encontrar uma falha ao tentar ativar serviços de domínio do Azure AD. Esta falha é devido a nome está em conflito do nome de domínio nessa rede virtual. Esta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido de serviços de domínio do Azure AD. Em alternativa, pode anular aprovisionar o domínio existente e, em seguida, avance para o ativar serviços de domínio do Azure AD.

> [AZURE.WARNING] Não pode mover dos serviços de domínio a outra rede virtual depois de ter ativado o serviço.


## <a name="network-security-groups-and-subnet-design"></a>Grupos de segurança de rede e estrutura de sub-rede
Um [Grupo de segurança de rede (NSG)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de lista de controlo de acesso (ACL) que permitir ou negar o tráfego de rede para o seu instâncias VM numa rede Virtual. NSGs podem ser associados com sub-redes ou instâncias VM individuais dentro desse sub-rede. Quando uma NSG está associada uma sub-rede, as regras ACL aplicam a todas as instâncias VM nessa sub-rede. Além disso, o tráfego para um VM individual pode ser restrito mais associando um NSG diretamente para esse VM.

![Estrutura de sub-rede recomendadas](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Melhores práticas para escolher uma sub-rede
- Implemente os serviços de domínio do Azure AD para uma **sub-rede dedicada separada** na rede da sua virtual Azure.

- Não se aplicam NSGs à sub-rede dedicada para o seu domínio gerido. Se tem aplicar NSGs à sub-rede dedicada, se certificar de que **não bloquear as portas necessárias para o serviço e gerir os seus domínios**.

- Não restringir demasiado o número de endereços IP disponíveis dentro da sub-rede dedicada para o seu domínio gerido. Esta restrição impede o serviço de disponibilização de dois controladores de domínio para o seu domínio gerido.

- **Não ativar serviços de domínio do Azure AD na sub-rede de gateway** da sua rede virtual.


> [AZURE.WARNING] Quando associar um NSG com uma sub-rede na qual dos serviços de domínio do Azure AD está ativado, pode interromper a capacidade da Microsoft de serviço e gerir o domínio. Para além disso, a sincronização entre o inquilino do Azure AD e o seu domínio gerido é interrompida. **O SLA não se aplica a implementações onde um NSG ser aplicado que bloqueia dos serviços de domínio do Azure AD de atualizar e gerir o seu domínio.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Portas necessárias para serviços de domínio do Azure AD
As seguintes portas são necessárias para serviços de domínio do Azure AD do serviço e mantêm o seu domínio gerido. Certifique-se de que estas portas não são bloqueadas para a sub-rede na qual ativou o seu domínio gerido.

| Número de porta | Objetivo |
|---|---|
| 443 | Sincronização com o seu inquilino do Azure AD |
| 3389 | Gestão do seu domínio |
| 5986 | Gestão do seu domínio |
| 636 | Proteger o acesso de LDAP (LDAPS) para o seu domínio gerido |



## <a name="network-connectivity"></a>Conectividade da rede
Um domínio gerido de serviços de domínio do Azure AD pode ser ativado apenas a partir de uma única rede virtual clássica no Azure. Redes virtuais criadas utilizando o Gestor de recursos do Azure não são suportadas.


### <a name="scenarios-for-connecting-azure-networks"></a>Cenários de ligação de redes Azure
Liga Azure redes virtuais a utilizar o domínio gerido em qualquer um dos seguintes cenários de implementação:

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Utilizar o domínio gerido no mais do que uma rede virtual clássica Azure
Pode ligar a outras redes virtuais clássicas Azure à rede virtual clássica Azure na qual ativou dos serviços de domínio do Azure AD. Esta ligação VPN permite-lhe utilizar o domínio gerido com o seu das cargas de trabalho implementadas noutras redes virtual.

![Conectividade da rede virtual clássico](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utilizar o domínio gerido numa rede virtual com base no Gestor de recursos
Pode ligar uma rede virtual com base no Gestor de recursos à rede virtual clássica Azure na qual ativou dos serviços de domínio do Azure AD. Esta ligação permite-lhe utilizar o domínio gerido com o seu das cargas de trabalho implementadas na rede virtual com base no Gestor de recursos.

![Gestor de recursos para a conectividade da rede virtual clássico](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Opções de ligação de rede

- **Ligações de VNet para VNet utilizando ligações de VPN do site para o site**: ligar uma rede virtual a outra rede virtual (VNet para VNet) é semelhante a estabelecer ligação uma rede virtual para uma localização do site no local. Ambos os tipos de conectividade utilizam um gateway VPN para fornecer um túnel seguro através do IPsec/IKE.

    ![Conectividade da rede virtual utilizando VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Obter mais informações - ligar redes virtuais utilizando VPN gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Ligações de VNet para VNet com a rede virtual efectuado**: Rede Virtual efectuado é um mecanismo que liga duas redes virtuais na mesma região através da rede Azure estrutura principal. Assim que dispõe, as duas redes virtuais aparecem como um para todos os efeitos de conectividade. Ainda são geridos como recursos separados, mas máquinas virtuais nestes redes virtuais pode comunicar com os outros diretamente ao utilizar endereços IP privados.

    ![Conectividade de rede virtual utilizando efectuado](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Obter mais informações - virtuais efectuado de rede](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Azure rede virtual efectuado](../virtual-network/virtual-network-peering-overview.md)

- [Configurar uma ligação de VNet para VNet para o modelo clássico de implementação](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Grupos de segurança de rede Azure](../virtual-network/virtual-networks-nsg.md)

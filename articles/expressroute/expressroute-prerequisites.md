<properties
   pageTitle="Pré-requisitos para ExpressRoute adoção | Microsoft Azure"
   description="Esta página fornece uma lista dos requisitos seja cumprida antes de pode encomendar um circuito Azure ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos de ExpressRoute e lista de verificação  

Para se ligar aos serviços em nuvem Microsoft utilizando ExpressRoute, terá de confirmar que foram satisfeitos os seguintes requisitos listados nas secções abaixo.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta Azure

- Uma conta do Microsoft Azure válida e ativa. Isto é necessário para o circuito ExpressRoute do programa de configuração. ExpressRoute circuitos são recursos dentro de subscrições Azure. Uma subscrição do Azure é um requisito, mesmo se conectividade está limitada a serviços em nuvem Microsoft não Azure, tais como serviços do Office 365 e CRM online.
- Uma subscrição ativa do Office 365 (se utilizar os serviços do Office 365). Consulte a secção [requisitos específicos do Office 365](#office-365-specific-requirements) deste artigo para obter mais informações.

## <a name="connectivity-provider"></a>Fornecedor de conectividade
- Pode trabalhar com um [parceiro de conectividade de ExpressRoute](expressroute-locations.md#partners) para ligar à nuvem da Microsoft. Pode configurar uma ligação entre o seu rede no local e o Microsoft de [três formas](expressroute-introduction.md#howtoconnect). 
- Se o seu fornecedor de não for um parceiro de conectividade ExpressRoute, ainda pode ligar na nuvem da Microsoft através de um [fornecedor do exchange na nuvem](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Requisitos de rede
- **Conectividade redundante**: não existe nenhuma requisito redundância depende da conectividade física entre si e o seu fornecedor. Microsoft requer redundantes BGP sessões para configurar o entre routers da Microsoft e os routers peering, mesmo quando tiver apenas [uma ligação física para o exchange uma nuvem](expressroute-faqs.md#onep2plink). 
- **Encaminhamento**: dependendo de como se liga ao Microsoft Cloud, ou o seu fornecedor de precisa de configurar e gerir as sessões BGP para [Encaminhamento de domínios](expressroute-circuit-peerings.md). Alguns fornecedor de conectividade de Ethernet ou o fornecedor do exchange na nuvem que pode oferecer gestão BGP como um valor-Adicionar serviço.
- **NAT**: Microsoft aceita apenas endereços IP públicos através do Microsoft efectuado. Se estiver a utilizar endereços IP privados na sua rede no local, ou suas necessidades de fornecedor para traduzir os endereços IP privados para o endereço IP público os endereços [utilizando a NAT](expressroute-nat.md).
- **QoS**: Skype para empresas tem vários serviços (por exemplo, texto vídeo e voz,) que requerem diferenciar QoS tratamento. Que e o seu fornecedor de devem seguir os [requisitos QoS](expressroute-qos.md).
- **Segurança de rede**: deverá tomar em consideração [segurança da rede](../best-practices-network-security.md) ao ligar para o Microsoft Cloud através do ExpressRoute.
 
## <a name="office-365"></a>Office 365

Se planeia ativar o Office 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações sobre os requisitos do Office 365.


- [Descrição geral de ExpressRoute para Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Encaminhamento com ExpressRoute para Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Planeamento da rede e Otimização do desempenho do Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Calculadoras de largura de banda de rede e ferramentas](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Integração do Office 365 com ambientes no local](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM Online 
Se planeia ativar CRM Online num ExpressRoute, reveja os seguintes documentos para obter mais informações sobre o CRM Online

- [CRM Online URLs](https://support.microsoft.com/kb/2655102) e [intervalos de endereços IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Próximos passos

- Para mais informações sobre ExpressRoute, consulte as [ExpressRoute FAQ](expressroute-faqs.md).
- Localize um fornecedor de conectividade de ExpressRoute. Consulte o artigo [ExpressRoute parceiros e efectuadas localizações](expressroute-locations.md).
- Consulte os requisitos para o [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
- Configure a sua ligação de ExpressRoute.
    - [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurar o encaminhamento](expressroute-howto-routing-classic.md)
    - [Ligar uma VNet um circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)


<properties
    pageTitle="Como planear a sua rede virtual de uma coleção de RemoteApp do Azure | Microsoft Azure"
    description="Saiba como planear a sua rede virtual de uma coleção de RemoteApp do Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Como planear a sua rede virtual para RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este documento descreve como configurar a rede virtual Azure (VNET) e a sub-rede para RemoteApp do Azure. Se não está familiarizado com redes virtuais Azure, esta é uma funcionalidade que ajuda-o para virtualizar em sua infraestrutura de rede na nuvem e para criar soluções híbrida com o Azure e os recursos no local. Pode ler mais sobre a mesma [aqui](../virtual-network/virtual-networks-overview.md).

Se pretender definir políticas de segurança para o tráfego (recebida e enviada) na sua rede virtual onde está a implementar RemoteApp do Azure, recomendamos criar uma sub-rede separada para Azure RemoteApp do resto do seu implementações na rede virtual Azure. Para mais informações sobre como definir políticas de segurança na sua sub-rede da rede virtual Azure, leia [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Tipos de coleções de RemoteApp do Azure com redes virtuais Azure

Gráficos seguintes mostram as duas opções de coleções de sites diferente quando pretender utilizar uma rede virtual.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Azure RemoteApp nuvem coleções de sites com VNET

 ![Azure RemoteApp - colecção de nuvem com um VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Isto representa uma coleção de RemoteApp do Azure onde todos os recursos que a sessão RemoteApp anfitriões precisam de aceder ao são implementados no Azure. Podem ser a mesma VNET como o VNET RemoteApp ou um VNET diferente no Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Azure RemoteApp híbrido coleções de sites com VNET

![Azure RemoteApp - colecção de híbrido com um VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Isto representa uma coleção de RemoteApp do Azure onde estão alguns dos recursos que os anfitriões de sessão RemoteApp precisam de aceder ao implementada no local. O VNET RemoteApp está ligado à rede no local a utilizar tecnologias de híbrido Azure como VPN ou Express encaminhar o site para o site.


## <a name="how-the-system-works"></a>Como funciona o sistema

Nas folhas de rosto RemoteApp do Azure implementa o Azure máquinas virtuais (com a sua imagem carregada) à sub-rede de rede virtual que tenha escolhido durante o aprovisionamento. Caso tenha optado por para uma coleção de híbrido, vamos tentar resolver o FQDN do controlador de domínio que introduziu no fluxo de trabalho aprovisionamento com o servidor DNS fornecido na rede virtual.  
Se estiver a ligar a uma rede virtual existente, certifique-se mostrar as portas necessárias nos seus grupos de segurança de rede na sua sub-rede RemoteApp do Azure. 

Recomendamos que utilize uma [sub-rede grande o suficiente para RemoteApp do Azure](remoteapp-vnetsizing.md). É suportada pelo rede Azure Virtual maior /8 (utilizando as definições de sub-rede CIDR). Sua sub-rede aceda deve ser grande o suficiente para acomodar todas as VMs RemoteApp o Azure durante a escala de segurança quando mais utilizadores estão a aceder às aplicações. 

Seguem-se os aspetos que é necessário ativar na sua sub-rede rede virtual: 

2.  O tráfego de saída da sub-rede deve ser permitido num intervalo de porta 10101 10175 para comunicar com um dos serviços na RemoteApp do Azure internos.
3.  O tráfego de saída tenham permissão a partir da sua sub-rede aceda para ligar ao armazenamento de Azure na porta 443
4.  Se tiver o Active Directory alojado no Azure, certifique-se de que qualquer VM dentro da sub-rede de rede virtual para RemoteApp do Azure é possível ligar a esse domínio. O DNS na rede virtual deve ser conseguir resolver o FQDN deste controlador de domínio.


## <a name="virtual-network-with-forced-tunneling"></a>Rede virtual com túnel forçada

[Forçada túnel](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) agora é suportada para todas as colecções RemoteApp do Azure novas. Atualmente não suportamos a migração de uma coleção existente para suportar túnel forçada.  Tem de eliminar todas as suas coleções existentes utilizando o VNET que está a ligar RemoteApp do Azure e crie um novo para obter forçada túnel ativa nas suas coleções de. 

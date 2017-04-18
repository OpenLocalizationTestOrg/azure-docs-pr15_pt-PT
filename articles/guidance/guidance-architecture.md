
<properties
   pageTitle="Azure orientações | padrões & práticas | Microsoft Azure"
   description="Referência Azure arquitecturas"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Referência Azure arquitecturas

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Este conteúdo está em desenvolvimento ativo. Se torna útil hoje em dia, para que estamos a torná-lo disponível para a pré-visualização. Vamos valor os seus comentários._

Os nossos arquitecturas de referência estiverem dispostas por cenário, com vários arquitecturas relacionadas agrupadas em conjunto.
Cada arquitetura individual oferece práticas recomendadas e à passos, bem como um componente executável que engloba as recomendações.
Muitos das arquitecturas são gradual; construir na parte superior de arquitecturas anteriores que têm menos requisitos.

## <a name="designing-your-infrastructure-for-resiliency"></a>Estruturar a sua infraestrutura para RDP

Nesta série começa com práticas recomendadas para uma configuração VM ideal e culminates numa região com várias implementação com activação pós-falha.

- [Executar o Windows VM no Azure](guidance-compute-single-vm.md)
- [Executar uma VM Linux no Azure](guidance-compute-single-vm-linux.md)
- [Executar vários VMs para escalabilidade e disponibilidade](guidance-compute-multi-vm.md)
- [Executar o Windows VMs para uma arquitetura de camadas](guidance-compute-n-tier-vm.md)
- [Executar Linux VMs para uma arquitetura de camadas](guidance-compute-n-tier-vm-linux.md)
- [A executar o Windows VMs em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters.md)
- [A ser executada Linux VMs em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>Ligar a sua rede no local ao Azure

Nesta série começa por demonstrar as formas de ligar a sua rede existente ao Azure. Em seguida, expande para cobrir requisitos para disponibilidade e segurança.

- [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN](guidance-hybrid-network-vpn.md)
- [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [Implementar uma arquitetura de rede híbrido altamente disponíveis](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Proteger a sua rede híbrido

Nesta série abrange provas dadas práticas sobre como criar DMZ no Azure para ligações seguras provenientes do seu centro de dados no local e na Internet.

- [Implementar uma DMZ entre o Azure e o seu centro de dados no local](guidance-iaas-ra-secure-vnet-hybrid.md)
- [Implementar uma DMZ entre Azure e a Internet](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Fornecer serviços de identidade

Nesta série começa por demonstrar como utilizar o Azure Active Directory para fornecer a autenticação de utilizador no Azure. Em seguida, expande cenários complexos expandir a sua infraestrutura de adiciona para Azure e utilizar o ADFS para delegação de folha de rosto.

- [Execução do Azure Active Directory](./guidance-identity-aad.md)
- [Expandir serviços do Active Directory (adiciona) para Azure](./guidance-identity-adds-extend-domain.md)
- [Criar uma floresta de recursos de serviços de diretório do Active Directory (adiciona) no Azure](./guidance-identity-adds-resource-forest.md)
- [Implementar a serviços de Federação do Active Directory (ADFS) no Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Criação de aplicação web dimensionáveis Azure PaaS

Nesta série abrange recomendações para construir aplicações web dimensionáveis e altamente disponíveis. 

- [Aplicação web básicas](guidance-web-apps-basic.md)
- [Melhorar a escalabilidade numa aplicação web](guidance-web-apps-scalability.md)
- [Aplicação Web com elevada disponibilidade](guidance-web-apps-multi-region.md)

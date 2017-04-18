<properties
   pageTitle="Descrição geral de monitorização e gestão de segurança Azure | Microsoft Azure"
   description=" Azure fornece mecanismos de segurança para ajudar a gestão e controlo dos serviços em nuvem Azure e máquinas virtuais a.  Este artigo fornece uma descrição geral destas funcionalidades de segurança de core e serviços. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Descrição geral de monitorização e gestão de segurança Azure

Azure fornece mecanismos de segurança para ajudar a gestão e controlo dos serviços em nuvem Azure e máquinas virtuais a. Este artigo fornece uma descrição geral destas funcionalidades de segurança de core e serviços. São fornecidas hiperligações para artigos que irão dar-detalhes de cada para saber mais.

A segurança dos seus serviços de nuvem Microsoft é uma parceria e responsabilidade partilhada entre si e à Microsoft. Responsabilidade partilhada significa que a Microsoft está responsável pela Microsoft Azure e centros de segurança física dos respetivos dados (através da utilização de protecção de segurança como portas de entrada do distintivo bloqueado, limites e guardas). Além disso, o Azure fornece fortes níveis de segurança na nuvem na camada do software que satisfaça as necessidades de segurança, privacidade e conformidade dos seus clientes excessiva.

É o proprietário os seus dados e identidades, responsabilidade para protegê-las, a segurança dos seus recursos no local e a segurança dos componentes de nuvem através da qual tem controlo. Microsoft fornece capacidades para ajudar a proteger os seus dados e aplicações e controlos de segurança. Baseia-se a sua responsabilidade de segurança no tipo de serviço na nuvem.

O gráfico seguinte resume o saldo do responsável pela Microsoft e o cliente.

![Responsabilidade partilhada][1]

Para um vôo picado mais aprofundado para gestão de segurança, consulte [Gestão da segurança no Azure](azure-security-management.md).

Seguem-se as funcionalidades principais para ser abordados neste artigo:

- Controlo de acesso baseado em funções
- Antimalware
- Autenticação Multifator
- ExpressRoute
- Gateways de rede virtual
- Gestão de identidades privilegiados
- Proteção de identidade
- Centro de segurança

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Controlo de acesso baseado em funções (RBAC) fornece a gestão de acesso extensivamente para recursos Azure. Utilizar RBAC, pode conceder as pessoas apenas a quantidade de acesso que necessitam para efetuar as suas tarefas.  RBAC também pode ajudar a garantir que quando as pessoas sair da organização perdem o acesso aos recursos na nuvem.

Saiba mais:

- [Blogue da equipa do Active Directory no RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Controlo de acesso baseado em funções Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware

Com o Azure pode utilizar o software de antimalware de fornecedores de segurança principais como Microsoft, Symantec, tendência Micro, McAfee e Kaspersky para ajudar a proteger o seu máquinas virtuais do ficheiros maliciosos, adware e outras ameaças.

Microsoft Antimalware oferece-lhe a capacidade de instalar um agente de antimalware para PaaS funções e máquinas virtuais. Com base no sistema Centro Endpoint Protection, esta funcionalidade traz provas dadas no local tecnologia de segurança para a nuvem.

Oferecemos também integração abrangente para uma de tendência [Segurança abrangente](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ e [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produtos da plataforma do Azure. DeepSecurity é uma solução antivírus e SecureCloud é uma solução de encriptação. DeepSecurity será implementada dentro VMs utilizando um modelo de extensão. Com o portal da IU e PowerShell, pode optar por utilizar DeepSecurity dentro de novos VMs que estão a ser fiadas para cima ou VMs existentes que já estão implementados.

Proteção de ponto final de Symantec (Set) também é suportada no Azure. Através do portal integração, clientes têm a capacidade de especificar que pretendem utilizar Set. dentro de uma VM. Set. pode ser instalado numa nova VM através do Portal do Azure ou pode ser instalado numa existente VM através do PowerShell.

Saiba mais:

- [Implementar soluções Antimalware em máquinas virtuais Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Microsoft Antimalware para serviços em nuvem Azure e máquinas virtuais](../security/azure-security-antimalware.md)
- [Como instalar e configurar tendência Micro abrangente segurança como um serviço numa VM do Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Novas opções de Antimalware para proteger máquinas virtuais Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Autenticação Multifator

Azure autenticação multifator (MFA) é um método de autenticação que necessita da utilização de mais do que um método de confirmação e adiciona uma segunda crítica camada de segurança para suplementos de início de sessão do utilizador e operações. MFA o ajuda a salvaguardar informações acesso a dados e as aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião. Fornece-forte autenticação através de um intervalo de opções de verificação — chamada telefónica, mensagem de texto ou aplicação móvel notificação ou verificação de código e 3 festa JURAMENTO tokens.

Saiba mais:

- [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [O que é o Azure a autenticação Multifator?](../multi-factor-authentication/multi-factor-authentication.md)
- [Como funciona a autenticação Multifator de Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute permite-lhe expandir redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com ExpressRoute, pode estabelecer ligações a serviços em nuvem Microsoft, como o Microsoft Azure, Office 365 e CRM Online. Conectividade pode ser a partir de uma rede (IP VPN) qualquer para qualquer, uma rede de Ethernet ponto a ponto ou uma ligação de publicação em virtual através de um fornecedor de conectividade a uma funcionalidade de cocriação localização. ExpressRoute ligações não aceda através da Internet pública. Esta opção permite-ligações ExpressRoute oferecer mais fiabilidade, velocidades mais rápidas, latências inferiores e superior segurança que as ligações típicas através da Internet.

Saiba mais:

- [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Os Gateways de VPN, também denominado Azure Virtual Gateways de rede, são utilizados para enviar tráfego de rede entre redes virtuais e localizações no local. Também são utilizados para enviar tráfego entre várias redes virtuais dentro Azure (VNet para VNet).  Os gateways VPN fornecem conectividade de publicação em local seguro entre Azure e a sua infraestrutura.

Saiba mais:

- [Sobre VPN gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Descrição geral de segurança de rede Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Gestão de identidades privilegiados

Por vezes, os utilizadores têm de efectuar operações privilegiadas nas outras aplicações de SaaS ou recursos Azure. Isto significa frequentemente organizações tem de conceder-lhes acesso privilegiado permanente no Azure Active Directory (Azure AD). Este é um crescente risco de segurança para recursos alojado na nuvem porque organizações suficientemente não é possível monitorizar o que os utilizadores estão a fazer com o seu acesso privilegiado.
Para além disso, se uma conta de utilizador com o access privilegiados está comprometida, essa um violação pode afetar a sua segurança nuvem global. Gestão de identidades do Azure AD privilegiados ajuda para resolver este risco ao baixar o tempo de exposição de privilégios e aumentar a visibilidade para utilização.  

Gestão de identidades privilegiados introduz o conceito de um administrador temporário para uma função ou "apenas em hora" o acesso de administrador que é um utilizador que tem de concluir o processo de ativação para essa função atribuída. O processo de ativação alterações a atribuição do utilizador para uma função no Azure AD a partir do período inativo para ativo, para um tempo especificado como 8 horas.

Saiba mais:

- [Gestão de identidades do Azure AD privilegiados](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Introdução ao Azure AD privilegiados gestão de identidades](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Proteção de identidade

Proteção de identidade do Azure Active Directory (AD) fornece uma vista consolidada de atividades de início de sessão suspeitas e potenciais vulnerabilidades para ajudar a proteger a sua empresa. Proteção de identidade Deteta actividades suspeitas para utilizadores e identidades privilegiados (admin), com base em sinais como ataques de força bruta, fuga credenciais e inícios de sessão a partir de localizações não está familiarizados em infectados dispositivos.

Ao fornecer notificações e remediação recomendada, protecção de identidade ajuda a mitigar riscos em tempo real. Calcula a gravidade do risco de utilizador e, pode configurar políticas baseado nos riscos para o ajudar a aplicação de salvaguarda aceder a partir de futuras ameaças automaticamente.

Saiba mais:

- [Proteção de identidade do Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canal 9: Azure AD e a apresentação de identidade: pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de segurança

Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

Centro de segurança ajuda-o a otimizar e monitorizar a segurança dos seus recursos Azure por:

- Permitindo-lhe definir políticas para os recursos de subscrição Azure de acordo com as necessidades de segurança da sua empresa e o tipo de aplicações ou confidencialidade dos dados de cada subscrição.
- Monitorizar o estado da sua máquinas virtuais Azure, redes e aplicações.
- Fornecer uma lista de alertas de segurança com prioridade alta, incluindo os alertas do soluções de parceiros integrada, juntamente com as informações que necessárias para investigar rapidamente e recomendações sobre como solucionar uma ataque.

Saiba mais:

- [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

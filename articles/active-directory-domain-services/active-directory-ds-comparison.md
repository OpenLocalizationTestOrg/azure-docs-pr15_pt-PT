<properties
    pageTitle="Serviços de domínio do Azure AD: Comparar Azure AD dos serviços de domínio para controladores de domínio faça mesmo | Microsoft Azure"
    description="Comparar o Azure Active Directory Domain Services para controladores de domínio faça mesmo"
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
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Como decidir se dos serviços de domínio do Azure AD se adequa a sua casos de utilização
Serviços de domínio do AD Azure permite-lhe implementar o seu das cargas de trabalho em serviços de infraestrutura do Azure, sem ter de se preocupar manter a sua infraestrutura de identidade. Este serviço gerido é diferente a partir de uma implementação do Active Directory do Windows Server típica que implementar e administrar sozinho. O serviço foi concebido para facilidade de implementação, monitorização do Estado de funcionamento automatizado e remediação e uma infraestrutura de identidade simples para a nuvem. Vamos constantemente estão a evoluir o serviço para adicionar suporte para cenários comuns de implementação.

Decidir se pretendem utilizar serviços de domínio do Azure AD ou giratório para cima e gerir a sua própria infraestrutura de AD (faça mesmo) no Azure:

- Ver a lista de [funcionalidades disponibilizadas pelos serviços de domínio do Azure AD](active-directory-ds-features.md).

- Reveja comuns [cenários de implementação para serviços de domínio do Azure AD](active-directory-ds-scenarios.md).

- Por fim, [comparar os serviços de domínio do Azure AD para uma opção de AD resolução](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparar os serviços de domínio do Azure AD para o domínio de AD faça mesmo no Azure
A seguinte tabela ajuda-o a decidir entre utilizando os serviços de domínio do Azure AD e gerir o seu próprio infraestrutura de AD no Azure.

|**Funcionalidade**|**Serviços de domínio do Azure AD**|**AD «Faça mesmo» no Azure VMs**|
|---|:---:|:---:|
|[**Serviço gerido**](active-directory-ds-comparison.md#managed-service)|**& #x 2713;**|**& #x 2715;**|
|[**Implementações seguras**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713;**|Administrador tem de seguro de implementação.|
|[**Servidor de DNS**](active-directory-ds-comparison.md#dns-server)|**& #x 2713;** (serviço gerido)|**& #x 2713;**|
|[**Privilégios de administrador do domínio ou Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715;**|**& #x 2713;**|
|[**Associação de domínio**](active-directory-ds-comparison.md#domain-join)|**& #x 2713;**|**& #x 2713;**|
|[**Autenticação de domínio utilizando NTLM e Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713;**|**& #x 2713;**|
|[**Estrutura de or personalizada**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713;**|**& #x 2713;**|
|[**Extensões de esquema**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715;**|**& #x 2713;**|
|[**Confia floresta e domínio AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715;**|**& #x 2713;**|
|[**Ler LDAP**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713;**|**& #x 2713;**|
|[**Seguro LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713;**|**& #x 2713;**|
|[**Escrever LDAP**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715;**|**& #x 2713;**|
|[**Política de grupo**](active-directory-ds-comparison.md#group-policy)|Simples|Completo|
|[**Distribuído geo híbridas**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715;**|**& #x 2713;**|


#### <a name="managed-service"></a>Serviço gerido
Azure domínios de serviços de domínio do AD são geridos pelo Microsoft. Não tiver de se preocupar a aplicação de patches, atualizações, monitorização, cópias de segurança e garantir disponibilidade do seu domínio. Estas tarefas de gestão são oferecidas como um serviço pelo Microsoft Azure dos seus domínios geridos.

#### <a name="secure-deployments"></a>Implementações seguras
O domínio gerido está bloqueado em segurança para baixo por práticas recomendadas de segurança da Microsoft para implementações do AD. Estas práticas recomendadas haste a partir do décadas a equipa de produto de AD da experiência de engenharia e implementações AD de suporte. Para implementações resolução, tem de efetuar passos de implementação específico para bloquear a seta para baixo/seguro sua implementação.

#### <a name="dns-server"></a>Servidor de DNS
Um domínio gerido de serviços de domínio do Azure AD inclui geridos serviços DNS. Os membros do grupo 'AAD CC administradores' podem gerir DNS no domínio gerido. Os membros deste grupo são fornecidos todos os privilégios de administração de DNS para o domínio gerido. Gestão de DNS pode ser efetuada utilizando a 'Consola de administração de DNS' incluída no pacote de ferramentas de administração de servidor remoto (FARS).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilégios de domínio ou o administrador da empresa
Estes privilégios elevados não são oferecidos num domínio gerido AAD DS. Não não possível executar as aplicações que requerem estes privilégios elevados para ser instalado/executar contra geridos domínios. Um subconjunto mais pequeno de privilégios administrativos está disponível para membros do grupo designado 'AAD CC administradores' administração delegada. Estes privilégios incluem privilégios para configurar o DNS, configurar a política de grupo, ganhar privilégios de administrador no domínio máquinas, etc.

#### <a name="domain-join"></a>Associação de domínio
Pode participar máquinas virtuais para o domínio gerido semelhante a como participar computadores para um domínio de AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticação de domínio utilizando NTLM e Kerberos
Com os serviços de domínio do Azure AD, pode utilizar as suas credenciais da empresa para autenticar com o domínio gerido. Credenciais são mantidas sincronizadas com o seu inquilino do Azure AD. Para sincronizada inquilinos, a ligação do Azure AD assegura que as alterações para credenciais efetuadas no local são sincronizadas para Azure AD. Com a configuração de domínio faça mesmo, poderá ter de configurar uma relação de confiança de domínio com uma floresta de conta no local para os utilizadores autenticar com as respetivas credenciais da empresa. Em alternativa, poderá ter de configurar a replicação de AD para se certificar de que as palavras-passe de utilizador sincronizar para o domínio Azure controlador máquinas virtuais.

#### <a name="custom-ou-structure"></a>Estrutura de or personalizada
Os membros do grupo 'AAD CC administradores' podem criar personalizado ou dentro do domínio gerido.

#### <a name="schema-extensions"></a>Extensões de esquema
Não é possível expandir o esquema de um domínio gerido de serviços de domínio do Azure AD base. Por conseguinte, aplicações que dependem de extensões de esquema do AD (por exemplo, novos atributos em objeto do utilizador) não podem ser levantadas e deslocado para a domínios AAD DS.

#### <a name="ad-domain-or-forest-trusts"></a>Domínio de AD ou fidedignidades de floresta
Domínios geridos não podem ser configurados para configurar as relações de fidedignidade (entrada/saída) com outros domínios. Por conseguinte, cenários como implementações do recurso floresta ou casos onde preferir não sincronizar palavras-passe para Azure AD não é possível utilizar os serviços de domínio do Azure AD.

#### <a name="ldap-read"></a>Leitura LDAP
O domínio gerido suporta LDAP ler das cargas de trabalho. Por conseguinte, pode implementar aplicações que executam operações de leitura LDAP contra o domínio gerido.

#### <a name="secure-ldap"></a>LDAP seguro
Pode configurar os serviços de domínio do Azure AD para proporcionar um acesso seguro LDAP para o seu domínio gerido, incluindo através da internet.

#### <a name="ldap-write"></a>Escrever LDAP
O domínio gerido é só de leitura para objetos de utilizador. Por conseguinte, aplicações que executam operações de escrita LDAP contra atributos do objeto do utilizador não funciona num domínio gerido. Para além disso, as palavras-passe de utilizador não podem ser alteradas dentro do domínio gerido. Outro exemplo seria modificação de membros do grupo ou atributos de grupo dentro do domínio gerido, que não é permitido. No entanto, as alterações para atributos de utilizador ou palavras-passe efetuadas no Azure AD (através do portal do PowerShell/Azure) ou no local AD são sincronizados para o domínio gerido AAD DS.

#### <a name="group-policy"></a>Política de grupo
Construções de política de grupo sofisticadas não são suportadas no domínio gerido AAD DS. Por exemplo, não é possível criar e implementar GPO separado para cada or personalizado no domínio ou utilizar WMI filtrar GP filtragem. Existe um incorporados GPO cada para os contentores 'AADDC computadores' e 'AADDC utilizadores', pode ser personalizado para configurar a política de grupo.

#### <a name="geo-dispersed-deployments"></a>Dispersão geo híbridas
Azure domínios geridos de serviços de domínio do AD estão disponíveis numa única rede virtual no Azure. Cenários que requerem controladores de domínio para estar disponível em regiões Azure múltiplos em todo o mundo, a configurar controladores de domínio no Azure IaaS VMs poderá ser melhor alternativa.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opções de implementação (DIY) AD «Faça mesmo»
Poderá ter casos de utilização de implementação onde precisa de algumas das capacidades disponibilizadas por uma instalação do Windows Server AD. Nestes casos, considere uma das seguintes opções (DIY) faça mesmo:

- **Domínio de nuvem autónomo:** Pode configurar um programa autónomo 'domínio na nuvem' utilizando Azure máquinas virtuais que tenham sido configuradas como controladores de domínio. Este infraestrutura não integrar com o seu no local ambiente AD. Esta opção iria necessitar de um conjunto diferente de 'credenciais da nuvem' login/administrar VMs na nuvem.

- **Implementação do recurso floresta:** Pode configurar um domínio na topologia de floresta de recurso, com o Azure máquinas virtuais configuradas como controladores de domínio. Em seguida, pode configurar uma relação de confiança AD com o seu no local ambiente AD. Pode computadores de associação de domínio (Azure VMs) para esta floresta recurso na nuvem. Autenticação de utilizador acontece ao longo do quer uma ligação VPN/ExpressRoute ao seu diretório no local.

- **Estender o seu domínio no local para Azure:** Pode ligar uma rede virtual Azure a sua rede no local, utilizando uma ligação VPN/ExpressRoute, para que possam ser associados Azure VMs às suas instalações AD. Outra alternativa consiste em promover controladores réplica do seu domínio no local no Azure como uma VM. Pode, em seguida, configure-o para criar uma réplica através de uma ligação VPN/ExpressRoute para o seu diretório no local. Neste modo de implementação eficazmente expande o seu domínio no local para Azure.

> [AZURE.NOTE] Pode determinar que uma opção faça mesmo melhor é adequada para a sua implementação-casos de utilização. Considere a [partilha de comentários](active-directory-ds-contact-us.md) nos ajudar a compreender o que iria ajudar funcionalidades que escolheu dos serviços de domínio do Azure AD no futuro. Este feedback ajuda-na evoluir a adequar às necessidades dos seus implementação e casos de utilização do serviço.

Vamos ter publicado [diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) para ajudar a tornar mais fácil a instalações faça mesmo.


## <a name="related-content"></a>Conteúdo relacionado
- [Serviços de domínio do Azure AD funcionalidades-](active-directory-ds-features.md)

- [Cenários de implementação - serviços de domínio do Azure AD](active-directory-ds-scenarios.md)

- [Diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

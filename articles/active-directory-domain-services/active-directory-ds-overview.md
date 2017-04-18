<properties
    pageTitle="Descrição geral do Azure Active Directory Domain Services | Microsoft Azure"
    description="Descrição geral do Azure Active Directory Domain Services"
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
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Serviços de domínio do Azure AD

## <a name="overview"></a>Descrição geral
Serviços de infraestrutura de Azure permitem-lhe implementar uma vasta gama de computação soluções de uma forma ágil. Com o Azure máquinas virtuais pode implementar quase instantaneamente e pagar apenas com os minutos. Utilizar o suporte para Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, pode implementar qualquer carga de trabalho, qualquer idioma, em praticamente qualquer sistema operativo. Estas vantagens permitem-lhe migrar as aplicações mais antigas implementados no local para o Azure, para guardar no despesas operacionais.

Um aspecto chave de migrar aplicações no local para Azure está a processar as necessidades de identidade destas aplicações. Tenha em atenção o diretório aplicações podem depender LDAP para leitura ou de acesso de escrita ao diretório da empresa ou depender de autenticação integrada do Windows (autenticação Kerberos ou NTLM) para autenticar utilizadores finais. Aplicações do linha de negócio (LOB) em execução no Windows Server, normalmente, são implementadas no domínio aderido máquinas, para que estes podem ser geridos em segurança utilizando a política de grupo. Para aplicações 'elevação e shift' no local à nuvem, estes dependências da infraestrutura de identidade da empresa tem de ser resolvidos.

Os administradores ativar com frequência para um dos seguintes soluções para satisfazer as necessidades de identidade das suas aplicações implementadas no Azure:

- Implemente uma ligação VPN do site para o site das cargas de trabalho em execução dos serviços de infraestrutura do Azure e o diretório da empresa no local.
- Alargar a infraestrutura de domínio/floresta AD empresarial ao configurar controladores de domínio de réplica utilizando máquinas virtuais Azure.
- Implemente um domínio autónomo no Azure utilizando implementados como Azure máquinas virtuais os controladores de domínio.

Todas as seguintes abordagens sofrem de custo de alto e overhead administrativo. Os administradores são necessários para implementar controladores de domínio utilizando máquinas virtuais no Azure. Para além disso, precisam de gerir, proteger, correcção, monitorizar, fazer cópia de segurança e resolver estes máquinas virtuais. Dependência de ligações de VPN ao diretório no local faz com que das cargas de trabalho implementadas no Azure vulnerável a falhas de rede breves ou de corrente. Estes falhas de rede por sua vez resultam em inferior de tempo útil e fiabilidade reduzida para estas aplicações.

Vamos concebido serviços de domínio do Azure AD para fornecer uma alternativa mais fácil.


## <a name="introducing-azure-ad-domain-services"></a>Introdução aos serviços de domínio do Azure AD
Azure AD Domain Services fornece serviços de domínio gerido como associação de domínio, autenticação Kerberos/NTLM do grupo política, LDAP, que são totalmente compatíveis com o Windows Server Active Directory. Pode consumir estes serviços de domínio sem necessidade de lhe implementar, gerir e correcção controladores de domínio na nuvem. Serviços de domínio do AD Azure integra-se com o seu inquilino existente do Azure AD, tornando assim possíveis para os utilizadores inicie sessão com as respetivas credenciais da empresa. Para além disso, pode utilizar grupos existentes e contas de utilizador para proteger o acesso aos recursos, assegurando uma mais suave 'elevação-e-shift' de recursos no local a serviços de infraestrutura do Azure.

Funcionalidade do Azure AD Domain Services funciona na perfeição, independentemente de se o seu inquilino do Azure AD é apenas na nuvem ou sincronizada com o Active Directory no local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Serviços de domínio do Azure AD para organizações apenas na nuvem
Um apenas na nuvem inquilino do Azure AD (muitas vezes, chamadas 'inquilinos geridos') não tem qualquer menores de identidade no local. Por outras palavras, são todas as nativos na nuvem - ou seja, criadas e geridas no Azure AD contas de utilizador, os respetivos palavras-passe e os membros do grupo. Considere para um pouco a incorporá Contoso é uma apenas na nuvem inquilino do Azure AD. Como é mostrado na ilustração seguinte, o administrador da Contoso configurou uma rede virtual nos serviços de infraestrutura do Azure. Aplicações e das cargas de trabalho do servidor são implementadas na rede virtual em máquinas virtuais do Azure. Dado Contoso é um inquilino apenas na nuvem, todas as identidades do utilizador, as respetivas credenciais, e os membros do grupo são criados e geridos no Azure AD.

![Descrição geral dos serviços de domínio do Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso administrador de TI pode ativar serviços de domínio do Azure AD do seu inquilino do Azure AD e escolher disponibilizar os serviços de domínio na rede virtual. A partir daí, os serviços de domínio do Azure AD disposições um domínio gerido e torna disponível na rede virtual. Todas as contas de utilizador, os membros do grupo e as credenciais de utilizador disponíveis no inquilino do Azure AD da Contoso também estão disponíveis neste domínio recentemente criado. Esta funcionalidade permite aos utilizadores na organização início de sessão para o domínio utilizando as respetivas credenciais da empresa - por exemplo, quando se liga remotamente ao domínio máquinas através do ambiente de trabalho remoto. Os administradores podem aprovisionar o acesso aos recursos no domínio utilizando os membros do grupo existente. Aplicações implementadas em máquinas virtuais da rede virtual podem utilizar funcionalidades, como a associação de domínio, LDAP ler, LDAP vincular, autenticação NTLM e Kerberos e política de grupo.

Alguns aspetos importantes do domínio gerido que está aprovisionado pelos serviços de domínio do Azure AD são os seguintes:

- Contoso administrador de TI não necessita de gerir, correcção ou monitorizar este domínio ou a quaisquer controladores de domínio para este domínio gerido.
- Não é necessário para gerir a replicação de AD para este domínio. Contas de utilizador, os membros do grupo e as credenciais de inquilino do Azure AD da Contoso são automaticamente disponíveis para este domínio gerido.
- Uma vez que o domínio é gerido pelo Azure AD serviços de domínio, Contoso administrador de TI não tiver privilégios de administrador do domínio ou o administrador da empresa neste domínio.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Serviços de domínio do Azure AD para organizações híbrido
As organizações com um híbrido infraestrutura de TI consumam uma mistura de recursos na nuvem e no local. Essas organizações sincronizar informações de identidade a partir do seu diretório no local ao seu inquilino do Azure AD. Organizações híbrido para migrar mais o aspecto das suas aplicações no local à nuvem, especialmente deverá ter em consideração diretório aplicações legadas, os serviços de domínio do Azure AD podem ser úteis às mesmas.

Construções de betão Corporation tem implementado a [ligação do Azure AD](../active-directory/active-directory-aadconnect.md), para sincronizar identidade informações a partir do seu diretório no local ao seu inquilino do Azure AD. As informações de identidade são sincronizadas incluem contas de utilizador, os respetivos hashes credenciais para a autenticação (sincronização de palavra-passe) e os membros do grupo.

> [AZURE.NOTE] A **sincronização de palavra-passe é obrigatória para organizações híbrido utilizar os serviços de domínio do Azure AD**. Este requisito é uma vez que as credenciais dos utilizadores são necessários no domínio gerido fornecido pelos serviços de domínio do Azure AD para autenticar estes utilizadores através de métodos de autenticação NTLM ou Kerberos.

![Serviços de domínio do Azure AD para construções de betão Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

A ilustração anterior mostra como as organizações com um híbrido infraestrutura de TI, tal como Corporation construções de betão, podem utilizar os serviços de domínio do Azure AD. Aplicações e das cargas de trabalho de servidor que necessitam de serviços de domínio do construções de betão são implementadas numa rede virtual nos serviços de infraestrutura do Azure. Do construções de betão administrador de TI pode ativar serviços de domínio do Azure AD do seu inquilino do Azure AD e escolher disponibilizar um domínio gerido esta rede virtual. Uma vez que construções de betão são uma organização com um híbrido infraestrutura de TI, contas de utilizador, grupos e as credenciais são sincronizadas ao seu inquilino do Azure AD a partir do seu diretório no local. Esta funcionalidade permite aos utilizadores iniciar sessão no domínio utilizando as respetivas credenciais da empresa - por exemplo, quando ligar remotamente ao máquinas aderido ao domínio através do ambiente de trabalho remoto. Os administradores podem aprovisionar o acesso aos recursos no domínio utilizando os membros do grupo existente. Aplicações implementadas em máquinas virtuais da rede virtual podem utilizar funcionalidades, como a associação de domínio, LDAP ler, LDAP vincular, autenticação NTLM e Kerberos e política de grupo.

Alguns aspetos importantes do domínio gerido que está aprovisionado pelos serviços de domínio do Azure AD são os seguintes:

- O domínio gerido é um domínio autónomo. Não é uma extensão de domínio da construções de betão no local.
- Do construções de betão administrador de TI não necessita de gerir, patches, ou monitorizar os controladores de domínio para este domínio gerido.
- Não é necessário para gerir a replicação de AD para este domínio. Contas de utilizador, os membros do grupo e as credenciais de diretório no local da construções de betão são sincronizadas para Azure AD através da ligação do Azure AD. Estes contas de utilizador, os membros do grupo e as credenciais estão automaticamente disponíveis dentro do domínio gerido.
- Uma vez que o domínio é gerido pelo Azure AD serviços de domínio, construções de betão administrador de TI não tiver privilégios de administrador do domínio ou o administrador da empresa neste domínio.


## <a name="benefits"></a>Benefícios
Com os serviços de domínio do Azure AD, que possa desfrutar os seguintes benefícios:

-   **Simples** – pode satisfazer as necessidades de identidade da máquinas virtuais implementadas a serviços de infraestrutura de Azure com alguns cliques simples. Não é necessário implementar e gerir a infraestrutura de identidade na configuração do Azure ou conectividade voltar a sua infraestrutura de identidade no local.

-   **Integrados** – serviços de domínio do Azure AD fortemente está integrado com o seu inquilino do Azure AD. Agora pode usar o Azure AD como um diretório de empresa baseado na nuvem integrada caters às necessidades dos seus aplicações modernos e de aplicações deverá ter em consideração diretório tradicionais.

-   **Compatíveis** – serviços de domínio do Azure AD é criada com base em infraestrutura de classe do enterprise provas dadas do Active Directory do Windows Server. Por conseguinte, as aplicações podem depender um maior grau de compatibilidade com funcionalidades do Active Directory do Windows Server. Nem todas as funcionalidades disponíveis no Windows Server AD estão atualmente disponíveis nos serviços de domínio do Azure AD. No entanto, as funcionalidades disponíveis são compatíveis com as funcionalidades do Windows Server AD correspondentes que depender na sua infraestrutura no local. As capacidades de associação LDAP, Kerberos, NTLM, política de grupo e domínio constituem uma oferta maduro que tenha sido testada e refinada sobre várias versões do Windows Server.

-   **Rentável** – com os serviços de domínio do Azure AD, pode evitar o encargo de infraestruturas e gestão de que está associado a gerir a infraestrutura de identidade para suportar aplicações deverá ter em consideração diretório tradicionais. Pode mover estas aplicações para serviços de infraestrutura do Azure e beneficiem das vantagens dos maiores poupanças despesas operacionais.

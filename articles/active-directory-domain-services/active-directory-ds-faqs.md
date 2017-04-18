<properties
    pageTitle="Perguntas mais frequentes - Azure Active Directory Domain Services | Microsoft Azure"
    description="Perguntas mais frequentes sobre o Azure Active Directory Domain Services"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Perguntas mais frequentes (FAQ)

Esta página respostas a perguntas mais frequentes sobre o Azure Active Directory Domain Services. Manter a verificar existência de atualizações.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Referem-se ao nosso [Guia de resolução de problemas](active-directory-ds-troubleshooting.md) para soluções para problemas comuns encontrados durante a configuração ou administração dos serviços de domínio do Azure AD.


### <a name="configuration"></a>Configuração

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios para um único diretório do Azure AD?
Não. Só pode criar um único domínio servido por serviços de domínio do Azure AD para um único diretório do Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Pode ativar serviços de domínio do Azure AD numa rede virtual Gestor de recursos do Azure?
Não. Serviços de domínio do AD Azure só podem ser activados numa rede virtual Azure clássica. Pode ligar a rede virtual clássica a uma rede virtual do Gestor de recursos utilizando rede virtual efectuado utilizar um domínio gerido numa rede virtual do Gestor de recursos.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso fazer dos serviços de domínio do Azure AD disponíveis no múltiplas redes virtuais dentro da minha subscrição?
O próprio serviço não suporta diretamente este cenário. Serviços de domínio do AD Azure está disponível no apenas uma rede virtual cada vez. No entanto, pode configurar a conectividade entre várias redes virtuais para expor os serviços de domínio do Azure AD para outras redes virtuais. Este artigo descreve como pode [Ligar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Pode ativar serviços de domínio do Azure AD através do PowerShell?
PowerShell/automatizado implementação dos serviços de domínio do Azure AD não está disponível neste momento.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Serviços de domínio do Azure AD está disponível no novo portal do Azure?
Não. Serviços de domínio do AD Azure podem ser configurados apenas no [Azure portal clássico](https://manage.windowsazure.com). Vamos esperar alargar o suporte para o [Azure portal](https://portal.azure.com) no futuro.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Pode adicionar os controladores de domínio para um domínio gerido de serviços de domínio do Azure AD?
Não. O domínio fornecido pelos serviços de domínio do Azure AD é um domínio gerido. Não é necessário aprovisionar, configurar ou, caso contrário, gerir controladores de domínio para este domínio - estes atividades de gestão de são fornecidas como um serviço pela Microsoft. Por conseguinte, não pode adicionar controladores de domínio adicionais (leitura / escrita ou só de leitura) para o domínio gerido.

### <a name="administration-and-operations"></a>Administração e operações

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso ligar para o controlador de domínio para o meu domínio gerido utilizando o ambiente de trabalho remoto?
Não. Não tem permissões para ligar ao controladores de domínio para o domínio gerido através do ambiente de trabalho remoto. Os membros do grupo 'AAD CC administradores' podem administrar o domínio gerido utilizando ferramentas de administração do AD como o Centro de administração do Active Directory (ADAC) ou AD PowerShell. Estas ferramentas são instaladas utilizando a funcionalidade 'Ferramentas de administração de servidor remoto' num Windows server aderido ao domínio gerido.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Posso tenha ativado dos serviços de domínio do Azure AD. Conta de utilizador que utilizar para máquinas de associação de domínio para este domínio?
Os utilizadores que adicionou ao grupo administrativo (por exemplo, ' AAD CC administradores') podem máquinas de associação de domínio. Para além disso, os utilizadores neste grupo são concedidos acesso de ambiente de trabalho remoto a máquinas aderido ao domínio.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Posso usar privilégios de administrador do domínio para o domínio fornecida pelos serviços de domínio do Azure AD?
Não. Não é concedido privilégios administrativos no domínio gerido. Privilégios de administrador de domínio e administrador da empresa não estão disponíveis para utilizar dentro do domínio. Administrador de domínio existente ou grupos de administrador de empresa no seu diretório do Azure AD também não são concedidos privilégios de administrador de domínio/enterprise no domínio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Pode modificar os membros do grupo a utilizar LDAP ou outras ferramentas administrativas do AD no domínios fornecidos pelos serviços de domínio do Azure AD?
Não. Os membros do grupo não podem ser modificados no domínios servidos pelos serviços de domínio do Azure AD. O mesmo se aplica atributos de utilizador. No entanto poderá alterar os membros do grupo ou atributos de utilizador no Azure AD ou no seu domínio no local. Estas alterações são sincronizadas automaticamente para serviços de domínio do Azure AD.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Pode expandir o esquema do domínio fornecido pelos serviços de domínio do Azure AD?
Não. Esquema de é administrado pela Microsoft para o domínio gerido. Extensões de esquema não são suportadas pelos serviços de domínio do Azure AD.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Pode modificar ou adicionar registos DNS no meu domínio gerido?
Sim. Os utilizadores que pertencem ao grupo 'AAD CC administradores' são concedidos privilégios de administrador de DNS, para modificar os seus registos DNS no domínio gerido. Estes utilizadores podem utilizar a consola do Gestor de DNS num computador a executar o Windows Server aderido ao domínio gerido, a gestão do DNS. Para utilizar a consola do Gestor de DNS, instale 'Ferramentas do servidor DNS', que é a parte da funcionalidade opcional 'Ferramentas de administração de servidor remoto' no servidor. Obter mais informações sobre [utilitários para administrar, monitorização e resolução de problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.


### <a name="billing-and-availability"></a>Faturação e de disponibilidade

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>É que um serviço pago dos serviços de domínio do Azure AD?
Sim. Para mais informações, consulte o artigo [preços de página](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Existe uma versão de avaliação gratuita do serviço?
Este serviço está incluído na versão de avaliação gratuita para Azure. Pode inscrever-se para uma [versão de avaliação gratuita um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>Pode obter serviços de domínio do Azure AD como parte do conjunto de aplicações de mobilidade Enterprise (EMS)?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>É preciso Azure AD Premium para utilizar os serviços de domínio do Azure AD?
Não. Serviços de domínio do AD Azure é uma serviço Azure repartição e não faz parte do EMS. Serviços de domínio do Azure AD estão disponíveis para todas as edições do Azure AD (gratuito, Basic e, Premium) e são faturada numa base de hora a hora, consoante a utilização.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Que regiões Azure está disponível no serviço?
Referem-se para a página [Dos serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para ver uma lista das regiões Azure onde o dos serviços de domínio do Azure AD está disponível.

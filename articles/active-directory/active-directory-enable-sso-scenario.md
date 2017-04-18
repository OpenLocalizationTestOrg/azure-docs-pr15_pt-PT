<properties
    pageTitle="Gerir aplicações com o Azure Active Directory | Microsoft Azure"
    description="Este artigo os benefícios de como integrar o Azure Active Directory com o seu no local, na nuvem e SaaS aplicações."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>Gerir aplicações com o Azure Active Directory

Para além do fluxo de trabalho real ou conteúdo, empresas têm dois requisitos básicos para todas as aplicações:

1. Para aumentar a produtividade, aplicações devem ser mais fácil descobrir e aceder a

2. Para ativar a segurança e governação, a organização necessita de controlo e supervisão sobre quem pode e realmente está a aceder às cada aplicação

Parte do mundo das aplicações de nuvem Isto pode melhor ser atingido utilizando a identidade para controlar "*quem tem permissão para fazer o QUÊ*".

Na informática terminologia:

- *Quem* é conhecido como *identidade* - a gestão de utilizadores e grupos

- *O que* é conhecido como *Gestão de acesso* – a gestão de acesso aos recursos protegidos

Ambos os componentes em conjunto são conhecidos como *identidade e gestão de acesso (IAM)*, que é definido pelo grupo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) como "*a disciplina de segurança que permite aos indivíduos direito aceder aos recursos direita à direita horas para as razões direita*".

Tudo bem, o que é o problema? Se IAM é *não são geridos* num único local com uma solução integrada:

- Os administradores de identidade têm de individualmente criar e actualizar contas de utilizador em todas as aplicações em separado, uma atividade redundante e demorada.

- Os utilizadores têm a memorizar múltiplas credenciais para aceder às aplicações precisam de trabalhar com. Como resultado, os utilizadores tendem para anotar as suas palavras-passe ou utilize outras soluções de gestão de palavra-passe que introduz outros riscos de segurança de dados.

- Atividades redundantes, morosa reduzir a quantidade de vez que os utilizadores e administradores estão a trabalhar actividades de negócio que aumentar a linha de parte inferior da sua empresa.

Sim, o que geralmente impede que as organizações de aprovar soluções integradas que poderá IAM?

- Soluções mais técnicas são baseadas nas plataformas de software que precisam de ser implementada e adaptada por cada organização para as suas próprias aplicações.

- Nuvem aplicações são com frequência aprovadas taxa de juro superior de organização de TI pode integrar com soluções IAM existentes.

- Segurança e monitorização ferramentas necessitam de personalização adicional e integração para alcançar abrangentes E2E cenários.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory integrado com aplicações

Azure Active Directory é identidade abrangente da Microsoft como uma solução de serviço (IDaaS) que:

- Permite IAM como um serviço na nuvem 

- Fornece uma gestão central de acesso, sessão único (SSO) e elaboração de relatórios 

- Suporta integrada gestão de acesso para [milhares de aplicações](https://azure.microsoft.com/marketplace/active-directory/) na Galeria de aplicação, incluindo Salesforce, Google Apps, caixa, Concur e muito mais. 


Com o Azure Active Directory, todas as aplicações publica para os seus parceiros e clientes (empresa ou consumidor) têm a mesma identidade e as capacidades da gestão de acesso.<br> Permite-lhe significativamente reduzir os custos operacionais.

O que acontece se que necessita para implementar uma aplicação que ainda não está listada na Galeria de aplicação? Enquanto este é um pouco mais demorada do que a configuração do SSO para aplicações a partir da Galeria de aplicação, Azure AD fornece-lhe um assistente que o ajuda a com a configuração.

O valor do Azure AD vai para além das aplicações de nuvem "apenas". Também pode utilizá-la com as aplicações no local ao fornecer acesso remoto seguro. Com acesso remoto seguro, pode eliminar o a necessidade de VPNs ou outras implementações de gestão de acesso remoto tradicionais.

Ao fornecer gestão central de acesso e início de sessão único (SSO) para todas as suas aplicações, Azure AD fornece a solução para os problemas de segurança e a produtividade de dados principal.

- Os utilizadores podem aceder a várias aplicações com um início de sessão que lhe dá uma mais tempo a rendimentos gerar ou de negócios atividades de operações concluídas.

- Os administradores de identidade podem gerir o acesso às aplicações num só local.

A vantagem para o utilizador e para a sua empresa é óbvia. Vamos mais detalhada dos benefícios para um administrador de identidade e a organização.

## <a name="integrated-application-benefits"></a>Benefícios da aplicação integrada

O processo SSO tem dois passos:

- Autenticação, o processo de validar a identidade do utilizador.

- Autorização, a decisão para activar ou bloquear o acesso a um recurso, com uma política de acesso.

Quando utilizar Azure AD para gerir as aplicações e ativar o SSO:

- Autenticação é feita no local (por exemplo, AD) ou conta do Azure AD do utilizador.

- Autorização executa sobre a Azure AD atribuição proteção política e garantir a experiência de utilizador final consistentes e permitindo-lhe adicionar atribuição, localizações e condições MFA em qualquer aplicação, independentemente das suas capacidades internas.

Importante para compreender que a forma como a autorização é activado sobre a aplicação de destino-varia dependendo de como a aplicação foi integrada com o Azure AD.

- **Aplicações integradas previamente pelo fornecedor de serviços** Como o Office 365 e o Azure, estes são aplicações criadas diretamente no Azure AD e depender para os respetivos abrangentes identidade e acesso as capacidades da gestão. Acesso a estas aplicações é activado através de informações do directório e emissão de tokens.

- **Aplicações integradas previamente pela Microsoft e aplicações personalizadas** Estas são as aplicações de nuvem independentes que dependem de um diretório de interno da aplicação e podem funcionam de forma independente Azure AD. Acesso a estas aplicações é activado através da emissão de uma aplicação específica das credenciais mapeadas para uma conta de aplicações. Dependendo as capacidades de aplicação, a credencial poderá ter um token de Federação ou nome de utilizador e palavra-passe de uma conta que anteriormente aprovisionada na aplicação.

- **No local aplicações** Aplicações publicados através do proxy de aplicação do Azure AD principalmente ativar o acesso a aplicações no local. Estas aplicações dependem de uma central no diretório local, como o Windows Server Active Directory. Acesso a estas aplicações é activado por acionar o proxy para a prestação o conteúdo de aplicação para o utilizador final enquanto honrar o requisito de início de sessão no local.

Por exemplo, se um utilizador adere a sua organização, terá de criar uma conta do utilizador no Azure AD para as operações de início de sessão principais. Se este utilizador requer o acesso a uma aplicação gerida como Salesforce, também tem de criar uma conta para este utilizador no Salesforce e ligá-lo para a conta Azure para tornar o SSO trabalhar. Quando o utilizador sai da organização, é aconselhável para eliminar a conta do Azure AD e todas as contas de homólogo para a IAM armazena das aplicações do utilizador tinha acesso.

## <a name="access-detection"></a>Deteção de acesso

Em modernas empresas, os departamentos de TI frequentemente não estão em mente todas as aplicações de nuvem que estão a ser utilizadas. Em conjunto com a deteção de aplicação na nuvem, Azure AD fornece uma solução para detetar estas aplicações.

## <a name="account-management"></a>Gestão de contas

Normalmente, a gestão de contas nas várias aplicações do é um processo manual realizado por IT ou suporte pessoal na organização. Azure AD automatizado totalmente gestão de contas ao longo de todas as aplicações de serviço de fornecedor integrada e nessas aplicações integradas previamente pela Microsoft suporte de aprovisionamento automatizado do utilizador ou SAML JIT.

## <a name="automated-user-provisioning"></a>Aprovisionamento automatizado do utilizador

Algumas aplicações fornecem interfaces de automatização para criação e remoção (ou desativação) de contas. Se um fornecedor oferecer esta uma interface, é utilizada por Azure AD. Reduz os custos do seu operacionais avançados, uma vez que as tarefas administrativas ocorrem automaticamente e melhora a segurança do seu ambiente porque diminui a hipótese de acesso não autorizado.

## <a name="access-management"></a>Gestão de acesso

Utilizar o Azure AD pode gerir acesso a aplicações utilizando individuais ou regra pelo esforço atribuições. Também pode delegar aceder a gestão de para as pessoas certas na organização garantir a melhor supervisão e reduzindo os encargos suporte técnico.

## <a name="on-premises-applications"></a>Aplicações no local

Incorporado na aplicação o proxy permite-lhe publicar as suas aplicações no local para os utilizadores que resulta em ambos consistentes aceder a experiência com a aplicação de nuvem modernos e os benefícios de capacidades de monitorização, relatórios e de segurança do Azure AD.

## <a name="reporting-and-monitoring"></a>Relatórios e de controlo

Azure AD fornece relato previamente integrada e monitorizar as funcionalidades que permitem-lhe saber quem tem acesso a aplicações e, quando estes realmente utilizados.

## <a name="related-capabilities"></a>Funcionalidades relacionadas

Com o Azure AD pode proteger as aplicações com as políticas de acesso granular e previamente integrada MFA. Para obter mais informações sobre o Azure MFA consulte [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Introdução

Para começar a integração de aplicações com o Azure AD, consulte o artigo [Guia de introdução de integrar o Azure Active Directory com aplicações de introdução](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Consulte também

[Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

<properties
  pageTitle="Gerir o acesso às aplicações com o Azure AD |  Microsoft Azure"
  description="Descreve como Azure Active Directory permitem às organizações especificar as aplicações para o qual cada utilizador tem acesso."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Gerir o acesso às aplicações

Gestão de acesso contínuo, avaliação de utilização e relatórios continuam a ser um desafio depois de uma aplicação está integrada no sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou suporte técnico terá de efetuar uma função ativa em curso na gerir o acesso às suas aplicações. Por vezes, a atribuição é executada por um departamento de TI geral ou divisão. Muitas vezes, a decisão de atribuição se destina a ser delegado para o Decisor de negócios, exigir os respetivos aprovação antes de efetuar IT a atribuição.  Outras organizações investirem em integração com um existente automatizado identidade e acesso sistema de gestão, como o controlo de acesso baseado em funções RBCA () ou controlo de acesso baseado em atributo (ABAC). A integração e de desenvolvimento regra tendem para ser especializadas e dispendioso. Monitorização ou criar relatórios sobre um dos abordagem de gestão são os suas próprias investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como é que o Azure Active Directory é ajuda?

 Azure AD suporta a gestão de acesso extenso para aplicações configuradas, permitir às organizações alcançar facilmente as políticas de acesso à direita que se situa entre atribuição automática, com base no atributo (cenários ABAC ou RBAC) através da delegação e incluindo gestão do administrador. Com o Azure AD pode facilmente alcançar políticas complexas, combinando vários modelos de gestão para uma única aplicação e pode ainda novamente utilizar regras de gestão nos aplicações com as mesmo audiências.

 - [Adicionar novo ou aplicações existentes](active-directory-sso-integrate-saas-apps.md)


 Atribuição de aplicações do Azure AD foca-se em dois modos de atribuição principal:

- **Atribuição individual** Um administrador de TI com permissões de Administrador Global do diretório pode selecionar contas de utilizador individual e conceder-lhes acesso à aplicação.
- **Atribuição com base no grupo (paga Azure AD apenas)** Um administrador de TI com permissões de Administrador Global do diretório pode atribuir um grupo para a aplicação. Acesso de utilizadores específicos são determinado pela se estiverem membros do grupo ao tempo que tentarem aceder à aplicação. Por outras palavras, um administrador de forma eficaz pode criar uma regra de atribuição, começando "qualquer membro do grupo atribuído actual possui acesso à aplicação". Os administradores de utilizar esta opção de atribuição, podem beneficiar a partir de qualquer uma das opções de gestão do grupo Azure AD, incluindo [com base no atributo grupos dinâmicos](active-directory-accessmanagement-manage-groups.md), grupos de sistema externo (por exemplo, do Active Directory no local ou dia de trabalho) ou grupos administrador- ou gerido pelo automática-circulação. Um único grupo pode ser facilmente atribuído para várias aplicações, garantindo que aplicações com afinidade atribuição podem partilhar regras de atribuição, reduzir a complexidade de gestão global. Tenha em atenção ao grupo interno aninhado associações não são suportadas para a atribuição de grupo para aplicações neste momento.

Utilizar estes modos de duas atribuição, os administradores podem alcançar qualquer abordagem de gestão de atribuição conveniente.

Com o Azure AD, a utilização e relatórios de atribuição está totalmente integrado, permitir que os administradores facilmente reportar o estado de atribuição, erros de atribuição e a utilização par.

## <a name="complex-application-assignment-with-azure-ad"></a>Atribuição de aplicação complexo com Azure AD

Considere a hipótese de uma aplicação como o Salesforce. Em muitas organizações, Salesforce é principalmente utilizado pelas organizações de vendas e marketing. Muitas vezes, os membros da equipa de marketing altamente tem privilégios acesso a equipa de vendas, enquanto membros da equipa de vendas tem acesso limitado. Em muitos casos, uma população abrangente dos técnicos de informação têm acesso restrito para a aplicação. Exceções a estas regras complicar questões. É frequentemente reservados as equipas de liderança marketing ou vendas para conceder acesso a um utilizador ou alterar os respetivos funções independentemente dos existentes destas regras genéricas.

Com o Azure AD, aplicações, como o Salesforce podem ser pré-configurada para single sign-on (SSO) e de aprovisionamento automatizado. Assim que a aplicação está configurada, um administrador pode demorar a ação única para criar e atribuir os grupos adequados. Neste exemplo, um administrador foi possível executar as seguintes atribuições:

- [Grupos dinâmicos](active-directory-accessmanagement-manage-groups.md) pode ser definida para representar automaticamente todos os membros das equipas de vendas e marketing utilizando os atributos como departamento ou função:

    - Todos os membros de grupos de marketing seriam ser atribuídos à função "marketing" no Salesforce

    - Todos os membros da equipa de vendas grupos seriam ser atribuídos à função "venda" no Salesforce. Um refinamento mais poderia utilizar vários grupos que representam equipas de vendas regionais atribuídas a funções de Salesforce diferentes.

- Para permitir que o mecanismo exceção, um grupo de gestão personalizada foi criado para cada função. Por exemplo, o grupo "Salesforce marketing exceção" pode ser criado como um grupo de gestão personalizada. O grupo pode ser atribuído à função de marketing de Salesforce e a equipa de marketing liderança pode ser efetuada proprietários. Membros da equipa do liderança marketing poderiam adicionar ou remover utilizadores, definido uma política de associação, ou mesmo aprovar ou recusar pedidos dos utilizadores individuais para participar. Isto é suportado através de uma experiência adequado o trabalho de informações que não requer a realização formação especializada proprietários ou membros.

Neste caso, todos os utilizadores atribuídos seriam ser automaticamente aprovisionados para Salesforce, conforme são adicionados para diferentes grupos que seria ser atualizada os respetivos atribuição de funções no Salesforce. Os utilizadores seriam poderão detetar e aceder Salesforce através do painel de acesso do Microsoft aplicação, clientes do Office web, ou até mesmo ao navegar para a sua página de início de sessão do Salesforce organizacional. Os administradores seria conseguem ver facilmente a utilização e da atribuição Estado com Azure AD de comunicação.

Os administradores podem empregam [acesso condicional do Azure AD](active-directory-conditional-access.md) para definir as políticas de acesso para funções específicas. Podem incluir estas políticas se for permitido acesso fora ambiente empresarial e até mesmo autenticação Multifator ou dispositivo requisitos para alcançar acesso em vários casos.

## <a name="how-can-i-get-started"></a>Como posso começar a utilizar?

Em primeiro lugar, se ainda não estiver a utilizar o Azure AD e se for um administrador de TI:

 - [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) -Pode inscrever-se para hoje uma avaliação de 30 dias gratuita e implementar a solução de nuvem primeira em 5 minutos utilizando esta ligação

Azure AD que ativar a partilha de conta incluem:

- [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
- Adicionar aplicações ao Azure AD
- Introdução ao atribuição
- Atribuição de aplicação perguntas mais frequentes
- [Dashboard/relatórios de utilização da aplicação](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Onde posso saber mais?

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Proteger as aplicações com o access condicional](active-directory-conditional-access.md)
- [Grupo de gestão personalizada gestão/SSAA](active-directory-accessmanagement-self-service-group-management.md)

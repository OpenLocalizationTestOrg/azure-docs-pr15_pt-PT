
<properties
    pageTitle="Referência técnica do Azure Active acesso a directórios condicional | Microsoft Azure"
    description="Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando autenticar o utilizador e antes de permissão do acesso para a aplicação. Assim que forem cumpridas destas condições, o utilizador autenticado e permissão de acesso para a aplicação."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referência técnica do Azure Active acesso condicional de diretório

## <a name="services-enabled-with-conditional-access"></a>Serviços activados com acesso condicional
Regras de acesso condicional são suportadas através de vários tipos de aplicação do Azure AD. Esta lista inclui:

- Aplicações federadas a partir da Galeria de aplicação do Azure AD
- Aplicações de SSO de palavra-passe a partir da Galeria de aplicação do Azure AD
- Aplicações registadas com o Proxy de aplicação do Azure
- Desenvolvido pela linha de negócio e as aplicações do inquilinos com várias registadas com a Azure AD
- Visual Studio Online
- Aplicação Remote Azure
-   Dynamics CRM
- Yammer do Microsoft Office 365
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online (inclui o OneDrive para empresas)


## <a name="enable-access-rules"></a>Ativar as regras de acesso

Cada regra pode ser ativada ou desactivada num por bases de aplicação. Quando as regras são **ON** vai ser ativadas e impostas para aceder a aplicação de utilizadores. Quando são **OFF** estes não serão utilizadas e não terão impacto no sinal de utilizadores na experiência.

## <a name="applying-rules-to-specific-users"></a>Aplicar regras a utilizadores específicos
Podem ser aplicadas regras para conjuntos de utilizadores com base num grupo de segurança através da definição de **Aplicar a**específicos. **Aplicar a** pode ser definido para **Todos os utilizadores** ou **grupos**. Quando definida para **Todos os utilizadores** as regras serão aplicada a todos os utilizadores com acesso à aplicação. A opção de **grupos** permite segurança específica e grupos de distribuição para estar selecionada, as regras só serão impostas para estes grupos.

Quando implementar uma regra, é comuns para o aplicar primeiro conjunto limitado de utilizadores, que são membros dos grupos de tal exija. Uma vez concluída a regra pode ser aplicada a **Todos os utilizadores**. Isto irá acionar a regra para impostas para todos os utilizadores na organização.

Selecionados grupos podem também ser isentas de política através da opção **exceto** . Qualquer membros dos grupos destes serão excluídos mesmo que aparecem num grupo incluído.

## <a name="at-work-networks"></a>"No trabalho" redes


As regras de acesso condicional que utilizam uma rede "no trabalho", depender de intervalos de endereços IP fidedignos que tenham sido configurados no Azure AD, ou utilização dos afirmação "dentro corpnet" a partir do AD FS. Estas regras incluem:

- Exigir autenticação multifator quando não estiver no local de trabalho
- Bloquear o acesso do quando não estiver no local de trabalho

Opções para especificar "no trabalho" redes

1. Configure intervalos de endereços IP fidedignos na [página de configuração de autenticação multifator](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Política de acesso condicional irá utilizar os intervalos configurados em cada pedido de autenticação e emissão de tokens para avaliar regras. 
2. Configurar a utilização do interior corpnet reclamar, esta opção pode ser utilizada com directórios federados, utilizando o AD FS. [Mais informações sobre o interior coronet afirmações](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configure intervalos de endereços IP públicos. No separador configurar, para o seu diretório, pode definir endereços IP públicos. Acesso condicional utilizará estas, tal como 'no local de trabalho' endereços IP, esta opção permite-intervalos adicionais configurar, acima do limite de endereço IP 50 que é aplicada pela página definição MFA.



## <a name="rules-based-on-application-sensitivity"></a>Regras baseadas em sensibilidade de aplicação

As regras são configuradas por aplicação permitindo que os serviços de valor elevado protegidos sem que afetam o acesso a outros serviços. Regras de acesso condicional podem ser configuradas no separador **Configurar** da aplicação. 

Regras previewhttp:

- **Exigir autenticação multifator**
 - Todos os utilizadores que esta política é aplicada a serão necessários para autenticar através de pelo menos uma vez a autenticação multifator.
 
- **Exigir autenticação multifator quando não estiver no local de trabalho**
 - Se esta política é aplicada, todos os utilizadores é necessários ter executado pelo menos uma vez a autenticação multifator se estes acederem ao serviço a partir de uma localização remota não trabalho. Se forem movidos a partir de uma trabalho localização remota, vai ser necessário para efetuar a autenticação multifator ao aceder ao serviço.
 
- **Bloquear o acesso do quando não estiver no local de trabalho** 
 - Quando os utilizadores movem do trabalho para uma localização remota, serão bloqueadas se a política "Bloquear o acesso do quando não estiver no local de trabalho" é aplicada às mesmas.  Estes serão novamente permitidas acesso quando a uma localização de trabalho.


## <a name="related-topics"></a>Tópicos relacionados

- [Proteger o acesso ao Office 365 e outras aplicações ligado ao Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

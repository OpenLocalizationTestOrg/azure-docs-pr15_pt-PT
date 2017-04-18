<properties
    pageTitle="Acesso condicional para aplicações publicados com Proxy de aplicação do Azure AD"
    description="Abrange como configurar o acesso condicional para aplicações que publicar para ser acedida remotamente utilizando o Proxy de aplicação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>Trabalhar com o access condicional

Pode configurar regras de acesso para conceder acesso condicional às aplicações publicado utilizando o Proxy de aplicação. Permite-lhe:

- Exigir autenticação multifator por aplicação
- Exigir autenticação multifator apenas quando os utilizadores não estão no local de trabalho
- Bloquear utilizadores acedam a aplicação quando não está a trabalhar

Estas regras podem ser aplicadas a todos os utilizadores e grupos ou apenas para utilizadores e grupos específicos. Por predefinição, a regra será aplicada a todos os utilizadores que têm acesso à aplicação. No entanto, a regra também pode ser restringida a utilizadores que são membros dos grupos de segurança especificado.  

Regras de acesso são avaliadas quando um utilizador acede a uma aplicação federada que utiliza OAuth 2.0, OpenID ligar, SAML ou WS Federação. Além disso, as regras de acesso são avaliadas com OAuth 2.0 e ligar OpenID quando um token de atualização é utilizado para adquirir um token de acesso.

## <a name="conditional-access-prerequisites"></a>Pré-requisitos de acesso condicional

- Subscrição do Azure Active Directory Premium
- Um inquilino do Azure Active Directory federado ou gerido
- Inquilinos federados exigem que a autenticação multifator (MFA) de estar ativada  
    ![Configurar regras de acesso - exigir autenticação multifator](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurar uma autenticação multifator por aplicação
1. Inicie sessão como administrador no portal do Azure clássico.
2. Aceda ao Active Directory e selecione a pasta na qual pretende ativar o Proxy de aplicação.
3. Clique em **aplicações** e desloque-se até à secção de **Regras de acesso** . Secção de regras de acesso só é apresentada para aplicações publicadas utilizando o Proxy de aplicação que utilizam a autenticação federada.
4. Ative a regra ao selecionar as **Regras de acesso a ativar** para **no**.
5. Especifique os utilizadores e grupos para o qual as regras serão aplicada. Utilize o botão **Adicionar grupo** para selecionar um ou mais grupos a que será aplicada a regra de acesso. Esta caixa de diálogo pode também ser utilizada para remover grupos seleccionados.  Quando as regras estão seleccionadas para aplicar a grupos, as regras de acesso só serão impostas para utilizadores que pertencem a um dos grupos de segurança especificados.  

  - Para excluir explicitamente grupos de segurança da regra, selecione **exceto** e especificar um ou mais grupos. Os utilizadores que são membros de um grupo na lista excepto serão não exigidos que para efetuar a autenticação multifator.  

  - Se um utilizador tiver sido configurado para utilizar a funcionalidade de autenticação multifator por utilizador, esta definição irá precedência sobre as regras de autenticação multifator de aplicação. Isto significa que um utilizador que tenha sido configurado para autenticação multifator de por utilizador será necessário para efetuar a autenticação multifator mesmo que tenham sido excluídos das regras de autenticação multifator a aplicação. Saiba mais sobre a [autenticação multifator e as definições de por utilizador](../multi-factor-authentication/multi-factor-authentication.md).

6. Selecione a regra de acesso que pretende definir:
    - **Autenticação multifator exigir**: os utilizadores a quem aplicam regras de acesso será necessários para autenticação multifator concluída antes de aceder a aplicação para o qual se aplica a regra.
    - **Autenticação multifator exigir quando não estiver no local de trabalho**: utilizadores tentarem aceder à aplicação a partir de um endereço IP de confiança irão não ser necessário executar autenticação multifator. Os intervalos de endereços IP fidedignos podem ser configurados na página de definições de autenticação multifator.
    - **Bloquear o acesso do quando não estiver no local de trabalho**: os utilizadores a tentar aceder à aplicação a partir de fora da rede da sua empresa não poderão aceder à aplicação.


## <a name="configuring-mfa-for-federation-services"></a>Configurar MFA para serviços de Federação
Para federados inquilinos, autenticação multifator (MFA) poderá ser executada por Azure Active Directory ou no local servidor do AD FS. Por predefinição, MFA irá ocorrer em qualquer página alojada pelo Azure Active Directory. Para configurar MFA no local, execute o Windows PowerShell e utilize a propriedade – SupportsMFA para definir o módulo Azure AD.

O exemplo seguinte mostra como ativar MFA no local ao utilizar o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no inquilino contoso.com:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Para além de definir este sinalizador, tem de ser configurada para executar a autenticação multifator a instância do inquilino federados AD FS. Siga as instruções para [Implementar o Microsoft Azure autenticação multifator no local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Consulte também

- [Trabalhar com aplicações deverá ter em consideração em afirmações](active-directory-application-proxy-claims-aware-apps.md)
- [Publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Publicar aplicações utilizando o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)

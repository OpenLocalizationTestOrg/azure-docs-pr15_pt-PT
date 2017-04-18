<properties
    pageTitle="Azure acesso condicional para as aplicações de SaaS | Microsoft Azure"
    description="Acesso condicional no Azure AD permite-lhe configurar regras de acesso a autenticação multifator de por aplicação e a capacidade de bloquear o acesso de utilizadores não numa rede fidedigno. "
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Introdução ao Azure Active Directory condicional Access

Azure Active Directory acesso condicional para as aplicações de [SaaS](https://azure.microsoft.com/overview/what-is-saas/) e Azure AD ligado permite-lhe as aplicações a que configurar o acesso condicional com base no grupo, localização e sensibilidade de aplicação. 

Com o access condicional com base em sensibilidade de aplicação, pode definir regras de acesso a autenticação multifator (MFA) por aplicação. MFA por aplicação fornece a capacidade de bloquear o acesso de utilizadores que não estão numa rede fidedignos. Pode aplicar regras MFA a todos os utilizadores que estão atribuídos para a aplicação ou apenas para os utilizadores dentro de grupos de segurança especificado.  Os utilizadores podem ser excluídos do requisito de MFA se estão a aceder a aplicação a partir de um endereço de IP que está no interior da rede da empresa.

Estas capacidades estará disponíveis para clientes que comprou uma licença do Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Pré-requisitos de cenário
* Licença para o Azure Active Directory Premium

* Federados ou gerido inquilino do Azure Active Directory

* Inquilinos federados requerem que a autenticação multifator de estar ativada.

## <a name="configure-per-application-access-rules"></a>Configurar regras de acesso por aplicação

Esta secção descreve como configurar regras de acesso por aplicação.

1. Inicie sessão no portal do Azure clássica ao utilizar uma conta que seja um administrador global do Azure AD.
2. No painel esquerdo, selecione **Do Active Directory**.
3. No separador do diretório, selecione o seu diretório.
4. Selecione o separador de **aplicações** .
5. Selecione a aplicação que a regra será definida para.
6. Selecione o separador de **Configurar** .
7. Desloque para baixo para a secção de regras de acesso. Selecione a regra de acesso pretendido.
8. Especificar os utilizadores que a regra será aplicada a.
9. Active a política selecionando **ativado de estar ligado**.

##<a name="understanding-access-rules"></a>Noções sobre regras de acesso

Esta secção fornece uma descrição detalhada das regras de acesso suportados no Azure condicional o acesso da aplicação.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Especificar os utilizadores as regras de acesso aplicam-se para

Por predefinição, a política será aplicada a todos os utilizadores que têm acesso à aplicação. No entanto, também pode restringir a política para utilizadores que são membros dos grupos de segurança especificado. No botão **Adicionar grupo** é utilizado para selecionar um ou mais grupos da caixa de diálogo de seleção de grupo que a regra de acesso serão aplicadas a. Esta caixa de diálogo pode também ser utilizada para remover grupos seleccionados. Quando as regras estão seleccionadas para aplicar a grupos, as regras de acesso só serão impostas para utilizadores que pertencem a um dos grupos de segurança especificados.

Grupos de segurança podem também ser explicitamente excluídos da política ao selecionar a opção **exceto** e especificar um ou mais grupos. Os utilizadores que forem membro de um grupo na lista **exceto** não serão sujeito o requisito de autenticação multifator, mesmo se estiverem um membro de um grupo de que se aplica a regra de acesso a.
A regra de acesso é mostrada abaixo irão necessitar todos os utilizadores no grupo gestores para utilizar a autenticação multifator ao aceder à aplicação.

![Definir regras de acesso condicional com MFA](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Regras de acesso condicional com MFA
Se tiver sido configurado um utilizador utilizando a funcionalidade de autenticação multifator por utilizador, esta definição no utilizador será combinar com as regras de autenticação multifator da aplicação. Isto significa que um utilizador que tenha sido configurado para autenticação multifator de por utilizador será exigido que para efetuar a autenticação multifator mesmo que tenham sido excluídos das regras de autenticação multifator de aplicação. Saiba mais sobre as definições de autenticação e por utilizador de multifator.

### <a name="access-rule-options"></a>Opções de acesso à regra
São suportadas as seguintes opções:

* **Exigir autenticação multifator**: os utilizadores a quem as regras de acesso aplicarem a, será necessário para autenticação multifator concluída antes de aceder à aplicação que se aplica a política para.

* **Exigir autenticação multifator quando não estiver no local de trabalho**: um utilizador que está a chegar a partir de um endereço IP de confiança não será pedido para efetuar a autenticação multifator. Os intervalos de endereços IP fidedignos podem ser configurados na página de definições de autenticação multifator.

* **Bloquear o acesso do quando não estiver no local de trabalho**: um utilizador que não está a chegar a partir de um endereço IP de confiança irá estar bloqueado. Os intervalos de endereços IP fidedignos podem ser configurados na página de definições de autenticação multifator.

### <a name="setting-rule-status"></a>Definir o estado de regra
Estado do Access regra permite ativar as regras ou desativar. Quando as regras de acesso estão desactivados, o requisito de autenticação multifator não é aplicado.

### <a name="access-rule-evaluation"></a>Avaliação das regras de acesso

Regras de acesso são avaliadas quando um utilizador acede a uma aplicação federada que utiliza OAuth 2.0, OpenID ligar, SAML ou WS Federação. Além disso, as regras de acesso são avaliadas quando os OAuth 2.0 e, em seguida, OpenID ligar-se utilizar um token de atualização para adquirir um token de acesso. Se a avaliação da política falhar quando for utilizado um token de atualização, será devolvido o erro **invalid_grant** , isto indica que o utilizador tem de voltar a autenticar para o cliente.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurar os serviços de Federação para fornecer a autenticação multifator

Para federados inquilinos, MFA poderá ser executado por Azure Active Directory ou no local servidor do AD FS.

Por predefinição, irá ocorrer MFA a uma página alojada pelo Azure Active Directory. Para configurar MFA no local, a propriedade de **– SupportsMFA** tem de estar definida como **Verdadeiro** no Azure Active Directory, ao utilizar o módulo Azure AD para Windows PowerShell.

O exemplo seguinte mostra como ativar MFA no local ao utilizar o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no inquilino contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Para além de definir este sinalizador, tem de ser configurada para executar a autenticação multifator a instância do inquilino federados AD FS. Siga as instruções para [Implementar o Azure a autenticação Multifator no local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Artigos relacionados

- [Proteger o acesso ao Office 365 e outras aplicações ligado ao Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

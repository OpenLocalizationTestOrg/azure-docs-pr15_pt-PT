<properties
   pageTitle="Resolução de problemas: 'Active Directory' item está em falta ou não está disponível | Microsoft Azure "
   description="O que fazer quando o item de menu do Active Directory não aparece no Portal de gestão do Azure."
   services="active-directory"
   documentationCenter="na"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Resolução de problemas: 'Active Directory' item está em falta ou não está disponível

Muitos das instruções para utilizar funcionalidades do Azure Active Directory e os serviços comecem por "Aceda ao Portal de gestão do Azure e clique em **do Active Directory**." Mas o que deve fazer se o item de menu ou extensão do Active Directory não aparecer ou se estiver marcado **Não está disponível**? Este tópico destina-se para o ajudar. Descreve as condições em qual **Do Active Directory** não aparecer ou não está disponível e explica como continuar.

## <a name="active-directory-is-missing"></a>O Active Directory está em falta

Normalmente, um item **Do Active Directory** é apresentado no menu de navegação à esquerda. As instruções em procedimentos do Azure Active Directory partem do pressuposto de que este item está na sua vista.

![Captura de ecrã: Active Directory no Azure](./media/active-directory-troubleshooting/typical-view.png)

O item do Active Directory é apresentado no menu de navegação à esquerda quando qualquer uma das seguintes condições é verdadeira. Caso contrário, não é apresentado o item.

* O utilizador actual sessão iniciada com uma conta Microsoft (anteriormente conhecida como um Windows Live ID).

    OU

* O inquilino Azure tem um diretório e a conta atual é um administrador do diretório.

    OU

* O inquilino Azure tem pelo menos um espaço de nomes do controlo de acesso do Azure AD (ACS). Para mais informações, consulte o artigo [Espaço de nomes de controlo de acesso](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    OU

* O inquilino Azure tem pelo menos um fornecedor de autenticação Multifator de Azure. Para obter mais informações, consulte [Administrar fornecedores de autenticação Multifator de Azure](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Para criar um espaço de nomes do controlo de acesso ou um fornecedor de autenticação Multifator, clique em **+ Novo** > **Serviços de aplicação** > **Do Active Directory**.

Para obter direitos administrativos para um diretório, tenha um administrador de atribuir uma função de administrador a sua conta. Para obter detalhes, consulte o artigo [atribuir funções de administrador](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>O Active Directory não está disponível

Quando clica em **+ Novo** > um item **Do Active Directory** de**Serviços de aplicação**, é apresentada. Especificamente, o item do Active Directory é apresentada quando qualquer uma das funcionalidades do Active Directory, como o diretório, controlo de acesso ou fornecedor de autenticação Multifator, estão disponíveis ao utilizador actual.

No entanto, enquanto a página é carregada, o item está esbatido e está marcado como **Não está disponível**. Este é um estado temporário. Se aguardar alguns segundos, o item fica disponível. Se o atraso é prolongado, atualizar a página web com frequência resolve o problema.

![Captura de ecrã: Active Directory não está disponível](./media/active-directory-troubleshooting/not-available.png)

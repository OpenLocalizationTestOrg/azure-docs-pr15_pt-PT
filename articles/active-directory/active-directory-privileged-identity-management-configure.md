<properties
    pageTitle="Gestão de identidades do Azure AD privilegiados | Microsoft Azure"
    description="Um tópico que explica o que é a gestão de identidades do Azure AD privilegiados e de como utilizar PIM para melhorar a segurança da nuvem."
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
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Gestão de identidades do Azure AD privilegiados

Com a gestão de identidades privilegiados Azure Active Directory (AD), pode gerir, controlo e monitorizar o acesso dentro da sua organização. Isto inclui acesso aos recursos no Azure AD e outros serviços onlinehttps da Microsoft como o Office 365 ou o Microsoft Intune.  

> [AZURE.NOTE] Gestão de identidades privilegiados só está disponível com a edição de Premium P2 do Azure Active Directory. Para mais informações, consulte o artigo [edições do Azure Active Directory](active-directory-editions.md).

As organizações pretende minimizar o número de pessoas que têm acesso a proteger informações ou recursos, uma vez que o que reduz o risco de um utilizador malicioso introdução que o access. No entanto, os utilizadores ainda precisa de efectuar operações privilegiadas nas aplicações do Azure, Office 365 ou SaaS. As organizações dar aos utilizadores acesso privilegiados no Azure AD sem monitorização o que os utilizadores estão a fazer com os respetivos privilégios de administrador. Gestão de identidades do Azure AD privilegiados ajuda para resolver este risco.  

Gestão de identidades do Azure AD privilegiados ajudá-lo:  

- Ver quais os utilizadores que estão os administradores do Azure AD
- Ativar a pedido, "apenas em hora" acesso administrativo ao Microsoft Online Services como o Office 365 e o Intune
- Obter relatórios sobre o histórico de acesso de administrador e alterações nas atribuições de administrador
- Receber alertas sobre o acesso a uma função privilegiado

Azure AD privilegiados da gestão de identidades pode gerir a incorporada funções organizacionais Azure AD, incluindo:  

- Administrador global
- Administrador de faturação
- Administrador de serviços  
- Administrador do utilizador
- Palavra-passe de administrador

## <a name="just-in-time-administrator-access"></a>Apenas nos acesso de administrador de tempo

Historicamente, poderá atribuir um utilizador a uma função de administrador através do portal clássico Azure ou o Windows PowerShell. Como resultado, esse utilizador torna-se um **administrador permanente**, sempre ativo na função atribuída. Gestão de identidades do Azure AD privilegiados introduz o conceito de um **administrador elegível**. Administradores elegíveis devem ser utilizadores que precisam de aceder privilegiado agora e, em seguida, mas não diariamente. A função está inactiva até que o utilizador precisa de aceder, em seguida, estas concluir o processo de ativação e tornam-se um administrador ativo durante um período de tempo predeterminado.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Ativar a gestão de identidades privilegiados para o seu diretório

Pode começar a utilizar a gestão de identidades do Azure AD privilegiados no [portal do Azure](https://portal.azure.com/).

>[AZURE.NOTE] Tem de ser um administrador global com uma conta institucional (por exemplo, @yourdomain.com), não uma conta Microsoft (por exemplo, @outlook.com), para ativar a gestão de identidades do Azure AD privilegiados para um diretório.

1. Inicie sessão no [Azure portal](https://portal.azure.com/) como um administrador global do diretório da sua.
2. Se a sua organização tiver mais do que um diretório, selecione o seu nome de utilizador no canto superior direito do Azure portal. Selecione a pasta onde irá utilizar a gestão de identidades do Azure AD privilegiados.
3. Selecione **mais serviços** e utilizar a caixa de texto de filtro para procurar **a gestão de identidades do Azure AD privilegiados**.
4. Selecione **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de gestão de identidades privilegiados abre.

Se for a primeira pessoa para utilizar a gestão de identidades do Azure AD privilegiados no seu diretório, em seguida, o [Assistente de segurança](active-directory-privileged-identity-management-security-wizard.md) guia-o a experiência de atribuição inicial. Após esta automaticamente torna-se os primeiro do **administrador de segurança** e o **administrador da função privilegiados** do diretório.

Apenas um administrador de função privilegiados pode gerir o acesso de outros administradores. Pode [conceder a capacidade de gestão de PIM a outros utilizadores](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Dashboard de gestão de identidades privilegiado

Gestor de identidades do Azure AD privilegiados fornece um dashboard que dá-lhe informações importantes, tais como:

- Alertas que apontam saída oportunidades para melhorar a segurança
- O número de utilizadores que estão atribuídos a cada função privilegiada  
- O número de administradores elegíveis e permanentes
- Revisões acesso contínuo

![Dashboard PIM - captura de ecrã][2]

## <a name="privileged-role-management"></a>Gestão de funções privilegiados

Com a gestão de identidades do Azure AD privilegiados, pode gerir os administradores da adicionando ou removendo permanentes ou elegíveis administradores para cada função.

![Administradores de adicionar/remover PIM - captura de ecrã][3]

## <a name="configure-the-role-activation-settings"></a>Configurar as definições de ativação de função

Utilizar as [definições da função](active-directory-privileged-identity-management-how-to-change-default-settings.md) pode configurar as propriedades de ativação função elegível, incluindo:

- A duração do período de ativação de função
- A notificação de ativação de função
- A informação que necessita de um utilizador para fornecer durante o processo de ativação de função  

![Captura de ecrã do PIM definições - activação de administrador-][4]

Tenha em atenção que na imagem, os botões para **Autenticação Multifator** são desativados. Para determinados, altamente privilégios funções, necessitamos MFA para protecção acrescida.

## <a name="role-activation"></a>Ativação de função  

Para [Ativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md), um administrador elegível pedidos de um limite de tempo "ativação" para a função. A ativação pode ser pedida utilizando a opção **Ativar meu função** na gestão de identidades do Azure AD privilegiados.

Quem pretende ativar uma função de administrador tem de iniciar a gestão de identidades do Azure AD privilegiados no portal do Azure.

A ativação de função está personalizável. Nas definições de PIM, pode determinar o comprimento de ativação e o que o administrador tem de fornecer para ativar a função de informações.

![Ativação de função de pedido de administrador PIM - captura de ecrã][5]

## <a name="review-role-activity"></a>Rever a atividade de função

Existem duas formas de controlar como os funcionários e administradores estão a utilizar funções privilegiadas. A primeira opção está a utilizar [Histórico de auditoria](active-directory-privileged-identity-management-how-to-use-audit-log.md). O histórico de auditoria os registos registar alterações na atribuições de funções privilegiados e histórico de ativação de função.

![Histórico de ativação PIM - captura de ecrã][6]

A segunda opção é configurar normal [revê do access](active-directory-privileged-identity-management-how-to-start-security-review.md). Estes revisões access podem ser realizados por e atribuídas revisor (por exemplo, um Gestor de equipa) ou os empregados podem rever os próprios. Este é a melhor forma para monitorizar a quem ainda requer o access e quem já não faz.


## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

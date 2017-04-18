<properties
    pageTitle="Configurar o Azure Active Directory para a gestão de acesso de aplicação de serviço automática | Microsoft Azure"
    description="Gestão de grupos de gestão personalizada permite aos utilizadores criar e gerir grupos de segurança ou grupos do Office 365 no Azure Active Directory e oferece aos utilizadores a possibilidade de grupo de segurança do pedido ou membros de grupos do Office 365"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Configurar o Azure Active Directory para gestão de grupos de gestão personalizada

Gestão de grupos de gestão personalizada permite aos utilizadores criar e gerir grupos de segurança ou grupos do Office 365 no Azure Active Directory (Azure AD). Os utilizadores também podem pedir grupo de segurança ou membros de grupos do Office 365 e, em seguida, o proprietário do grupo pode aprovar ou negar associação. Desta forma, no dia a dia controlo dos membros do grupo pode ser delegado para as pessoas que compreender o contexto de negócio para essa associação. Funcionalidades de gestão personalizada grupo estão disponíveis apenas para grupos de segurança e grupos do Office 365, mas não para grupos de segurança com capacidade de correio ou listas de distribuição.

Gestão de grupos de gestão personalizada atualmente compreende dois cenários essenciais: delegado a gestão de grupos e gestão de grupos de gestão personalizada.

- **Gestão de grupo delegada** 
   um exemplo é um administrador que está a gerir o acesso a uma aplicação de SaaS que está a utilizar a empresa. Gerir esses direitos de acesso está a ficar complicado, para que este administrador pede o proprietário da empresa para criar um novo grupo. O administrador atribui acesso para a aplicação para o novo grupo e adiciona para o grupo todas as pessoas ainda aceder a para a aplicação. O proprietário da empresa, em seguida, pode adicionar mais utilizadores e esses utilizadores automaticamente configurações para a aplicação. O proprietário da empresa não ter de esperar para que o administrador gerir o acesso de utilizadores. Se o administrador atribui as mesmas permissões a um gestor num grupo de negócio diferente, em seguida, essa pessoa também pode gerir o acesso para os seus próprios utilizadores. Nem o proprietário da empresa nem o Gestor, pode ver ou gerir uns dos outros utilizadores. O administrador pode continuar a ver todos os utilizadores que têm acesso à aplicação e direitos de acesso de bloco caso seja necessário.

- **Gestão de grupos de gestão personalizada** 
   um exemplo deste cenário é dois utilizadores que ambos os têm a sites do SharePoint Online que configurados separadamente. Se pretendem conceder acesso de equipas uns dos outros para os seus sites. Para realizar esta tarefa, que possam criar um grupo no Azure AD e no SharePoint Online seleciona cada um desse grupo para fornecer acesso aos seus sites. Quando alguém acesso, eles pedem-lo a partir do painel de acesso e depois da aprovação são acesso a ambos os sites do SharePoint Online automaticamente. Mais tarde, um deles decide que todas as pessoas que acederem ao site de também devem obter acesso a uma determinada aplicação SaaS. O administrador da aplicação SaaS pode adicionar direitos de acesso para a aplicação para o site do SharePoint Online. De, em seguida, em quaisquer pedidos de que obtenham aprovados dá acesso para os dois sites SharePoint Online e também para esta aplicação SaaS.

## <a name="making-a-group-available-for-end-user-self-service"></a>Disponibilização de um grupo para o utilizador final personalizada

1. No [portal clássica Azure](https://manage.windowsazure.com), abra o seu diretório do Azure AD.

2. No separador **Configurar** , defina a **Gestão de grupos de delegado** para ativado.

3. Defina **os utilizadores podem criar grupos de segurança** ou **os utilizadores podem criar grupos do Office** para ativado.

Quando **que os utilizadores podem criar grupos de segurança** é activado, todos os utilizadores no seu diretório são permitidos para criar novos grupos de segurança e adicionar membros a estes grupos. Estes novos grupos também seriam aparecer no painel de acesso para todos os outros utilizadores. Se a definição de política no grupo o permitir, outros utilizadores podem criar pedidos para aderir a estes grupos. Se **os utilizadores podem criar grupos de segurança** estiver desativado, os utilizadores não é possível criar grupos e não é possível alterar grupos existentes para os quais aos quais um proprietário. No entanto, continuam a poder gerir os membros dos grupos e aprovar pedidos de outros utilizadores para participar seus grupos.

Também pode utilizar **os utilizadores a quem podem utilizar o Self-Service para grupos de segurança** para alcançar um controlo de acesso mais detalhado sobre a gestão de grupos de gestão personalizada para os seus utilizadores. Quando **que os utilizadores podem criar grupos** está ativado, todos os utilizadores no seu diretório são permitidos para criar novos grupos e adicionar membros a estes grupos. Ao definir também **os utilizadores a quem podem utilizar o Self-Service para grupos de segurança** a algumas, é restringir gestão a um grupo limitado de utilizadores do grupo. Quando este parâmetro estiver definido para algumas, tem de adicionar utilizadores ao grupo SSGMSecurityGroupsUsers antes de poderem podem criar novos grupos e adicionar membros às mesmas. Ao definir **os utilizadores a quem podem utilizar o Self-Service para grupos de segurança** a todos, ativar todos os utilizadores no seu diretório para criar novos grupos.

Também pode utilizar a caixa de **grupo que pode utilizar o Self-Service para grupos de segurança** para especificar um nome personalizado para um grupo cujos membros podem utilizar o Self-Service.

## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlets para configurar as definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

* [O que é o Azure Active Directory?](active-directory-whatis.md)

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

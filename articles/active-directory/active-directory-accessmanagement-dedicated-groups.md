<properties
    pageTitle="Dedicado grupos no Azure Active Directory | Microsoft Azure"
    description="Descrição geral de como dedicada de grupos de trabalho no Azure Active Directory e como são criadas."
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
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Grupos dedicados no Azure Active Directory

No Azure Active Directory (Azure AD), a funcionalidade grupos dedicada automaticamente cria e preenche associação para grupos do Azure AD predefinida. Membros dos grupos dedicados de não podem ser adicionados ou removidos utilizando o portal clássico Azure, os cmdlets do Windows PowerShell, ou através de programação.

>[AZURE.NOTE] Grupos dedicados exigem que uma licença do Azure AD Premium é atribuída a
>- o administrador que gere a regra num grupo
>- todos os utilizadores que são selecionados pela regra para ser um membro do grupo

**Para ativar dedicados de grupos**

1. No [portal clássica Azure](https://manage.windowsazure.com), selecione **Do Active Directory**e, em seguida, abra o diretório da sua organização.

2. Selecione o separador de **grupos** e, em seguida, abra o grupo que pretende editar.

3. Selecione o separador de **Configurar** e, em seguida, defina **Ativar grupos dedicado** como **Sim**.

Assim que a mudança de ativar dedicada grupos é definida como **Sim**, pode ativar ainda mais o diretório criar automaticamente o grupo de dedicada de todos os utilizadores definindo a **Ativar "Todos os utilizadores" grupo** mudar para **Sim**. Em seguida, também pode editar o nome deste grupo dedicado ao escrevê-la na **nome a apresentar para "Todos os utilizadores" grupo** campo.

O grupo de todos os utilizadores pode ser utilizado para atribuir as mesmas permissões a todos os utilizadores no seu diretório. Por exemplo, pode conceder todos os utilizadores no seu aceda diretório para uma aplicação de SaaS ao atribuir acesso para o grupo dedicado de todos os utilizadores para esta aplicação.

O grupo de todos os utilizadores dedicado inclui todos os utilizadores no diretório, incluindo convidados e os utilizadores externos. Se precisar de um grupo exclui que os utilizadores externos, em seguida, consegui-lo ao criar um grupo com uma regra dinâmica com base no atributo como as seguintes:

                (user.userPrincipalName -notContains "#EXT#@")

Para um grupo que exclua todos os convidados, utilize uma regra, como as seguintes:

                (user.userType -ne "Guest")

Para saber mais sobre como criar regras *Avançadas* (regras que podem conter várias comparações) para a associação a grupos dinâmicos, consulte o artigo [atributos de utilizar para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).


Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

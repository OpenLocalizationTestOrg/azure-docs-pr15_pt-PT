<properties
    pageTitle="Gerir grupos no Azure Active Directory | Microsoft Azure"
    description="Como criar e gerir grupos para gerir utilizadores Azure utilizando o Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Gerir grupos no Azure Active Directory

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-groups-create-azure-portal.md)
- [Azure portal clássico](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


Uma das funcionalidades de gestão de utilizadores do Azure Active Directory (Azure AD) é a capacidade de criar grupos de utilizadores. Utilizar um grupo para efetuar tarefas de gestão de tais como atribuir permissões de licenças ou a um número de utilizadores em simultâneo. Também pode utilizar grupos para atribuir permissões de acesso a

- Recursos, tais como os objetos no diretório
- Recursos externos ao diretório como SaaS aplicações, serviços Azure, os sites do SharePoint ou recursos no local

Além disso, o proprietário de um recurso também pode atribuir acesso a um recurso a um grupo do Azure AD pertencente a outra pessoa. Desta atribuição concede os membros desse acesso do grupo ao recurso. Em seguida, o proprietário do grupo gere associação ao grupo. Eficazmente, o proprietário do recurso delegados ao proprietário do grupo a permissão para atribuir aos utilizadores para o recurso.

## <a name="how-do-i-create-a-group"></a>Como posso criar um grupo?

Dependendo dos serviços para o qual a sua organização tiver subscrito, pode criar um grupo a utilizar um dos seguintes procedimentos:
- portal clássico do Azure
- portal de conta do Office 365
- portal de conta do Windows Intune

Irá descrevemos tarefas conforme é executada no portal do Azure clássico. Para mais informações sobre como utilizar não Azure portais para gerir o seu diretório do Azure AD, consulte [administrar no diretório da sua Azure AD](active-directory-administer.md).

1. No [portal clássica Azure](https://manage.windowsazure.com), selecione **Do Active Directory**e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador de **grupos** .

3. Selecione **Adicionar grupo**.

4. Na janela **Adicionar grupo** , especifique o nome e descrição de um grupo.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Como adicionar ou remover utilizadores individuais num grupo de segurança?

**Para adicionar um utilizador individual a um grupo**

1. No [portal clássica Azure](https://manage.windowsazure.com), selecione **Do Active Directory**e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador de **grupos** .

3. Abra o grupo ao qual pretende adicionar membros. Abra o separador de **Membros** do grupo selecionado se-ainda não apresentar.

4. Selecione **Adicionar membros**.

5. Na página **Adicionar membros** , selecione o nome do utilizador ou um grupo ao qual pretende adicionar como um membro deste grupo. Certifique-se de que este nome é adicionado ao painel de **selecionado** .


**Para remover um utilizador individual de um grupo**

1. No [portal clássica Azure](https://manage.windowsazure.com), selecione **Do Active Directory**e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador de **grupos** .

3. Abra o grupo a partir do qual pretende remover membros.

4. Selecione o separador de **Membros** , selecione o nome do membro que pretende remover deste grupo e, em seguida, clique em **Remover**.

6. Confirme na linha de comandos que pretende remover este membro do grupo.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Como posso gerir a associação de um grupo dinamicamente?

No Azure AD, pode facilmente muito de configurar uma regra de simples para determinar quais os utilizadores estão a ser membros do grupo. Uma regra simples é um valor que faz com apenas uma única comparação. Por exemplo, se um grupo é atribuído a uma aplicação de SaaS, pode configurar uma regra para adicionar os utilizadores que têm um cargo de "Representante de vendas." Esta regra, em seguida, concede acesso a esta aplicação SaaS a todos os utilizadores com esse cargo no seu diretório.

Quando os atributos de uma alteração de utilizador, o sistema avalia todas as regras de grupo dinâmicos num diretório para ver se a alteração de atributo do utilizador seria acionar qualquer grupo adiciona ou remove. Se um utilizador satisfizer uma regra num grupo, que sejam adicionados como um membro a esse grupo. Se já não satisfizerem a regra de um grupo são membro, estes são removidos como um membros a partir desse grupo.

> [AZURE.NOTE] Pode configurar uma regra para dinâmico membros de grupos de segurança ou grupos do Office 365. Os membros do grupo interno aninhado atualmente não são suportados para atribuição com base no grupo às aplicações.
>
> Associações dinâmicas para grupos exigem uma licença do Azure AD Premium a serem atribuídas à
>
> - O administrador que gere a regra num grupo
> - Todos os membros do grupo

**Para ativar a associação dinâmica para um grupo**

1. No [portal clássica Azure](https://manage.windowsazure.com), selecione **Do Active Directory**e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador de **grupos** e abra o grupo que pretende editar.

3. Selecione o separador de **Configurar** e, em seguida, defina **Ativar associações dinâmico** como **Sim**.

4. Defina uma regra única simple para o grupo controlar como dinâmica associação para este funções de grupo. Certifique-se a **Adicionar utilizadores onde** opção está selecionada e, em seguida, selecione uma propriedade de utilizador a partir da lista (por exemplo, departamento, cargo, etc.)

5. Em seguida, selecione uma condição (não é igual a, é igual a, não é iniciado com, começa com, não contém, contém, não correspondência, CORRESP).

6. Especificar um valor de comparação para a propriedade do utilizador selecionado.

Para saber mais sobre como criar regras *Avançadas* (regras que podem conter várias comparações) para a associação a grupos dinâmicos, consulte o artigo [atributos de utilizar para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlets para configurar as definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

* [O que é o Azure Active Directory?](active-directory-whatis.md)

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

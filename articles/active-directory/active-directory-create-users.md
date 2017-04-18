<properties
    pageTitle="Adicionar novos utilizadores para o Azure Active Directory | Microsoft Azure"
    description="Explica como adicionar novos utilizadores ou alterar as informações de utilizador no Azure Active Directory."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Adicionar novos utilizadores ou os utilizadores com contas do Microsoft Azure Active Directory

Adicione utilizadores para preencher o seu diretório. Este artigo explica como adicionar novos utilizadores na sua organização e como adicionar utilizadores que tenham contas do Microsoft. Para mais informações sobre como adicionar utilizadores a partir de outros directórios no Azure Active Directory ou adicionar utilizadores de empresas do parceiro, consulte o artigo [Adicionar utilizadores a partir de outras empresas de parceiro no Azure Active Directory ou directórios](active-directory-create-users-external.md). Utilizadores adicionados não tem permissões de administrador por predefinição, mas pode atribuir funções aos mesmos em qualquer altura.

## <a name="add-a-user"></a>Adicionar um utilizador

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com) com uma conta que é um administrador global para o diretório.
2. Selecione **Do Active Directory**e, em seguida, selecione o nome do diretório da sua organização.
3. Selecione o separador de **utilizadores** e, em seguida, na barra de comandos, selecione **Adicionar utilizador**.
4. Na página **diga-nos acerca deste utilizador** , em **tipo de utilizador**, selecione:

    - **Novo utilizador na sua organização** – adiciona uma nova conta de utilizador no seu diretório.
    - **Utilizador com uma conta existente do Microsoft** – adiciona uma conta existente do consumidor do Microsoft para o seu diretório (por exemplo, uma conta do Outlook)

5. Dependendo do **tipo de utilizador**, introduza um nome de utilizador (para o novo utilizador) ou um endereço de e-mail (para um utilizador com uma conta Microsoft).
6. Na página de **perfil de** utilizador, forneça um nome próprio e apelido, um nome amigável e uma função de utilizador a partir da lista de **funções** . Para mais informações sobre as funções de utilizador e administrador, consulte [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se pretende **Ativar a autenticação Multifator** para o utilizador.
7. Na página de **obter a palavra-passe temporária** , selecione **Criar**.

> [AZURE.IMPORTANT] Se a sua organização utiliza mais do que um domínio, que devem saber sobre os seguintes problemas ao adicionar uma conta de utilizador:
>
> - Para adicionar contas de utilizador com o mesmo nome principal de utilizador (UPN) em domínios, **primeiro** adicionar, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido** geoffgrisso@contoso.com.
> - **Não** adicionar geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Por esta ordem é importante e pode ser complicado para anular.

## <a name="change-user-information"></a>Alterar as informações de utilizador

Pode alterar o atributo qualquer utilizador exceto o ID do objeto.

1. Abra o seu diretório.
2. Selecione o separador de **utilizadores** e, em seguida, selecione o nome a apresentar do utilizador que pretende alterar.
3. Faça as alterações e, em seguida, clique em **Guardar**.

Se o utilizador que está a alterar é sincronizado com o seu serviço de Active Directory no local, não pode alterar as informações do utilizador através deste procedimento. Para alterar o utilizador, utilize as ferramentas de gestão de Active Directory no local.

## <a name="guest-user-management-and-limitations"></a>Gestão de utilizadores convidado e limitações

As contas de convidado que os utilizadores a partir de outros directórios que foram convidados para o seu diretório para aceder a documentos do SharePoint, aplicações ou outros recursos Azure. Uma conta de convidado no seu diretório tem o atributo UserType subjacente definida como "Convidado". Os utilizadores normais (especificamente, membros do seu diretório) pode ter o atributo UserType "Membro".

Os convidados têm um conjunto de direitos limitado no diretório. Estes direitos limitam a capacidade para convidados descobrir informações sobre os outros utilizadores no diretório. No entanto, os utilizadores convidados ainda podem interagir com os utilizadores e grupos associados a recursos que está a trabalhar. Utilizadores convidados podem:

- Ver outros utilizadores e grupos associados a uma subscrição do Azure ao qual estão atribuídos
- Ver os membros de grupos aos quais pertencem
- Procurar a outros utilizadores no diretório, se souber o endereço de e-mail completo do utilizador
- Ver apenas um conjunto de atributos dos utilizadores que procurar – limitados para apresentar nome, endereço de e-mail, nome principal de utilizador (UPN) e miniatura da fotografia limitado
- Obter uma lista de domínios verificados no diretório
- Consentimento para aplicações, conceder-lhes o mesmo acesso que os membros têm no seu diretório

## <a name="set-guest-user-access-policies"></a>Configurar políticas de acesso de utilizador convidado

No separador **Configurar** de um diretório inclui opções para controlar o acesso de utilizadores convidados. Estas opções podem ser alteradas apenas no portal de clássico Azure por um administrador global do diretório. Atualmente, não existe nenhum método PowerShell ou API.

Para abrir o separador **Configurar** no portal do Azure clássico, selecione **Do Active Directory**e, em seguida, selecione o nome do diretório.

![Configure o separador no Azure Active Directory][1]

Em seguida, pode editar as opções para controlar o acesso de utilizadores convidados.

![Opções de controlo de acesso para utilizadores convidados][2]


## <a name="whats-next"></a>O que se segue

- [Adicionar utilizadores a partir de outras empresas de parceiro no Azure Active Directory ou directórios](active-directory-create-users-external.md)
- [Administrar Azure AD](active-directory-administer.md)
- [Gerir palavras-passe no Azure AD](active-directory-manage-passwords.md)
- [Gerir grupos no Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

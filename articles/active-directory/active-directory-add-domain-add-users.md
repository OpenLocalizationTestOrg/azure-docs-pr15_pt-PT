<properties
    pageTitle="Atribuir utilizadores a um domínio personalizado no Azure Active Directory | Microsoft Azure"
    description="Como preencher um domínio personalizado no Azure Active Directory com contas de utilizador."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>Atribuir utilizadores a um domínio personalizado

Depois de ter adicionado o seu domínio personalizado para o Azure Active Directory, tem de adicionar contas de utilizador para este domínio para que possa começar a autenticá-los.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Utilizadores sincronizados de um diretório na rede da empresa

Se já configurou uma ligação entre o seu no local do Active Directory e o Azure Active Directory, a sincronização pode preencher as contas. Para mais informações sobre como sincronizar Azure Active Directory com o Active Directory no local, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Os utilizadores adicionados e geridos na nuvem

Para alterar o domínio para uma conta existente do utilizador:

1.  Abra o portal do Azure clássica ao utilizar uma conta que seja um administrador global ou um administrador do utilizador.

2.  Abra o seu diretório.

3.  Selecione o separador de **utilizadores** .

4.  Selecione o utilizador a partir da lista.

5.  Alterar o domínio para o utilizador e, em seguida, selecione **Guardar**.

Isto também pode ser feito utilizando o [PowerShell do Microsoft](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Selecione um domínio personalizado ao criar um novo utilizador

1.  Abra o portal do Azure clássica ao utilizar uma conta que seja um administrador global ou um administrador do utilizador.

2.  Abra o seu diretório.

3.  Selecione o separador de **utilizadores** .

4.  Na barra de comandos, selecione **Adicionar**.

5.  Quando adiciona o nome de utilizador, selecione o domínio personalizado a partir da lista de domínios.

6.  Selecione **Guardar**.

## <a name="next-steps"></a>Próximos passos

-   [Utilizar nomes de domínio personalizado para simplificar a experiência de início de sessão para os seus utilizadores](active-directory-add-domain.md)

-   [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md)

-   [Saiba mais sobre os conceitos de gestão de domínio no Azure AD](active-directory-add-domain-concepts.md)

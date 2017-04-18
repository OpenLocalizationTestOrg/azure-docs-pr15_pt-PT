<properties
    pageTitle="Azure AD e aplicações: atribuir grupos à aplicação | Microsoft Azure"
    description="Como implementar a atribuição de grupo para aplicações do Azure."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="inhenk"/>

# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Azure AD e aplicações: atribuir grupos à aplicação
Antes de poder atribuir utilizadores e grupos para uma aplicação, devem exigir atribuição de utilizador. Para saber como necessitam de atribuição de utilizador, consulte o artigo [Que exige o atribuição de utilizador](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

Este artigo assume que já tiver criado grupos do active Directory que estiver a utilizar para esta aplicação.

## <a name="assigning-groups-to-an-application"></a>Atribuir grupos à aplicação
1. Inicie sessão no portal do Azure com uma conta de administrador.
2. Clique no item **Todos os itens** no menu de lista principal.
3. Escolha o directório que estiver a utilizar para a aplicação.
4. Clique no separador **aplicações** .
5. Selecione a aplicação a partir da lista de aplicações associadas este diretório.
6. Clique no separador de **Utilizadores e grupos** .
7. Filtre a lista de grupos no active directory utilizando na lista pendente de **grupos** .
8. Selecione o grupo.
9. Clique em **atribuir**.
10. Clique em **Sim** quando lhe for pedido.

## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

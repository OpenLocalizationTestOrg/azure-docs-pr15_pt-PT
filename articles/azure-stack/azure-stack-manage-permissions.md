<properties
    pageTitle="Gerir permissões para os recursos por utilizador Azure empilhados (administrador de serviços e inquilino) | Microsoft Azure"
    description="Como administrador do serviço ou inquilino, saiba como gerir permissões para os recursos por utilizador."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Gerir permissões de utilizador

Um utilizador no Azure pilha pode ser um leitor, o proprietário ou contribuinte para cada instância de uma subscrição, grupo de recursos ou serviço. Por exemplo, o utilizador A poderá ter permissões de leitor para 1 de subscrição, mas tem permissões de proprietário para Máquina Virtual 7.

-   Leitor: O utilizador pode ver tudo, mas não pode efetuar as alterações.

-   Contribuinte: Utilizador pode gerir tudo exceto acesso aos recursos.

-   Proprietário: Utilizador pode gerir tudo, incluindo o acesso aos recursos.


## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um utilizador

1.  Inicie sessão com uma conta que possua permissões de proprietário para o recurso que pretende gerir.

2.  No pá para o recurso, clique no ícone do **Access** ![](media/azure-stack-manage-permissions/image1.png).

3.  No pá **utilizadores** , clique em **funções**.

4.  No pá **funções** , clique em **Adicionar** para adicionar permissões para o utilizador.

## <a name="next-steps"></a>Próximos passos

[Adicionar um inquilino do Azure pilha](azure-stack-add-new-user-aad.md)

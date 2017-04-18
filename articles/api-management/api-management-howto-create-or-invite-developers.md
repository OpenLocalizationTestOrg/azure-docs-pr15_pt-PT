<properties 
    pageTitle="Como gerir contas de utilizador no Azure API gestão | Microsoft Azure" 
    description="Saiba como criar ou convidar os utilizadores na gestão de API do Azure" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Como gerir contas de utilizador no Azure API gestão

Em gestão de API, os programadores são os utilizadores das APIs do que está a utilizar a gestão de API expor. Este guia mostra a como criar e convidar os programadores para utilizar a API e os produtos que está a disponibilizá-lhes com a instância de gestão de API. Para obter informações sobre como gerir contas de utilizador através de programação, consulte a documentação de [entidade utilizador](https://msdn.microsoft.com/library/azure/dn776330.aspx) na referência [API gestão restantes](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Criar um novo Programador

Para criar um novo programador, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API. Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

![Portal do Publisher][api-management-management-console]

Clique em **utilizadores** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Adicionar utilizador**.

![Criar para programadores][api-management-create-developer]

Introduza a **mensagem de correio electrónico**, a **palavra-passe**e o **nome** para o novo programador e clique em **Guardar**.

![Criar para programadores][api-management-add-new-user]

Por predefinição, o programador recentemente criado contas **ativos**e associados ao grupo **os programadores** .

![Novo para programadores][api-management-new-developer]

Contas de programador que estão num estado **activo** podem ser utilizadas para aceder a todas as APIs para os quais têm subscrições. Para associar o programador recentemente criado grupos adicionais, consulte o artigo [como associar grupos com os programadores][].

## <a name="invite-developer"> </a>Convidar um programador

Para convidar um programador, clique em **utilizadores** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Convidar utilizador**.

![Convidar para programadores][api-management-invite-developer]

Introduza o nome e endereço de e-mail do programador e clique em **Convidar**.

![Convidar para programadores][api-management-invite-developer-window]

É apresentada uma mensagem de confirmação, mas o programador recentemente convidado não aparecer na lista até assim que aceitarem o convite. 

![Convidar confirmação][api-management-invite-developer-confirmation]

Quando um programador está convidado, uma mensagem de e-mail é enviada para o programador. Esta mensagem de correio electrónico é gerada utilizando um modelo e é personalizável. Para mais informações, consulte o artigo [configurar modelos de e-mail][].

Assim que o convite for aceite, a conta fica ativa.

## <a name="block-developer"></a> Desativar ou reativar uma conta de programador

Por predefinição, as contas de programador recentemente criado ou convidados são **ativo**. Para desativar uma conta de programador, clique em **Bloco**. Para reativar uma conta de programador bloqueados, clique em **Ativar**. Uma conta de programador bloqueados não pode aceder ao portal de programador ou qualquer APIs de chamadas. Para eliminar uma conta de utilizador, clique em **Eliminar**.

![Programador do bloco][api-management-new-developer]

## <a name="reset-a-user-password"></a>Repor uma palavra-passe de utilizador

Para repor a palavra-passe para uma conta de utilizador, clique no nome da conta.

![Repor palavra-passe][api-management-view-developer]

Clique em **Repor palavra-passe** para enviar uma ligação ao utilizador para repor a sua palavra-passe.

![Repor palavra-passe][api-management-reset-password]

Para trabalhar através de programação com contas de utilizador, consulte a documentação de [entidade utilizador](https://msdn.microsoft.com/library/azure/dn776330.aspx) na referência [API gestão restantes](https://msdn.microsoft.com/library/azure/dn776326.aspx) . Para repor uma palavra-passe da conta de utilizador para um valor específico, pode utilizar a operação de [atualizar um utilizador](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) e especificar a palavra-passe pretendida.

## <a name="pending-verification"></a>Verificação pendente

![Verificação pendente][api-management-pending-verification]

## <a name="next-steps"> </a>Passos seguintes

Assim que um programador conta estiver criada, pode associá-la com as funções e subscrever APIs e produtos. Para mais informações, consulte o artigo [como criar e utilizar grupos][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Como criar e utilizar grupos]: api-management-howto-create-groups.md
[Como associar grupos os programadores]: api-management-howto-create-groups.md#associate-group-developer

[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Configurar modelos de correio eletrónico]: api-management-howto-configure-notifications.md#email-templates
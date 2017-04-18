<properties
    pageTitle="Gerir o acesso aos recursos com os grupos do Azure Active Directory | Microsoft Azure"
    description="Como utilizar grupos no Azure Active Directory para gerir o acesso de utilizador no local e na nuvem aplicações e recursos."
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


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Gerir o acesso aos recursos com os grupos do Azure Active Directory

Azure Active Directory (Azure AD) é uma solução de gestão identidade e acesso completa que fornece um conjunto robusto de funcionalidades para gerir o acesso a no local e na nuvem aplicações e recursos, incluindo a serviços online da Microsoft, como o Office 365 e de aplicações SaaS não Microsoft do mundo. Este artigo fornece uma descrição geral, mas se quiser começar a utilizar os grupos do Azure AD neste momento, siga as instruções em [Gerir grupos de segurança no Azure AD](active-directory-accessmanagement-manage-groups.md). Se quiser saber como pode utilizar o PowerShell para gerir grupos no Azure Active directory pode ler mais em [cmdlets de pré-visualização do Azure Active Directory para gestão de grupos](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Para utilizar o Azure Active Directory, tem uma conta Azure. Se não tiver uma conta, pode [Inscrever-se para uma conta Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).


Azure AD, dentro de uma das principais funcionalidades é a capacidade de gerir o acesso aos recursos. Estes recursos podem fazer parte do directório, tal como as maiúsculas/minúsculas de permissões para gerir objetos através de funções no diretório ou recursos externos ao diretório, por exemplo, aplicações de SaaS, serviços Azure e sites do SharePoint ou no recursos local. Existem quatro formas que um utilizador pode ser atribuído direitos de acesso a um recurso:


1. Atribuição direta

    Podem ser atribuída aos utilizadores diretamente para um recurso pelo proprietário do recurso.

2. Associação a grupos

    Um grupo pode ser atribuído a um recurso ao proprietário do recurso e ao fazê-lo, conceder os membros desse acesso do grupo ao recurso. Membros do grupo, em seguida, podem ser gerido pelo proprietário do grupo. Eficazmente, o proprietário do recurso delegados a permissão para atribuir aos utilizadores para o recurso para o proprietário do grupo.

3. Baseada em regras

    O proprietário do recurso pode utilizar uma regra para express os utilizadores que devem ser atribuídos acesso a um recurso. O resultado da regra depende os atributos utilizados na regra e os respetivos valores para utilizadores específicos e ao fazê-lo, o proprietário do recurso delegados eficazmente à direita para gerir o acesso ao seu recurso à origem de autoritativas os atributos que são utilizadas na regra. O proprietário do recurso ainda gere a regra propriamente dito e determina qual atributos e os valores fornecem acesso aos seus recursos.

4. Autoridade de externa

    O acesso a um recurso deriva a partir de uma fonte externa; Por exemplo, um grupo de que é sincronizado a partir de uma origem de autoritativas como num diretório no local ou com uma aplicação de SaaS como dia de trabalho. O proprietário do recurso atribui o grupo para fornecer acesso ao recurso e origem externa gere os membros do grupo.

  ![Descrição geral de diagrama de gestão do access](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Veja um vídeo que explica a gestão de acesso

Pode ver um breve vídeo que explica mais sobre isto:

**Azure AD: Introdução a associação dinâmica para grupos**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Como aceder a gestão de trabalho do Azure Active Directory?
No centro do Azure AD solução de gestão de acesso é o grupo de segurança. Utilizar um grupo de segurança para gerir o acesso aos recursos é um paradigma famoso, que lhe permite para saber uma maneira fácil compreensão e flexível fornecer acesso a um recurso para o grupo de utilizadores pretendido. O proprietário do recurso (ou o administrador do directório), pode atribuir um grupo para fornecer um determinado acesso para a direita para os recursos pertencem. Serão fornecidos os membros do grupo o acesso e o proprietário do recurso pode delegar o direito para gerir a lista de membros de um grupo a outra pessoa, tal como um Gestor de departamento ou um administrador de suporte técnico.

![Diagrama de gestão de acesso do Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

O proprietário de um grupo também pode disponibilizar desse grupo para pedidos de gestão personalizada. Ao fazê-lo, um utilizador final pode procurar e localizar o grupo e fazer um pedido para participar, de forma eficaz à procura de permissão para aceder aos recursos que são geridos através do grupo. O proprietário do grupo pode configurar o grupo para que sejam aprovados automaticamente de pedidos de associação ou exigem a aprovação pelo proprietário do grupo. Quando um utilizador faz um pedido para aderir a um grupo, o pedido de associação é reencaminhado para os proprietários do grupo. Se um dos proprietários aprovar o pedido, o utilizador pedir é notificado e o utilizador é aderido ao grupo. Se um dos proprietários recusa o pedido, o utilizador pedir é notificado mas não aderido ao grupo.


## <a name="getting-started-with-access-management"></a>Introdução ao access management
Está pronto para começar a utilizar? Que deverá tentar saída algumas das tarefas básicas que pode fazer com os grupos do Azure AD. Utilize estas capacidades para fornecer acesso especializado para diferentes grupos de pessoas para recursos diferentes na sua organização. Uma lista de primeiros passos básicos estão indicadas abaixo.

* [Criar uma regra de simple para configurar associações dinâmicas para um grupo](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [Utilizar um grupo para gerir o acesso às aplicações SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Disponibilização de um grupo para o utilizador final personalizada](active-directory-accessmanagement-self-service-group-management.md)

* [Sincronizar um grupo no local para Azure utilizando a ligação do Azure AD](active-directory-aadconnect.md)

* [Gerir proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Passos seguintes para a gestão de acesso
Agora que tem compreendeu as noções básicas de gestão do access, aqui estão algumas funcionalidades avançadas adicionais disponíveis no Azure Active Directory para gerir o acesso às suas aplicações e recursos.

* [Utilizando os atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Gerir grupos de segurança no Azure AD](active-directory-accessmanagement-manage-groups.md)

* [Configurar o grupos dedicados Azure AD](active-directory-accessmanagement-dedicated-groups.md)

* [Referência de Graph API para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure Active Directory cmdlets para configurar as definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

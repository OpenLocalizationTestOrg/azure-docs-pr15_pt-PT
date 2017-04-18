<properties
    pageTitle="Acesso ao condicional Azure Active Directory FAQ | Microsoft Azure"
    description="Perguntas mais frequentes sobre acesso condicional "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Acesso ao condicional Azure Active Directory perguntas mais frequentes

## <a name="which-applications-work-with-conditional-access-policies"></a>As aplicações que funcionam com as políticas de acesso condicional?

**A:** Consulte o tópico, [condicional aplicações do access – o que são suportadas](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Políticas de acesso condicional são impostas para colaboração B2B e utilizadores convidados?

**A:** As políticas são impostas para utilizadores de colaboração B2B. No entanto, em alguns casos, um utilizador poderá não conseguir cumprir o requisito de política se, por exemplo, uma organização não suporta a autenticação multifator. 

A política não é imposta atualmente para utilizadores do SharePoint convidados. A relação de convidado é mantida no SharePoint. As políticas de utilizadores convidados contas não estão sujeitos a acesso no servidor de autenticação. Acesso de convidado pode ser gerido no SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Se uma política de SharePoint Online também aplica o OneDrive para empresas?

**A:** Sim.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Por que motivo não posso definir uma política de na aplicações de cliente, como o Word ou Outlook?

**A:** Uma política de acesso condicional define requisitos para aceder a um serviço e é aplicada quando autenticação acontece nesse serviço. A política não está definida diretamente na aplicação de cliente; em vez disso, é aplicado quando-chamadas para um serviço. Por exemplo, uma política definida no SharePoint se aplica a clientes do SharePoint que entra em contacto e uma política definida no Exchange aplica-se para o Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional aplicam-se para contas de serviço?

**A:** Políticas de acesso condicional aplicam a todas as contas de utilizador. Isto inclui contas de utilizador utilizadas como contas de serviço. Em muitos casos, uma conta de serviço que é executada automática não é possível satisfazer uma política. Isto acontece, por exemplo, quando é necessária uma MFA. Nestes casos, podem ser excluídas contas de serviços de uma política, utilizando as definições de gestão de políticas de acesso condicional. Saiba mais sobre como aplicar uma política de aos utilizadores aqui.

<properties
    pageTitle="Notificações de aprovisionamento de conta | Microsoft Azure"
    description="Saiba como para se certificar de que o utilizador será notificado de problemas relacionados com o aprovisionamento de utilizador que requerem a sua atenção ao ativar as notificações de aprovisionamento de conta."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Notificações de aprovisionamento de conta

Com o aprovisionamento de utilizador, pode automatizar o processo de gestão de utilizadores em aplicações de SaaS de terceiros. <br>
Enquanto este é um processo automatizado, a interação com este processo de vez em quando é necessária. <br>
Isto acontece, por exemplo, quando a palavra-passe da conta que configurou para trocar dados com uma terceira parte SaaS aplicação expirou. 

Ao ativar as notificações de aprovisionamento de conta, pode garantir que o utilizador será notificado de problemas relacionados com o aprovisionamento de utilizador que requerem a sua atenção.

Ativar ou desativar as notificações do aprovisionamento como parte do seu utilizador configuração para SaaS aplicações de terceiros de aprovisionamento de conta.

![Aprovisionamento de utilizador][1] 



Para ativar as notificações de aprovisionamento de conta, selecione a caixa de verificação relacionada na página de diálogo de **confirmação** e, em seguida, escreva o alias de e-mail do destinatário.

![Notificações de aprovisionamento de conta][2]
 


Pode introduzir uma lista de distribuição como destinatário; No entanto, é importante ter em atenção que a notificação por correio electrónico contém ligações para os relatórios que apenas podem ser acedidas pelos administradores do Azure AD.

Se tiver ativadas de notificações do aprovisionamento de conta, irá receber mensagens de correio eletrónico sobre problemas críticos relacionados com aprovisionamento de utilizador. No entanto, para evitar uma sobrecarga de correio eletrónico, apenas receberá uma notificação por correio electrónico por dia para cada aplicação SaaS que o e-mail da notificação está ativado para.


##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Automatizar utilizador aprovisionamento/Deprovisioning às aplicações SaaS](active-directory-saas-app-provisioning.md)
- [Personalizar os mapeamentos de atributo para aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
- [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Controlo do âmbito filtros para aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
- [Utilizar SCIM para activar automáticas de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações](active-directory-scim-provisioning.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png
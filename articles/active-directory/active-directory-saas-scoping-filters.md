<properties
    pageTitle="Aplicação baseada no atributo aprovisionamento com o controlo do âmbito filtros | Microsoft Azure"
    description="Saiba como utilizar o âmbito de filtros para impedir que os objectos nas aplicações que suportam aprovisionamento automatizado utilizador a partir do realmente a ser aprovisionado se um objeto não corresponderem às necessidades de negócio."
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


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Aplicação baseada no atributo aprovisionamento com filtros de controlo do âmbito

É o objectivo de nesta secção explicam como utilizar filtros âmbito para definir regras baseadas no atributo que vai determinar quais os utilizadores que serão aprovisionados para a aplicação.





## <a name="clauses-and-scope-groups"></a>Orações e grupos do âmbito


![Controlo do âmbito filtro][1] 




Âmbito filtros são definidos por um ou mais **grupos de âmbito**, cada um dos quais mantenha uma ou mais **cláusulas**. Para ver as cláusulas para um grupo determinado âmbito, expandi-lo ao clicar na seta para a esquerda do nome do grupo.

Uma **cláusula** determina os utilizadores que são permitidos para passar através do filtro do âmbito por avaliar atributos de cada utilizador. Por exemplo, poderá ter uma cláusula que necessita que o atributo de «estado» de um utilizador ser igual a Nova Iorque, o que significa que apenas os utilizadores de Nova Iorque serão aprovisionados para a aplicação.

![Controlo do âmbito nome do grupo][2] 



Cada **grupo âmbito** começa com obrigatório uma **cláusula**, conforme apresentado na captura de ecrã acima. Esta cláusula simplesmente indica que o utilizador tem primeiro de ser atribuído à aplicação antes de ser avaliada pelos seus filtros âmbito. Esta cláusula não pode ser eliminada ou modificada.

Pode adicionar novas cláusulas ou novos grupos de âmbito premindo no botão adequado. Pode dar cada grupo âmbito um nome ao editar a respetiva propriedade **Nome do grupo âmbito** .





## <a name="how-scoping-filters-are-evaluated"></a>Como são avaliados de filtros de controlo do âmbito

Durante o aprovisionamento, podemos teste cada utilizador atribuído contra os âmbito filtros para determinar se esse utilizador merece acesso à aplicação. Poderá pensar de cada cláusula como sendo um teste que deve ser transmitido para que o utilizador evitar a introdução filtradas. 

Se tiver vários grupos de âmbito definidos, cada utilizador tem de passar pelo menos um dos-los para poder aceder à aplicação. Dentro de cada grupo âmbito, no entanto, o utilizador tem de passar cada cláusula única para passar esse grupo âmbito específico. 

Por outras palavras, poderá pensar grupos âmbito como sendo ou faria em conjunto e pode achar das cláusulas nelas como sendo AND seria em conjunto. Por exemplo, considere o filtro âmbito abaixo:


![Controlo do âmbito nome do grupo][2]  


De acordo com este filtro âmbito, os utilizadores devem satisfazer os seguintes critérios, para poder ser aprovisionada:

1. Tem de ser atribuídas a aplicação.

2. Tem de trabalhar no departamento de engenharia

3. Tem de ser trabalho Castelo branco ou Canadá.


##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Automatizar utilizador aprovisionamento e Deprovisioning SaaS aplicações](active-directory-saas-app-provisioning.md)
- [Personalizar os mapeamentos de atributo para aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
- [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Notificações de aprovisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Utilizar SCIM para activar automáticas de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações](active-directory-scim-provisioning.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png

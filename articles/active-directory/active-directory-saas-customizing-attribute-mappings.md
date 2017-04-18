<properties
    pageTitle="Personalizar os mapeamentos de atributo | Microsoft Azure"
    description="Saiba que mapeamentos de atributo para aplicações SaaS no Azure Active Directory como pode modificá-los para abordar as necessidades da sua empresa."
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


# <a name="customizing-attribute-mappings"></a>Personalizar os mapeamentos de atributo


Microsoft Azure AD fornece suporte para o aprovisionamento de utilizador das aplicações do SaaS terceiros como Salesforce, Google Apps e as outras pessoas. Se tiver o utilizador de aprovisionamento para terceiros um SaaS aplicação ativada, o Portal de gestão do Azure controla os valores de atributo no formulário de uma configuração denominado "mapeamento de atributo".

Existe um conjunto de mapeamentos de atributo entre os objetos de utilizador do Azure AD e objetos de utilizador de cada aplicação SaaS pré-configurado. Algumas aplicações gerir outros tipos de objetos, tais como a grupos ou contactos. <br> 
Pode personalizar os mapeamentos de atributo predefinidos de acordo com as necessidades da sua empresa. Isto significa que, pode alterar ou eliminar os mapeamentos de atributo existente ou criar novos mapeamentos de atributo.

No portal do Azure AD, pode aceder a esta funcionalidade clicando em atributos na barra de ferramentas de uma aplicação de SaaS.

> [AZURE.NOTE] A ligação de **atributos** só está disponível se tiver ativado para uma aplicação de SaaS de aprovisionamento de utilizador. 


![Equipa de vendas][1] 


Quando clica em atributos na barra de ferramentas da lista de mapeamentos atuais que estão configuradas para uma aplicação de SaaS.

A captura de ecrã seguinte apresenta um exemplo para isto:



![Equipa de vendas][2]  


No exemplo acima, pode ver que o atributo **NomePróprio** de um objecto gerido no Salesforce é povoado com o valor de **givenName** a ligada de objeto do Azure AD.

Se pretende personalizar os mapeamentos de atributo ou se quiser reverter personalizado as definições para a configuração predefinida, pode fazê-lo ao clicar no botão relacionado na barra de ferramentas na parte inferior de uma aplicação.


![Equipa de vendas][3]  


Existem mapeamentos de atributos que são necessários por uma aplicação de SaaS a funcionar corretamente. Na tabela atributos, os mapeamentos de atributo relacionado com **Sim** como valor para o atributo **necessário** . Se for necessário um mapeamento de atributo, não consegue eliminar. Neste caso, a funcionalidade de **Eliminar** não está disponível.

Para modificar um mapeamento de atributo existente, basta selecionar o mapeamento de e, em seguida, clique em **Editar**. Isto reúne uma página de diálogo permite-lhe modificar o mapeamento de atributo selecionado.


![Editar o mapeamento de atributo][4]  



## <a name="understanding-attribute-mapping-types"></a>Noções sobre tipos de mapeamento de atributos


Com mapeamentos de atributo, pode controlar como os atributos são povoados na terceira parte SaaS aplicação. Existem quatro tipos de mapeamento diferentes suportados:

- **Direta** – o atributo de destino é povoada com o valor de um atributo do objeto associado no Azure AD.


- **Constante** – o atributo de destino é povoada com uma determinada cadeia que especificou.


- **Expressão** - o atributo de destino é povoada com base no resultado de uma expressão de script semelhantes. Para obter mais detalhes, consulte o artigo [Expressões de escrita para mapeamentos de atributo no Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Nenhum** – o atributo de destino é não modificada à esquerda. No entanto, se o atributo de destino estiver vazio alguma vez, vai ser povoada com o valor predefinido que especificar.



Para além destes quatro tipos de mapeamento de atributo básicas, mapeamentos de atributos personalizados suportam o conceito de uma atribuição de valor **predefinido** . A atribuição de valor predefinido assegura que um atributo de destino é povoado com um valor se existir um valor nenhuma no Azure AD nem no objecto de destino.

Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Num ambiente inicializado, apenas os objetos que exige o atualizações são processados durante um ciclo de sincronização. Atualizar os mapeamentos de atributo tem um impacto no desempenho de um ciclo de sincronização. Isto acontece porque todos os objectos geridos para ser reevaluated necessita de uma atualização para a configuração de mapeamento de atributo. Por esta razão, é uma melhor prática recomendada para manter o número de alterações consecutivas para os mapeamentos de atributo no mínimo.


##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Automatizar utilizador aprovisionamento/Deprovisioning às aplicações SaaS](active-directory-saas-app-provisioning.md)
- [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Controlo do âmbito filtros para aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
- [Utilizar SCIM para activar automáticas de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações](active-directory-scim-provisioning.md)
- [Notificações de aprovisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

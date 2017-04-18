<properties 
    pageTitle="Como utilizar propriedades no políticas de gestão de API do Azure" 
    description="Saiba como utilizar propriedades no políticas de gestão de API do Azure." 
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


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Como utilizar propriedades no políticas de gestão de API do Azure

Políticas de gestão de API são uma funcionalidade avançada do sistema que permitir que o publisher alterar o comportamento da API do através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido de ou resposta de uma API. Declarações de política podem ser construídas utilizando os valores de texto literal, expressões de política e propriedades. 

Cada instância do serviço de gestão de API tem uma colecção de propriedades de pares valor/chave globais para a instância do serviço. Estas propriedades podem ser utilizadas para gerir valores de cadeia constante através de todas as políticas e configuração de API. Cada propriedade tem os seguintes atributos.


| Atributo | Tipo            | Descrição                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nome      | cadeia          | O nome da propriedade. Pode conter apenas letras, dígitos, período, traço e carateres de sublinhado. |
| Valor     | cadeia          | O valor da propriedade. Não poderá estar vazio ou consistir apenas de espaço em branco.                           |
| Secreta    | Booleano         | Determina se o valor é uma palavra-passe e deve ser encriptado ou não.                                |
| Etiquetas      | matriz de cadeia | Opcional etiquetas que, quando fornecidos podem ser utilizados para filtrar a lista de propriedades.                               |

Propriedades são configuradas no portal do publisher, no separador **Propriedades** . No exemplo seguinte, três propriedades estiverem configuradas.

![Propriedades][api-management-properties]

Valores de propriedade podem conter cadeias literais e [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx). A tabela seguinte mostra as propriedades de três exemplo anterior e os respetivos atributos. O valor de `ExpressionProperty` é uma política de expressão que devolve uma cadeia que contém a data e hora atuais. A propriedade `ContosoHeaderValue` é assinalado como uma palavra-passe, para o valor não seja apresentado.

| Nome               | Valor                      | Secreta | Etiquetas    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | FALSO  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | VERDADEIRO   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | FALSO  |         |

## <a name="to-use-a-property"></a>Para utilizar uma propriedade

Para utilizar uma propriedade de uma política, coloque o nome da propriedade dentro de um par de chavetas como duplo `{{ContosoHeader}}`, conforme mostrado no seguinte exemplo.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

Neste exemplo, `ContosoHeader` são utilizados como o nome de um cabeçalho numa `set-header` política, e `ContosoHeaderValue` são utilizados como o valor do cabeçalho dessa. Quando esta política é avaliada durante um pedido de ou resposta para o gateway de gestão de API `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos pelos respetivos valores de propriedade respetivos.

Propriedades podem ser utilizadas como atributo concluído ou valores de elemento conforme mostrado no exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, conforme mostrado no exemplo seguinte:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propriedades também podem conter expressões de política. No exemplo seguinte, o `ExpressionProperty` é utilizado.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Quando esta política é avaliada, `{{ExpressionProperty}}` é substituído pelo respetivo valor: `@(DateTime.Now.ToString())`. Uma vez que o valor for uma expressão de política, a expressão é avaliada e a política avança com a sua execução.

Pode testar este fora no portal do programador ao contactar o suporte uma operação que tenham uma política com as propriedades no âmbito. No seguinte exemplo, uma operação denomina-se com o exemplo anterior duas `set-header` políticas com as propriedades. Tenha em atenção que a resposta contém dois cabeçalhos personalizados que tenham sido configurados através das políticas com as propriedades.

![Portal de programador][api-management-send-results]

Se observe o [rastreio Inspector de API](api-management-howto-api-inspector.md) para uma chamada do que inclui as políticas de exemplo anterior duas com as propriedades, pode ver as duas `set-header` políticas com os valores de propriedade inseridas assim como a avaliação da expressão de política para a propriedade que continha a expressão de política.

![Rastreio API Inspector][api-management-api-inspector-trace]

Tenha em atenção que, enquanto valores de propriedade podem conter expressões de política, valores de propriedade não podem conter outras propriedades. Se o texto que contém uma referência de propriedade é utilizado para um valor de propriedade, tais como `Property value text {{MyProperty}}`, essa referência de propriedade não ser substituída e serão incluída como parte do valor da propriedade.

## <a name="to-create-a-property"></a>Para criar uma propriedade

Para criar uma propriedade, clique em **Adicionar propriedade** no separador **Propriedades** .

![Adicionar propriedade][api-management-properties-add-property-menu]

**Nome** e o **valor** são valores necessários. Se este valor da propriedade é uma palavra-passe, selecione a caixa de verificação **Esta é uma palavra-passe** . Introduza um ou mais etiquetas opcionais para o ajudar a organizar as suas propriedades e clique em **Guardar**.

![Adicionar propriedade][api-management-properties-add-property]

Quando uma nova propriedade é guardada, a caixa de texto de **propriedade de pesquisa** é povoada com o nome da nova propriedade e é apresentada a nova propriedade. Para apresentar todas as propriedades, desmarque a caixa de texto de **propriedade de procura** e prima enter.

![Propriedades][api-management-properties-property-saved]

Para obter informações sobre como criar uma propriedade de utilizar a API REST, consulte o artigo [criar uma propriedade de utilizar a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Para editar uma propriedade

Para editar uma propriedade, clique em **Editar** ao lado de propriedade para editar.

![Editar propriedade][api-management-properties-edit]

Faça as alterações pretendidas e clique em **Guardar**. Se mudar o nome da propriedade, quaisquer políticas que fazem referência a dessa propriedade são atualizadas automaticamente para utilizar o novo nome.

![Editar propriedade][api-management-properties-edit-property]

Para obter informações sobre como editar uma propriedade de utilizar a API REST, consulte o artigo [Editar uma propriedade de utilizar a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para eliminar uma propriedade

Para eliminar uma propriedade, clique em **Eliminar** ao lado de propriedade para eliminar.

![Eliminar propriedade][api-management-properties-delete]

Clique em **Sim, eliminá-la** para confirmar.

![Confirmar a eliminação][api-management-delete-confirm]

>[AZURE.IMPORTANT] Se a propriedade é referenciada por quaisquer políticas, não será possível para eliminá-la com êxito até remover a propriedade de todas as políticas que utilização-la.

Para obter informações sobre como eliminar uma propriedade de utilizar a API REST, consulte [Eliminar uma propriedade de utilizar a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Para procurar e filtrar propriedades

No separador **Propriedades da** inclui procura e filtragem de funcionalidades para ajudar a gerir as suas propriedades. Para filtrar a lista de propriedades por nome da propriedade, introduza um termo de pesquisa na caixa de texto **propriedade de pesquisa** . Para apresentar todas as propriedades, desmarque a caixa de texto de **propriedade de procura** e prima enter.

![Pesquisa][api-management-properties-search]

Para filtrar a lista de propriedades por valores de etiqueta, introduza um ou mais etiquetas a caixa de texto do **filtro por etiquetas** . Para apresentar todas as propriedades, desmarque a caixa de texto **Filtrar por etiquetas** e prima enter.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Próximos passos

-   Saiba mais sobre como trabalhar com as políticas
    -   [Políticas de gestão de API no](api-management-howto-policies.md)
    -   [Referência da política](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Ver uma descrição geral em vídeo

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png


<properties 
    pageTitle="Como adicionar operações a uma API na gestão de API do Azure | Microsoft Azure" 
    description="Saiba como adicionar operações a uma API na gestão de API do Azure." 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Como adicionar operações a uma API na gestão de API do Azure

Antes de pode ser utilizada uma API na gestão de API, tem de ser adicionadas operações. Este guia mostra como adicionar e configurar diferentes tipos de operações para uma API na gestão de API.

## <a name="add-operation"> </a>Adicione uma operação

Operações são adicionadas e configuradas para uma API no portal do publisher. Para aceder ao portal do publisher, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Selecione a API pretendida no portal do publisher e, em seguida, selecione o separador de **operações** . 

![Operações][api-management-operations]

Clique em **Adicionar operação** para adicionar uma nova operação. Será apresentada a **nova operação** e no separador **assinatura** será seleccionado por predefinição.

![Operação de adição][api-management-add-operation]

Especifique o **verbo HTTP** ao selecionar a partir da lista pendente.

![Método de HTTP][api-management-http-method]

<a name="url-template"></a>

Defina o modelo de URL ao escrever num fragmento de URL que consiste um ou mais segmentos de caminho de URL e zero ou mais parâmetros de cadeia de consulta. O modelo de URL, acrescentado ao URL de base da API do identifica uma única operação de HTTP. Pode conter um ou mais denominada variáveis partes que são identificadas por chavetas. Estas peças variáveis são denominadas parâmetros de modelo em são atribuídas dinamicamente valores extraídas de URL o pedido quando o pedido está a ser processado pela plataforma de gestão de API.

![Modelo de URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Se pretender, especifique o **URL reescrita de modelo**. Esta opção permite-lhe utilizar o modelo de URL padrão para processamento de pedidos recebidos no front-end, durante a chamada back-end através de um URL de acordo com o modelo de reescrita convertido. Parâmetros de modelo a partir do modelo de URL devem ser utilizados num modelo de reescrita. O exemplo seguinte mostra o tipo de conteúdo como codificado tal como segmento de caminho no serviço web do exemplo anterior, pode ser fornecido como um parâmetro de consulta na API publicada através da plataforma de gestão de API utilizando os modelos de URL.

![Reescrita de modelo de URL][api-management-url-template-rewrite]

Os autores das chamadas para a operação irão utilizar o formato `/customers?customerid=ALFKI` e esta será mapeada para `/Customers('ALFKI')` quando é chamado o serviço de back-end.


Nome a **apresentar** e o **Descrição** fornecer uma descrição da operação de são utilizadas para fornecer documentação para os programadores de utilizar esta API no portal do programador.

![Descrição][api-management-description]

A descrição da operação pode ser especificada como texto simples ou HTML na caixa de texto de **Descrição** .

## <a name="operation-caching"> </a>Operação colocação em cache

Resposta colocação em cache reduz a latência cobrada pelos consumidores API, consumo de largura de banda diminui e diminui a carga na HTTP web API de execução do serviço. 

Para fácil e rapidamente ativar colocação em cache para a operação, selecione o separador de **Colocação em cache** e selecione a caixa de verificação **Ativar** .

![Colocação em cache][api-management-caching-tab]

**Duração** Especifica o período de tempo durante o qual a resposta operação permanece na cache de. O valor predefinido é 3600 segundos ou 1 hora.

Chaves de cache são utilizadas para diferenciar entre as respostas para que a resposta correspondente a cada chave cache diferentes irá obter o seu próprio valor em cache em separado. Opcionalmente, introduza os parâmetros de cadeia de consulta específica e/ou cabeçalhos de HTTP para ser utilizado na informática valores chave da cache nas caixas de texto **variação por parâmetros de cadeia de consulta** e **variação por cabeçalhos** respetivamente. Quando são nenhum pedido especificado, completo URL e os seguintes valores de cabeçalho HTTP são utilizados em geração de chaves de cache: **Aceitar** e **Conjunto de caracteres aceitar**.

>Para mais informações sobre a colocação em cache e políticas de colocação em cache, consulte o artigo [como resultados da operação de cache na gestão de API do Azure][].


## <a name="request-parameters"> </a>Parâmetros do pedido

Parâmetros da operação são geridos no separador parâmetros. Os parâmetros especificados no **Modelo de URL** no separador **assinatura** são automaticamente adicionados e podem ser alterados apenas ao editar o modelo de URL. Parâmetros adicionais podem ser introduzidos manualmente.

Para adicionar um novo parâmetro de consulta, clique em **Adicionar o parâmetro de consulta** e introduza as seguintes informações:

-   **Nome** - nome do parâmetro.
-   **Descrição** - uma breve descrição do parâmetro (opcional).
-   **Tipo de** -tipo de parâmetro seleccionado na lista pendente para baixo.
-   **Valores** - valores que pode ser atribuído a este parâmetro. Um dos valores pode ser marcado como sendo o predefinido (opcional).
-   **Obrigatório** - tornar o parâmetro obrigatória ao selecionar a caixa de verificação. 

![Pedido de parâmetros][api-management-request-parameters]

## <a name="request-body"> </a>Pedir corpo

Se permitir a operação (por exemplo, colocar, mensagem) e requer um corpo poderá fornecer um exemplo da mesma em todos os formatos suportados representação (por exemplo, XML, json). 

>Corpo do pedido é utilizado apenas para fins documentação e não é validado.

Para introduzir um corpo do pedido, mude para o separador **corpo** .

Clique em **Adicionar representação**, comece a escrever o nome de tipo de conteúdo pretendida (por exemplo, aplicação/json), selecione-o no menu pendente e cole o exemplo de corpo do pedido pretendida no formato seleccionado na caixa de texto. 

![Corpo do pedido][api-management-request-body]

No adicionais para representações, também pode especificar uma descrição opcional texto na caixa de texto de **Descrição** .

## <a name="responses"> </a>Respostas

É aconselhável fornecem exemplos de respostas para todos os códigos de estado que pode produzir a operação. Código de estado de cada poderá ter mais do que um exemplo de corpo de resposta, um para cada um dos tipos de conteúdo suportados. 

Para adicionar uma resposta, clique em **Adicionar** e comece a escrever o código de estado pretendido. Neste exemplo, o código de estado é **200 OK**. Assim que o código é apresentado no menu pendente, selecione-o e o código de resposta é criado e adicionado ao seu funcionamento.

![Código de resposta][api-management-response-code]

Clique em **Adicionar representação**, comece a escrever o nome do tipo de conteúdo pretendida (por exemplo, aplicação/json) e, em seguida, selecione-o na lista pendente para baixo.

![Tipo de conteúdo de corpo][api-management-response-body-content-type]

Cole o exemplo de corpo de resposta no formato seleccionado na caixa de texto. 

![Corpo de resposta][api-management-response-body]

Se pretender, adicione uma descrição opcional para a caixa de texto **Descrição** .

Assim que a operação está configurada, clique em **Guardar**.


## <a name="next-steps"> </a>Passos seguintes

Assim que as operações são adicionadas a uma API, o próximo passo é associar API um produto e publicá-lo para que os programadores podem ligar as respectivas operações.

-   [Como criar e publicar um produto][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como os resultados da operação de cache na gestão de API do Azure]: api-management-howto-cache.md
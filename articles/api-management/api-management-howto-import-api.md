<properties 
    pageTitle="Conceitos de chave de gestão de API" 
    description="Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos chaves API gestão." 
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

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Como importar a definição de uma API com operações na gestão de API do Azure

Em gestão de API, novos APIs podem ser criados e as operações adicionadas manualmente ou API pode ser importados juntamente com as operações num único passo.

APIs e os respetivos operações podem ser importadas utilizando os seguintes formatos.

-   WADL
-   Swagger

Este guia mostra como criar uma nova API e importar suas operações num único passo. Para obter informações sobre como criar manualmente uma API e adicionar operações, consulte o artigo [como criar APIs][] e [como adicionar operações para uma API][].

## <a name="import-api"> </a>Importar uma API

APIs são criados e configurados no portal do publisher. Para aceder ao portal do publisher, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

![Portal do Publisher][api-management-management-console]

Clique **APIs** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Importar API**.

![API de importação][api-management-import-apis]

A janela **Importar API** tem três separadores que correspondem aos três formas para fornecer a especificação de API.

-   **A partir da área de transferência** permite-lhe colar a especificação de API na caixa de texto designadas.
-   **A partir de ficheiro** permite-lhe procurar e selecione o ficheiro que contém a especificação da API.
-   **A partir do URL** permite-lhe fornecer o URL para a especificação de para a API.

![Formato de importação de API][api-management-import-api-clipboard]

Depois de fornecer a especificação de API, utilize os botões de opção no lado direito para indicar o formato especificação. São suportados os seguintes formatos.

-   WADL
-   Swagger

Em seguida, introduza um **URL da Web API sufixo**. Isto é anexado ao URL do seu serviço de gestão de API de base. A base URL é comuns para todos os APIs alojados em cada instância de um serviço de gestão de API. Gestão de API distinga APIs pelo respetivo sufixo e, consequentemente, o sufixo deve ser exclusivo para cada API numa instância de serviço de gestão de API específica.

Depois de todos os valores são introduzidos, clique em **Guardar** para criar a API e as operações associadas. 

>[AZURE.NOTE] Para obter um tutorial da importação uma calculadora básica API no formato de Swagger, consulte o artigo [Gerir o primeiro API na gestão de API do Azure](api-management-get-started.md).

## <a name="export-api"></a> Uma API de exportação

Para além de importar APIs novos, pode exportar as definições dos seus APIs a partir do portal do publisher. Para fazê-lo, clique em **Exportar API** no **separador Resumo** da sua **API**.

![API de exportação][api-management-export-api]

APIs podem ser exportados utilizando WADL ou Swagger. Selecione o formato pretendido, clique em **Guardar**e escolha a localização na qual pretende guardar o ficheiro.

![Formato de API de exportação][api-management-export-api-format]

## <a name="next-steps"> </a>Passos seguintes

Quando é criada uma API e as operações importadas, pode rever e configurar quaisquer definições adicionais, adicione a API para um produto e publicá-lo para que fique disponível para programadores. Para mais informações, consulte os seguintes guias.

-   [Como configurar as definições de API][]
-   [Como criar e publicar um produto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance

[Como adicionar operações a uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como criar APIs]: api-management-howto-create-apis.md
[Como configurar as definições de API]: api-management-howto-create-apis.md#configure-api-settings

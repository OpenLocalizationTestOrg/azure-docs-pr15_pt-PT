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
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>O que é a gestão de API?

Gestão de API ajuda as organizações publicar APIs para externos, parceiros e os programadores internos para desbloquear as potencialidades dos seus dados e serviços. Empresas em qualquer lugar são está à procura para expandir os respetivos operações como uma plataforma digital, criar canais novos, encontrar novos clientes e condução Cativação mais aprofundada com os existentes. Gestão de API fornece as competências core para se certificar de um programa de API efetuada com êxito através da Cativação de programador, informações de negócio, análise, segurança e proteção.

Veja o vídeo seguinte para obter uma descrição geral de gestão de API do Azure e saiba como utilizar a gestão de API para adicionar muitas funcionalidades à sua API, incluindo o controlo de acesso, limitação de velocidade, monitorização, registo de eventos e resposta colocação em cache, com o trabalho mínimo da sua parte.

> [AZURE.VIDEO azure-api-management-overview]

Para utilizar a gestão de API, os administradores criar APIs. Cada API consiste numa ou mais operações e cada API pode ser adicionado a um ou mais produtos. Para utilizar uma API, os programadores subscreva um produto que contém essa API e, em seguida, podem ligar a operação a API, sujeito a quaisquer políticas de utilização que possam estar em vigor.

Este tópico fornece uma descrição geral dos conceitos chaves API gestão.

>[AZURE.NOTE] Para obter mais informações, consulte o artigo o [Gestão de API baseado na nuvem: aproveitar o Power APIs](http://j.mp/ms-apim-whitepaper) documento técnico de PDF. Este documento técnico introdutório sobre a gestão de API pela CITO Research abrange: 
>
> - Requisitos de API comuns e os desafios
>     - Desacoplamento APIs e apresentar fachadas
>     - Começar os programadores e execução rapidamente
>     - Proteger o acesso
>     - Métricas e de análise
> - A obtenção de controlo e conhecimentos aprofundados com uma plataforma de gestão de API
> - Utilizar a nuvem vs no local soluções
> - Gestão de Azure API

## <a name="apis"> </a>APIs e operações

APIs são a Fundação de uma instância do serviço de gestão de API. Cada API representa um conjunto de operações disponíveis para os programadores. Cada API contém uma referência para o serviço de back-end implementa a API e o mapa de operações para as operações implementada pelo serviço back-end. As operações na gestão de API são altamente configuráveis, com controlo sobre o URL mapeamento, consultas e parâmetros do caminho, pedido e conteúdo da resposta e colocação em cache de resposta de operação. Também podem ser implementadas o limite da taxa, quotas e políticas de restrição de IP ao nível de operação individuais ou API.

Para mais informações, consulte o artigo [como criar APIs][] e [como adicionar operações para uma API][].


## <a name="products"></a> Produtos

Os produtos estão como APIs forem apresentados para os programadores. Os produtos da gestão de API tem um ou mais APIs e estão configurados com um título, descrição e termos de utilização. Produtos podem ser **aberto** ou **protegido**. Produtos protegidos tem de subscrever antes de poderem ser utilizados, enquanto este estiver aberto podem ser utilizados produtos sem uma subscrição. Quando estiver pronto para ser utilizado por programadores um produto pode ser publicado. Assim que for publicado, pode ser visualizada (e no caso de produtos protegidos subscrever o) por programadores. Aprovação de subscrição está configurada ao nível do produto e pode exigir a aprovação do administrador ou ser aprovada automática.

Grupos são utilizados para gerir a visibilidade de produtos para os programadores. Produtos conceder visibilidade a grupos e os programadores podem ver e subscrever os produtos que são visíveis para os grupos na qual pertence. 

Para mais informações, consulte o artigo [como criar e publicar um produto][] e o vídeo seguinte.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Grupos

Grupos são utilizados para gerir a visibilidade de produtos para os programadores. Gestão de API tem os seguintes grupos de sistema imutáveis.

-   **Os administradores** - os administradores de subscrição Azure são os membros deste grupo. Os administradores gerir instâncias do serviço de gestão de API, criar a APIs, operações e produtos que são utilizados pelos programadores.
-   **Os programadores** - autenticado developer portal para utilizadores de se situar para este grupo. Os programadores são clientes a que criarem aplicações utilizando o seu APIs. Os programadores são concedidos aceder ao portal de programador e criem a aplicações que chamam a operações de uma API.
-   **Os convidados** - utilizadores portal de programador não autenticados, tal como potenciais clientes visitar o portal de Programador de uma instância de gestão de API de se situar para este grupo. -Lhes podem ser concedido determinado acesso só de leitura, tal como a capacidade de ver APIs, mas não chamá-los.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tirar partido externos grupos no associado inquilinos do Azure Active Directory](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Personalizar e grupos externos podem ser utilizados ao longo dos grupos de sistema no que lhe dá uma acesso e visibilidade os programadores a produtos API. Por exemplo, poderia criar um grupo personalizado para os programadores afiliados com uma organização parceiro específico e lhes permitem aceder a API da partir de um produto que contenha apenas APIs relevantes. Um utilizador pode ser um membro de mais do que um grupo.

Para mais informações, consulte o artigo [como criar e utilizar grupos][].

## <a name="developers"></a> Os programadores

Os programadores representam as contas de utilizador de uma instância do serviço de gestão de API. Os programadores podem ser criados ou convidados a aderir por administradores ou pode inscrever a partir do [portal de programador][]. Cada programador é um membro de um ou mais grupos e pode ser subscrever os produtos que conceder visibilidade a nesses grupos.

Quando os programadores subscrever um produto-lhes são concedido a chave principal e secundária para o produto. Esta chave é utilizada quando efetuar chamadas para APIs o produto.

Para mais informações, consulte o artigo [como criar ou convidar os programadores][] e [como associar grupos com os programadores][].

## <a name="policies"></a> Políticas

As políticas são uma poderosa capacidade de gestão do API permitir que o publisher alterar o comportamento da API do através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido de ou resposta de uma API. Demonstrações populares incluem a conversão do formato de XML para JSON e limitar de taxas de chamada para restringir a quantidade de chamadas a partir de um programador e muitas outras políticas estão disponíveis.

Expressões de política podem ser utilizados como valores de atributo ou valores de texto em qualquer uma das políticas de gestão de API, a menos que a política caso contrário, especifica. Algumas políticas, tais como as políticas de [fluxo de controlo](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) e [Definir variável](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) são baseadas em expressões de política. Para mais informações, consulte o artigo [Avançadas políticas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)e veja o vídeo seguinte.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Para uma lista completa das políticas de gestão de API, consulte o artigo [referência da política][]. Para mais informações sobre como utilizar e configurar políticas, consulte o artigo [políticas de gestão de API][]. Para obter um tutorial sobre como criar um produto com a taxa de políticas de limite e quota, consulte o artigo [como criar e configurar definições avançadas de produto][]. Para uma demonstração, consulte o vídeo seguinte.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Developer portal para

O portal de programador é onde os programadores podem saber mais sobre as operações de APIs, ver e chamada e subscrever produtos. Potenciais clientes podem aceder a Help portal de programador, visualizar APIs e operações e inscrever-se. O URL do seu portal do programador está localizado no dashboard de no Portal clássica do Azure para a instância do serviço de gestão de API.

Pode personalizar o aspeto e funcionalidade do seu portal de programador ao adicionar conteúdo personalizado, personalizar estilos e adicionar a imagem corporativa.

## <a name="api-management-and-the-api-economy"></a>Gestão de API e a economia API

Para saber mais sobre a gestão de API, veja a seguinte apresentação a partir de conferência da Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portal de programador]: #developer-portal

[Como criar APIs]: api-management-howto-create-apis.md
[Como adicionar operações a uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como criar e utilizar grupos]: api-management-howto-create-groups.md
[Como associar grupos os programadores]: api-management-howto-create-groups.md#associate-group-developer
[Como criar e configurar definições avançadas de produto]: api-management-howto-product-with-rules.md
[Como criar ou convidar os programadores]: api-management-howto-create-or-invite-developers.md
[Referência da política]: api-management-policy-reference.md
[Políticas de gestão de API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 

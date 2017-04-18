<properties
pageTitle="Aplicação do Azure Active Directory e objetos de serviço de capital | Microsoft Azure"
description="Num debate da relação entre aplicação e objectos principais de serviço no Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Aplicação e objectos principais de serviço no Azure Active Directory
Quando ler sobre um Azure Active Directory (AD) "pedido", não é sempre limpar exatamente o que está a ser referido por autor. O objetivo deste artigo é para torná-lo mais claros, ao definir os aspectos conceptuais e betão da integração de aplicações do Azure AD, com um exemplo de registo e consentimento para uma [aplicação do inquilino com várias](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Descrição geral
Uma aplicação do Azure AD é mais abrangente sobre que apenas uma pedra do software. É um termo conceptual, que fazem referência não só a software da aplicação, mas o seu registo (também conhecidos por: a configuração de identidade) com o Azure AD, que permite-lhe participar em autenticação e autorização "conversações" o tempo de execução. Por definição, uma aplicação pode funcionar numa função de [cliente](active-directory-dev-glossary.md#client-application) (um recurso a consumir), uma função de [servidor do recurso](active-directory-dev-glossary.md#resource-server) (APIs exposição aos clientes) ou até mesmo ambos. O protocolo de conversação é definido por um [fluxo de OAuth 2.0 autorização de conceder](active-directory-dev-glossary.md#authorization-grant), com um objetivo de permitir que o cliente/recurso para acesso/proteger dados de um recurso respetivamente. Agora, vamos um nível mais aprofundado e consulte o artigo como o modelo de aplicação do Azure AD representa uma aplicação internamente. 

## <a name="application-registration"></a>Registo da aplicação
Quando registar uma aplicação no [portal clássica Azure][AZURE-Classic-Portal], dois objetos são criados no seu inquilino do Azure AD: um objeto de aplicação e um objecto principal de serviço.

#### <a name="application-object"></a>Objecto de aplicação
Uma aplicação do Azure AD é *definido* pelo respetivo e apenas objecto de aplicação, que reside no inquilino Azure AD onde a aplicação foi registada, designados de inquilino "principal" a aplicação. O objeto application fornece informações relacionadas com a identidade para uma aplicação e é um modelo a partir do qual o respetivo correspondente serviço principal torno dos objetos são *derivado* para utilização em tempo de execução. 

Poderá pensar da aplicação como a representação *global* da sua aplicação (para utilização em todos os inquilinos) e o capital de serviço, como a representação *local* (para utilização num inquilino específico). Azure AD Graph [entidade de aplicação] [ AAD-Graph-App-Entity] define o esquema de um objeto de aplicação. Um objeto de aplicação, por isso, tem uma relação de 1:1 com a aplicação de software e um 1:*n* relação com a sua correspondente *n* serviço principal torno dos objetos.

#### <a name="service-principal-object"></a>Objecto principal de serviço
O objecto principal de serviço define a política e permissões para uma aplicação, fornecer a base para um principal de segurança representar a aplicação ao aceder ao recursos em tempo de execução. Azure AD Graph [ServicePrincipal entidade] [ AAD-Graph-Sp-Entity] define o esquema de um objecto principal de serviço. 

É necessário um objecto principal de serviço no cada inquilino para o qual deve ser representada uma instância da utilização da aplicação, ativar o acesso seguro a recursos pertencentes a contas de utilizador a partir desse inquilino. Uma aplicação de inquilino único terá apenas uma principal do serviço (no seu inquilino principal). Um inquilino com várias [aplicação Web](active-directory-dev-glossary.md#web-client) também terá um principal de serviço no cada inquilino onde um administrador ou um utilizador ou utilizadores a partir desse inquilino tem dado o seu consentimento, permitindo-lhe para aceder aos seus recursos. Seguir consentimento, o objecto principal de serviço irá ser consultado para pedidos de autorização futuras. 

> [AZURE.NOTE] Quaisquer alterações realizadas ao objeto de aplicação, também serão refletidas na sua objecto principal de serviço no casa inquilino a aplicação apenas (o inquilino onde estava registada). Para aplicações do inquilinos com várias alterações ao objecto de aplicação não serão refletidas na objectos principais de serviço dos inquilinos qualquer consumidor, até o inquilino do consumidor remove o acesso e concede acesso novamente.

## <a name="example"></a>Exemplo
O diagrama seguinte ilustra a relação entre uma aplicação objecto da aplicação e correspondente objetos principais, no contexto de uma aplicação do inquilino com várias de exemplo denominado **HR aplicação**de serviço. Neste cenário, existem três inquilinos do Azure AD: 

- **Adatum** - o inquilino utilizado pela empresa que desenvolvido a **aplicação de recursos humanos**
- **Contoso** - o inquilino utilizado pela organização Contoso, que é um consumidor da **aplicação de recursos humanos**
- **Fabrikam** - o inquilino utilizado pela organização Fabrikam, que também consome a **aplicação de recursos humanos**

![Relação entre um objeto de aplicação e um objecto principal de serviço](./media/active-directory-application-objects/application-objects-relationship.png)

No diagrama anterior, passo 1 é o processo de criação a aplicação e os objectos principais de serviço no inquilino principal da aplicação.

No passo 2, quando os administradores da Contoso e Fabrikam concluir consentimento, um objecto principal de serviço é criado no inquilino do Azure AD da sua empresa e atribuído as permissões que o administrador concedido. Tenha em atenção que a aplicação de HR poderia ser configurado/concebidos para permitir que o seu consentimento pelos utilizadores para utilização individual.

No passo 3, os inquilinos do consumidor da HR aplicação (Contoso e Fabrikam) cada tem os seus próprios objecto principal de serviço. Cada representa o uso de uma instância da aplicação o tempo de execução, regida pelas permissões manifestado acordo pelo administrador da respetiva.

## <a name="next-steps"></a>Próximos passos
Objecto de aplicação de uma aplicação pode ser acedido através do API do Azure AD Graph, tal como ilustrado pelo respetivo OData [entidade de aplicação][AAD-Graph-App-Entity]

Objecto principal de serviço de uma aplicação pode ser acedido através do API do Azure AD Graph, tal como ilustrado pelo respetivo OData [ServicePrincipal entidade][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com
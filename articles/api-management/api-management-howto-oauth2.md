<properties 
    pageTitle="Como autorizar utilizar OAuth 2.0 no Azure API gestão de contas de programador" 
    description="Saiba como autorizar utilizar OAuth 2.0 no API gestão de utilizadores." 
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

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar utilizar OAuth 2.0 no Azure API gestão de contas de programador

Muitas APIs [OAuth 2.0](http://oauth.net/2/) para proteger a API e certifique-se de que apenas válidos utilizadores têm acesso e apenas poderão aceder a que está a direito de recursos de suporte. Para poder utilizar o interativos programador consola Azure API Management com essas APIs, o serviço permite-lhe configurar a sua instância do serviço para funcionar com o seu OAuth 2.0 API ativada.

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra-lhe como configurar o seu instância do serviço de gestão de API para utilizar a autorização de OAuth 2.0 para contas de programador, mas não mostrar-lhe como configurar um fornecedor de OAuth 2.0. A configuração para cada fornecedor OAuth 2.0 é diferente, apesar dos passos são semelhantes, e as peças de informações de utilizado na configuração OAuth 2.0 na sua instância do serviço de gestão de API necessárias são os mesmos. Este tópico mostra exemplos de como utilizar o Azure Active Directory como um fornecedor de OAuth 2.0.

>[AZURE.NOTE] Para mais informações sobre como configurar 2.0 OAuth utilizando o Azure Active Directory, consulte o artigo da [Web App-GraphAPI-DotNet][] amostra.

## <a name="step1"> </a>Configurar um servidor de autorização OAuth 2.0 no API gestão

Para começar a, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API.

![Portal do Publisher][api-management-management-console]

>[AZURE.NOTE] Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Clique em **segurança** no menu de **Gestão de API** à esquerda, clique em **OAuth 2.0**e, em seguida, clique em **servidor de autorização de adicionar**.

![OAuth 2.0][api-management-oauth2]

Depois de clicar em **Adicionar servidor de autorização**, é apresentado o novo formulário de autorização de servidor.

![Novo servidor][api-management-oauth2-server-1]

Introduza um nome e uma descrição opcional nos campos **nome** e **Descrição** . 

>[AZURE.NOTE] Estes campos são utilizados para identificar o servidor de autorização de OAuth 2.0 dentro a instância atual do serviço de gestão de API e os respetivos valores não provêm de servidor OAuth 2.0.

Introduza o **URL de página de registo do cliente**. Esta página é onde os utilizadores podem criar e gerir as respetivas contas e varia consoante o fornecedor de OAuth 2.0 utilizado. O **URL de página de registo do cliente** aponta para a página que os utilizadores podem utilizar para criar e configurar os seus próprios contas para OAuth 2.0 fornecedores que suportam a gestão de contas de utilizador. Algumas organizações não configurar ou utilizar esta funcionalidade, mesmo se o fornecedor de OAuth 2.0 suporta-lo. Se o seu fornecedor de OAuth 2.0 não tiver gestão de utilizadores de contas configurados, introduza um URL de marcador de posição aqui como o URL da sua empresa ou um URL, tal como `https://placeholder.contoso.com`.

A secção seguinte do formulário contém as definições de **código de autorização conceder tipos de** **URL de ponto final de autorização** **método** e autorização pedido.

![Novo servidor][api-management-oauth2-server-2]

Especifique o **código de autorização conceder tipos** ao selecionar os tipos de pretendida. **Código de autorização** é especificado por predefinição.

Introduza o **URL de ponto final de autorização**. Para o Azure Active Directory, este URL será semelhante o seguinte URL, onde `<client_id>` é substituído com o id de cliente que identifica sua aplicação para o servidor OAuth 2.0.

    https://login.windows.net/<client_id>/oauth2/authorize

O **método de pedido de autorização** Especifica como o pedido de autorização é enviado para o servidor OAuth 2.0. **Obter** está selecionada por predefinição.

A secção seguinte é onde o **URL de ponto final do Token**, **métodos de autenticação de cliente**, **token de acesso de enviar método**e **âmbito predefinido** estão especificados.

![Novo servidor][api-management-oauth2-server-3]

Um servidor do Azure Active Directory OAuth 2.0, o **URL do ponto final de Token** terá o seguinte formato, onde `<APPID>` contém o formato de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

A predefinição do **métodos de autenticação de cliente** é **básicas**e **token de acesso de enviar método** é **autorização cabeçalho**. Estes valores são configuradas nesta secção do formulário, juntamente com o **âmbito predefinido**.

A secção de **credenciais do cliente** contém o **ID de cliente** e o **segredo cliente**, que são obtidos durante o processo de criação e de configuração do seu servidor OAuth 2.0. Assim que o **ID de cliente** e o **segredo de cliente** estão especificados, a **redirect_uri** para o **código de autorização** é gerado. Este URI é utilizada para configurar o URL de resposta na configuração do seu servidor OAuth 2.0.

![Novo servidor][api-management-oauth2-server-4]

Se o **código de autorização conceder tipos** estiver definido para a **palavra-passe de proprietário do recurso**, a secção de **credenciais de palavra-passe de proprietário do recurso** é utilizada para especificar essas credenciais; caso contrário, pode deixá-lo em branco.

![Novo servidor][api-management-oauth2-server-5]

Assim que o formulário estiver concluído, clique em **Guardar** para guardar a configuração do servidor autorização API Management OAuth 2.0. Assim que a configuração do servidor é guardada, pode configurar APIs para utilizar esta configuração, conforme mostrado na secção seguinte.

## <a name="step2"> </a>Configurar uma API para utilizar a autorização de utilizador OAuth 2.0

Clique em **APIs** a partir do menu de **Gestão de API** à esquerda, clique no nome da API do pretendido, clique em **segurança**e, em seguida, selecione a caixa do **token 2.0**.

![Autorização de utilizador][api-management-user-authorization]

Selecione o **servidor de autorização** de pretendido a partir da lista pendente e clique em **Guardar**.

![Autorização de utilizador][api-management-user-authorization-save]

## <a name="step3"> </a>Testar a autorização de utilizador OAuth 2.0 no Portal do Programador

Depois de ter configurado o servidor de autorização OAuth 2.0 e configurado o seu API para utilizar esse servidor, pode testá-la ao aceder ao Portal do programador e uma API que entra em contacto.  Clique em **Developer portal para** no menu superior direito.

![Portal de programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API eco**.

![API eco][api-management-apis-echo-api]

>[AZURE.NOTE] Se tiver apenas uma API configurada ou visível à sua conta, em seguida, clicando em APIs leva-o diretamente para as operações para esse API.

Selecione a operação de **Obter recursos** , clique em **Abrir consola**e, em seguida, selecione o **código de autorização** a partir do menu pendente.

![Consola aberta][api-management-open-console]

Quando **código de autorização** está selecionada, é apresentada uma janela de pop-up com o formulário de início de sessão do fornecedor OAuth 2.0. Neste exemplo, o formulário de início de sessão é fornecido pelo Azure Active Directory.

>[AZURE.NOTE] Se tiver desativados de pop-ups ser-lhe-á pedido para ativá-las pelo browser. Depois de ativá-las, selecione de **código de autorização** novamente e o início de sessão no formulário serão apresentados.

![Início de sessão][api-management-oauth2-signin]

Depois de ter a sessão iniciada no, os **cabeçalhos de pedido** são povoada com um `Authorization : Bearer` cabeçalho que autoriza o pedido.

![Pedir token de cabeçalho][api-management-request-header-token]

Neste momento pode configurar os valores pretendidos para os restantes parâmetros e submeter o pedido. 

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre como utilizar a gestão de API e OAuth 2.0, consulte o vídeo seguinte e a que o acompanha [artigo](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao Azure API gestão]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[Web App-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps


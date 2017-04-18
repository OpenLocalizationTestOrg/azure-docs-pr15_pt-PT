<properties
    pageTitle="Segurança OAUTH SaaS conectores e API Apps | Azure"
    description="Ler sobre token de segurança na conectores e Apps API na aplicação de serviço de Azure; arquitetura de microservices; saas"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>Saiba mais sobre segurança OAUTH SaaS conectores

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

Muitas do Software como um conectores de serviço (SaaS) como o Facebook, Twitter, DropBox e assim sucessivamente exigir que os utilizadores autenticar a utilizar o protocolo OAUTH.  Ao utilizar estes conectores SaaS a partir das aplicações de lógica, fornecemos uma experiência de utilizador simplificada onde clicar em "Autorizar" no estruturador de lógica aplicações. Quando lhe **autorizar**, lhe for solicitado que inicie sessão em (se ainda não estiver) e forneça o seu consentimento para ligar ao serviço de SaaS em seu nome. Depois de fornecer consentimento e autorizar, as suas aplicações de lógica, em seguida, pode aceder a estes serviços SaaS.

## <a name="create-your-own-saas-app"></a>Criar a sua própria aplicação SaaS
Esta experiência simplificada é possível porque anteriormente criada e registada nossa aplicação nestes serviços SaaS.  Em certos casos, poderá querer registar e utilizar a sua própria aplicação.  Isto é necessário, por exemplo, quando pretender utilizar estes conectores SaaS nas suas aplicações personalizadas. Este exemplo utiliza a conexão DropBox, mas o processo é o mesmo para todas as conexões que dependem de OAUTH.

Ainda no contexto de lógica de aplicações, pode utilizar a sua própria aplicação em vez de utilizar a aplicação predefinida que fornecemos. Se o botão "Autorizar" Falha de ligar, pode tentar criar a sua própria aplicação. A seguinte lista apresenta estes passos para o conector de Twitter:

1. Abra a conexão Twitter no portal do Azure pré-visualização. Aceda a **Procurar** > **API aplicações**. Selecione a conexão Twitter:  
    ![][1]

2. Selecione **Definições** > **autenticação**:  
    ![][2]

3. Copie o valor de **Redirecionar URI** :  
    ![][3]

4. Aceda a [Twitter](http://apps.twitter.com) e **criar uma nova aplicação**. Na propriedade **URL de chamada de retorno** , cole o valor de **Redirecionar URI** copiado da conexão Twitter: ![][4]  
5. Quando é criada a sua aplicação do Twitter, selecione **chave e Tokens de acesso**. Copie estes valores.
6. Nas definições de autenticação de conector do Twitter, cole estes valores nas propriedades do **ID de cliente** e o **Segredo de cliente** :   
    ![][5]  
7. Guarde as suas definições de conector.  

Agora, deverá conseguir utilizar a conexão a partir das aplicações de lógica. Quando utiliza esta conexão a partir das aplicações de lógica, utilize a sua aplicação em vez da aplicação predefinida.  

> [AZURE.NOTE] Se tiver autorizado uma aplicação anteriormente, poderá ter de autorizar a aplicação.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

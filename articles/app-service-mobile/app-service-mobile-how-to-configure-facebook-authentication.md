<properties
    pageTitle="Como configurar a autenticação do Facebook para a sua aplicação de serviços de aplicação"
    description="Saiba como configurar a autenticação do Facebook para a sua aplicação de serviços de aplicação."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicação do Azure para utilizar o Facebook como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Facebook que tenha um endereço de e-mail verificado e um número de telemóvel. Para criar uma nova conta do Facebook, aceda a [facebook.com].

## <a name="register"> </a>Registar a sua aplicação ao Facebook

1. Inicie a sessão [portal do Azure]e navegue para a sua aplicação. Copie o **URL**. Irá utilizar este para configurar a sua aplicação do Facebook.

2. Outra janela do browser, navegue para o Web site de [Programadores do Facebook] e iniciar sessão com as suas credenciais de conta do Facebook.

3. (Opcional) Se não tiver já registado, clique em **aplicações** > **Registe-se como um programador**, em seguida, aceitar a política e siga os passos de registo.

4. Clique nas **minhas aplicações** > **Adicionar uma nova aplicação** > **Web site** > **Ignorar e criar o ID da aplicação**. 

5. Em **Nome a apresentar**, escreva um nome exclusivo para a sua aplicação, escreva o seu **E-Mail do contacto**, selecione uma **categoria** para a sua aplicação, em seguida, clique em **Criar ID da aplicação** e concluir a verificação de segurança. Isto leva-o para o dashboard para programadores para a sua nova aplicação do Facebook.

6. Em "Facebook Login", clique em **Começar**. Adicionar a aplicação **Redirecionar URI** **OAuth válido**redirecionar URIs, em seguida, clique em **Guardar alterações**. 

    > [AZURE.NOTE] URI de redireccionamento é o URL da sua aplicação acrescentado com o caminho, _/.auth/login/facebook/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que está a utilizar o esquema de HTTPS.

6. No painel de navegação esquerdo, clique em **Definições**. No campo **Secreta de aplicação** , clique em **Mostrar**, forneça a sua palavra-passe se solicitado, em seguida, tome nota dos valores de **ID da aplicação** e o **Segredo de aplicação**. Utilize estes mais tarde para configurar a aplicação no Azure.

    > [AZURE.IMPORTANT] O segredo aplicação é uma credencial de segurança importantes. Não partilhar este segredo com qualquer pessoa ou distribuí-la dentro de uma aplicação de cliente.

7. Conta do Facebook que foi utilizada para registar a aplicação é um administrador da aplicação. Neste momento, apenas os administradores podem iniciar sessão para esta aplicação. Para autenticar a outras contas de Facebook, clique em **Aplicação rever** e permitir **tornar < your aplicação nome-- > público** de ativar o acesso de público geral utilizando a autenticação do Facebook.

## <a name="secrets"> </a>Informações do Facebook adicionar à aplicação

1. Novamente no [portal do Azure], navegue para a sua aplicação. Clique em **Definições** > **autenticação / autorização**e certifique-se de que a **Aplicação de serviço de autenticação** está **ativada**.

2. Clique em **Facebook**, cole os valores de ID da aplicação e o segredo de aplicação que obteve anteriormente, opcionalmente, ative quaisquer âmbitos necessários a sua aplicação, em seguida, clique em **OK**.

    ![][0]

    Por predefinição, a aplicação de serviço fornece autenticação mas não restringir o acesso autorizado a sua APIs e o conteúdo do site. Tem de autorizar utilizadores no seu código de aplicação.

3. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados ao Facebook, defina a **ação a tomar quando não é autenticado pedido** ao **Facebook**. Isto requer que seja autenticado, todos os pedidos e todos os pedidos não autenticados redirecionados para o Facebook para autenticação.

4. Quando terminar de configurar autenticação, clique em **Guardar**.

Agora está pronto para utilizar o Facebook para autenticação na sua aplicação.

## <a name="related-content"> </a>Relacionados com o conteúdo

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Programadores do Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal do Azure]: https://portal.azure.com/

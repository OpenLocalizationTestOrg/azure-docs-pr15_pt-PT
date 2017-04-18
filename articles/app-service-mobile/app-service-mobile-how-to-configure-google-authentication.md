<properties
    pageTitle="Como configurar a autenticação do Google para a sua aplicação de serviços de aplicação"
    description="Saiba como configurar a autenticação do Google para a sua aplicação de serviços de aplicação."
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

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicação do Azure para utilizar o Google como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Google que tenha um endereço de e-mail verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registar as aplicações do Google

1. Inicie a sessão [portal do Azure]e navegue para a sua aplicação. Copie o **URL**, que utiliza mais tarde para configurar a sua aplicação do Google.

2. Navegue para o Web site [apis da Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) , inicie sessão com as credenciais da conta Google, clique em **Criar projeto**, forneça um **nome do projeto**, em seguida, clique em **Criar**.

3. Em **Social APIs** clique em **Google + API** e, em seguida, **Ativar**.

4. No painel de navegação esquerdo, **credenciais** > **OAuth consentimento ecrã**, em seguida, selecione o seu **endereço de E-Mail**, introduza um **Nome de produto**e clique em **Guardar**.

5. No separador de **credenciais** , clique em **criar credenciais** > **OAuth ID de cliente**, em seguida, selecione **aplicação Web**.

6. Cole o **URL** de aplicação de serviço que copiou anteriormente **Autorizados origens JavaScript**, em seguida, cole o seu redireccionar URI **Autorizados URI redirecionar**. O redirecionamento URI é o URL da sua aplicação acrescentado com o caminho, _/.auth/login/google/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Certifique-se de que está a utilizar o esquema de HTTPS. Em seguida, clique em **Criar**.

7. No ecrã seguinte, tome nota dos valores do ID de cliente e o segredo de cliente.


    > [AZURE.IMPORTANT]
    O segredo de cliente é uma credencial de segurança importantes. Não partilhar este segredo com qualquer pessoa ou distribuí-la dentro de uma aplicação de cliente.


## <a name="secrets"> </a>Google adicionar informações à aplicação

8. Novamente no [portal do Azure], navegue para a sua aplicação. Clique em **Definições**e, em seguida **autenticação / autorização**.

9. Se a autenticação / funcionalidade de autorização não estiver ativada, ative a transição para o **no**.

10. Clique em **Google**. Cole os valores de ID da aplicação e o segredo de aplicação que obteve anteriormente e, opcionalmente, ativar quaisquer âmbitos que requer a aplicação. Em seguida, clique em **OK**.

    ![][1]

    Por predefinição, a aplicação de serviço fornece autenticação mas não restringir o acesso autorizado a sua APIs e o conteúdo do site. Tem de autorizar utilizadores no seu código de aplicação.

17. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados pelo Google, defina a **ação a tomar quando não é autenticado pedido** para **Google**. Isto requer que seja autenticado, todos os pedidos e todos os pedidos não autenticados redirecionados para o Google para autenticação.

12. Clique em **Guardar**.

Agora está pronto para utilizar o Google para autenticação na sua aplicação.

## <a name="related-content"> </a>Relacionados com o conteúdo

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal do Azure]: https://portal.azure.com/


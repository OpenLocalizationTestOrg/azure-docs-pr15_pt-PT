<properties
    pageTitle="Como configurar a autenticação de Account Microsoft para a sua aplicação de serviços de aplicação"
    description="Saiba como configurar a autenticação de Account Microsoft para a sua aplicação de serviços de aplicação."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Microsoft Account

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicação do Azure para utilizar Account Microsoft como um fornecedor de autenticação. 

## <a name="register-microsoft-account"> </a>Registar a sua aplicação com a conta Microsoft

1. Inicie a sessão [portal do Azure]e navegue para a sua aplicação. Copie o **URL**, que mais tarde utiliza para configurar a sua aplicação com Account Microsoft.

2. Navegue até à página [As minhas aplicações] no Account Microsoft Developer Center e iniciar sessão com a sua conta Microsoft, se necessário.

3. Clique em **Adicionar uma aplicação**, em seguida, escreva um nome da aplicação e clique em **Criar aplicação**.

4. Tome nota do **ID da aplicação**, tal como irá necessitar mais tarde. 

5. Em "Plataformas", clique em **Adicionar plataforma** e selecione "Web".

6. Em "Redirecionar URIs" fornecer o ponto final para a sua aplicação, em seguida, clique em **Guardar**. 
 
    >[AZURE.NOTE]URI de redireccionamento é o URL da sua aplicação acrescentado com o caminho, _/.auth/login/microsoftaccount/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Certifique-se de que está a utilizar o esquema de HTTPS.

7. Em "Aplicação segredos", clique em **Gerar nova palavra-passe**. Tome nota do valor que é apresentada. Depois de sair da página, não será novamente apresentada.


    > [AZURE.IMPORTANT] A palavra-passe é uma credencial de segurança importantes. Não partilhar a palavra-passe com qualquer pessoa ou distribuí-la dentro de uma aplicação de cliente.

## <a name="secrets"> </a>Informações da conta do Microsoft de adicionar a sua aplicação de serviço de aplicação

1. Novamente no [portal do Azure], navegue para a sua aplicação, clique em **Definições** > **autenticação / autorização**.

2. Se a autenticação / funcionalidade de autorização não estiver ativada, ligue- **a**.

3. Clique em **Conta Microsoft**. Cole os valores de ID da aplicação e palavra-passe que obteve anteriormente e, opcionalmente, ative quaisquer âmbitos que requer a aplicação. Em seguida, clique em **OK**.

    ![][1]

    Por predefinição, a aplicação de serviço fornece autenticação mas não restringir o acesso autorizado a sua APIs e o conteúdo do site. Tem de autorizar utilizadores no seu código de aplicação.

4. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados por conta Microsoft, defina a **ação a tomar quando não é autenticado pedido** conta **Microsoft**. Isto requer que seja autenticado, todos os pedidos e todos os pedidos não autenticados redirecionados para a conta Microsoft para autenticação.

5. Clique em **Guardar**.

Agora está pronto para utilizar o Microsoft Account para autenticação na sua aplicação.

## <a name="related-content"> </a>Relacionados com o conteúdo

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Minhas aplicações]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/

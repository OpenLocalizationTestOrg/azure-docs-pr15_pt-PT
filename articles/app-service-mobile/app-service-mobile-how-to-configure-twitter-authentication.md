<properties
    pageTitle="Como configurar a autenticação do Twitter para a sua aplicação de serviços de aplicação"
    description="Saiba como configurar a autenticação do Twitter para a sua aplicação de serviços de aplicação."
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

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicação do Azure para utilizar Twitter como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Twitter que tenha um e-mail verificado endereço e número de telefone. Para criar uma nova conta do Twitter, aceda a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registar a aplicação com Twitter


1. Inicie a sessão [portal do Azure]e navegue para a sua aplicação. Copie o **URL**. Irá utilizar este para configurar a sua aplicação do Twitter.

2. Navegue para o Web site [Programadores Twitter] , inicie sessão com as credenciais da conta Twitter e clique em **Criar nova aplicação**.

3. Escreva o **nome** e uma **Descrição** para a nova aplicação. Copie do **URL** para o valor de **Web site a aplicação** . Em seguida, para o **URL de chamada de retorno**, cole o **URL de chamada de retorno** que copiou anteriormente. Este é o seu gateway aplicação Mobile acrescentado com o caminho, _/.auth/login/twitter/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que está a utilizar o esquema de HTTPS.

3.  Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **criar a aplicação Twitter**. Este procedimento regista a aplicação de apresenta os detalhes da aplicação.

4. Clique no separador **Definições** , selecione **Permitir esta aplicação ser utilizado para iniciar sessão com Twitter**e, em seguida, clique em **Definições de atualização**.

5. Selecione o separador **chaves e Tokens de acesso** . Tome nota dos valores da **Chave de consumidor (API chave)** e **palavra-passe consumidor (API secreta)**.

    > [AZURE.NOTE] O segredo consumidor é uma credencial de segurança importantes. Não partilhar este segredo com qualquer pessoa ou distribuí-lo com a sua aplicação.


## <a name="secrets"> </a>Informações adicionar Twitter à aplicação

13. Novamente no [portal do Azure], navegue para a sua aplicação. Clique em **Definições**e, em seguida **autenticação / autorização**.

14. Se a autenticação / funcionalidade de autorização não estiver ativada, ative a transição para o **no**.

15. Clique em **Twitter**. Cole o ID da aplicação e o segredo de aplicação valores que obteve anteriormente. Em seguida, clique em **OK**.

    ![][1]

    Por predefinição, a aplicação de serviço fornece autenticação mas não restringir o acesso autorizado a sua APIs e o conteúdo do site. Tem de autorizar utilizadores no seu código de aplicação.

17. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados por Twitter, defina a **ação a tomar quando não é autenticado pedido** para **Twitter**. Isto requer que seja autenticado, todos os pedidos e todos os pedidos não autenticados redirecionados para o Twitter para autenticação.

17. Clique em **Guardar**.

Agora está pronto para utilizar Twitter para autenticação na sua aplicação.

## <a name="related-content"> </a>Relacionados com o conteúdo

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Programadores do twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

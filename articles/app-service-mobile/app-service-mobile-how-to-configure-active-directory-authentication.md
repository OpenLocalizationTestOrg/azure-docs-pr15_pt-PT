<properties
    pageTitle="Como configurar a autenticação do Azure Active Directory para a sua aplicação de serviços de aplicação"
    description="Saiba como configurar a autenticação do Azure Active Directory para a sua aplicação de serviços de aplicação."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
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

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar os serviços de aplicação do Azure para utilizar o Azure Active Directory como um fornecedor de autenticação.

## <a name="express"> </a>Configurar Azure Active Directory utilizando as definições express

13. No [portal do Azure], navegue para a sua aplicação. Clique em **Definições**e, em seguida, **/ Autorização de autenticação**.

14. Se a autenticação / funcionalidade de autorização não estiver ativada, ative a transição para o **no**.

15. Clique **Azure Active Directory**e, em seguida, clique em **Express** em **Modo de gestão**.

16. Clique em **OK** para registar a aplicação no Azure Active Directory. Isto irá criar um novo registo. Se quiser escolher um registo existente como alternativa, clique em **selecionar uma aplicação existente** e, em seguida, para procurar o nome de um registo criado anteriormente no seu inquilino.
Clique em registo para a selecionar e clique em **OK**. Em seguida, clique em **OK** na pá de definições do Azure Active Directory.

    ![][0]

    Por predefinição, a aplicação de serviço fornece autenticação mas não restringir o acesso autorizado a sua APIs e o conteúdo do site. Tem de autorizar utilizadores no seu código de aplicação.

17. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados pelo Azure Active Directory, defina a **ação a tomar quando não é autenticado pedido** para **Iniciar sessão com o Azure Active Directory**. Isto requer que seja autenticado, todos os pedidos e todos os pedidos não autenticados redirecionados para o Azure Active Directory para autenticação.

17. Clique em **Guardar**.

Agora está pronto para utilizar o Azure Active Directory para autenticação na sua aplicação.

## <a name="advanced"> </a>(Método alternativo) configurar manualmente o Azure Active Directory com as definições avançadas
Também pode optar por fornecem definições de configuração de manualmente. Este é a solução preferida caso o inquilino AAD que pretende utilizar é a diferença entre o inquilino com as quais que inicie sessão no Azure. Para concluir a configuração, primeiro tem de criar um registo no Azure Active Directory e, em seguida, tem de fornecer algumas dos detalhes de registo para a aplicação de serviço.

### <a name="register"> </a>Registar a sua aplicação com o Azure Active Directory

1. Inicie a sessão [portal do Azure]e navegue para a sua aplicação. Copie o **URL**. Irá utilizar este para configurar a sua aplicação do Azure Active Directory.

3. Inicie sessão no [portal clássica Azure] e navegue para o **Active Directory**.

    ![][2]

4. Selecione o seu diretório e, em seguida, selecione o separador de **aplicações** na parte superior. Clique em **Adicionar** na parte inferior para criar um novo registo de aplicação.

5. Clique em **Adicionar uma aplicação que está a desenvolver a minha organização**.

6. No Assistente de aplicação de adicionar, introduza um **nome** para a sua aplicação e clique no tipo **E/ou de aplicações Web Web API** . Em seguida, clique em continuar.

7. Na caixa **URL no início de sessão** , cole o URL da aplicação que copiou anteriormente. Introduza esse mesmo URL na caixa **URI de ID da aplicação** . Em seguida, clique em continuar.

8. Assim que a aplicação for adicionada, clique no separador **Configurar** . Edite o **URL de responder** em **Single Sign-on** para ser o URL da sua aplicação acrescentado com o caminho, _/.auth/login/aad/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de que está a utilizar o esquema de HTTPS.

    ![][3]

9. Clique em **Guardar**. Em seguida, copie o **Código do cliente** para a aplicação. Irá configurar a sua aplicação para utilizar este mais tarde.

10. Na barra de comando do fundo, clique em **Pontos finais de vista**e, em seguida, copie o URL do **Documento de metadados de Federação** e transferir esse documento ou navegue até ao mesmo num browser.

11. Dentro do elemento de **EntityDescriptor** de raiz, deverá haver um atributo **ID da entidade** do formulário `https://sts.windows.net/` seguido por um GUID específico ao seu inquilino (denominado "ID de inquilino"). Copiar este valor - irá servir do seu **URL emissor**. Irá configurar a sua aplicação para utilizar este mais tarde.

### <a name="secrets"> </a>Adicionar Azure informações do Active Directory à aplicação

13. Novamente no [portal do Azure], navegue para a sua aplicação. Clique em **Definições**e, em seguida, **/ Autorização de autenticação**.

14. Se a funcionalidade de autenticação/autorização não estiver ativada, ative a transição para o **no**.

15. Clique **Azure Active Directory**e, em seguida, clique em **Avançadas** , em **Modo de gestão**. Cole o valor de ID de cliente e o URL de emissor que obteve anteriormente. Em seguida, clique em **OK**.

    ![][1]

    Por predefinição, a aplicação de serviço fornece autenticação mas não restringir o acesso autorizado a sua APIs e o conteúdo do site. Tem de autorizar utilizadores no seu código de aplicação.

17. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados pelo Azure Active Directory, defina a **ação a tomar quando não é autenticado pedido** para **Iniciar sessão com o Azure Active Directory**. Isto requer que seja autenticado, todos os pedidos e todos os pedidos não autenticados redirecionados para o Azure Active Directory para autenticação.

17. Clique em **Guardar**.

Agora está pronto para utilizar o Azure Active Directory para autenticação na sua aplicação.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar uma aplicação de cliente nativo

Azure Active Directory também permite-lhe registar nativos clientes, que fornece maior controlo sobre permissões de mapeamento. Precisa de isto se pretender executar os inícios de sessão através de uma biblioteca como a **Biblioteca de autenticação do Active Directory**.

1. Navegue para o **Active Directory** no [Azure portal clássica].

2. Selecione o seu diretório e, em seguida, selecione o separador de **aplicações** na parte superior. Clique em **Adicionar** na parte inferior para criar um novo registo de aplicação.

3. Clique em **Adicionar uma aplicação que está a desenvolver a minha organização**.

4. No Assistente de aplicação de adicionar, introduza um **nome** para a sua aplicação e clique no tipo de **Aplicação de cliente nativa** . Em seguida, clique em continuar.

5. Na caixa **URI de redirecionar** , introduza o ponto final de _/.auth/login/done_ do seu site, utilizando o esquema de HTTPS. Este valor deverá ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_. Se criar uma aplicação do Windows, em vez disso, utilize o [pacote SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.

6. Assim que a aplicação nativa foi adicionada, clique no separador **Configurar** . Localizar o **ID de cliente** e tome nota deste valor.

7. Desloque-se a página para baixo para a secção **permissões para outras aplicações** e clique em **Adicionar aplicação**.

8. Procurar a aplicação web que lhe registado anteriormente e clique no ícone de adição. Em seguida, clique em verificação para fechar a caixa de diálogo. Se a aplicação web não pode ser encontrado, navegue para o seu registo e adicionar um novo URL de responder (por exemplo, a versão HTTP do seu URL atual), clique em Guardar e, em seguida, repita estes passos – a aplicação deve ser apresentada na lista.

9. Na nova entrada que acabou de adicionar, abra no menu pendente **Delegada permissões** e selecione **acesso (nome)**. Em seguida, clique em **Guardar**.

Agora ter configurado uma aplicação de cliente nativo qual pode aceder a sua aplicação de serviço de aplicação.

## <a name="related-content"> </a>Relacionados com o conteúdo

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure portal clássico]: https://manage.windowsazure.com/
[alternative method]:#advanced

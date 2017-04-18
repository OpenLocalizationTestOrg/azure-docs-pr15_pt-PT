<properties
    pageTitle="Como ativar a publicação de aplicações de cliente nativa com aplicações do proxy | Microsoft Azure"
    description="Abrange como ativar aplicações de cliente nativo comunicar com o Azure AD aplicação Proxy Connector para fornecer acesso remoto seguro as suas aplicações no local."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Como ativar aplicações de cliente nativo interagir com aplicações do proxy

Azure Active Proxy de aplicação do diretório amplamente é utilizado para publicar aplicações de browser tal como o SharePoint, o Outlook Web Access e linha personalizada de aplicações empresariais. Também pode ser utilizado para publicar aplicações de cliente nativo, que diferem a partir das aplicações web, uma vez que são instalados num dispositivo. Isto é feito através do Azure AD emitido tokens que são enviadas em cabeçalhos de HTTP autorizar padrão de apoio.

![Relação entre os utilizadores finais, Azure Active Directory e aplicações publicadas](./media/active-directory-application-proxy-native-client/richclientflow.png)

O método recomendado para publicar estas aplicações é utilizar a biblioteca de autenticação AD Azure, que tratará de todos os sem problemas de autenticação e suporta vários ambientes de cliente diferente. Proxy de aplicação se adequa a [Aplicação nativa Web API cenário](active-directory-authentication-scenarios.md#native-application-to-web-api). O processo para consegui-lo é da seguinte forma:

## <a name="step-1-publish-your-application"></a>Passo 1: Publicar a sua aplicação

Publicar a aplicação de proxy, tal como faria com qualquer outra aplicação, atribuir utilizadores e conceder-lhes premium ou licenças básicas. Para obter mais informações, consulte [publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Passo 2: Configurar a aplicação

Configure a aplicação nativa da seguinte forma:

1. Inicie sessão no portal do Azure clássico.
2. Selecione o ícone do Active Directory no menu à esquerda e, em seguida, selecione o seu diretório.
3. No menu superior, clique em **aplicações**. Se a aplicações que não foram adicionadas ao seu diretório, esta página mostrar apenas a ligação **Adicionar uma aplicação** . Clique na ligação ou em alternativa pode clicar no botão **Adicionar** na barra de comandos.
4. Na página **que pretende fazer** , clique na ligação para **Adicionar uma aplicação que está a desenvolver a minha organização**.
5. Na página **diga-nos sobre a aplicação** , especifique um nome para a sua aplicação e selecione a **aplicação cliente nativa**. Clique no ícone de seta para continuar.
6. Na página **informações da aplicação** , fornecer o **URI redirecionar** para a aplicação de cliente nativo, em seguida, clique em marca de verificação para concluir.

A aplicação foi adicionada e, irão ser encaminhado para a página de início rápido para a sua aplicação.

## <a name="step-3-grant-access-to-other-applications"></a>Passo 3: Conceder acesso a outras aplicações

Ativar a aplicação nativa expostos para outras aplicações no seu diretório:

1. No menu superior, clique em **aplicações**, selecione a nova aplicação nativa e, em seguida, clique em **Configurar**.
2. Desloque-se até à secção **permissões para outras aplicações** . Clique no botão **Adicionar aplicação** e selecione a aplicação de proxy ao qual pretende conceder acesso a aplicação nativa a e, clique na marca de verificação no canto inferior direito. No menu pendente de **Permissões de delegado** , selecione a permissão de nova.

![Permissões para captura de ecrã outras aplicações - Adicionar aplicação](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passo 4: Editar a biblioteca de autenticação do Active Directory

Edite o código da aplicação nativa no contexto de autenticação do Active Directory autenticação biblioteca (ADAL) para incluir o seguinte:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

As variáveis devem ser substituídas da seguinte forma:

- Pode encontrar **TenantId** no GUID no URL da página de **configuração** da aplicação, à direita após "/ diretório /".
- **URL de front-end** é o URL de front-end introduzido na aplicação do Proxy e pode ser encontrado na página **configuração** da aplicação de proxy.
- **Id de cliente** da aplicação nativa podem ser encontradas na página **Configurar** da aplicação nativa.
- **Redirecionar URI da aplicação nativa** podem ser encontradas na página **Configurar** da aplicação nativa.

![Configurar a nova aplicação nativa captura de ecrã da página](./media/active-directory-application-proxy-native-client/new_native_app.png)

Para mais informações sobre o fluxo de aplicação nativa, consulte o artigo [aplicação nativa Web API](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Consulte também

- [Publicar aplicações utilizando o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhar com aplicações deverá ter em consideração em afirmações](active-directory-application-proxy-claims-aware-apps.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)

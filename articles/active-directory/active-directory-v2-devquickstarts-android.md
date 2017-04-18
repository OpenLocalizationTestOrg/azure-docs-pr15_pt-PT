<properties
    pageTitle="Aplicação do Azure Active Directory 2.0 Android | Microsoft Azure"
    description="Como criar uma aplicação Android que sinais em utilizadores com tanto pessoal conta Microsoft e trabalho ou escola contas e chamadas a API do gráfico ao utilizar bibliotecas de terceiros."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

#  <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar iniciar sessão a uma aplicação Android através de uma biblioteca de terceiros com API Graph utilizando o ponto final 2.0

A plataforma de identidade do Microsoft utiliza padrões abertos como oauth2 ainda e OpenID ligar. Os programadores podem utilizar qualquer biblioteca que pretendem integrar com os nossos serviços. Para ajudar os programadores a utilizar o nossa plataforma com outras bibliotecas, podemos escreveu tutoriais alguns como esta para demonstrar como configurar bibliotecas de terceiros para ligar a plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação de oauth2 RFC6749 ainda](https://tools.ietf.org/html/rfc6749) podem ligar a plataforma de identidade da Microsoft.

Com a aplicação que cria este tutorial, os utilizadores podem iniciar sessão na sua organização e, em seguida, procure próprios na sua organização ao utilizar a API do gráfico.

Se estiver habituado a oauth2 ainda ou OpenID ligar, muita esta configuração de exemplo poderá não adequadas para si. Recomendamos que leia [2.0 protocolos - OAuth 2.0 autorização de código de um fluxo](active-directory-v2-protocols-oauth-code.md) de fundo.

> [AZURE.NOTE] Algumas funcionalidades da nossa plataforma que têm uma expressão nas normas oauth2 ainda ou OpenID ligar, como o acesso condicional e gestão da política Intune, requerem a utilizar o nossa abrir origem das bibliotecas de identidade do Microsoft Azure.

O ponto final 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades.

> [AZURE.NOTE] Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).


## <a name="download-the-code-from-github"></a>Transferir o código do GitHub
O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Para segui-los, pode [Transferir a estrutura da aplicação como um. zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Também tem apenas pode transferir o exemplo e começar imediatamente:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registe-se uma aplicação
Criar uma nova aplicação no [portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou siga os passos detalhados sobre como [registar uma aplicação com o ponto final 2.0](active-directory-v2-app-registration.md).  Certifique-se de que:

- Copie o **Id da aplicação** que está atribuída a sua aplicação de uma vez que irá precisar mais rapidamente.
- Adicione a plataforma **móvel** para a sua aplicação.

> Nota: O portal de registo de aplicação fornece um valor de **URI redirecionar** . No entanto, neste exemplo tem de utilizar o valor predefinido de `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Transferir a biblioteca de terceiros NXOAuth2 e criar uma área de trabalho

Para este tutorial, irá utilizar OIDCAndroidLib a partir do GitHub, que é uma biblioteca de oauth2 ainda com base no código OpenID ligar do Google. Esta versão implementa o perfil de aplicação nativa e suporta o ponto final de autorização do utilizador. Estes são tudo o que vai precisar de integrar com a plataforma de identidade da Microsoft.

Clonar a repo OIDCAndroidLib para o seu computador.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Configurar o seu ambiente Android Studio

1. Criar um novo projeto de Android Studio e aceite as predefinições no assistente.

    ![Criar novo projeto no Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

    ![Dispositivos Android de destino](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

    ![Adicionar uma actividade Mobile](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

2. Para configurar o seu módulos de projeto, mova o repo clonado para a localização do projeto. Pode também criar projeto e, em seguida, cloná-lo diretamente para a localização do projeto.

    ![Módulos de projeto](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

3. Abra as definições de módulos do projeto utilizando o menu de contexto ou utilizando o atalho Ctrl + Alt + Maj + S.

    ![Definições de módulos do Project](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

4. Remova o módulo de aplicação predefinido porque quiser que apenas as definições de contentor de projeto.

    ![O módulo de aplicação predefinido](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

5. Importar módulos do repo clonado ao projeto atual.

    ![Importar gradle projecto](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG)
    ![criar nova página de módulo](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

6. Repita estes passos para o `oidlib-sample` módulo.

7. Verifique as dependências de oidclib na `oidlib-sample` módulo.

    ![dependências de oidclib no módulo de oidlib amostras](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

8. Clique em **OK** e aguarde que gradle sincronização.

    O settings.gradle deve aspeto:

    ![Captura de ecrã do settings.gradle](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

9. Criar a aplicação de exemplo para se certificar de que a amostra a ser executado corretamente.

    Não poderá Utilize esta opção ainda com o Azure Active Directory. Irá precisamos de configurar algumas pontos finais de pela primeira vez. Este é para se certificar de que não têm um problemas de Android Studio antes de Vamos começar a personalizar a aplicação de exemplo.

10. Criar e executar `oidlib-sample` como o destino no Android Studio.

    ![Progresso da compilação oidlib amostras](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

11. Eliminar o `app ` directory que foi deixado quando remover o módulo do projeto, porque Android Studio não eliminá-lo para a segurança.

    ![Estrutura de ficheiro que inclui o diretório de aplicações](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

12. Abra o menu **Editar configurações** para remover a configuração de executar que também foi deixada quando tiver removido o módulo do projeto.

    ![Menu de configurações editar](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![executar a configuração da aplicação](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Configurar os pontos finais da amostra

Agora que tem o `oidlib-sample` executar com êxito, editar algumas os pontos finais para o trabalho com o Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Configurar o seu cliente ao editar o ficheiro oidc_clientconf.xml

1. Uma vez que monetários oauth2 ainda estiver a utilizar apenas para obter um token e ligar a API do gráfico, defina o cliente para fazer a oauth2 ainda apenas. OIDC seja reencaminhado um exemplo posterior.

    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```

2. Configure o seu ID de cliente que recebeu a partir do portal de registo.

    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```

3. Configure o redirecionamento URI com aquele abaixo.

    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```

4. Configure o seu âmbitos de que precisa para aceder a API do gráfico.

    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

O `User.Read` valor na `oidc_scopes` permite-lhe ler o perfil básico a com a sessão iniciada do utilizador.
Pode obter mais informações sobre todos os âmbitos disponíveis na [Microsoft Graph âmbitos de permissão](https://graph.microsoft.io/docs/authorization/permission_scopes).

Se pretender que explicações sobre `openid` ou `offline_access` como âmbitos nas OpenID ligar, consulte o artigo [2.0 protocolos - OAuth 2.0 autorização código fluxo](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Configurar o seu os pontos finais de cliente ao editar o ficheiro oidc_endpoints.xml

- Abrir o `oidc_endpoints.xml` ficheiro e efetuar as seguintes alterações:

    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Estes pontos finais nunca deverão alterar se estiver a utilizar oauth2 ainda como protocolo de.

> [AZURE.NOTE]
Os pontos finais para `userInfoEndpoint` e `revocationEndpoint` atualmente não suportadas pelo Azure Active Directory. Se deixar estas com o valor de exemplo.com predefinido, será lembrado que não estão disponíveis no exemplo :-)


## <a name="configure-a-graph-api-call"></a>Configurar uma chamada de API do gráfico

- Abrir o `HomeActivity.java` ficheiro e efetuar as seguintes alterações:

    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Aqui uma chamada de Graph API simple devolve os nossos informações.

Estas são todas as alterações que tem de fazer. Executar o `oidlib-sample` aplicação e clique em **Iniciar sessão**.

Depois de ter autenticados com êxito, selecione o botão de **Pedir recurso protegido por** para testar a sua chamada para a API do gráfico.

## <a name="get-security-updates-for-our-product"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações sobre incidentes de segurança visitando o [TechCenter de segurança](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.

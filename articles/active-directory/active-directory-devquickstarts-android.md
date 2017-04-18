<properties
    pageTitle="Azure AD Android, introdução | Microsoft Azure"
    description="Como criar uma aplicação do Android que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Integrar o Azure AD para uma aplicação Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Se está a desenvolver uma aplicação de ambiente de trabalho, Azure AD torna simples e de fácil para si autenticar os seus utilizadores utilizando as respetivas contas do Active Directory.  Também lhe permite a aplicação de forma segura consumir qualquer web API protegido por Azure AD, tais como as APIs do Office 365 ou a API do Azure.

Para clientes Android que precisam para aceder aos recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL.  Objetivo de exclusivo do ADAL vida é tornam mais fácil para a sua aplicação obter tokens de acesso.  Para demonstrar tal como é fácil, aqui vamos irá criar uma aplicação de lista de itens pendentes Android que:

-   Obtém acesso tokens para ligar a uma lista das ações a fazer API utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Obtém lista um utilizador de acções a fazer
-   Utilizadores de sinais saída.

Para começar, terá de um inquilino do Azure AD na qual pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [Saiba como obter uma](active-directory-howto-tenant.md).

> [AZURE.TIP] Experimente a pré-visualização do nosso novo [developer portal para](https://identity.microsoft.com/Docs/Android) irá ajudá-lo a começar a trabalhar rapidamente com o Azure Active Directory em apenas alguns minutos!  Portal do programador irá guiá-lo durante o processo de registo de uma aplicação e integrar o seu código Azure AD.  Quando tiver terminado, terá uma aplicação simple que pode autenticar os utilizadores no seu inquilino e back-end que podem aceitar tokens e efetua validação. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Passo 1: Transferir e executar o servidor de exemplo do Node.js REST API TODO

Este exemplo é escrito especificamente para trabalhar contra nosso exemplo existente para criar um inquilino única acções a fazer REST API para o Microsoft Azure Active Directory. Este é um pré necessários para o guia de introdução.

Para obter informações sobre como configurar esta tarefa, visite a nossa amostras existentes aqui:

* [Serviço do Microsoft Azure Active Directory exemplo REST API para Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Passo 2: Registar a Web API com o seu inquilino do Microsoft Azure AD

**O que estou a posso fazer?**

*Microsoft Active Directory suporta a adição de dois tipos de aplicações. Web APIs que oferecem serviços para utilizadores e aplicações (quer na web ou uma aplicação em execução num dispositivo) e aceder a essas APIs da Web. Neste passo está a registar a API Web estiver a executar o localmente para testar este exemplo. Normalmente, esta API Web seria um serviço de resto que está a perguntar se pretende uma aplicação para aceder a funcionalidade. Microsoft Azure Active Directory pode proteger qualquer ponto final!*

*Aqui vamos são partindo do princípio de está a registar o TODO REST API acima referenciada, mas isto funciona para qualquer API Web motivos ter Azure Active Directory para proteger.*

Passos para registar uma API Web com a Microsoft Azure AD

1. Inicie sessão no [portal de gestão Azure](https://manage.windowsazure.com).
2. Clique em do Active Directory na navegação no lado esquerdo
3. Clique no inquilino do diretório onde quiser para registar a aplicação de exemplo.
4. Clique no separador aplicações.
5. No nível, clique em Adicionar.
6. Clique em "Adicionar uma aplicação a minha organização está a desenvolver".
7. Introduza um nome amigável para, selecione a aplicação, por exemplo, "TodoListService", "Web e/ou aplicação Web API" e clique em seguinte.
8. Para o URL de início de sessão, introduza o URL de base para o exemplo, o que está por predefinição `https://localhost:8080`.
9. Para URI de ID da aplicação, introduza `https://<your_tenant_name>/TodoListService`, substituindo `<your_tenant_name>` com o nome do seu inquilino do Azure AD.  Clique em OK para concluir o registo.
10. Enquanto ainda estiver no portal do Azure, clique no separador Configurar da sua aplicação.
11. **Encontrar o valor de ID de cliente e copiá-la preparação**, terá este mais tarde quando configurar a sua aplicação.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Passo 3: Registar a aplicação de Android Native Client de exemplo

Registar a sua aplicação web é o primeiro passo. Em seguida, terá de indicar ao Azure Active Directory sobre a aplicação também. Esta opção permite-a sua aplicação comunicar com a API Web apenas registado

**O que estou a posso fazer?**  

*Conforme descrito acima, Microsoft Azure Active Directory suporta a adição dois tipos de aplicações. Web APIs que oferecem serviços para utilizadores e aplicações (quer na web ou uma aplicação em execução num dispositivo) e aceder a essas APIs da Web. Neste passo está a registar a aplicação neste exemplo. Deve fazê-lo por ordem para esta aplicação possam pedido para aceder a API Web que apenas registado. Azure Active Directory irá recusar mesmo para permitir que a aplicação pedir iniciar sessão, a menos que está registado! Que faz parte de segurança do modelo.*

*Aqui vamos são partindo do princípio de está a registar esta aplicação de exemplo acima referenciada, mas isto funciona para qualquer aplicação que está a desenvolver.*

**Por que razão estou a posso colocação de uma aplicação e uma Web API um inquilino?**

*À medida que poderá ter estimado, poderia criar uma aplicação que acede a uma API externa que está registada no Azure Active Directory a partir de outro inquilino. Se fazê-lo, os seus clientes serão pedidos para aceitar a utilização da API na aplicação. A parte totalmente é, a biblioteca de autenticação do Active Directory para iOS leva-o até o cuidado deste consentimento para si! Como podemos obter funcionalidades mais avançadas, verá que esta é uma parte importante do trabalho necessário para aceder a obter o conjunto de APIs da Microsoft a partir do Azure e o Office, bem como qualquer outro fornecedor de serviços. Por agora, porque registado o seu Web API e a aplicação ao mesmo inquilino não verá qualquer solicita consentimento. Este nome costuma as maiúsculas/minúsculas se estiver a desenvolver uma aplicação apenas para a sua própria empresa utilizar.*

1. Inicie sessão no [portal de gestão Azure](https://manage.windowsazure.com).
2. Clique em do Active Directory na navegação no lado esquerdo
3. Clique no inquilino do diretório onde quiser para registar a aplicação de exemplo.
4. Clique no separador aplicações.
5. No nível, clique em Adicionar.
6. Clique em "Adicionar uma aplicação a minha organização está a desenvolver".
7. Introduza um nome amigável para a aplicação, por exemplo "TodoListClient-Android", selecione "nativo aplicação cliente" e clique em seguinte.
8. Para URI redirecionar, introduza `http://TodoListClient`.  Clique em Concluir.
9. Clique no separador Configurar da aplicação.
10. Encontrar o valor de ID de cliente e copiá-la preparação, terá de isto mais tarde quando configurar a sua aplicação.
11. Em "Permissões para outras aplicações", clique em "Adicionar aplicação".  Selecione "Outros" no menu pendente "Mostrar" e, clique na marca de verificação maiúscula.  Localize e clique na TodoListService e clique na marca de verificação da parte inferior para adicionar a aplicação.  Selecione "Acesso TodoListService" do menu pendente "Permissões de delegado" e guarde a configuração.



Para criar com Maven, pode utilizar o pom.xml ao nível superior

  * Clonar este repo para um diretório da sua escolha:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Siga os passos na [secção Prerequests para configurar o seu maven para android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configuração emulador com SDK 19
  * Vá para a pasta de raiz onde clonar que o repo
  * Execute o comando: instalar o mvn LIMPARB
  * Alterar o directório para o exemplo de guia de introdução: cd samples\hello
  * Execute o comando: mvn android: implementar android: executar
  * Deverá visualizar lançamento da aplicação
  * Introduza as credenciais de utilizador de teste para experimente!

Serão também submetidos para caixa pacotes junto do pacote de aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Passo 4: Transferir Android ADAL e adicioná-lo à área de trabalho Eclipse

Efetuámos-la mais facilmente dispõe de várias opções para utilizar esta biblioteca no seu projeto Android:

* Pode utilizar o código de origem para importar esta biblioteca para Eclipse e ligação para a sua aplicação.
* Se utilizar o Android Studio, pode utilizar o formato do pacote *aar* e os binários de referência.

####<a name="option-1-source-zip"></a>Opção 1: Postal de origem

Para transferir uma cópia do código fonte, clique em "ZIP transferir" no lado direito da página ou clique [aqui](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Opção 2: Origem através do Git

Para obter o código de origem do SDK através do git, escreva apenas:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Opção 3: Binários através do Gradle

Pode obter os binários do Maven central repo. Pacote AAR pode ser incluído da seguinte forma no seu projeto no AndroidStudio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Opção 4: aar através do Maven

Se estiver a utilizar o plug-in m2e Eclipse, pode especificar a dependência no seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Opção 5: jar pacote dentro de efectuar pasta
Pode obter o ficheiro para caixa a partir do maven a repo e largue os *efectuar* pasta no seu projeto. Tem de copiar os recursos necessários ao seu projeto, assim, uma vez que os pacotes de para caixa não incluam-las.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Passo 5: Adicionar referências a Android ADAL ao seu projeto


2. Adicione uma referência ao seu projeto e especificá-la como uma biblioteca Android. Se não tiver a certeza como fazer isto, [Clique aqui para obter mais informações] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Adicionar a dependência de projeto para depuração às definições do project

4. Atualize o ficheiro de AndroidManifest.xml do seu projeto para incluir:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Crie uma instância da AuthenticationContext na sua atividade principal. Os detalhes desta chamada estão fora do âmbito este ficheiro Leia-me, mas pode obter um bom início verificando a [Amostra de cliente nativo Android](https://github.com/AzureADSamples/NativeClient-Android). Abaixo está um exemplo:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Nota: mContext é um campo na sua atividade

8. Copie este bloco de código para processar o fim da AuthenticationActivity depois de introduzir credenciais de utilizador e recebe o código de autorização:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Para pedir um token, pode definir uma chamada de retorno

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Por fim, pedir um token de utilizar a chamada de retorno:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explicação dos parâmetros:

  * Recurso é necessário e é um recurso que está a tentar aceder.
  * ClientID é necessário e vem a partir do Portal de AzureAD.
  * Pode configurar redirectUri como o seu packagename. Não é necessária para ser fornecido para a chamada acquireToken.
  * PromptBehavior ajuda-o a pedir para introduzir as credenciais ignorar a cache e cookie.
  * Chamada de retorno será chamada após o código de autorização é trocado para obter um token.

  A chamada de retorno terá um objeto de AuthenticationResult que possui accesstoken, data expirado e idtoken informações.

Opcional: **acquireTokenSilent**

Pode ligar **acquireTokenSilent** para processar colocação em cache e atualização token. Fornece também a versão de sincronização. ID de utilizador-aceita como parâmetro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Corretor**: aplicação do portal do Microsoft Intune empresa irá fornecer o componente de corretor. ADAL irá utilizar a conta do corretor, se existir uma conta de utilizador é criada neste autenticador e programador optar por não ignorá-lo. Programador pode ignorar o utilizador corretor com:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Programador tem de registar redirectUri especial para utilização de corretor. RedirectUri está no formato de msauth://packagename/Base64UrlencodedSignature. Pode obter a sua redirecturi para a sua aplicação utilizando o script "brokerRedirectPrint.ps1" ou utilizar a API chamada mContext.getBrokerRedirectUri. Assinatura está relacionada com os seus certificados de assinatura.

 Modelo de corretor atual é de um utilizador. AuthenticationContext fornece método de API para obter o utilizador corretor.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Utilizador corretor será devolvido se conta for válida.

 O manifesto de aplicação deverá ter permissões para utilizar contas AccountManager: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Utilizar este tutorial, deverá ter o que precisa de integrar com êxito Azure Active Directory. Para obter mais exemplos deste trabalho, visite o AzureADSamples / repositório no GitHub.

## <a name="important-information"></a>Informações importantes

### <a name="customization"></a>Personalização

Recursos de projecto de biblioteca podem ser substituídos pelos recursos da aplicação. Isto acontece quando a sua aplicação está a criar. Por este motivo, pode personalizar o esquema de atividade de autenticação da forma que pretende. Tem de Certifique-se manter o id dos controlos que uses(Webview) ADAL.

### <a name="broker"></a>Corretor

Componente do corretor serão entregues com a aplicação do portal do Microsoft Intune da empresa. Conta será criada no Gestor de conta. Tipo de conta é "com.microsoft.workaccount". Apenas permite que conta SSO única. Criará cookie SSO para este utilizador depois de concluir HIP do dispositivo para das aplicações do.

### <a name="authority-url-and-adfs"></a>Url de certificação e ADFS

ADFS não é reconhecido como produção STS, pelo que necessita desativar a de deteção de instância e passar falso no construtor de AuthenticationContext.

Url de certificação tem STS instância e o nome do inquilino: https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>Consultar os itens em cache

ADAL fornece a cache de predefinido no SharedPreferences com algumas cache simple funções de consulta. Pode obter a cache atual do AuthenticationContext com:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Também pode fornecer sua implementação de cache, se pretender personalizá-lo.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL fornece a opção para especificar comportamento de linha de comandos. Será apresentada PromptBehavior.Auto IU se token de atualização é inválido e são necessárias credenciais de utilizador. PromptBehavior.Always irá ignorar a utilização da cache e Mostrar sempre IU.

### <a name="silent-token-request-from-cache-and-refresh"></a>Pedido de token silencioso cache de e atualização

Este método não utiliza o pop da IU para cima e não necessitam de uma atividade. Irá devolver token da cache, se disponível. Se tiver expirado token, tentará atualizá-los. Se token de atualização está expirada ou falha, irá devolver AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Também pode fazer chamadas com este método de sincronização. Pode definir null chamada de retorno para ou utilizar acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnósticos

Seguem-se as fontes principais de informação para diagnosticar problemas:

+ Exceções
+ Registos
+ Rastreios de rede

Além disso, repare que o ID de correlação é central para diagnósticos na biblioteca. Pode definir o seu correlação IDs faseadamente pedido por se pretender correlacionar uma ADAL no pedido com outras operações no seu código. Se não definir um id de correlação, em seguida, ADAL irá gerar um aleatório um and todas as mensagens do registo e chamadas de rede vai ser carimbo de data / com o id de correlação. O self gerado alterações de id em cada pedido.

#### <a name="exceptions"></a>Exceções

É óbvio que as este é o primeiro diagnóstico. Vamos tentar fornecem mensagens de erro úteis. Se encontrar uma que não é útil para ficheiro Introduza um problema e diga-nos. Consulte também fornecem informações sobre o dispositivo como modelo e SDK #.

#### <a name="logs"></a>Registos

Pode configurar a biblioteca para gerar mensagens de registo que pode utilizar para ajudar a diagnosticar problemas. Configurar o registo ao efetuar a chamada seguinte para configurar uma chamada de retorno ADAL irá utilizar para entregar desativar cada mensagem de registo como é gerado.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Mensagens podem ser escritas para um ficheiro de registo personalizados conforme visto abaixo. Infelizmente, não existe nenhuma forma padrão de obter registos a partir de um dispositivo. Existem alguns serviços que podem ajudá-lo com o seguinte. Pode também arranja seu próprio, tal como enviar o ficheiro num servidor.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Níveis de registo

+ Error(Exceptions)
+ Warn(Warning)
+ Informações (efeitos de informação)
+ Verboso (mais detalhes)

Definir o nível de registo da seguinte forma:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Todas as mensagens de registo são enviadas para logcat para além de chamadas de retorno qualquer registo personalizado.
Pode obter o registo para logcat de formulário um ficheiro como mostrado belog:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Obter mais exemplos sobre adb cmds: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Rastreios de rede

Pode utilizar várias ferramentas para capturar o tráfego HTTP ADAL gera.  O que é útil se estiver familiarizado com o protocolo OAuth ou se precisar de fornecer informações de diagnóstico para Microsoft ou outros canais de suporte.

Fiddler é a ferramenta de rastreio de HTTP mais fácil.  Utilize as ligações seguintes para o programa de configuração por excesso para o tráfego de rede ADAL corretamente registo.  Para poder ser útil é necessário configurar fiddler ou qualquer outra ferramenta como Carlos, para gravar o tráfego SSL não encriptado.  Nota: Rastreios gerados desta forma podem conter informações altamente privilegiadas como tokens de acesso, nomes de utilizador e palavras-passe.  Se estiver a utilizar contas de produção, não partilhe a estes rastreios com partes 3º.  Se precisar de fornecer um rastreio a pessoa para obter suporte, reproduza o problema com uma conta temporária com nomes de utilizador e palavras-passe que não se a partilha.

+ [Configurar o Fiddler para Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurar regras de Fiddler para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Modo de caixa de diálogo
método de acquireToken sem atividade suporta a linha de comandos da caixa de diálogo.

### <a name="encryption"></a>Encriptação

ADAL encripta o tokens e o arquivo no SharedPreferences por predefinição. Pode observe a classe de StorageHelper para ver os detalhes. Android introduzida AndroidKeyStore para 4.3(API18) armazenamento seguro de chaves privadas. ADAL utiliza que para API18 e acima. Se pretender utilizar ADAL para versões SDK inferiores, terá de fornecer chave secreta na AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>Contestação oauth2 ainda portadores

AuthenticationParameters classe fornece a funcionalidade para obter o authorization_uri de oauth2 ainda HIP de tipo de ligação.

### <a name="session-cookies-in-webview"></a>Cookies de sessão na vista Web

Vista da Web Android não limpar os cookies sessão após a aplicação estiver fechada. Pode tratar este com o código de exemplo abaixo:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Mais informações sobre os cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Substituições de recursos

A biblioteca de ADAL inclui cadeias em inglês para as seguintes mensagens de ProgressDialog duas.

A aplicação deverá substituí-las se cadeias localizadas são pretendidas.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>Caixa de diálogo NTLM
Versão adal 1.1.0 suporta NTLM caixa de diálogo que é processada através de evento onReceivedHttpAuthRequest a partir do WebViewClient. Esquema de caixa de diálogo e cadeias podem ser personalizadas.

### <a name="cross-app-sso"></a>Publicação em aplicação SSO
Saiba [como ativar a publicação em aplicação SSO no Android utilizando ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

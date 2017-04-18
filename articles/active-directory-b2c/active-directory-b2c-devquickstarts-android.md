<properties
    pageTitle="Azure B2C diretório ativos: Chamar uma web API a partir de uma aplicação do Android | Microsoft Azure"
    description="Este artigo mostrar-lhe como criar um lista de itens pendentes do Android aplicação que liga um Node.js da web API utilizando tokens de tipo de ligação OAuth 2.0. A aplicação Android e web API utilizam Azure Active Directory B2C para a gestão de identidades do utilizador e autenticar os utilizadores."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: Chamar uma web API a partir de uma aplicação do Android

> [AZURE.WARNING] Neste tutorial requer alguns atualizações importantes, especificamente para remover a utilização de ADAL Android para B2C.  Vamos para publicar frescas instruções para a utilização do Azure AD B2C nas aplicações do Android na próxima semana e, recomendamos que mantém até nesse período de tempo.  Mas se pretende apenas tente coisas saída, à vontade continuar com o artigo abaixo.



Ao utilizar B2C Azure Active Directory (Azure AD), pode adicionar funcionalidades de gestão de identidade de gestão personalizada poderosas às suas aplicações Android e web APIs alguns passos breve. Este artigo mostrar-lhe como criar Android "lista de acções a fazer" aplicação que liga um Node.js da web API utilizando tokens de tipo de ligação OAuth 2.0. A aplicação Android e web API utilizam B2C do Azure AD para a gestão de identidades do utilizador e autenticar os utilizadores.

Este guia de introdução requer que tem um web API está protegida por Azure AD com B2C para funcionar plenamente. Criámos um para .NET e Node.js para utilizar. Este guia passo a passo assume que a amostra de web API Node.js está configurada. Para mais informações, consulte o artigo [Azure AD B2C web API para Node.js tutorial](active-directory-b2c-devquickstarts-api-node.md).

Para clientes Android que precisam para aceder aos recursos protegidos, Azure AD fornece o Active Directory autenticação biblioteca (ADAL). É o único objectivo de ADAL facilitar a sua aplicação obter tokens de acesso. Para demonstrar como é fácil, este guia irá criamos uma aplicação de lista de acções a fazer Android que:

- Obtém tokens de acesso que utilizam uma lista de acções a fazer API utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Obtém a listas de afazeres dos utilizadores.
- Sinais saída utilizadores.

> [AZURE.NOTE] Este artigo não abrange como implementar o início de sessão no, inscrição e gestão de perfil utilizando Azure AD B2C. Foca-se sobre como ligar web APIs depois do utilizador é autenticado. Se ainda não o fez, deverá começar com o [.NET web app introdução tutorial](active-directory-b2c-devquickstarts-web-dotnet.md) saber mais sobre as noções básicas do Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um directory B2C do Azure AD

Antes de poder utilizar Azure AD B2C, tem de criar um diretório ou de inquilinos. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e mais. Se ainda não tiver uma, [criar um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, tem de criar uma aplicação no seu diretório B2C. Isto dá informações do Azure AD que precisa de comunicar com a sua aplicação de forma segura. A aplicação e a web API são representados por um único **ID da aplicação** neste caso, uma vez que incluem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). Não se esqueça de:

- Incluir uma **aplicação web**/**web API** na aplicação.
- Introduza `urn:ietf:wg:oauth:2.0:oob` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
- Criar um **secreta de aplicação** para a sua aplicação e copiá-lo. Irá necessitá-la mais tarde. Tenha em atenção que este valor tem de estar [escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de a utilizar.
- Copie o **ID da aplicação** que está atribuída a sua aplicação. Também terá este mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as suas políticas

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém três identidade experiências: inscrever-se, inicie sessão e inicie sessão utilizando o Facebook.  Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando cria as políticas de três, não se esqueça de:

- Selecione o **nome a apresentar** e outros atributos de inscrição na política de inscrição.
- Selecione os **nome a apresentar** e o **ID do objeto** afirmações da aplicação em todas as políticas. Pode escolher também em outras afirmações.
- Copie o **nome** de cada política depois de o criar. Deve ter o prefixo `b2c_1_`.  Tem os seguintes nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a sua aplicação.

Tenha em atenção que este artigo não aborda como utilizar as políticas que acabou de criar. Para saber mais sobre como funcionam as políticas de Azure AD B2C, comece com o [.NET web app introdução tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Transferir o código

O código para este tutorial [são mantidas no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para criar a amostra à medida que avança, pode [Transferir o projecto estrutura como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **É necessário para transferir a estrutura para concluir este tutorial.** Devido a complexidade da implementar uma aplicação do Android totalmente funcional, a estrutura tem o código UX que será executado depois de concluir este tutorial. Esta é uma medida de poupar tempo para programadores. O código UX não é germano para o tópico sobre como adicionar B2C para uma aplicação do Android.

A aplicação completa também está [disponível como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou na `complete` ramo do mesmo repositório de.

Para criar com Maven, pode utilizar `pom.xml` no nível superior.

  1. Siga os passos na [secção de pré-requisitos para configurar o Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configure um emulador com SDK 21.
  3. Vá para a pasta de raiz onde clonar que o repo.
  4. Execute o comando `mvn clean install`.
  5. Alterar o directório para o exemplo de guia de introdução `cd samples\hello`.
  6. Execute o comando `mvn android:deploy android:run`.

Deverá visualizar a aplicação de iniciação. Introduza as credenciais de utilizador de teste para experimentar.

Pacotes de arquivo de Java (para caixa) serão também submetidos junto do pacote de arquivo Android (AAR).

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Transferir o ADAL Android e adicioná-lo à área de trabalho Android Studio

Tem opções para saber como utilizar esta biblioteca no seu projeto Android:

* Pode utilizar o código de origem para importar a biblioteca para Eclipse e ligação para a sua aplicação.
* Se utilizar o Android Studio, pode utilizar o formato do pacote AAR e os binários de referência.

### <a name="option-1-binaries-via-gradle-recommended"></a>Opção 1: Binários através do Gradle (recomendado)

Pode obter os binários da repo central Maven. O pacote de AAR pode ser incluído no seu projeto no Android Studio (por exemplo, num `build.gradle`) da seguinte forma:

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Opção 2: AAR através do Maven

Se utilizar o `m2e` Plug-in nas Eclipse, pode especificar a dependência no seu `pom.xml` ficheiro:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Opção 3: Origem através do Git (últimos ordene novamente)

Para obter o código de origem do SDK através do Git, introduza:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilizar o ramo **convergência.**

## <a name="set-up-your-configuration-file"></a>Configurar o seu ficheiro de configuração

Utilizar a configuração de que configurou anterior no portal do B2C para configurar o projecto Android.

Abrir `helpes/Constants.java` e preencher os valores para o seguinte:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: Âmbitos que passar para o servidor ao qual pretende pedem novamente a partir do servidor quando um utilizador a iniciar sessão. Para que a pré-visualização B2C, passar `client_id`. No entanto, isto é esperado para alterar para `read scopes` no futuro. Este documento será atualizado quando que ocorre.
- `ADDITIONAL_SCOPES`: Âmbitos adicionais que pretende utilizar para a aplicação. Estes são esperados para ser utilizado no futuro.
- `CLIENT_ID`: O ID da aplicação que tem a partir do portal.
- `REDIRECT_URL`: O redirecionamento onde o que esperar o token para publicar exclusivamente anterior.
- `EXTRA_QP`: Nada extra que pretende passar para o servidor num formato de URL codificada.
- `FB_POLICY`: A política de que está a invocar. Esta é a parte mais importante para este guia passo a passo.
- `EMAIL_SIGNIN_POLICY`: A política de que está a invocar. Esta é a parte mais importante para este guia passo a passo.
- `EMAIL_SIGNUP_POLICY`: A política de que está a invocar. Esta é a parte mais importante para este guia passo a passo.

## <a name="add-references-to-android-adal-to-your-project"></a>Adicionar referências a Android ADAL ao seu projeto

> [AZURE.NOTE]  ADAL para Android utiliza um modelo com base em intenção para invocar autenticação. Tipos "apresente" a aplicação para trabalhar. Este exemplo todo e todos os ADAL para Android, centros de sobre como gerir tipos e passar informações entre elas.

Em primeiro lugar, informe Android sobre o esquema da sua aplicação, incluindo tipos que pretende utilizar. Estes tipos vai ser explicados detalhadamente mais abaixo neste tutorial.

Atualizar o seu projeto `AndroidManifest.xml` ficheiro para incluir todos os seus tipos:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Como pode ver, pode definir cinco atividades. Irá utilizar qualquer uma delas.

- `AuthenticationActivity`: Esta informação é proveniente de ADAL e fornece-lo a vista de início de sessão no web.
- `LoginActivity`: Esta ação apresentará as políticas de início de sessão e os botões para cada política.
- `SettingsActivity`: Utilize esta opção para alterar as definições de aplicação o tempo de execução.
- `AddTaskActivity`: Utilize esta opção para adicionar tarefas à sua REST API que estejam protegidos por Azure AD.
- `ToDoActivity`: Esta é a atividade principal que apresenta as tarefas.

## <a name="create-the-sign-in-activity"></a>Criar a atividade de início de sessão

Crie uma atividade de principal e chame- `LoginActivity`.

Criar um ficheiro denominado `LoginActivity.java`.

Tem de iniciar a atividade e adicionar alguns botões que controlam o seu IU. Isto é familiar para se que escreveu código Android antes.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Agora ter criado botões que utilizam o `ToDoActivity` intenção (que liga ADAL para quando precisar de um token). Estes fazem-lo ao utilizar a sua atividade como uma referência e um parâmetro adicional. Este parâmetro extra é transmitido pelo `intent.putExtra()` método. Pode definir `"thePolicy"` utilizando que especificou no `Constants.java`. Isto indica que a intenção qual a política para invocar durante a autenticação.

## <a name="create-the-settings-activity"></a>Criar a atividade de definições

Esta é uma atividade que preenche as definições de IU.

Criar um ficheiro denominado `SettingsActivity.java` para simples criar, ler, atualizar e eliminar (CRUD) operações.

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Criar a atividade de adicionar tarefas

Pode utilizar esta atividade para adicionar uma tarefa ao seu ponto final REST API.

Criar um ficheiro denominado `AddTaskActivity.java` e escrever a seguinte.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Criar a atividade da lista de acções a fazer

Esta é a atividade mais importante. Pode empregam para obter um token a partir do Azure AD para uma política de e, em seguida, utilize esse token para ligar ao servidor de REST API tarefa.

Criar um ficheiro denominado `ToDoActivity.java` e escrever a seguinte. (As chamadas serão posteriormente explicadas.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Poderá ter reparou que este depende métodos que ainda não tiver sido escritos ainda. Incluem `updateLoggedInUser()`, `clearSessionCookie()`, e `getTasks()`. Pode escrever as mais tarde neste guia. Em segurança pode ignorar os erros no Android Studio por agora.

Explicação dos parâmetros:

  - `SCOPES`: É necessário, os âmbitos para pedir acesso a que está a tentar. Para a pré-visualização do B2C, este é o mesmo que `client_id`, mas isto é esperado para mudar no futuro.
  - `POLICY`: A política para quando pretende autenticar o utilizador.
  - `CLIENT_ID`: Obrigatório, vem a partir do portal do Azure AD.
  - `redirectUri`: Pode ser configurado como nome do pacote. Não é necessário fornecido para a `acquireToken` de chamadas.
  - `getUserInfo()`: A forma de procurar se um utilizador já está na cache. O parâmetro também descreve como pedir um utilizador se esse utilizador não for encontrado ou se token de acesso do utilizador não é válido. Este método vai ser escrito mais tarde neste guia.
  - `PromptBehavior.always`: Ajuda a pedir para introduzir as credenciais ignorar a cache e cookie.
  - `Callback`: Chamado depois de um código de autorização é trocado para obter um token. Este terá um objeto `AuthenticationResult`, que contém token de acesso, data de expiração e informações relativas ao token ID.

> [AZURE.NOTE]  A aplicação portal da empresa de Microsoft Intune fornece o componente de corretor e pode estar instalado no dispositivo do utilizador. A aplicação oferece um único acesso em início de sessão (SSO) em todas as aplicações no dispositivo. Os programadores devem estar preparados para permitir que para o Intune. ADAL para Android irá utilizar a conta de corretor se existir uma conta de utilizador criada no autenticador. Para utilizar o corretor, o programador tem de registar um especial `redirectUri` para o corretor a utilizar. `redirectUri`está no formato de msauth://packagename/Base64UrlencodedSignature. Pode obter `redirectUri` para a sua aplicação utilizando o script `brokerRedirectPrint.ps1` ou ao utilizar a chamada à API `mContext.getBrokerRedirectUri()`. A assinatura está relacionada com os seus certificados de assinatura a partir da Google Play store.

 Também pode ignorar o utilizador corretor utilizando:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Para reduzir a complexidade deste guia de introdução do B2C, podemos tiver optado por participar para ignorar o corretor no nosso exemplo.

Em seguida, crie métodos de auxiliar para obtém o token sozinho durante a chamadas de autenticação para a tarefa API.

Na mesma `ToDoActivity.java` ficheiro, escreva o seguinte.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Adicionar também métodos que serão "definir" e "obter" `AuthenticationResult` (que tem o token) ao projecto global `Constants`. Apesar de `ToDoActivity.java` utiliza `sResult` nos seus fluxos, tem de adicionar estes métodos. Se não, de outras atividades não terão acesso ao token de para trabalhar (tal como adicionar uma tarefa no `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Criar um método para devolver um identificador de utilizador

ADAL para Android representa o utilizador a forma de um `UserIdentifier` objeto. Isto gere o utilizador. Pode utilizar o objeto para identificar se o mesmo utilizador está a ser utilizado nas chamadas. Ao utilizar estas informações, pode depender a cache, em vez de efetuar uma chamada de nova no servidor. Para facilitar, criámos a `getUserInfo()` método devolve `UserIdentifier`. Pode utilizá-lo com `acquireToken()`. Criámos também um `getUniqueId()` método que devolve a identificação de `UserIdentifier` na cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Escrever métodos helper

Em seguida, escreva alguns métodos helper para o ajudar a limpar cookies e forneça `AuthenticationCallback`. Estes métodos são utilizados simplesmente por exemplo para se certificar de que é num estado limpo ao ligar para o seu `ToDo` atividade.

No mesmo ficheiro denominado `ToDoActivity.java`, escreva o seguinte.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Ligar a tarefa API

Depois de ter a sua atividade pronta para agarrar tokens, pode escrever o seu API para aceder ao servidor de tarefa.

`getTasks`Fornece uma matriz que representa as tarefas no seu servidor.

Começar com `getTasks`.

No mesmo ficheiro denominado `ToDoActivity.java`, escreva o seguinte.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Também escreva um método que será inicializado suas tabelas na primeira execução.

No mesmo ficheiro denominado `ToDoActivity.java`, escreva o seguinte.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

Pode ver que este código requer alguns métodos adicionais para fazer o seu trabalho. Escreva esses seguinte.

### <a name="create-an-endpoint-url-generator"></a>Criar um gerador de URL de ponto final

É necessário gerar o URL de ponto final que vai ser a ligar. Fazê-lo no mesmo ficheiro de classe.

**No mesmo ficheiro** designado por `ToDoActivity.java`, escreva o seguinte.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Tenha em atenção que adicionar o token de acesso a pedido no código de outros fabricantes referido na secção seguinte.

## <a name="write-some-ux-methods"></a>Escrever alguns métodos UX

Android requer que processar alguns chamadas de retorno para trabalhar com a aplicação. Estes são `createAndShowDialog` e `onResume()`. Isto é familiar para se que escreveu código Android antes.

No mesmo ficheiro denominado `ToDoActivity.java`, escreva o seguinte.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Em seguida, faça a gestão do seu chamadas de retorno da caixa de diálogo.

No mesmo ficheiro denominado `ToDoActivity.java`, escreva o seguinte.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Agora deverá ter um `ToDoActivity.java` ficheiro compila. O projeto inteiro também deve compilar neste momento.

## <a name="run-the-sample-app"></a>Executar a aplicação de exemplo

Por fim, criar e executar a aplicação Android Studio ou Eclipse. Se inscrever ou iniciar sessão na aplicação. Crie tarefas para o utilizador tiver sessão iniciada no. Terminar sessão e volta a iniciar sessão como um utilizador diferente e, em seguida, criar tarefas para esse utilizador.

Repare que as tarefas são armazenado por utilizador no API, porque a API extrai a identidade do utilizador do token de acesso que recebe.

Para sua referência, o exemplo concluída [é fornecida como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Informações importantes


### <a name="encryption"></a>Encriptação

ADAL encripta o tokens e o arquivo no `SharedPreferences` por predefinição. Pode observar a `StorageHelper` escolares para ver os detalhes. Android introduzida **AndroidKeyStore para 4.3(API18)** armazenamento seguro de chaves privadas. ADAL utiliza que para API18 e acima. Se pretender utilizar ADAL para versões SDK inferiores, terá de fornecer uma chave secreta na `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookies de sessão na vista de web

Vista da Android web não limpar os cookies sessão após a aplicação estiver fechada. Pode processar isto com o seguinte código de exemplo.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Saiba mais acerca de cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

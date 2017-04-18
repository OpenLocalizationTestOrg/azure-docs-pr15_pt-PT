<properties
    pageTitle="Como ativar a publicação em aplicação SSO IOS utilizando ADAL | Microsoft Azure"
    description="Como utilizar as funcionalidades do ADAL SDK para permitir o início de sessão entre as aplicações. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como ativar a publicação em aplicação SSO IOS utilizando ADAL


Fornecer único início de sessão (SSO) para que os utilizadores só precisam introduzir as suas credenciais de uma vez e ter automaticamente essas credenciais trabalha pertencentes a várias aplicações é agora esperada pelos clientes. A dificuldade de introduzir o seu nome de utilizador e palavra-passe num ecrã pequeno, muitas vezes vezes combinados com um factor adicional (2FA) como uma chamada telefónica ou de um código texted, resultados na insatisfação rápida se um utilizador tem de fazer mais do que uma vez para o seu produto.

Além disso, se tirar partido uma plataforma de identidade que outras aplicações podem usar como Accounts Microsoft ou uma conta de trabalho do Office 365, a clientes esperam que as credenciais de estar disponível para utilizar em todas as suas aplicações, independentemente do fornecedor.

A plataforma Microsoft Identity, juntamente com os nossos SDK identidade da Microsoft, não todo este trabalho para si e fornece-lhe a capacidade de Maravilhe os seus clientes com SSO quer no seu próprio conjunto de aplicações ou, tal como com os nossos corretor capacidade e aplicações autenticador, ao longo de todo o dispositivo.

Este tutorial indicar-lhe como configurar os nossos SDK dentro da sua aplicação para fornecer este benefício aos seus clientes.

Este tutorial aplica-se ao:

* Azure Active Directory
* B2C Azure Active Directory
* B2B Azure Active Directory
* Acesso ao condicional Azure Active Directory


Repare que o documento abaixo pressupõe que possuir um conhecimento como [aprovisionar aplicações no portal do legado para o Azure Active Directory](./develop/active-directory-how-to-integrate.md) , bem como tem uma aplicação integrada com o [Microsoft identidade iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceitos SSO a plataforma de identidade da Microsoft

### <a name="microsoft-identity-brokers"></a>Corretor de identidade da Microsoft

Microsoft fornece aplicações para cada plataforma móvel que permitem a eliminação de credenciais através de aplicações a partir de diferentes fornecedores, bem como permite funcionalidades melhoradas especiais que necessitam de um só local seguro a partir da qual validar credenciais. Chamamos estes **corretor**. No iOS e Android estas são fornecidas através de aplicações transferíveis que os clientes instalar de forma independente ou podem ser enviados para o dispositivo por uma empresa que gere a algumas ou todas as do dispositivo para os respetivos funcionário. Estes corretor suporte Gerir segurança apenas para algumas aplicações ou todo o dispositivo com base no que os administradores de TI pretendem. No Windows esta funcionalidade é fornecida por um Seletor de conta incorporado para o sistema operativo, tecnicamente como o corretor de autenticação de Web conhecido.

Para compreender como utilizamos estes corretor e como os seus clientes poderão vê-los no seu fluxo de início de sessão para a plataforma Microsoft Identity continue a ler para obter mais informações.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões para iniciar sessão em dispositivos móveis

Acesso a credenciais em dispositivos siga dois padrões básicas para a plataforma Microsoft Identity:

* Não corretor assistidos inícios de sessão
* Broker assistidos inícios de sessão

#### <a name="non-broker-assisted-logins"></a>Não corretor assistidos inícios de sessão

Inícios de sessão assistidos corretor não são experiências de início de sessão que ocorrem em linha com a aplicação e utilizar o armazenamento local no dispositivo para essa aplicação. Este armazenamento pode ser partilhado através de aplicações, mas as credenciais estão intimamente vinculadas à aplicação ou conjunto de aplicações utilizar esse credenciais. Esta é a experiência que mais provável é tenha tido em muitas aplicações móveis onde introduzir um nome de utilizador e palavra-passe na própria aplicação.

Estes inícios de sessão tem os seguintes benefícios:

-  Experiência do utilizador existe totalmente dentro da aplicação.
-  Credenciais podem ser partilhadas por aplicações que são assinadas pelo mesmo certificado, fornecer uma experiência único início de sessão para o seu conjunto de aplicações.
-  Controlo à volta a experiência de início de sessão é fornecido para a aplicação antes e depois de iniciar sessão.

Estes inícios de sessão tem as seguintes desvantagens:

- Utilizador não é possível uma experiência de sessão único no através de todas as aplicações que utilizam um Microsoft Identity, apenas através desses Identities Microsoft que estão a aplicação é o proprietário e tiver configurado.
- A aplicação não podem ser utilizados com mais avançadas funcionalidades de business como acesso condicional ou utilize o conjunto de aplicações do InTune de produtos.
- A aplicação não suportar a autenticação de certificados com base para utilizadores empresariais.

Eis uma representação de funcionamento dos SDK identidade da Microsoft com o armazenamento partilhado das suas aplicações para ativar o SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Broker assistidos inícios de sessão

Assistido corretor inícios de sessão são experiências de início de sessão que ocorrem dentro da aplicação corretor e utilizam o armazenamento e a segurança do corretor para partilhar as credenciais em todas as aplicações no dispositivo que tirar partido da plataforma Microsoft Identity. Isto significa que as aplicações irão depender do corretor para poder de início de sessão utilizadores. No iOS e Android estas são fornecidas através de aplicações transferíveis que os clientes instalar de forma independente ou podem ser enviados para o dispositivo por uma empresa que gere o dispositivo para o utilizador. Um exemplo deste tipo de aplicação é a aplicação do Azure autenticador IOS. No Windows esta funcionalidade é fornecida por um Seletor de conta incorporado para o sistema operativo, tecnicamente como o corretor de autenticação de Web conhecido.
A experiência varia consoante a plataforma e pode ser por vezes perturbadora para os utilizadores se não for gerida corretamente. Que conhece provavelmente mais este padrão se tiver a aplicação Facebook instalada e utilizar a funcionalidade de início de sessão do Facebook noutra aplicação. A plataforma Microsoft Identity tira partido do mesmo padrão.

Para iOS que isto leva a uma transição"" animação onde a aplicação é enviada para o fundo enquanto as aplicações do Azure autenticador é fornecido para primeiro plano para o utilizador selecionar qual que gostaria de iniciar sessão com a conta.  

Para Android e Windows o Seletor de conta é apresentado na parte superior da aplicação que é menos perturbadora ao utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o corretor obtém chamado

Se estiver instalado um corretor compatível no dispositivo, como a aplicação autenticador Azure, dos SDK identidade da Microsoft automaticamente irá fazer o trabalho invocar corretor automaticamente quando um utilizador indica que pretendem inicie sessão com qualquer conta a partir da plataforma Microsoft Identity. Isto pode ser uma Account Microsoft pessoal, trabalho ou conta escolar, ou uma conta que fornece e anfitrião no Azure utilizando os nossos produtos B2C e B2B. Ao utilizar a encriptação e algoritmos extremamente seguros podemos Certifique-se de que as credenciais estão pedidas e entregue regressar à aplicação de uma forma segura. Os detalhes técnicos exato desses mecanismos não é publicado mas foram desenvolvidos com colaboração por Apple e o Google.

**O programador tem a escolha de se o Microsoft identidade SDK chamadas o corretor ou utiliza o fluxo de assistido não corretor.** No entanto se o Programador de optar por não está a utilizar o fluxo assistido corretor perder o benefício de tirar partido da credenciais SSO que o utilizador pode já ter adicionado no dispositivo, bem como impede que os respetivos aplicação a ser utilizada com funcionalidades de business que aos seus clientes, tal como o Access condicional, as capacidades da gestão de Intune, a Microsoft fornece e autenticação baseada em certificado.

Estes inícios de sessão tem os seguintes benefícios:

-  Utilizador experiências SSO em todas as suas aplicações, independentemente do fornecedor.
-  A aplicação pode tirar partido de funcionalidades mais avançadas do negócio tal como o Access condicional ou utilizar o conjunto de aplicações do InTune de produtos.
-  A aplicação pode suportar a autenticação de certificados com base para utilizadores empresariais.
- Experiência de início de sessão muito mais segura como a identidade da aplicação e o utilizador são verificadas pela aplicação corretor com encriptação e algoritmos de segurança adicional.

Estes inícios de sessão tem as seguintes desvantagens:

- No iOS o utilizador é que transitaram fora experiência da sua aplicação enquanto credenciais são escolhidas.
- Perda da capacidade de gerir a experiência de início de sessão para os seus clientes na aplicação.



Eis uma representação de funcionamento dos SDK identidade da Microsoft com as aplicações do corretor para ativar o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Com esta informação de fundo que deverá conseguir compreender e implementar o SSO dentro da sua aplicação utilizando a plataforma Microsoft Identity e SDK melhor pelas.


## <a name="enabling-cross-app-sso-using-adal"></a>Ativar a publicação em aplicação SSO utilizando ADAL

Aqui vamos utilizar o iOS ADAL SDK para:

- Ativar não corretor SSO assistido para o conjunto de aplicações
- Ativar o suporte para assistido corretor SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ativar o SSO para que não sejam corretor assistido SSO

Para que não sejam corretor SSO assistido através de aplicações dos SDK identidade da Microsoft gerir muitas da complexidade da SSO por si. Isto inclui localizar o utilizador à direita na cache e manutenção de uma lista de utilizadores com sessão iniciada automaticamente à consulta.

Para ativar o SSO através de aplicações que é o proprietário que tem de fazer o seguinte:

1. Certifique-se todos os seus utilizador de aplicações, o ID de cliente mesmo ou aplicação ID.
* Certifique-se de que todas as aplicações utilizar para partilhar o mesmo certificado de assinatura a partir da Apple para que possam partilhar keychains
* Pedir o direito de porta-chaves mesmo para cada uma das suas aplicações.
* Envie o SDK identidade da Microsoft keychain partilhada que pretende que configuremos automaticamente para utilizar.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Utilizar o mesmo ID de cliente / aplicação ID para todas as aplicações no seu conjunto de aplicações

Para a plataforma Microsoft Identity saber que tem permissão para partilhar tokens em suas aplicações, cada uma das suas aplicações será necessário partilhar a mesma ID de cliente ou aplicação ID. Este é o identificador exclusivo que foi fornecido quando registada as aplicações do primeira no portal.

Poderá estar a pensar como irá identificar diferentes aplicações para o serviço Microsoft Identity se utiliza o ID de aplicação do mesmo. A resposta é com o **URIs redirecionar**. Cada aplicação pode ter vários URIs redirecionar registada no portal de ativação. Cada aplicação no seu conjunto de aplicações terá um redirecionamento diferentes URI. Um exemplo do aspeto isto é abaixo:

Redireccionar App1 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

Redireccionar App2 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

Redireccionar App3 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estas são as funções aninhadas no mesmo ID de cliente / ID da aplicação e procurados com base no redirecionamento URI devolvido-na configuração SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Tenha em atenção que o formato destes URIs redirecionar são explicado abaixo. Pode utilizar qualquer URI de redirecionar, a menos que deseja suportar o corretor, caso em que tem o seguinte aspeto anteriores*



#### <a name="create-keychain-sharing-between-applications"></a>Criar a porta-chaves partilha entre aplicações

Ativar a partilha da keychain está fora do âmbito deste documento e abrangido pelo Apple no seu documento [Capacidades de adicionar](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). O que é importante é decidir o que pretende o seu porta-chaves para ser chamado e adicione essa funcionalidade através de todas as suas aplicações.

Quando tiver direitos ao configurar o corretamente que deverão ver um ficheiro no diretório do seu projeto direito `entitlements.plist` que contiver algo com um aspeto semelhante ao seguinte:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Assim que tiver o direito de porta-chaves ativado em cada uma das suas aplicações e estiver pronto para utilizar o SSO, envie o Microsoft identidade SDK seu porta-chaves utilizando a seguinte definição no seu `ADAuthenticationSettings` com as seguintes definições:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Quando partilha uma porta-chaves entre as aplicações de qualquer aplicação pode eliminar utilizadores ou pior eliminar todos os tokens ao longo da sua aplicação. Este é particularmente desastrosa caso possua aplicações que dependem dos tokens de trabalho do fundo. Partilhar uma porta-chaves significa que tem de ser muito cuidado no todos e remove operações através dos SDK identidade da Microsoft.

Já está! O Microsoft identidade SDK agora irá partilhar as credenciais em todas as suas aplicações. A lista de utilizadores também será partilhada em várias instâncias da aplicação.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO do corretor assistido SSO

A capacidade de uma aplicação para utilizar qualquer corretor que está instalado no dispositivo está **desativada por predefinição**. Para que possa utilizar a aplicação com o corretor tem de fazer alguma configuração adicional e adicione algum código para a sua aplicação.

Os passos a seguir são:

1. Ativar o modo de corretor numa chamada de código da aplicação para o SDK do MS.
2. Estabelecer uma nova redireccionar URI e forneça que para a aplicação e o registo de aplicação.
3. Registar um esquema de URL.
4. Suporte de iOS9: adicionar uma permissão ao ficheiro info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de corretor na sua aplicação
A capacidade para a sua aplicação utilizar o corretor está ativada quando cria a configuração inicial do objeto autenticação ou "contexto". Fazer isto ao definir o seu tipo de credenciais no seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
O `AD_CREDENTIALS_AUTO` definição permite que o Microsoft identidade SDK tentar a chamada para o corretor, `AD_CREDENTIALS_EMBEDDED` irá impedir que o Microsoft identidade SDK chamadas para o corretor.

#### <a name="step-2-registering-a-url-scheme"></a>Passo 2: Registar um esquema de URL
A plataforma Microsoft Identity utiliza URLs para invocar o corretor e, em seguida, devolver o controlo regressar à aplicação. Para concluir a esse ida e volta precisa de um esquema de URL registado para a aplicação que ficará a saber sobre a plataforma Microsoft Identity. Isto pode ser para além de outros esquemas de aplicação que pode ter registado anteriormente com a aplicação.

> [AZURE.WARNING]
Recomendamos fazer o esquema de URL bastante exclusivo para minimizar as hipóteses de outra aplicação com o mesmo esquema de URL. Apple não impor a exclusividade de esquemas de URL que estão registados na app store.

Abaixo está um exemplo de como é apresentado na configuração do projeto. Poderá também fazer isto no XCode, assim:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 3: Estabelecer uma nova redirecionar URI com esquema de URL

Para se certificar de que estamos sempre devolver os tokens de credenciais para a aplicação correta, é necessário para se certificar de que chamamos de regressar à aplicação de uma forma que pode verificar o sistema operativo do iOS. O sistema operativo do iOS relatórios para as aplicações do Microsoft corretor o ID do pacote da aplicação que entra em contacto-lo. Isto não pode ser fraudulento por uma aplicação de rogue. Por conseguinte, podemos tirar partido isto juntamente com o URI da nossa aplicação corretor para garantir que os tokens são devolvidos à aplicação correto. Necessitamos lhe estabelecer este redirecionamento exclusivo URI ambos na sua aplicação e definir como um URI redirecionar no nosso portal de programador.

URI de redireccionamento tem de ser sob a forma inicial de:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este URI redirecionar tem de ser especificado no seu registo de aplicação utilizando o [portal clássica Azure](https://manage.windowsazure.com/). Para mais informações sobre o registo de aplicação do Azure AD, consulte o artigo [integração com o Azure Active Directory](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passo 3: adicionar um redireccionamento URI no seu portal de aplicação e Dev Center para suportar a autenticação de certificado baseado

Para suportar a orientação do diapositivo notas autenticação baseada numa que segunda "msauth" tem de ser registados na sua aplicação e o [Azure portal clássico](https://manage.windowsazure.com/) para processar a autenticação de certificado se pretender adicionar esse suporte na sua aplicação.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Passo 4: iOS9: adicionar um parâmetro de configuração para a sua aplicação

ADAL utiliza – canOpenURL: para verificar se o corretor está instalado no dispositivo. IOS 9 Apple bloqueado o que podem consultar esquemas de uma aplicação. É necessário adicionar "msauth" à secção LSApplicationQueriesSchemes da sua `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Configurou o SSO!

Agora o Microsoft identidade SDK serão automaticamente partilhar as credenciais em suas aplicações tanto invocar o corretor caso esteja presente no seu dispositivo.

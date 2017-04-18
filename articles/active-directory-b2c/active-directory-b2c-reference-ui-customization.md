<properties
    pageTitle="Azure Active B2C de diretório: Personalização de interface (IU) utilizador | Microsoft Azure"
    description="Um tópico sobre as funcionalidades de personalização (IU) de interface de utilizador no Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure B2C diretório ativos: Personalizar a interface de utilizador (IU) do Azure AD B2C

Experiência de utilizador é fundamental numa aplicação do consumidor destinado. É a diferença entre uma boa aplicação e um excelente e entre consumidores meramente ativos e aqueles verdadeiramente ocupadas. Azure Active Directory (Azure AD) B2C permite-lhe personalizar consumidor inscrição, início de sessão no (*Ver nota abaixo*), de edição, de perfil e páginas com controlo pixel perfeito repor a palavra-passe.

> [AZURE.NOTE]
Atualmente, conta local início de sessão no páginas, a palavra-passe accompaning repor páginas e podem ser personalizados e-mails de verificação apenas utilizando a [funcionalidade de imagem corporativa da empresa](../active-directory/active-directory-add-company-branding.md) e não pelo mecanismos descritos neste artigo.

Neste artigo, vai ler sobre:

- A funcionalidade de personalização do página utilizador interface (IU).
- Uma ferramenta que irá ajudá-lo teste a funcionalidade de personalização de IU de página, utilizando os nossos conteúdo de exemplo.
- Os elementos de IU core em cada tipo de página.
- Melhores práticas ao exercer esta funcionalidade.

## <a name="the-page-ui-customization-feature"></a>A funcionalidade de personalização da IU de página

Com a funcionalidade de personalização de IU de página, pode personalizar o aspeto e funcionalidade do consumidor inscrição, iniciar sessão, páginas de reposição de palavra-passe e edição de perfil (ao configurar [políticas](active-directory-b2c-reference-policies.md)). Os utilizadores poderão terá experiências totalmente integradas quando navegar entre as páginas de aplicação e fornecido pelo Azure AD B2C.

Ao contrário de outros serviços onde opções de IU estão limitadas ou só estão disponíveis através do APIs, Azure AD B2C utiliza uma abordagem moderna (e mais simples) para uma personalização da IU página.

Eis como funciona: Azure AD B2C é executado código no browser do seu consumidor e utiliza uma abordagem moderna denominada [Partilha de recursos cruzada origem (CORS)](http://www.w3.org/TR/cors/) para carregar o conteúdo a partir de um URL que especificou numa política. Pode especificar o URL diferentes para páginas diferentes. O código intercala elementos de interface de utilizador a partir do Azure AD B2C com o conteúdo carregado a partir do seu URL e apresenta a página para o consumidor. Tudo o que precisa de fazer é:

1. Criar HTML5 correcto conteúdo com um `<div id="api"></div>` elemento (precisa de ser um elemento vazio) algures localizado na `<body>`. Este marcas de elemento onde o conteúdo do Azure AD B2C é inserido.
2. Aloje o seu conteúdo num ponto final HTTPS (com CORS permitidos).
3. Os elementos de IU que Azure AD B2C insere de estilo.

## <a name="test-out-the-ui-customization-feature"></a>Testar a funcionalidade de personalização da IU

Se quiser experimentar a funcionalidade de personalização da IU utilizando o nosso exemplo HTML e conteúdos CSS, fornecemos é [uma ferramenta de helper simples](active-directory-b2c-reference-ui-customization-helper-tool.md) que os carregamentos pendentes e configura o conteúdo de exemplo no seu armazenamento de Blobs do Azure.

> [AZURE.NOTE]
Pode alojar o seu conteúdo IU em qualquer lugar: em servidores web, CDNs, AWS S3, sistemas de partilha de ficheiros, etc. Desde que o conteúdo está alojado num ponto final da HTTPS publicamente disponível (CORS permitidos), está pronto. Estamos a utilizar o armazenamento de Blobs do Azure ilustrativas apenas para fins de.

### <a name="the-most-basic-html-content-for-testing"></a>O conteúdo HTML mais básico para testar a ligação

É mostrado abaixo é o HTML mais básico conteúdo que pode utilizar para testar esta funcionalidade. Utilize a mesma ferramenta de helper fornecida anteriormente para carregar e configurar o este conteúdo no seu armazenamento de Blobs do Azure. Em seguida, pode verificar os botões básicos, não estilizado e campos de formulário em cada página são apresentados e funcional.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Os elementos de IU core em cada tipo da página

Nas secções seguintes, irá encontrar exemplos de fragmentos HTML5 que Azure AD B2C intercala a `<div id="api"></div>` elemento localizada no seu conteúdo. **Não inserir estes fragmentos no seu conteúdo HTML 5.** O serviço do Azure AD B2C insere-los em tempo de execução. Utilize estes exemplos para estruturar o seus próprio folhas de estilo.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Azure AD B2C conteúdo inserido "identidade fornecedor seleção página"

Esta página contém uma lista de fornecedores de identidades que o utilizador pode escolher a partir do durante a inscrição ou iniciar sessão. Estes são em fornecedores de identidade de rede social como o Facebook e Google +, ou contas locais (com base no nome de utilizador ou endereços de correio eletrónico).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Azure AD B2C conteúdo inserido "Local página de conta inscrição"

Esta página contém um formulário de inscrição que o utilizador tem para preencher quando inscrever-se para uma conta local que é baseada num endereço de e-mail ou um nome de utilizador. O formulário pode conter os controlos de entrada diferentes, tal como a caixa de entrada de texto, caixa de entrada de palavra-passe, botão de opção, selecione único pendente caixas e seleções múltiplas caixas de verificação.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure AD B2C conteúdo inserido a "" Social inscrição na página de conta"

Esta página contém um formulário de inscrição que tem o consumidor para preencher quando inscrever-se utilizando uma conta existente a partir de um fornecedor de identidade de rede social como o Facebook ou Google +. Esta página é semelhante à conta local inscrição página (mostrada na secção anterior), à exceção dos campos de entrada de palavra-passe.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Azure AD B2C conteúdo inserido "unificado inscrição ou início de sessão na página"

Esta página trata tanto inscrição e início de sessão dos consumidores, que podem utilizar fornecedores de identidade de rede social como o Facebook ou Google + ou contas locais.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Azure AD B2C conteúdo inserido "página de autenticação multifator"

Nesta página, os utilizadores podem verificar os respetivos números de telefone (utilizando o texto ou voz) durante a inscrição ou iniciar sessão.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Azure AD B2C conteúdo inserido a "" página de erro"

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Factos a não esquecer quando criar o seu conteúdo

Se estiver a planear utilizar a funcionalidade de personalização da IU de página, reveja as seguintes melhores práticas:

- Não copiar conteúdo de predefinido do B2C o Azure AD e tentar modificá-lo. É aconselhável criar o seu conteúdo HTML5 de raiz e utilizar conteúdo predefinido como referência.
- Em todas as páginas (exceto as páginas de erro) fornecidas pela iniciar sessão, inscrição e políticas de perfil de edição, folhas de estilo que fornece terão que substituir as folhas de estilo predefinido que recomendamos adicionar nestas páginas na <head> fragmentos. Em todas as páginas fornecidas pela inscrição ou iniciar sessão e políticas de reposição de palavra-passe e as páginas de erro em todas as políticas, terá de fornecer todos os o estilo si mesmo.
- Por motivos de segurança, podemos não permitem-lhe incluir qualquer JavaScript no seu conteúdo. A maior parte do que precisa de deverá estar disponível terminar a caixa. Caso contrário, utilizar a [Voz do utilizador](http://feedback.azure.com/forums/169401-azure-active-directory) para pedir a nova funcionalidade.
- Versões de browser suportadas:
    - Internet Explorer 11
    - O Internet Explorer 10
    - Internet Explorer 9 (limitado)
    - Internet Explorer 8 (limitado)
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0

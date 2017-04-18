
<properties
   pageTitle="Cenários de autenticação de Azure AD | Microsoft Azure"
   description="Uma descrição geral dos cinco cenários de autenticação mais comuns para Azure Active Directory (AAD)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Cenários de autenticação de Azure AD

Azure Active Directory (Azure AD) simplifica autenticação para programadores, fornecendo identidade como uma origem de serviço, com o suporte para protocolos de norma da indústria como OAuth 2.0 e OpenID ligar, bem como abrir bibliotecas para diferentes plataformas ajudar a começar a codificação rapidamente. Este documento irá ajudá-lo compreender o suporta vários de cenários Azure AD e irá mostrar-lhe como começar a utilizar. Está dividido em secções que se seguem:

- [Noções básicas de autenticação no Azure AD](#basics-of-authentication-in-azure-ad)

- [Em afirmações em Tokens de segurança do Azure AD](#claims-in-azure-ad-security-tokens)

- [Noções básicas de registar uma aplicação no Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Tipos de aplicações e aos cenários](#application-types-and-scenarios)

  - [Browser para a aplicação Web](#web-browser-to-web-application)

  - [Aplicação única página (SPA)](#single-page-application-spa)

  - [Aplicação nativa Web API](#native-application-to-web-api)

  - [Aplicação Web para Web API](#web-application-to-web-api)

  - [Daemon aplicação ou de servidor Web API](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Noções básicas de autenticação no Azure AD

Se não estiver familiarizado com os conceitos básicos de autenticação no Azure AD, leia esta secção. Caso contrário, poderá querer ignorar para baixo até [tipos de aplicações e cenários](#application-types-and-scenarios).

Vamos considere o cenário mais básico onde identidade é necessária: precisa de um utilizador num web browser autenticar a uma aplicação web. Este cenário é descrito em maior detalhe na secção de [Browser para a aplicação Web](#web-browser-to-web-application) , mas é um ponto de partida útil para ilustrar as funcionalidades do Azure AD e lo a conceptualizar como funciona o cenário. Considere o seguinte diagrama para este cenário:

![Descrição geral do início de sessão a aplicação web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

O diagrama acima deve ter em conta, eis o que precisa de saber sobre os vários componentes:

- Azure AD é o fornecedor de identidade, responsável para verificar a identidade dos utilizadores e aplicações que existam no diretório de uma organização e finalmente emissão tokens de segurança de autenticação com êxito desses utilizadores e aplicações.


- Uma aplicação que pretende deixem de autenticação do Azure AD tem de estar registada no Azure AD que regista e identifica exclusivamente a aplicação no diretório.


- Os programadores podem utilizar as bibliotecas de autenticação abrir origem Azure AD para facilitar a autenticação através do tratamento os detalhes do protocolo por si. Para mais informações, consulte o [Azure Active Directory autenticação bibliotecas](active-directory-authentication-libraries.md) .


• Assim que um utilizador tenha sido autenticado, a aplicação tem de validar token de segurança do utilizador para se certificar de que a autenticação foi efetuada com êxito para as partes pretendidas. Os programadores podem utilizar as bibliotecas de autenticação fornecido para processar validação do qualquer token a partir do Azure AD, incluindo JSON Web Tokens (JWT) ou SAML 2.0. Se pretender executar a validação manualmente, consulte a documentação de [Processador de tokens de JWT](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Azure AD utiliza criptografia de chave pública para assinar tokens e certifique-se de que são válidos. Consulte o artigo [Importante informações sobre como iniciar sessão chave sobreposição no Azure AD](active-directory-signing-key-rollover.md) para obter mais informações sobre a lógica necessária, tem de ter na sua aplicação para garantir que é sempre atualizado com as teclas mais recentes.


• O fluxo de pedidos e respostas para o processo de autenticação é determinado pelo protocolo de autenticação que foi utilizado, tal como OAuth 2.0, OpenID ligar, WS Federation ou SAML 2.0. Estes protocolos são explicados detalhadamente mais no tópico [Protocolos de autenticação do Azure Active Directory](active-directory-authentication-protocols.md) e nas secções abaixo.

> [AZURE.NOTE] Azure AD suporta o 2.0 OAuth e padrões OpenID ligar-se que aproveitar utilização de tokens de tipo de ligação, incluindo tokens portadores representados como JWTs. Uma ligação OAuth é um token de segurança lightweight que concede o acesso de "ligação" para um recurso protegido. Neste sentido, "portadores" são qualquer uma das partes que pode apresentar o token. Apesar de uma festa tem autenticar-se primeiro com o Azure AD para receber a ligação OAuth, se não forem tomados os passos necessários para proteger o token na transmissão e armazenamento, pode ser interceptada e utilizado por das partes indesejada. Enquanto alguns tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas utilizá-los, tokens de tipo de ligação não têm este mecanismo e devem ser transportados num canal seguro, tal como a segurança da camada de transporte (HTTPS). Se uma ligação OAuth é transmitido em claro, um homem-in o nome do meio ataque pode ser utilizado por uma festa maliciosa para adquirir o token e utilizá-lo para um acesso não autorizado a um recurso protegido. Aplicam os mesmos princípios de segurança quando armazenar ou colocação em cache tokens de tipo de ligação para utilizar posteriormente. Certifique-se sempre que a sua aplicação transmite e armazena tokens de tipo de ligação de uma forma segura. Para obter mais considerações de segurança no tokens de tipo de ligação, consulte o artigo [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).


Agora que tem uma descrição geral de informações básicas, leia as secções abaixo para compreender como aprovisionamento funciona no Azure AD e os cenários comuns Azure AD suporta.


## <a name="claims-in-azure-ad-security-tokens"></a>Em afirmações em Tokens de segurança do Azure AD

Tokens de segurança emitidos por Azure AD contêm em afirmações ou declarações de informações sobre o assunto que tenha sido autenticado. Estes afirmações podem ser utilizadas pela aplicação para várias tarefas. Por exemplo, podem ser utilizadas para validar o token, identificar o inquilino do diretório o assunto, apresentar informações do utilizador, determinar a autorização do assunto e assim sucessivamente. As afirmações presentes no qualquer token de segurança determinado são dependentes o tipo de token, o tipo de credencial utilizada para autenticar o utilizador e a configuração da aplicação. Uma breve descrição de cada tipo de afirmação emitido por Azure AD é fornecida na tabela abaixo. Para mais informações, consulte [Token suportados e os tipos de afirmações](active-directory-token-and-claims.md).


| Afirmação | Descrição |
|-------|-------------|
| ID da aplicação | Identifica a aplicação que está a utilizar o token.
| Audiência | Identifica o recurso destinatário que o token destina. |
| Referência de classe de contexto de autenticação de aplicação | Indica como o cliente foi autenticado (cliente público vs. confidencial cliente). |
| Autenticação instantâneas | A data e hora em que ocorreu a autenticação de registos. |
| Método de autenticação | Indica a forma como o assunto do token de foi autenticado (palavra-passe, certificado, etc.). |
| Nome próprio | Fornece o nome de indicado do utilizador definido no Azure AD. |
| Grupos | Contém grupos de Ids de Azure AD objeto que o utilizador é membro. |
| Fornecedor de identidades | Registos que foram autenticados o assunto do token de fornecedor de identidade. |
| Emitido na | Registos a hora em que foi emitido o token, frequentemente utilizado para a última modificação da token. |
| Emissor | Identifica o STS que emitida o token, bem como o inquilino do Azure AD. |
| Apelido | Fornece o apelido do utilizador como definir no Azure AD. |
| Nome | Fornece um valor humano legível que identifica o assunto do token de. |
| Id de objecto | Contém um identificador exclusivo, imutáveis do assunto no Azure AD. |
| Funções | Contém amigáveis nomes de funções da aplicação AD Azure que o utilizador tenha sido concedido. |
| Âmbito | Indica as permissões atribuídas à aplicação cliente. |
| Assunto | Indica o capital sobre o qual o token afirma informações. |
| Id de inquilino | Contém um identificador exclusivo, imutáveis do inquilino diretório que emitiu o token. |
| Duração do token | Define o intervalo de tempo dentro dos quais um token é válido. |
| Nome Principal de utilizador | Contém o nome principal de utilizador do assunto. |
| Versão | Contém o número da versão do token de. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Noções básicas de registar uma aplicação no Azure AD

Deverá estar registado junto a qualquer aplicação que outsources autenticação do Azure AD num diretório. Este passo envolve informar Azure AD sobre a aplicação, incluindo o URL onde se encontra, o URL para enviar respostas após a autenticação, o URI para identificar a aplicação e muito mais. Esta informação é necessária para algumas razões principais:

- Azure AD necessita de coordenadas para comunicar com a aplicação quando tokens de início de sessão ou troca de processamento. Estas incluem as seguintes:

  - URI de ID da aplicação: O identificador para uma aplicação. Este valor é enviada para Azure AD durante a autenticação para indicar que a aplicação que pretende que o autor da chamada um token para. Para além disso, este valor é incluída no token de, para que a aplicação sabe que estava o destino pretendido.


  - Responder URL e redirecionar URI: no caso de uma web API ou aplicação web, o URL de resposta é a localização para o qual Azure AD vai enviar a resposta de autenticação, incluindo um token se autenticação foi efetuada com êxito. No caso de uma aplicação nativa, o URI redirecionar é um identificador exclusivo para o qual Azure AD irá redirecioná-user-agent num pedido de OAuth 2.0.


  - ID de cliente: O ID de uma aplicação, que é gerado pelo Azure AD quando a aplicação é registada. Quando pedir um código de autorização ou token, o ID de cliente e a chave, são enviadas para Azure AD durante a autenticação.


  - Chave: A chave que é enviada juntamente com um ID de cliente ao autenticar para Azure AD para ligar para um web API.


- Precisa de Azure AD garantir que a aplicação tiver as permissões necessárias para aceder aos seus dados do diretório, outras aplicações na sua organização e assim sucessivamente

Aprovisionamento fica mais claro quando compreender o que não existem duas categorias de aplicações que podem ser desenvolvidas e integradas com o Azure AD:

- Pedido de inquilino único: uma aplicação de inquilino única destina-se a utilização de uma organização. Estes são aplicações do normalmente linha de negócio (LoB) escritas por um programador de empresa. Uma aplicação de inquilino única só tem de ser acedidas pelos utilizadores no um diretório e, como resultado,-lo só necessita de ser aprovisionada no diretório de um. Estas aplicações normalmente são registadas por um programador na organização.


- Aplicação do inquilino com várias: uma aplicação do inquilino com várias destina-se para utilização em muitas organizações, não apenas uma organização. Estes são normalmente software-como-a-(SaaS) aplicações de serviço escritas por um fornecedor de software independente (ISV). Inquilinos com várias aplicações tem de ser configurações para cada diretório onde serão utilizados, necessita de utilizador ou o administrador consentimento para registá-los. Este processo consentimento é iniciado uma aplicação foi registada no diretório e é concedida acesso a API do gráfico ou talvez outra web API. Quando um utilizador ou administrador a partir de uma organização diferente se inscreve utilizar a aplicação, são apresentados com uma caixa de diálogo que mostra as permissões que requer a aplicação. O utilizador ou o administrador pode, em seguida, o seu consentimento para a aplicação, que dá à aplicação acesso aos dados indicados e, por fim regista a aplicação no seu diretório. Para mais informações, consulte o artigo [Descrição geral do quadro consentimento](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Algumas considerações adicionais surgirem quando desenvolver uma aplicação do inquilino com várias em vez de uma aplicação de inquilino único. Por exemplo, se são tornar a aplicação disponível para utilizadores em vários directórios, terá de um mecanismos para determinar qual o inquilino se encontram. Só necessita de uma aplicação de inquilino único procurar no seu próprio diretório para um utilizador, enquanto que necessita de uma aplicação do inquilino com várias identificar um utilizador específico a partir de todos os directórios no Azure AD. Para realizar esta tarefa, o Azure AD fornece um ponto final autenticação comuns onde qualquer aplicação do inquilino com várias pode direccionar início de sessão no pedidos de, em vez de um ponto final de específicas do inquilino. Este ponto final é https://login.microsoftonline.com/common para todos os directórios no Azure AD, Considerando que um ponto final de específicas do inquilino poderá ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto final comuns é particularmente importante ter em consideração quando desenvolver a sua aplicação de uma vez que terá da lógica necessária para processar múltiplos inquilinos do durante a sessão, terminar sessão e token validação.

Se estiver atualmente a desenvolver uma aplicação de inquilino única mas pretende disponibilizar para muitas organizações, pode facilmente fazer alterações a aplicação e respectiva configuração no Azure AD para torná-lo com várias inquilino capazes. Além disso, o Azure AD utiliza a chave de assinatura mesmo para todos os tokens em todos os directórios, se estiver a fornecer autenticação numa única inquilino ou a aplicação do inquilino com várias.

Cada cenário listado neste documento inclui uma sub-secção que descreve os requisitos de aprovisionamento. Para obter informações mais detalhadas sobre uma aplicação no Azure AD e as diferenças entre as aplicações do inquilinos com várias e simples de aprovisionamento, consulte o artigo [Aplicações de integração com o Azure Active Directory](active-directory-integrating-applications.md) para obter mais informações. Continue a ler para compreender os cenários de aplicação comuns no Azure AD.

## <a name="application-types-and-scenarios"></a>Tipos de aplicações e aos cenários

Cada um dos cenários descritos neste documento pode ser desenvolvida utilizando vários idiomas e plataformas. Estas são todas as cópias por amostras de código completo que estão disponíveis no nosso [Guia de exemplos de código](active-directory-code-samples.md)ou diretamente a partir do correspondente [Github repositórios de exemplo](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Além disso, se a aplicação necessitar de uma parte específica ou segmento de um cenário de ponto a ponto, na maioria dos casos essa funcionalidade pode ser adicionada forma independente. Por exemplo, se tiver uma aplicação nativa que liga um web API, pode facilmente adicionar uma aplicação web que também chamadas web API. O diagrama seguinte ilustra estes cenários e tipos de aplicação, e como podem ser adicionados diferentes componentes:

![Tipos de aplicações e aos cenários](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Estes são os cinco cenários de aplicação primária suportados pelo Azure AD:

- [Browser para a aplicação Web](#web-browser-to-web-application): um utilizador tem de iniciar sessão para uma aplicação web que é protegida pelo Azure AD.

- [Aplicação de página única (SPA)](#single-page-application-spa): um utilizador tem de iniciar sessão para uma aplicação de única página que é protegida pelo Azure AD.

- [Aplicação nativa para Web API](#native-application-to-web-api): necessita de uma aplicação nativa que é executada num telemóvel, tablet ou PC autenticar um utilizador para obter recursos a partir de um API que é protegido pelo Azure AD.

- [Aplicação Web para Web API](#web-application-to-web-api): necessita de uma aplicação web obter recursos a partir de um API protegido por Azure AD.

- [Daemon ou a aplicação de servidor Web API](#daemon-or-server-application-to-web-api): necessita de uma aplicação daemon ou uma aplicação de servidor com a interface de utilizador não web obter recursos a partir de um API protegido por Azure AD.

### <a name="web-browser-to-web-application"></a>Browser para a aplicação Web

Esta secção descreve uma aplicação que autentica um utilizador num web browser para uma aplicação web. Neste cenário, a aplicação web indica o browser do utilizador para iniciar sessão-los no Azure AD. Azure AD devolve uma resposta de início de sessão através do browser do utilizador, que contém em afirmações sobre o utilizador num token de segurança. Este cenário suporta início de sessão utilizando os protocolos WS Federation, SAML 2.0 e OpenID ligar.


#### <a name="diagram"></a>Diagrama
![Fluxo de autenticação para browser a aplicação web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo


1. Quando um utilizador visita a aplicação e precisa de iniciar sessão, são redirecionadas através de um pedido de início de sessão para o ponto final de autenticação no Azure AD.


2. O utilizador inicia sessão na página Iniciar sessão.


3. Se a autenticação é efetuada com êxito, o Azure AD cria um token de autenticação e devolve uma resposta de início de sessão para o URL de resposta da aplicação que foi configurado no Portal de gestão do Azure. Para uma aplicação de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui afirmações sobre o utilizador e o Azure AD que forem exigidas pela aplicação para validar o token.


4. A aplicação de valida o token ao utilizar uma chave pública de assinatura e informações sobre o emissor disponível no documento de metadados de Federação do Azure AD. Após a aplicação de valida o token, Azure AD inicia uma nova sessão com o utilizador. Esta sessão permite ao utilizador aceder à aplicação até que expire.


#### <a name="code-samples"></a>Exemplos de código


Consulte os exemplos de código para Web Browser para os cenários de aplicação Web. E, verifica frequência – Recomendamos adicionar novas amostras sempre. [Browser para a aplicação Web](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>Se ter registado


- Inquilino única: Se estiver a criar uma aplicação para a sua organização,-deverá estar registado junto no diretório da sua empresa utilizando o Portal de gestão do Azure.


- Múltiplos inquilinos: Se estiver a criar uma aplicação que pode ser utilizada por utilizadores fora da sua organização, tem de estar registado no diretório da sua empresa, mas também deverá estar registado junto no diretório de cada organização que irá estar a utilizar a aplicação. Para tornar a aplicação disponível no seu diretório, pode incluir um processo de inscrição para os seus clientes que permite-lhes consentimento para a sua aplicação. Quando se inscreverem para a sua aplicação, serão apresentadas com uma caixa de diálogo que mostra as permissões que requer a aplicação e, em seguida, a opção consentimento. Consoante as permissões necessárias, um administrador da outra organização poderá ser necessário para dar o seu consentimento. Quando o utilizador ou administrador autorize, a aplicação é registada no seu diretório. Para mais informações, consulte o artigo [Aplicações de integração com o Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiração do token

A sessão do utilizador expira quando o tempo de vida do token emitido por Azure AD expira. A aplicação pode encurtar este período de tempo se pretender, tal como a assinatura de utilizadores com base num período de inatividade. Quando a sessão expira, será pedido ao utilizador para iniciar sessão novamente.





### <a name="single-page-application-spa"></a>Aplicação única página (SPA)

Esta secção descreve autenticação para uma única página de aplicação, que utiliza Azure AD e a autorização de acesso OAuth 2.0 implícita concedam para proteger os seu web API novamente terminar. Aplicações de página única normalmente estão estruturadas como uma camada de apresentação de JavaScript (front-end) que é executada no browser e Web API back-end que é executado num servidor e implementa lógica de negócio a aplicação. Para saber mais sobre a autorização implícita conceder e ajuda decidir se se trata de adequados para o seu cenário de aplicação, consulte o artigo [Compreender o fluxo de conceder implícito oauth2 ainda no Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Neste cenário, quando o utilizador inicia sessão, o JavaScript front end utilizações [biblioteca de autenticação do Active Directory para JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) e conceder a autorização implícitos para obter um token de ID (id_token) a partir do Azure AD. O token é colocada em cache e o cliente anexa-lo ao pedido de como a ligação OAuth quando efetuar chamadas para o respetivo Web API back-end, que é protegida utilizando o software OWIN intermédio. 
#### <a name="diagram"></a>Diagrama

![Diagrama de aplicação de página única](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo

1. O utilizador navega para a aplicação web.


2. A aplicação devolve o JavaScript front-end (camada de apresentação) para o browser.


3. O utilizador inicia sessão, por exemplo, clicando num ligação de início de sessão. O browser envia obter para o ponto final de autorização Azure AD para pedir um token de ID. Este pedido inclui o URL de ID e resposta do cliente nos parâmetros de consulta.


4. Azure AD valida o URL de resposta contra o URL de resposta registado que foi configurado no Portal de gestão do Azure.


5. O utilizador inicia sessão na página Iniciar sessão.


6. Se a autenticação é efetuada com êxito, o Azure AD cria um token de ID e devolve-o como um fragmento de URL (#) para o URL de resposta da aplicação. Para uma aplicação de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui afirmações sobre o utilizador e o Azure AD que forem exigidas pela aplicação para validar o token.


7. O código do cliente JavaScript em execução no browser extrai o token de resposta para utilizar em proteger chamadas para web a aplicação que novamente API termina.


8. Browser chamadas web a aplicação novamente API termina com o token de acesso no cabeçalho da autorização.

#### <a name="code-samples"></a>Exemplos de código


Consulte os exemplos de código para cenários de aplicação de página única (SPA). Certifique-se de que verifica frequência – Recomendamos adicionar novas amostras sempre. [Aplicação única página (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>Se ter registado


- Inquilino única: Se estiver a criar uma aplicação para a sua organização,-deverá estar registado junto no diretório da sua empresa utilizando o Portal de gestão do Azure.


- Múltiplos inquilinos: Se estiver a criar uma aplicação que pode ser utilizada por utilizadores fora da sua organização, tem de estar registado no diretório da sua empresa, mas também deverá estar registado junto no diretório de cada organização que irá estar a utilizar a aplicação. Para tornar a aplicação disponível no seu diretório, pode incluir um processo de inscrição para os seus clientes que permite-lhes consentimento para a sua aplicação. Quando se inscreverem para a sua aplicação, serão apresentadas com uma caixa de diálogo que mostra as permissões que requer a aplicação e, em seguida, a opção consentimento. Consoante as permissões necessárias, um administrador da outra organização poderá ser necessário para dar o seu consentimento. Quando o utilizador ou administrador autorize, a aplicação é registada no seu diretório. Para mais informações, consulte o artigo [Aplicações de integração com o Azure Active Directory](active-directory-integrating-applications.md).

Depois de registar a aplicação, tem de ser configurado para utilizar OAuth 2.0 implícito conceder protocolo. Por predefinição, este protocolo é desativado para aplicações. Para ativar o protocolo de conceder implícito oauth2 ainda para a sua aplicação, transferir manifesto sua aplicação a partir do Portal de gestão do Azure, defina o valor de "oauth2AllowImplicitFlow" para true e, em seguida, carregar o manifesto anterior ao portal. Para obter instruções detalhadas, consulte o artigo [Ativar OAuth 2.0 implícito conceder para única página de aplicações](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiração do token

Quando utiliza ADAL.js para gerir a autenticação com Azure AD, beneficiar várias funcionalidades que facilitam a atualizar um token expirado, bem como a introdução tokens para recursos de API web adicionais que podem ser chamados pela aplicação. Quando o utilizador com êxito autentica com Azure AD, é estabelecida uma sessão protegida por um cookie para o utilizador entre o browser e o Azure AD. É importante ter em atenção que a sessão existe entre o utilizador e o Azure AD e não entre o utilizador e a aplicação web em execução no servidor. Quando um token de expira, ADAL.js utiliza esta sessão para obter silenciosamente o token de outra. Faz isto utilizando um iFrame oculto para enviar e receber o pedido utilizando o protocolo de conceder implícito OAuth. ADAL.js também pode utilizar este mesmo mecanismo para silenciosamente obter os tokens de acesso a partir do Azure AD para outros web API recursos que as chamadas de aplicação estes recursos de suporte recursos de publicação em origem partilha (CORS), desde que estão registadas no diretório do utilizador e qualquer consentimento necessário foi atribuído pelo utilizador durante a sessão.


### <a name="native-application-to-web-api"></a>Aplicação nativa Web API


Esta secção descreve uma aplicação nativa que liga um web API em nome de um utilizador. Este cenário é criado no tipo de conceder OAuth 2.0 autorização código com um cliente público, conforme descrito na secção 4.1 da [especificação OAuth 2.0](http://tools.ietf.org/html/rfc6749). Aplicação nativa obtém um token de acesso do utilizador através do protocolo OAuth 2.0. Este token de acesso é enviada, em seguida, no pedido de para a web API, que autoriza o utilizador e devolve o recurso pretendido.

#### <a name="diagram"></a>Diagrama

![Aplicação nativa Web API do diagrama](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Fluxo de autenticação para a aplicação nativa à API

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo


Se estiver a utilizar as bibliotecas de autenticação AD, a maior parte dos detalhes do protocolo descritos abaixo é processados por si, como o pop-up do browser, colocação em cache token e processamento de tokens de atualização.

1. Utilizar um browser pop-up, que a aplicação nativa torna um pedido para o ponto final de autorização no Azure AD. Este pedido inclui o ID de cliente e o redirecionamento URI da aplicação nativa conforme mostrado no Portal de gestão e a aplicação ID URI para a web API. Se o utilizador já não tiver iniciado sessão, for pedidos para iniciar sessão novamente


2. Azure AD autentica o utilizador. Se esta é uma aplicação do inquilino com várias e consentimento é necessário para utilizar a aplicação, o utilizador será exigido que consentimento se que ainda não tiver feito. Depois de conceder consentimento e relativamente a autenticação com êxito, Azure AD problemas de uma resposta de código de autorização para redirecionar a aplicação de cliente URI.


3. Quando uma resposta de código de autorização novamente para o redirecionamento URI de problemas do Azure AD, a aplicação de cliente deixa de interação do browser e retira o código de autorização da resposta. Utilizar este código autorização, a aplicação de cliente envia um pedido para o ponto final token Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID de cliente e redireccionar URI) e o recurso pretendido (aplicação ID URI para a web API).


4. O código de autorização e informações sobre a API de web e de aplicação de cliente são validados pelo Azure AD. Após a validação com êxito, Azure AD devolve dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD devolve informações básicas sobre o utilizador, tal como a sua apresentação nome e inquilino ID.


5. Por HTTPS, a aplicação de cliente utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Ligação" no cabeçalho do pedido de autorização para a web API. Em seguida, web API valida o token JWT e se validação for concluído com êxito, devolve o recurso pretendido.


6. Quando o token de acesso expira, a aplicação de cliente vai receber um erro que indica que o utilizador precisa autenticar novamente. Se a aplicação tem um token de atualização válido, pode ser utilizado para adquirir um novo token de acesso sem perguntar ao utilizador para iniciar sessão novamente. Se o token de atualização de expirar, a aplicação terá de forma interativa autenticar o utilizador mais uma vez.


> [AZURE.NOTE] O token de atualização emitido por Azure AD pode ser utilizado para aceder a várias recursos. Por exemplo, se tiver uma aplicação de cliente que tem permissão para ligar duas web APIs, o token de atualização pode ser utilizado para obter um acesso token para o outro web API também.


#### <a name="code-samples"></a>Exemplos de código


Consulte os exemplos de código para aplicação nativa aos cenários Web API. E, verifica frequência – Recomendamos adicionar novas amostras sempre. [Aplicação nativa Web API](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>Se ter registado


- Inquilino única: Ambas o nativo aplicação e da web API tem de estar registados no mesmo directório no Azure AD. Web API pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o acesso a aplicação nativa seus recursos. A aplicação de cliente, em seguida, seleciona as permissões pretendidas a partir do menu pendente "Permissões para outras aplicações" no Portal de gestão do Azure.


- Múltiplos inquilinos: em primeiro lugar, a aplicação nativa apenas nunca registado no programador ou do diretório do publisher. Em segundo lugar, a aplicação nativa está configurada para indicar que as permissões, é necessário para serão funcionais. Esta lista de permissões necessárias é apresentada numa caixa de diálogo quando um utilizador ou administrador no directório de destino dá consentimento para a aplicação, que torna disponível em à sua organização. Algumas aplicações requerem permissões de apenas nível de utilizador, as quais podem consentimento da todos os utilizadores na organização. Outras aplicações requerem permissões do nível de administrador, as quais um utilizador na organização não é possível consentimento para. Apenas um administrador de diretório pode dar o seu consentimento para as aplicações que requerem este nível de permissões. Quando o utilizador ou administrador autorize, apenas web API é registada no seu diretório. Para mais informações, consulte o artigo [Aplicações de integração com o Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiração do token


Quando a aplicação nativa utiliza respectivo código de autorização para obter um acesso JWT token, também recebe um token de atualização JWT. Quando expira o token de acesso, o token de atualização pode ser utilizado para voltar a autenticar o utilizador sem necessidade de iniciar sessão novamente. Este token de atualização, em seguida, é utilizado para autenticar o utilizador que resulta numa nova token de acesso e token de atualização.





### <a name="web-application-to-web-api"></a>Aplicação Web para Web API


Esta secção descreve uma aplicação web que necessita para obter recursos a partir de um API. Neste cenário, existem dois tipos de identidade que pode utilizar a aplicação web para autenticar e chamar web API: uma identidade de aplicação ou uma identidade de utilizador delegada.

*Identidade da aplicação:* Este cenário utiliza conceder de credenciais de cliente OAuth 2.0 para autenticar como a aplicação e aceder à web API. Ao utilizar uma identidade de aplicação, da web API só pode detetar que a aplicação web é chamá-lo, como web API não receber as informações sobre o utilizador. Se a aplicação recebe informações sobre o utilizador, será enviado através do protocolo de aplicação e não está assinado pelo Azure AD. Web API fidedignidades que o utilizador autenticado, a aplicação web. Por este motivo, este padrão chama um subsystem fidedigna.

*Delegada identidade do utilizador:* Neste cenário, pode ser feito de duas maneiras: OpenID ligar e conceder de código de autorização de OAuth 2.0 com um cliente confidencial. A aplicação web obtém um token de acesso do utilizador, o que prova para a web API que o utilizador autenticado com êxito para a aplicação web e de que a aplicação web conseguiu obter uma identidade de utilizador delegada para ligar a web API. Este token de acesso é enviado no pedido de para a web API, que autoriza o utilizador e devolve o recurso pretendido.

#### <a name="diagram"></a>Diagrama

![Aplicação Web ao diagrama Web API](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo

A identidade da aplicação e de tipos de identidade do utilizador delegado são explicados o fluxo abaixo. A diferença entre as mesmas chave é que a identidade do utilizador delegada têm primeiro de adquirir um código de autorização antes do utilizador pode iniciar sessão e obter acesso para a web API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Conceder as credenciais de identidade da aplicação com cliente OAuth 2.0

1. Um utilizador tiver sessão iniciado Azure AD na aplicação web (consulte o [Browser para a aplicação Web](#web-browser-to-web-application) acima).


2. A aplicação web tem de adquirir um token de acesso para que possa autenticar para a web API e obter o recurso pretendido. Torna um pedido para o ponto final de token Azure AD, fornecer a credencial, ID de cliente e de aplicação web do API ID URI.


3. Azure AD autentica a aplicação e devolve um token de acesso JWT que é utilizado para ligar a web API.


4. Por HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Ligação" no cabeçalho do pedido de autorização para a web API. Em seguida, web API valida o token JWT e se validação for concluído com êxito, devolve o recurso pretendido.

##### <a name="delegated-user-identity-with-openid-connect"></a>Ligar a identidade do utilizador delegada com OpenID

1. Um utilizador tiver sessão iniciado aplicação web Azure AD (consulte a secção de [Browser para a aplicação Web](#web-browser-to-web-application) acima). Se o utilizador da aplicação web não deu ainda para permitir a aplicação web telefonar a web API do seu nome, o utilizador terá de consentimento. A aplicação irá apresentar as permissões, é necessário e, se qualquer um destes permissões ao nível do administrador, um utilizador normal no diretório não poderão consentimento. Este processo consentimento só se aplica a aplicações do inquilinos com várias, aplicações de inquilino não único, como a aplicação irá já possui as permissões necessárias. Quando o utilizador tiver sessão iniciada, a aplicação web recebido um token de ID com informações sobre o utilizador, bem como um código de autorização.


2. Utilizar o código de autorização emitido por Azure AD, a aplicação web envia um pedido para o ponto final token Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID de cliente e redireccionar URI) e o recurso pretendido (aplicação ID URI para a web API).


3. O código de autorização e informações sobre a aplicação web e web API são validados pelo Azure AD. Após a validação com êxito, Azure AD devolve dois tokens: um token de acesso JWT e um token de atualização JWT.


4. Por HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Ligação" no cabeçalho do pedido de autorização para a web API. Em seguida, web API valida o token JWT e se validação for concluído com êxito, devolve o recurso pretendido.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade do utilizador delegada com OAuth 2.0 autorização código conceder

1. Um utilizador já iniciou sessão numa aplicação web, cujo mecanismo de autenticação seja independente dos Azure AD.


2. A aplicação web requer um código de autorização para adquirir um token de acesso, de modo que emite um pedido de através do browser para o ponto final de autorização Azure AD, fornecer o ID de cliente e redirecionar URI para a aplicação web após a autenticação efetuada com êxito. O utilizador inicia sessão Azure AD.


3. Se o utilizador da aplicação web não deu ainda para permitir a aplicação web telefonar a web API do seu nome, o utilizador terá de consentimento. A aplicação irá apresentar as permissões, é necessário e, se qualquer um destes permissões ao nível do administrador, um utilizador normal no diretório não poderão consentimento. Este processo consentimento só se aplica a aplicações do inquilinos com várias, aplicações de inquilino não único, como a aplicação irá já possui as permissões necessárias.


4. Depois do utilizador deu, a aplicação web recebe o código de autorização que necessita de adquirir um token de acesso.


5. Utilizar o código de autorização emitido por Azure AD, a aplicação web envia um pedido para o ponto final token Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID de cliente e redireccionar URI) e o recurso pretendido (aplicação ID URI para a web API).


6. O código de autorização e informações sobre a aplicação web e web API são validados pelo Azure AD. Após a validação com êxito, Azure AD devolve dois tokens: um token de acesso JWT e um token de atualização JWT.


7. Por HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Ligação" no cabeçalho do pedido de autorização para a web API. Em seguida, web API valida o token JWT e se validação for concluído com êxito, devolve o recurso pretendido.

#### <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para a aplicação Web para cenários Web API. E, verifica frequência – Recomendamos adicionar novas amostras sempre. [Aplicação Web API](active-directory-code-samples.md#web-application-to-web-api)a Web.


#### <a name="registering"></a>Se ter registado

- Inquilino única: De identidade da aplicação e casos de identidade do utilizador delegado, a aplicação web e web API deverá estar registado junto no mesmo directório no Azure AD. Web API pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o acesso da aplicação web aos seus recursos. Se está a ser utilizado um tipo de identidade do utilizador delegado, tem a aplicação web selecionar as permissões pretendidas a partir do menu pendente "Permissões para outras aplicações" no Portal de gestão do Azure. Este passo não é necessário se o tipo de identidade da aplicação está a ser utilizado.


- Múltiplos inquilinos: em primeiro lugar, a aplicação web está configurada para indicar que as permissões, é necessário para serão funcionais. Esta lista de permissões necessárias é apresentada numa caixa de diálogo quando um utilizador ou administrador no directório de destino dá consentimento para a aplicação, que torna disponível em à sua organização. Algumas aplicações requerem permissões de apenas nível de utilizador, as quais podem consentimento da todos os utilizadores na organização. Outras aplicações requerem permissões do nível de administrador, as quais um utilizador na organização não é possível consentimento para. Apenas um administrador de diretório pode dar o seu consentimento para as aplicações que requerem este nível de permissões. Quando o utilizador ou administrador autorize, a aplicação web e web API ambos estão registados no seu diretório.

#### <a name="token-expiration"></a>Expiração do token

Quando a aplicação web utiliza o respectivo código de autorização para obter um acesso JWT token, também recebe um token de atualização JWT. Quando expira o token de acesso, o token de atualização pode ser utilizado para voltar a autenticar o utilizador sem necessidade de iniciar sessão novamente. Este token de atualização, em seguida, é utilizado para autenticar o utilizador que resulta numa nova token de acesso e token de atualização.


### <a name="daemon-or-server-application-to-web-api"></a>Daemon aplicação ou de servidor Web API


Esta secção descreve uma aplicação daemon ou servidor de que necessita para obter recursos a partir de um API. Existem dois sub-cenários que se aplicam a nesta secção: um daemon que necessita para ligar a um web API, criada com base em OAuth 2.0 credenciais conceder de tipo de cliente; e uma aplicação de servidor (tal como uma web API) que necessita para ligar a um web API, criada com base em especificação de rascunho OAuth 2.0 On-Behalf-Of.

Para o cenário quando necessita de uma aplicação daemon ligar a um web API, é importante compreender algumas coisas. Em primeiro lugar, interação do utilizador não é possível com uma aplicação de daemon, requer a aplicação para que a sua própria identidade. Um exemplo de uma aplicação daemon é um processo, ou um serviço do sistema operativo a ser executada em segundo plano. Este tipo de aplicação os pedidos de um token de acesso utilizando a sua identidade da aplicação e apresentar o seu cliente ID, credencial (palavra-passe ou certificado) e de aplicações ID URI para Azure AD. Depois de autenticação com êxito, o daemon recebe um token de acesso a partir do Azure AD, em seguida, é utilizado para ligar para a web API.

Para o cenário quando necessita de uma aplicação de servidor ligar a um web API, é útil utilizar um exemplo. Imagine que um utilizador tem autenticado numa aplicação nativa e esta aplicação nativa ligar para um web API. Azure AD emite um token de acesso JWT para ligar a web API. Se necessitar da API web ligar a outra descendentes web API, pode utilizar o fluxo em nome de para delegar a identidade do utilizador e autenticar à segunda camada web API.

#### <a name="diagram"></a>Diagrama

![Daemon aplicação ou de servidor a Diagrama Web API](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Conceder as credenciais de identidade da aplicação com cliente OAuth 2.0

1. Em primeiro lugar, tem a aplicação do servidor autenticar com Azure AD como propriamente dito, sem qualquer interação humana como uma caixa de diálogo de interativa início de sessão. Torna um pedido para o ponto final de token Azure AD, fornecer as credenciais, ID de cliente e de aplicação ID URI.


2. Azure AD autentica a aplicação e devolve um token de acesso JWT que é utilizado para ligar a web API.


3. Por HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Ligação" no cabeçalho do pedido de autorização para a web API. Em seguida, web API valida o token JWT e se validação for concluído com êxito, devolve o recurso pretendido.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade do utilizador delegada com especificação de rascunho em nome de OAuth 2.0

O fluxo de outros fabricantes referido abaixo assume que um utilizador tenha sido autenticado na outra aplicação (tal como uma aplicação nativa) e a identidade do utilizador foi utilizada para adquirir um token de acesso para a primeira camada da web API.

1. Aplicação nativa envia o token de acesso para a primeira camada da web API.


2. A API do primeiro camada web envia um pedido para o ponto final token Azure AD, fornecer o seu cliente ID e as credenciais, bem como token de acesso do utilizador. Além disso, o pedido é enviado com um on_behalf_of parâmetro que indica a web API está a pedir tokens novos para ligar a um web descendentes API em nome de utilizador original.


3. Azure AD verifica se o primeiro camada web API tem permissões para aceder a segunda camada web API e valida o pedido, devolver um token de acesso JWT e um JWT atualizar token de para a primeira camada da web API.


4. Por HTTPS, a API do primeiro camada web, em seguida, liga-lhe segunda camada web API por acrescentar a cadeia token no cabeçalho no pedido de autorização. Primeira camada web API pode continuar a ligar a segunda camada da web API desde o token de acesso e atualização de tokens são válidos.

#### <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para Daemon aplicação ou de servidor para cenários Web API. E, verifica frequência – Recomendamos adicionar novas amostras sempre. [Servidor ou Daemon aplicação Web API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Se ter registado

- Inquilino única: De identidade da aplicação e casos de identidade do utilizador delegado, a aplicação de servidor ou daemon deverá estar registado junto no mesmo directório no Azure AD. Web API pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o daemon ou acesso do servidor aos seus recursos. Se está a ser utilizado um tipo de identidade do utilizador delegado, tem a aplicação do servidor selecionar as permissões pretendidas a partir do menu pendente "Permissões para outras aplicações" no Portal de gestão do Azure. Este passo não é necessário se o tipo de identidade da aplicação está a ser utilizado.


- Múltiplos inquilinos: em primeiro lugar, a aplicação de servidor ou daemon está configurada para indicar que as permissões, é necessário para serão funcionais. Esta lista de permissões necessárias é apresentada numa caixa de diálogo quando um utilizador ou administrador no directório de destino dá consentimento para a aplicação, que torna disponível em à sua organização. Algumas aplicações requerem permissões de apenas nível de utilizador, as quais podem consentimento da todos os utilizadores na organização. Outras aplicações requerem permissões do nível de administrador, as quais um utilizador na organização não é possível consentimento para. Apenas um administrador de diretório pode dar o seu consentimento para as aplicações que requerem este nível de permissões. Quando o utilizador ou administrador autorize, ambos web APIs registadas no seu diretório.

#### <a name="token-expiration"></a>Expiração do token

Quando a primeira aplicação utiliza o respectivo código de autorização para obter um acesso JWT token, também recebe um token de atualização JWT. Quando expira o token de acesso, o token de atualização pode ser utilizado para voltar a autenticar o utilizador sem perguntar para introduzir as credenciais. Este token de atualização, em seguida, é utilizado para autenticar o utilizador que resulta numa nova token de acesso e token de atualização.

## <a name="see-also"></a>Consulte também

[Guia do programador do Azure Active Directory](active-directory-developers-guide.md)

[Exemplos de código do Azure Active Directory](active-directory-code-samples.md)

[Informações importantes sobre como iniciar sessão sobreposição chave no Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

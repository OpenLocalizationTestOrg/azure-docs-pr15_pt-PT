<properties
   pageTitle="Noções sobre a oauth2 ainda implícito conceder fluxo no Azure Active Directory | Microsoft Azure"
   description="Saiba mais sobre a implementação do Azure Active Directory da oauth2 ainda implícita conceder fluxo, e se está à direita para a sua aplicação."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Noções sobre o fluxo de conceder implícito oauth2 ainda no Azure Active Directory (AD)

A conceder implícito oauth2 ainda é notorious para a ser conceder com a lista de preocupações de segurança na especificação de oauth2 ainda mais longa. E ainda, que é a abordagem implementada por ADAL JS e aquele que recomendamos quando escrever SPA aplicações. O que dá? É todos os uma questão de concessões: e como desativa, a conceder implícito é a melhor abordagem pode prosseguir para as aplicações que consumam uma API de Web através de JavaScript a partir de um browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a conceder implícito oauth2 ainda?

Quintessential [conceder do código de autorização de oauth2 ainda](https://tools.ietf.org/html/rfc6749#section-1.3.1) está a conceder autorização que utiliza dois pontos finais separados. O ponto final de autorização é utilizado para a fase de interação do utilizador, que resulta num código de autorização. O ponto final do token, em seguida, é utilizado pelo cliente para o intercâmbio de código para um token de acesso e frequentemente um token de atualização, assim. Aplicações Web são necessárias para apresentar os seus próprios credenciais de aplicação para o ponto final do token, para que o servidor de autorização pode autenticar o cliente.

[Conceder a oauth2 ainda implícita](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante permitindo um cliente obter um token de acesso (e id_token, no caso de [OpenId ligar](http://openid.net/specs/openid-connect-core-1_0.html)) diretamente a partir do ponto final de autorização, sem contactar o ponto final do token nem autenticar a aplicação de cliente. Esta variante foi concebido especificamente para JavaScript baseados aplicações em execução num Web browser: na especificação de oauth2 ainda original, tokens são devolvidos num fragmento URI. Que torna os bits tokens disponíveis para o código JavaScript no cliente, mas garante que não serão incluídas no redirecionamentos por defeito até o servidor. Devolver tokens através do browser redireciona diretamente a partir do ponto final de autorização. Também não tem a vantagem de eliminando qualquer requisitos para cruzada chamadas de origem, que são necessários se a aplicação de JavaScript é necessário para o ponto final do token de contacto.

Uma característica importante da conceder implícito oauth2 ainda é o facto de tal fluxos de tokens de atualização nunca remetente para o cliente. Como podemos ver na secção seguinte, que não é realmente necessário e na verdade seria um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a conceder implícito oauth2 ainda

Tal como a especificação da oauth2 ainda declara, a conceder implícito tem foram concebido para permitir agente do utilizador de aplicações – isto é, aplicações JavaScript executar dentro de um browser. A definir característica desses pedidos é que o código JavaScript é utilizado para aceder a recursos do servidor (normalmente uma API Web) e para atualizar a aplicação UX em conformidade. Pense de aplicações, como o Gmail ou o Outlook Web Access: Quando seleciona uma mensagem da sua pasta a receber, apenas o painel de visualização de mensagem é alterado para apresentar a nova seleção, enquanto o resto da página permanece foram modificado. Este é ao contrário tradicionais com base no Word Web apps, onde cada interação utilizador resulta numa nova de colocação de página inteira e uma composição de página completa da resposta do servidor novo.

Aplicações que tiram a abordagem JavaScript com base para o lado são designadas por única página de aplicações, ou estações termais: a ideia é que as aplicações servem apenas uma página HTML inicial e JavaScript associado, com todas as interações subsequentes a ser condicionada por chamadas da Web API executadas através de JavaScript. No entanto, abordagens híbrido, onde a aplicação é principalmente condicionada pelo nova colocação mas executa ocasionais JS chamadas, não são antigas – o debate sobre a utilização do fluxo implícito é relevante para aqueles também.

Aplicações baseadas em redirecionar seguro, normalmente, os respetivos pedidos através de cookies, no entanto, que abordagem não funciona bem para JavaScript aplicações. Cookies só funcionam contra o domínio que terem sido gerados para, enquanto JavaScript chamadas poderão ser direccionadas direção a outros domínios. Na verdade, que frequentemente será as maiúsculas/minúsculas: Pense das aplicações invocar API do Microsoft Graph, API do Office, Azure API – todos os que residem fora do domínio a partir de onde a aplicação é servida. Uma tendência de crescimento para aplicações JavaScript deve ter em todos os sem back-end confiar no festa 3º APIs da Web para implementar a sua função de negócio a 100%.

Atualmente, o método preferencial proteger as chamadas para um Web API é utilizar a oauth2 ainda portadores token abordagem, onde cada chamada está a acompanhar por um token de acesso oauth2 ainda. A API Web examina token de acesso de entrada e, se encontra na-os âmbitos necessários,-concede acesso a operação pedida. O fluxo implícito fornece um mecanismo conveniente para aplicações JavaScript obter tokens de acesso para um Web API, que oferece várias vantagens em relação à cookies:

- Tokens podem ser obtidos sujeito sem qualquer necessidade de cruzada origem chamadas – obrigatório registo de redirecionamento URI a que sejam devolvidos tokens garanta que tokens não são deslocadas
- As aplicações de JavaScript podem obter tantas tokens de acesso à medida que necessitam e para tantas Web APIs fazem alvo – com nenhuma restrição de domínios
- Funcionalidades de HTML5, como sessão ou armazenamento local conceder controlo total sobre a colocação em cache token e gestão de tempo de vida, Considerando que a gestão de cookies é opaca para a aplicação
- Tokens de acesso não são sensíveis a ataques de falsificação (CSRF) de pedido de publicação em vários sites

O fluxo de conceder implícito não emite tokens de atualização, principalmente por motivos de segurança. Um token de atualização não é como sentido estrito limitadas ao como tokens de acesso, conceder power muito mais do que, por conseguinte, a provocar extremidade mais danos caso-é fuga. No fluxo de implícito tokens são entregues no URL, por conseguinte, o risco de intercepção é superior a conceder de código de autorização.

No entanto, tenha em atenção que uma aplicação de JavaScript tem outro mecanismo à sua disposição para renovar tokens de acesso sem repetidamente perguntar ao utilizador para introduzir as credenciais. A aplicação pode utilizar um iframe oculto para executar novos pedidos de tokens contra o ponto final de autorização do Azure AD: desde que o browser ainda tem uma sessão activa (ler: tem um cookie de sessão) contra domínio Azure AD, o pedido de autenticação com êxito pode ocorrer sem qualquer necessidade de interação do utilizador. 

Este modelo concede a aplicação de JavaScript a capacidade de forma independente renovar tokens de acesso e até mesmo adquirir novos para uma nova API (desde que o utilizador anteriormente manifestado acordo das mesmas. Isto evita os encargos adicionados de adquirir, manter e proteger um artefacto valor elevado, tal como um token de atualização. Artefacto que faz a renovação silenciosa possíveis, cookie da sessão Azure AD, for gerido fora da aplicação. Outra vantagem desta abordagem é que um utilizador pode sessão a partir do Azure AD, utilizar qualquer uma das aplicações iniciou sessão no Azure AD, a ser executada em qualquer um dos separadores do browser. Isto resulta a eliminação de cookie da sessão de Azure AD e a aplicação de JavaScript automaticamente irá perder a capacidade de renovar tokens para o utilizador com a sessão iniciada saída.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Conceder a implícito é adequado para a minha aplicação?

Conceder a implícito apresenta riscos mais que outros concede. As áreas que precisa para pagar a atenção para bem encontram-se documentados (por exemplo consulte [Utilização indevida de acesso Token para representar o proprietário do recurso no fluxo implícito] [ OAuth2-Spec-Implicit-Misuse] e [OAuth 2.0 ameaça modelo e considerações de segurança][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco superior é em grande medida devido ao facto de destina-se para permitir que as aplicações que executar código ativo, servido por um recurso remoto para um browser. Se está a planear uma arquitetura SPA tiver sem componentes de back-end ou tenciona invocar uma API Web através de JavaScript, a utilização do fluxo implícito para aquisição token é recomendada.

Se a aplicação for um cliente nativo, o fluxo implícito não é uma excelente ajustar. Falta de cookie da sessão Azure AD no contexto de um cliente nativo privar a aplicação a partir do meio de manutenção de uma sessão de tempo lived. O que significa as aplicações do repetidamente irá pedir ao utilizador quando a obtenção de tokens de acesso para novos recursos.

Se estiver a desenvolver uma aplicação Web que inclui uma back-end e consumir uma API a partir do seu código back-end, o fluxo implícito também não é uma boa opção. Outros concede dar-lhe power muito mais. Por exemplo, a conceder de credenciais de cliente oauth2 ainda fornece a capacidade de obter tokens refletem as permissões atribuídas à aplicação de si, por oposição a delegações de utilizador. Isto significa que o cliente tem a capacidade para manter o acesso aos recursos de programação mesmo quando um utilizador não esteja ativamente envolvido numa sessão e assim sucessivamente. Não só que, mas essas concede dar mais elevadas garantias de segurança. Por exemplo, tokens de acesso de trânsito nunca através do browser do utilizador, não a ser guardadas no histórico de browser do risco etc. A aplicação de cliente também pode executar autenticação forte quando pedir um token.

## <a name="next-steps"></a>Próximos passos

- Para obter uma lista completa de recursos para programadores, incluindo informações de referência para os protocolos e oauth2 ainda autorização conceder monetários suporte por Azure AD, direcione para o [Azure AD para programadores do guia][AAD-Developers-Guide]
- Veja [como integrar uma aplicação do Azure AD]  [ ACOM-How-To-Integrate] para profundidade adicional sobre o processo de integração de aplicação.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819


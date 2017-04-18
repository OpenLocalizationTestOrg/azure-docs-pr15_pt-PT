<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Os tipos de tokens emitidos no B2C de diretório ativa Azure."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>


# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: Referência Token

Azure Active Directory (Azure AD) B2C emite vários tipos de tokens de segurança, tal como processa cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, características de segurança e conteúdos de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens

Azure AD B2C suporta o [protocolo de autorização de OAuth 2.0](active-directory-b2c-reference-protocols.md), que faça uso de tokens de acesso e tokens de atualização. Também suporta autenticação e iniciar sessão através de [OpenID ligar](active-directory-b2c-reference-protocols.md), que apresenta um tipo de terceiro do token de: o token de ID. Cada um dos seguintes tokens é representada como uma ligação OAuth.

Uma ligação OAuth é um token de segurança lightweight que concede o acesso de "ligação" para um recurso protegido. A ligação é qualquer uma das partes que pode apresentar o token. Azure AD tem autenticar-se primeiro uma festa antes de-pode receber uma ligação OAuth. Mas, se não forem tomados os passos necessários para proteger o token na transmissão e armazenamento, pode ser interceptada e utilizado por das partes indesejada. Alguns tokens de segurança têm um mecanismo incorporado para impedir que as partes não autorizadas utilizá-los, mas tokens de tipo de ligação não têm este mecanismo. Devem ser transportados num canal seguro, tal como a segurança da camada de transporte (HTTPS).

Se uma ligação OAuth é transmitido fora de um canal seguro, uma festa maliciosa pode utilizar um homem no meio ataque para adquirir o token e utilizá-la para obter acesso não autorizado a um recurso protegido. Aplicam os mesmos princípios de segurança quando tokens portadores são armazenados ou em cache para utilização posterior. Certifique-se sempre que a sua aplicação transmite e armazena tokens de tipo de ligação de uma forma segura.

Para considerações de segurança adicionais no tokens de tipo de ligação, consulte o artigo [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens de problemas do Azure AD B2C são implementados como tokens de web JSON (JWTs). Um JWT constitui um meio compacto, seguro para URL de transferência de informações entre duas partes. JWTs contêm informações conhecidas como em afirmações. Estes são declarações de informações sobre o tipo de ligação e o assunto do token de. Os pedidos no JWTs estejam objetos JSON que são codificados e serializados para a transmissão. Uma vez que JWTs emitidos por Azure AD B2C são assinados, mas não encriptados, pode facilmente de inspecionar os conteúdos de uma JWT para depurá-lo. Estão disponíveis várias ferramentas que pode fazê-lo, incluindo [calebb.net](http://calebb.net). Para mais informações sobre JWTs, consulte [especificações JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de ID

Um token de ID é um formulário do token de segurança que receba a sua aplicação partir B2C de AD o Azure `authorize` e `token` pontos finais. Tokens de ID são representados como [JWTs](#types-of-tokens)e contêm em afirmações que pode utilizar para identificar os utilizadores na sua aplicação. Quando são adquiridos tokens de ID a partir de `authorize` ponto final, são utilizadas com frequência para os utilizadores para aplicações web de início de sessão. Quando são adquiridos tokens de ID a partir de `token` ponto final, estes podem ser enviados nos pedidos HTTP durante a comunicação entre dois componentes da mesma aplicação ou serviço. Pode utilizar as afirmações num token de ID conforme pretender. São normalmente utilizados para apresentar as informações da conta ou tomar decisões de controlo de acesso numa aplicação.  

Tokens de ID tem sessão iniciadas, mas não estiverem encriptados neste momento. Quando a sua aplicação ou API recebe um token de ID, tem de [Validar a assinatura](#token-validation) para revelar-se de que o token é autêntico. Sua aplicação ou API também tem de validar afirmações alguns no token de para provar que é válida. Dependendo dos requisitos de cenário, podem variar afirmações validadas por uma aplicação, mas a aplicação deve efetuar algumas [comuns afirmação validações](#token-validation) cada cenário.

#### <a name="sample-id-token"></a>Token de ID de exemplo
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Tokens de acesso

Um token de acesso também é um formulário do token de segurança que receba a sua aplicação partir B2C de AD o Azure `authorize` e `token` pontos finais. Tokens de acesso também são representados como [JWTs](#types-of-tokens)e contêm em afirmações que pode utilizar para identificar os utilizadores na sua serviços web e APIs.

Tokens de acesso tem sessão iniciadas, mas não são muito semelhantes às tokens de id e encriptada neste momento.  Tokens de acesso deverão ser utilizados para fornecer acesso aos serviços web e APIs e para identificar & autenticar o utilizador desses serviços.  No entanto, não fornece qualquer declaração de autorização desses serviços.  Isto é que o `scp` afirmação na tokens de acesso não limitar ou representam caso contrário, o acesso que tenha sido concedido o assunto do token de.

Quando o seu API recebe um token de acesso, tem [Validar a assinatura](#token-validation) para revelar-se de que o token é autêntico. A API também tem de validar afirmações alguns no token de para provar que é válida. Dependendo dos requisitos de cenário, podem variar afirmações validadas por uma aplicação, mas a aplicação deve efetuar algumas [comuns afirmação validações](#token-validation) cada cenário.

### <a name="claims-in-id--access-tokens"></a>Em afirmações no ID e Tokens de acesso

Quando utiliza o Azure AD B2C, tem extensivamente controlo sobre o conteúdo do seu tokens. Pode configurar [políticas](active-directory-b2c-reference-policies.md) para enviar a determinados conjuntos de dados de utilizador em afirmações que requer a sua aplicação de suas operações. Estes afirmações podem incluir propriedades padrão, tais como o utilizador `displayName` e `emailAddress`. Pode também incluir [atributos de utilizador personalizada](active-directory-b2c-reference-custom-attr.md) que pode definir no seu diretório B2C. Cada ID e acesso token de que recebe irá conter um determinado conjunto de afirmações relacionadas com segurança. As aplicações podem utilizar estes em afirmações para autenticar segura os utilizadores e pedidos.

Tenha em atenção que as afirmações em tokens de ID não são devolvidas numa ordem particular. Além disso, podem ser introduzidos novos pedidos em tokens de ID em qualquer altura. A aplicação não deve quebrar como novas em afirmações são introduzidas. Eis as afirmações que esperava existir em tokens de ID e acesso emitidos por Azure AD B2C. Quaisquer afirmações adicionais serão determinadas pelo políticas. Para praticar, experimente da inspeção de afirmações no token de ID de exemplo ao colá-la como [calebb.net](http://calebb.net). Podem encontrar mais detalhes na [especificação OpenID ligar](http://openid.net/specs/openid-connect-core-1_0.html).

| Nome | Afirmação | Valor de exemplo | Descrição |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audiência | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Uma audiência afirmação identifica o destinatário pretendido do token de. Azure AD B2C, a audiência é ID a aplicação da aplicação, como atribuída para a sua aplicação no portal do registo de aplicação. A sua aplicação deverá validar este valor e rejeitar o token se não coincidir com. |
| Emissor | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Esta afirmação identifica o serviço de tokens de segurança (STS) que constrói e devolve o token. Também identifica do diretório do Azure AD na qual o utilizador foi autenticado. A aplicação deverá validar a afirmação emissor para se certificar de que o token é proveniente do ponto final 2.0. |
| Emitido na | `iat` | `1438535543` | Esta afirmação é a hora em que foi emitido o token, representado em tempo época. |
| Tempo de expiração | `exp` | `1438539443` | O tempo de expiração afirmação é a hora em que o token torna-se inválida, representado em hora época. A aplicação deve utilizar este pedido para confirmar a validade da duração do token.  |
| Não antes de | `nbf` | `1438535543` | Esta afirmação é a hora em que o token torna-se válido, representado em vez de época. Este nome costuma igual à hora que o token foi emitido. A aplicação deve utilizar este pedido para confirmar a validade da duração do token.  |
| Versão | `ver` | `1.0` | Esta é a versão do ID token, conforme definido pelo Azure AD. |
| Código hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código está incluído num token de ID apenas quando o token é emitido juntamente com um código de autorização OAuth 2.0. Um hash código pode ser utilizado para validar a autenticidade de um código de autorização. Veja a [especificação OpenID ligar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como executar esta validação. |
| Hash token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash token de acesso é incluído num token de ID apenas quando o token é emitido juntamente com um token de acesso OAuth 2.0. Um hash token de acesso pode ser utilizado para validar a autenticidade de um token de acesso. Veja a [especificação OpenID ligar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como executar esta validação. |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia de utilizado para mitigar ataques de repetição token. A aplicação, pode especificar um nonce num pedido de autorização, utilizando o `nonce` parâmetro de consulta. O valor fornecer no pedido de será emitido inalterados na `nonce` reclamar de apenas um token de ID. Isto permite a sua aplicação verificar se o valor em relação ao valor-especificado no pedido, associa sessão na aplicação de um determinado token de ID. A aplicação deverá executar esta validação durante o processo de validação token de ID. |
| Assunto | `sub` | `Not supported currently. Use oid claim.` | Este é um capital sobre o qual o token afirma informações, como o utilizador de uma aplicação. Este valor é imutáveis e não podem ser reatribuída ou reutilizados. Pode ser utilizado para executar verificações de autorização em segurança, tal como quando o token é utilizado para aceder a um recurso. No entanto, o pedido de assunto ainda não está implementado no B2C de AD Azure. Deve configurar as políticas para incluir o ID do objeto `oid` reclamar e utilize o valor para identificar os utilizadores em vez de utilizar a afirmação assunto para autorização. |
| Referência de classe de contexto de autenticação | `acr` | `b2c_1_sign_in` | Este é o nome da política que foi utilizado para adquirir o token de ID.  |
| Tempo de autenticação | `auth_time` | `1438535543` | Esta afirmação é a hora em que um último introduzido credenciais de utilizador, representado em tempo época. |


### <a name="refresh-tokens"></a>Atualizar tokens

Atualizar tokens são tokens de segurança que pode utilizar a aplicação para adquirir novas tokens de ID e aceder a tokens de um fluxo de OAuth 2.0. Fornecem sua aplicação com acesso a longo prazo a recursos em nome de utilizadores sem necessidade de interação com esses utilizadores.

Para receber uma atualização token numa resposta token, a aplicação terá de pedir a `offline_acesss` âmbito. Para saber mais sobre o `offline_access` âmbito, consulte a [referência de protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).

Atualizar tokens são e será sempre, completamente opaca para a sua aplicação. São emitidos por Azure AD e podem ser seja inspeccionadas e interpretadas apenas por Azure AD. São longa vida, mas a sua aplicação não deve ser escrita com a expetativa que irá durar um token de atualização para um período de tempo específico. Tokens de atualização podem ser invalidados em qualquer momento para uma variedade de motivos. É a única forma para a sua aplicação saber se um token de atualização é válido tentar resgatá-lo ao efetuar um pedido de token para Azure AD.

Quando um token de atualização para um novo token resgatar (e se tiver sido concedida a sua aplicação de `offline_access` âmbito), receberá um novo token de atualização na resposta token. Deverá guardar o token de atualização emitido recentemente. Deve substituir o token de atualização que utilizou anteriormente no pedido de. Isto ajuda a garantir que o seu tokens de atualização permanecem válidos para tão longas quanto possível.

## <a name="token-validation"></a>Validação token

Para validar um token, a sua aplicação deve verificar a assinatura e o em afirmações do token de.

Bibliotecas de abrir origem muitos estão disponíveis para validar JWTs, dependendo do seu idioma preferido. Recomendamos que explore estas opções em vez de implementar a sua própria lógica de validação. As informações neste guia podem ajudá-lo a aprender a utilizar corretamente nessas bibliotecas.

### <a name="validate-the-signature"></a>Validar a assinatura
Um JWT contém três segmentos, separados pelo `.` caráter. O primeiro segmento é o **cabeçalho**, o segundo é o **corpo**e o terceiro é a **assinatura**. O segmento de assinatura pode ser utilizado para validar autenticidade do token de modo a que pode ser considerado fidedigno pela sua aplicação.

Azure AD B2C tokens tem sessão iniciadas através da utilização dos algoritmos norma da indústria assimétricos encriptação, tal como RSA 256. O cabeçalho do token de contém informações sobre o método de encriptação e chave utilizado para assinar o token de:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar o token. O `kid` afirmação indica a chave pública específica que foi utilizada para assinar o token.

Em qualquer altura determinada, Azure AD pode assinar um token utilizando qualquer uma de um determinado conjunto de pares de chaves privadas público. Azure AD roda o conjunto de teclas possível periodicamente, deverá ser escrita a sua aplicação para processar automaticamente essas alterações chaves. Uma frequência razoável para verificar a existência de actualizações para as chaves públicas utilizadas pelo Azure AD é a cada 24 horas.

Azure AD B2C tem um extremo de metadados OpenID ligar. Esta opção permite-aplicações obter informações sobre Azure AD B2C o tempo de execução. Estas informações incluem os pontos finais, conteúdo de tokens e token de chaves de assinatura. Diretório da sua B2C contém um documento de metadados JSON para cada política. Por exemplo, o documento de metadados para o `b2c_1_sign_in` política na `fabrikamb2c.onmicrosoft.com` está localizado em:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`é o directório B2C utilizado para autenticar o utilizador e `b2c_1_sign_in` é a política utilizada para adquirir o token. Para determinar qual a política foi utilizada para assinar um token (e onde ir para obter os metadados), tem duas opções. Em primeiro lugar, o nome da política é incluído na `acr` reclamar no token de. Pode analisar em afirmações fora do corpo da JWT por base-64 descodificar corpo e anular a serialização a cadeia JSON que resulta. O `acr` afirmação será o nome da política que foi utilizado para emitir o token.  A outra opção é codificar a política do valor da `state` parâmetro quando emitir o pedido e, em seguida, codificá-lo para determinar qual a política foi utilizada. Qualquer método indicado é válido.

O documento de metadados é um objeto JSON que contém várias partes úteis de informações. Estas incluem a localização dos pontos finais necessárias para executar a autenticação OpenID ligar. Também incluir `jwks_uri`, que dá a localização do conjunto de chaves públicas que são utilizadas para assinar tokens. Que localização é fornecida aqui, mas é melhor obter a localização dinamicamente utilizando o documento de metadados e de análise saída `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

O documento JSON que se encontra neste URL contém todas as informações da chave públicas utilizado num momento específico. Pode utilizar a aplicação a `kid` reclamar no cabeçalho da JWT para selecionar a chave pública no documento JSON que é utilizado para assinar um nomeadamente token. -Em seguida, pode executar a validação de assinatura ao utilizar a correta chave pública e o algoritmo indicado.

Uma descrição de como executar a validação de assinatura está fora do âmbito deste documento. Bibliotecas de abrir origem muitos estão disponíveis para o ajudar com este caso precise da mesma.

### <a name="validate-the-claims"></a>Valide as afirmações
Quando a sua aplicação ou API recebe um token de ID,-lo também deve executar verificações de várias contra a violação no token de ID. Estes incluem, mas não estão limitados a:

- A **audiência** afirmação: esta operação verifica que o token de ID foi concebido para ser fornecidas para a sua aplicação.
- As afirmações **não antes** e **tempo de expiração** : estes Certifique-se de que o token de ID não expirou.
- A afirmação **emissor** : esta operação verifica se o token foi emitido para a sua aplicação por Azure AD.
- O **nonce**: Esta é uma estratégia de mitigação de ataque token de reprodução.

Para obter uma lista completa de validações que deve efetuar a sua aplicação, consulte a [especificação OpenID ligar](https://openid.net). Detalhes dos valores esperados para estes afirmações são incluídos na [secção token](#types-of-tokens)anterior.  

## <a name="token-lifetimes"></a>Durações tokens

As seguintes durações tokens são fornecidas para promover os seus conhecimentos. Podem ajudá-lo quando desenvolver e depurar aplicações. Tenha em atenção que as suas aplicações não devem ser escritas esperar qualquer um dos seguintes durações para que permaneçam constantes. Podem e irá alterar.  Pode ler mais sobre a personalização do tokens durações no Azure AD B2C [aqui](active-directory-b2c-token-session-sso.md).

| Token | Durante a vida. | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| Tokens de ID | Uma hora | Tokens de ID são normalmente válidas para uma hora. A aplicação web do pode utilizar este período de vida para manter as suas próprias sessões com utilizadores (recomendados). Também pode escolher a duração de uma sessão diferentes. Se a sua aplicação precisa de obter um novo ID token, basta necessita fazer um novo pedido de início de sessão no Azure AD. Se um utilizador tem uma sessão de browser válido com o Azure AD, esse utilizador poderá não ser necessário introduzir novamente as credenciais. |
| Atualizar tokens | Até 14 dias | Um token de atualização única seja válido para um máximo de 14 dias. No entanto, um token de atualização pode tornar-se inválidas em qualquer altura para qualquer número das razões. A aplicação deve continuar a tentar utilizar um token de atualização até que o pedido irá falhar, ou até que a sua aplicação substitui o token de atualização com um novo.  Um token de atualização também pode tornar-se inválidas se cerca de 90 dias passou uma vez que o utilizador introduziu última credenciais. |
| Códigos de autorização | Cinco minutos | Códigos de autorização são intencionalmente curto. Estes devem ser reembolsadas imediatamente para tokens de acesso, tokens de ID ou atualização tokens quando são recebidas. |

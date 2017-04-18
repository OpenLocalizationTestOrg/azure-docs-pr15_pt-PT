 <properties
   pageTitle="Referência do Azure AD Token | Microsoft Azure"
   description="Um guia para compreender e avaliação de afirmações nos tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos por Azure Active Directory (AAD)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Referência de token Azure AD

Azure Active Directory (Azure AD) emite vários tipos de tokens de segurança no processamento de cada fluxo de autenticação. Este documento descreve o formato, características de segurança e conteúdos de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens

Azure AD suporta o [protocolo de autorização de OAuth 2.0](active-directory-protocols-oauth-code.md), que faça uso de access_tokens e refresh_tokens.  Também suporta a autenticação e iniciar sessão através de [OpenID ligar](active-directory-protocols-openid-connect-code.md), que apresenta um tipo de terceiro do token, o id_token.  Cada um dos seguintes tokens é representada como um "de ligação OAuth".

Uma ligação OAuth é um token de segurança lightweight que concede o acesso de "ligação" para um recurso protegido. Neste sentido, "portadores" são qualquer uma das partes que pode apresentar o token. Apesar da autenticação com Azure AD é necessária para receberem uma ligação OAuth, devem ser tomados passos para proteger o token de, para impedir intercepção por das partes indesejada. Uma vez que tokens de tipo de ligação não possui um mecanismos incorporados para impedir que as partes não autorizadas a partir de utilizá-los, devem ser transportados num canal seguro, tal como a segurança da camada de transporte (HTTPS). Se uma ligação OAuth é transmitido em claro, um homem-in o nome do meio ataque pode ser utilizado para adquirir o token e acesso não autorizado a um recurso protegido. Aplicam os mesmos princípios de segurança quando armazenar ou colocação em cache tokens de tipo de ligação para utilizar posteriormente. Certifique-se sempre que a sua aplicação transmite e armazena tokens de tipo de ligação de uma forma segura. Para obter mais considerações de segurança no tokens de tipo de ligação, consulte o artigo [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos por Azure AD são implementados como JSON Web Tokens ou JWTs.  Um JWT constitui um meio compacto, seguro para URL de transferência de informações entre duas partes.  As informações contidas no JWTs são conhecidos como "em afirmações" ou declarações de informações sobre o tipo de ligação e o assunto do token de.  Os pedidos no JWTs estejam objetos JSON codificado e serializado para transmissão.  Uma vez que JWTs emitidos por Azure AD são tiver sessão iniciadas, mas não encriptados, pode facilmente de inspecionar os conteúdos de uma JWT para fins de depuração.  Existem várias ferramentas disponíveis para fazê-lo, tal como [jwt.calebb.net](http://jwt.calebb.net). Para mais informações sobre JWTs, pode referir-se para a [especificação de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens são uma forma de token de segurança de início de sessão na sua aplicação receba quando efetuar a autenticação utilizando [OpenID ligar](active-directory-protocols-openid-connect-code.md).  São representados como [JWTs](#types-of-tokens)e contêm em afirmações que pode utilizar para assinar o utilizador na sua aplicação.  Pode utilizar as afirmações num id_token conforme pretender - frequentemente utilizados para apresentar as informações da conta ou tomar decisões de controlo de acesso numa aplicação.

Id_tokens são tiver sessão iniciada, mas não encriptado neste momento.  Quando a aplicação recebe um id_token, tem de [Validar a assinatura](#validating-tokens) a provar a autenticidade o token e validar afirmações alguns no token de para provar a sua validade.  As afirmações validadas por uma aplicação variam consoante a requisitos de cenário, mas existem algumas [comuns afirmação validações](#validating-tokens) que a sua aplicação deve efetuar cada cenário.

Consulte a secção seguinte para obter informações sobre id_tokens afirmações, bem como um id_token de exemplo.  Tenha em atenção que as afirmações no id_tokens não são devolvidas numa ordem particular.  Além disso, novos pedidos podem ser introduzidos id_tokens em qualquer ponto no tempo - a aplicação não deve quebrar como novas em afirmações são introduzidas.  A lista seguinte inclui as afirmações que sujeito pode interpretar a sua aplicação no momento deste escrita.  Se for necessário, pode encontrar ainda mais detalhe na [especificação OpenID ligar](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Id_token de exemplo

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Para praticar, experimente da inspeção de afirmações no id_token exemplo ao colá-la como [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Em afirmações no id_tokens

| JWT afirmação | Nome | Descrição |
|-----------|------|-------------|
| `appid`| ID da aplicação | Identifica a aplicação que está a utilizar o token para aceder a um recurso. A aplicação pode agir como si próprio ou em nome de um utilizador. Normalmente, o ID da aplicação representa um objeto de aplicação, mas também pode representar um objecto principal de serviço no Azure AD. <br><br> **Exemplo JWT valor**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Audiência | O destinatário pretendido do token de. A aplicação que recebe o token tem de verificar que o valor de audiência está correto e rejeitar qualquer tokens destinados a uma audiência diferente. <br><br> **Exemplo SAML valor**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Exemplo JWT valor**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Referência de classe de contexto de autenticação de aplicação | Indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se forem utilizados ID de cliente e o segredo de cliente, o valor é 1. <br><br> **Exemplo JWT valor**: <br> `"appidacr": "0"`|
| `acr`| Referência de classe de contexto de autenticação | Indica a forma como o assunto foi autenticado, por oposição o cliente na afirmação de referência de classe de contexto de autenticação de aplicação. Um valor de "0" indica que a autenticação de utilizador final não cumprir os requisitos de ISO/IEC 29115. <br><br> **Exemplo JWT valor**: <br> `"acr": "0"`|
| | Autenticação instantâneas | Registos a data e hora em que ocorreu autenticação. <br><br> **Exemplo SAML valor**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Método de autenticação | Identifica a forma como o assunto do token de foi autenticado. <br><br> **Exemplo SAML valor**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Exemplo JWT valor**:`“amr”: ["pwd"]` |
| `given_name`| Nome próprio | Fornece o primeiro ou "tendo em conta" nome do utilizador, conforme definidas no objeto do Azure AD utilizador. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `"given_name": "Frank"` |
| `groups`| Grupos | Fornece os IDs de objeto que representam os membros do grupo o assunto. Estes valores são exclusivo (consulte ID do objeto) e podem ser utilizados em segurança para gerir o acesso, tal como impor autorização para aceder a um recurso. Os grupos incluídos na afirmação de grupos estão configurados numa base por aplicação, através da propriedade "groupMembershipClaims" de manifesto da aplicação. Um valor nulo irá excluir todos os grupos, um valor de "SecurityGroup" irá incluir apenas as associações de grupo de segurança do Active Directory e um valor de "Todos" irá incluem grupos de segurança e listas de distribuição do Office 365. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Fornecedor de identidades | Registos que foram autenticados o assunto do token de fornecedor de identidade. Este valor é idêntico ao valor da afirmação emissor a menos que seja a conta de utilizador num inquilino diferentes que o emissor. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Guarda a hora em que foi emitido o token. É frequentemente utilizado para medir a última modificação da token. <br><br> **Exemplo SAML valor**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Exemplo JWT valor**: <br> `"iat": 1390234181` |
| `iss` | Emissor | Identifica o serviço de tokens de segurança (STS) que constrói e devolve o token. Em tokens que Azure AD devolve, o emissor é sts.windows.net. GUID o valor de afirmação emissor é o ID de inquilino do diretório do Azure AD. O ID do inquilino é um identificador imutáveis e fiável do diretório. <br><br> **Exemplo SAML valor**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Exemplo JWT valor**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Apelido | Fornece o último nome, apelido ou família nome do utilizador, tal como foi definido no objeto do Azure AD utilizador. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `"family_name": "Miller"` |
| `unique_name`| Nome | Fornece um valor humano legível que identifica o assunto do token de. Este valor não há garantia de ser exclusivos dentro de um inquilino e foi concebido para ser utilizado apenas para fins de visualização. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID de objecto | Contém um identificador exclusivo de um objeto no Azure AD. Este valor é imutáveis e não podem ser reatribuída ou reutilizados. Utilize o ID do objeto para identificar um objeto em consultas para Azure AD. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Funções | Representa todas as funções de aplicação que o assunto foi concedido diretamente e indiretamente através dos membros do grupo e pode ser utilizado para impor o controlo de acesso baseado em funções. Funções da aplicação são definidas por aplicação base em, através de `appRoles` propriedade de manifesto da aplicação. O `value` propriedade de cada função aplicação é o valor que é apresentado na afirmação de funções. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Âmbito | Indica as permissões de representação concedidas à aplicação cliente. A permissão predefinido é `user_impersonation`. O proprietário do recurso seguro pode registar valores adicionais no Azure AD. <br><br> **Exemplo JWT valor**: <br> `"scp": "user_impersonation"`|
| `sub` |Assunto| Identifica o capital sobre o qual o token afirma informações, como o utilizador de uma aplicação. Este valor é imutáveis e não é possível reatribuir ou reutilizados, pelo que pode ser utilizada para executar verificações de autorização em segurança. Uma vez que o assunto é sempre presente nos tokens os problemas do Azure AD, é recomendável utilizar este valor num sistema de autorização de objectivo geral. <br> `SubjectConfirmation`Não é uma afirmação. Descreve como o assunto do token de fica verificado. `Bearer`indica que o assunto é confirmado ao seu dispor do token de. <br><br> **Exemplo SAML valor**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Exemplo JWT valor**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID de inquilino | Um identificador de imutáveis, não reutilizável que identifica o inquilino do diretório que emitiu o token. Pode utilizar este valor para aceder a recursos de diretório de inquilino específicas numa aplicação do inquilino com várias. Por exemplo, pode utilizar este valor para identificar o inquilino numa chamada à Graph API. <br><br> **Exemplo SAML valor**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Exemplo JWT valor**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Duração do token | Define o intervalo de tempo dentro dos quais um token é válido. O serviço que valida o token deve verificar que a data atual é no interior a duração do token, pessoa-deve rejeitar o token. O serviço poderão permitir que para fora do intervalo de duração do token até cinco minutos para ter em conta quaisquer diferenças na hora do relógio ("Hora distorção") entre Azure AD e o serviço. <br><br> **Exemplo SAML valor**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Exemplo JWT valor**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nome Principal de utilizador | Armazena o nome de utilizador do capital de utilizador.<br><br> **Exemplo JWT valor**: <br> `"upn": frankm@contoso.com`|
| `ver`| Versão | Armazena o número da versão do token de. <br><br> **Exemplo JWT valor**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso só são consumíveis pelo Microsoft Services neste ponto específico.  As suas aplicações não devem ter de executar qualquer validação ou inspeção de tokens de acesso para qualquer um dos cenários atualmente suportados.  Pode são processados os tokens de acesso como completamente opaca - são basta cadeias que a sua aplicação pode passar para a Microsoft nos pedidos HTTP.

Quando pedir um token de acesso, o Azure AD devolve também alguns metadados sobre o token de acesso para consumo da sua aplicação.  Estas informações incluem o tempo de validade do token de acesso e os âmbitos para o qual é válida.  Esta opção permite-sua aplicação para efetuar a colocação em cache inteligente de tokens de acesso sem ter de analisar abrir o token de acesso própria.

## <a name="refresh-tokens"></a>Atualizar tokens

Atualizar tokens são tokens de segurança, pode utilizar a aplicação para adquirir novas tokens de acesso num fluxo de OAuth 2.0.  Permitir a aplicação alcançar a longo prazo acesso aos recursos em nome de um utilizador sem necessidade de interação pelo utilizador.

Atualização tokens são com várias recursos, que significa que podem ser recebidos durante um pedido de token para um recurso, mas resgatar para tokens de acesso a um recurso completamente diferente. Para especificar múltiplos recursos, defina o `resource` parâmetro no pedido de para o recurso de destino.

Atualização tokens são completamente opacas para a sua aplicação. São longa vida, mas a sua aplicação não deve ser escrita esperar que irá durar um token de atualização durante qualquer período de tempo.  Tokens de atualização podem ser invalidados momento qualquer várias razões.  É a única forma para a sua aplicação saber se um token de atualização é válido tentar resgatá-lo ao efetuar um pedido de token ao ponto final token do Azure AD.

Ao resgatar um token de atualização para um novo token de acesso, receberá um novo token de atualização na resposta token.  Deverá guardar o token de atualização recentemente emitido, substituindo a que utilizou no pedido de.  Isto irá garante que seu tokens de atualização permanecem válidos para tão longas quanto possível.

## <a name="validating-tokens"></a>Validar tokens

Neste ponto no momento, a validação apenas token que deve ser necessário para executar a aplicação de cliente está a validar id_tokens.  Para poder validar um id_token, a aplicação deverá validar assinatura a id_token e as afirmações na id_token.

Fornecemos bibliotecas e exemplos de código que mostram como lidar facilmente a validação do token, se pretender compreender o processo subjacente.  Também existem várias bibliotecas de terceiros abrir origem para validação JWT, pelo menos uma opção para quase todas as plataforma e idioma. Para mais informações sobre bibliotecas de autenticação do Azure AD e exemplos de código, consulte o artigo [bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validar a assinatura

Um JWT contém três segmentos, separados pelo `.` caráter.  O primeiro segmento é conhecido como **cabeçalho**, o segundo como o **corpo**e a terceira como a **assinatura**.  O segmento de assinatura pode ser utilizado para validar a autenticidade da id_token para que pode ser considerado fidedigno pela sua aplicação.

Id_Tokens são assinados utilizem indústria algoritmos de encriptação assimétricos padrão, tais como RSA 256. O cabeçalho da id_token contém informações sobre o método de encriptação e chave utilizado para assinar o token de:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar do token, enquanto o `x5t` afirmação indica a chave pública específica que foi utilizada para assinar o token.

Em qualquer dado momento, Azure AD pode assinar uma id_token utilizando qualquer uma de um determinado conjunto de pares de chaves privadas público. Azure AD roda o conjunto possível de teclas numa base periódica, para que a sua aplicação deve ser escrita para processar automaticamente essas alterações chaves.  Uma frequência razoável para verificar a existência de actualizações para as chaves públicas utilizadas pelo Azure AD é a cada 24 horas.

Pode adquirir os assinatura principais dados necessários para validar a assinatura utilizando o documento de metadados OpenID ligar-se que se encontra no:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Experimente este URL num browser!

Este documento de metadados é um objeto JSON que contém várias partes úteis de informações, tais como a localização dos vários pontos finais necessários para realizar autenticação OpenID ligar.  

Também inclui uma `jwks_uri`, que dá a localização do conjunto de chaves públicas utilizado para assinar tokens.  O documento JSON que se encontra na `jwks_uri` contém todas as informações da chave públicas utilizado nesse momento específico.  Pode utilizar a aplicação a `kid` reclamar no cabeçalho JWT para selecionar qual chave pública neste documento foi utilizado para assinar um nomeadamente token.  -Lo, em seguida, pode executar a validação de assinatura com a chave pública correta e o algoritmo indicado.

Executar a validação de assinatura está fora do âmbito deste documento - existem muitos bibliotecas de abrir origem para o ajudar a fazê-lo, se necessário.

#### <a name="validating-the-claims"></a>Validar as em afirmações

Quando a aplicação recebe um id_token após o início de sessão do utilizador,-deve também executar alguns controlos contra a violação de id_token.  Estes incluem mas não estão limitados a:

  - A **audiência** afirmação - Certifique-se de que o id_token foi concebido para ser fornecidas para a sua aplicação.
  - As **Não antes** e **Tempo de expiração de** afirmações - para verificar se o id_token não expirou.
  - A **emissor** afirmação - verificar se o token de facto foi emitido para a sua aplicação por Azure AD.
  - O **Nonce** - a mitigar um ataque de repetição token.
  - e muito mais...

Para uma lista completa de validações afirmação que deve efetuar a sua aplicação, consulte a [especificação OpenID ligar](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalhes dos valores esperados para estes afirmações são incluídos na secção [id_token secção](#id-tokens) anterior.

## <a name="sample-tokens"></a>Tokens de exemplo

Esta secção apresenta das amostras do SAML e JWT tokens que Azure AD devolve. Estes exemplos permitem-lhe ver as afirmações no contexto.
SAML Token

Este é um exemplo de um token SAML típico.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT Token - representação de utilizador

Este é um exemplo de um típico JSON web o token (JWT) utilizado num fluxo de conceder autorização código.
Para além de afirmações, o token inclui um número de versão em **ver** e **appidacr**, a referência de classe de contexto de autenticação, que indica a forma como o cliente foi autenticado. Para um cliente público, o valor é 0. Se tiver sido utilizado um ID de cliente ou segredo cliente, o valor é 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Conteúdo relacionado
- Consulte as Azure AD Graph [operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e a [entidade de política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), para obter mais informações sobre a gestão de política de duração do token através da API do Azure AD Graph.
- Para obter mais informações e exemplos sobre como gerir políticas de através do PowerShell cmdlets, incluindo as amostras, consulte o artigo [configuráveis durações tokens no Azure AD](active-directory-configurable-token-lifetimes.md). 

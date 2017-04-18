<properties
    pageTitle="Referência de token Azure AD 2.0 | Microsoft Azure"
    description="Os tipos de tokens e em afirmações emitidas pelo ponto final 2.0"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="v20-token-reference"></a>referência de token 2.0

O ponto final 2.0 emite vários tipos de tokens de segurança no processamento de cada [fluxo de autenticação](active-directory-v2-flows.md). Este documento descreve o formato, características de segurança e conteúdos de cada tipo de token.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Tipos de tokens

O ponto final 2.0 suporta o [protocolo de autorização de OAuth 2.0](active-directory-v2-protocols.md), que faça uso de access_tokens e refresh_tokens.  Também suporta a autenticação e iniciar sessão através de [OpenID ligar](active-directory-v2-protocols.md#openid-connect-sign-in-flow), que apresenta um tipo de terceiro do token, o id_token.  Cada um dos seguintes tokens é representada como um "de ligação OAuth".

Uma ligação OAuth é um token de segurança lightweight que concede o acesso de "ligação" para um recurso protegido. Neste sentido, "portadores" são qualquer uma das partes que pode apresentar o token. Apesar de uma festa tem autenticar-se primeiro com o Azure AD para receber a ligação OAuth, se não forem tomados os passos necessários para proteger o token na transmissão e armazenamento, pode ser interceptada e utilizado por das partes indesejada. Enquanto alguns tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas utilizá-los, tokens de tipo de ligação não têm este mecanismo e devem ser transportados num canal seguro, tal como a segurança da camada de transporte (HTTPS). Se uma ligação OAuth é transmitido em claro, um homem-in o nome do meio ataque pode ser utilizado por uma festa maliciosa para adquirir o token e utilizá-lo para um acesso não autorizado a um recurso protegido. Aplicam os mesmos princípios de segurança quando armazenar ou colocação em cache tokens de tipo de ligação para utilizar posteriormente. Certifique-se sempre que a sua aplicação transmite e armazena tokens de tipo de ligação de uma forma segura. Para obter mais considerações de segurança no tokens de tipo de ligação, consulte o artigo [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo ponto final 2.0 são implementados como Json Web Tokens ou JWTs.  Um JWT constitui um meio compacto, seguro para URL de transferência de informações entre duas partes.  As informações contidas no JWTs são conhecidos como "em afirmações" ou declarações de informações sobre o tipo de ligação e o assunto do token de.  Os pedidos no JWTs estejam objetos JSON codificado e serializado para transmissão.  Uma vez que JWTs emitidos pelo ponto final 2.0 são tiver sessão iniciadas, mas não encriptados, pode facilmente de inspecionar os conteúdos de uma JWT para fins de depuração. Para mais informações sobre JWTs, pode referir-se para a [especificação de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens são uma forma de token de segurança de início de sessão na sua aplicação receba quando efetuar a autenticação utilizando [OpenID ligar](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  São representados como [JWTs](#types-of-tokens)e contêm em afirmações que pode utilizar para assinar o utilizador na sua aplicação.  Pode utilizar as afirmações num id_token conforme pretender - frequentemente utilizados para apresentar as informações da conta ou tomar decisões de controlo de acesso numa aplicação.  O ponto final 2.0 apenas problemas de um tipo de id_token, que tem um conjunto de afirmações independentemente do tipo de utilizador que tem sessão iniciada no consistente.  Que está a dizer que o formato e o conteúdo da id_tokens será o mesmo para ambos os utilizadores Account Microsoft pessoais e contas escolar ou profissional.

Id_tokens são tiver sessão iniciada, mas não encriptado neste momento.  Quando a aplicação recebe um id_token, tem de [Validar a assinatura](#validating-tokens) a provar a autenticidade o token e validar afirmações alguns no token de para provar a sua validade.  As afirmações validadas por uma aplicação variam consoante a requisitos de cenário, mas existem algumas [comuns afirmação validações](#validating-tokens) que a sua aplicação deve efetuar cada cenário.

Detalhes completos nas afirmações no id_tokens são fornecidos abaixo, assim como uma id_token de exemplo.  Tenha em atenção que as afirmações no id_tokens não são devolvidas numa ordem particular.  Além disso, novos pedidos podem ser introduzidos id_tokens em qualquer ponto no tempo - a aplicação não deve quebrar como novas em afirmações são introduzidas.  A lista abaixo inclui as afirmações que sujeito pode interpretar a sua aplicação no momento deste escrita.  Se for necessário, pode encontrar ainda mais detalhe na [especificação OpenID ligar](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Id_token de exemplo

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Para praticar, experimente da inspeção de afirmações no id_token exemplo ao colá-la como [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Em afirmações no id_tokens
| Nome | Afirmação | Valor de exemplo | Descrição |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audiência | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identifica o destinatário pretendido do token de.  No id_tokens, a audiência é Id a aplicação da aplicação, tal como atribuído para a sua aplicação no portal do registo de aplicação.  A sua aplicação deverá validar este valor e rejeitar o token se não coincidir com. |
| Emissor | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identifica o serviço de tokens de segurança (STS) que constrói e devolve o token, bem como o inquilino do Azure AD na qual o utilizador foi autenticado.  A aplicação deverá validar a afirmação emissor para se certificar de que o token é proveniente do ponto final 2.0.  Deverá também utilizar a parte guid da afirmação para restringir o conjunto de inquilinos que são permitidos para iniciar sessão na aplicação.  O guid que indica que o utilizador é um utilizador do consumidor da Microsoft conta é `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Emitido na | `iat` | `1452285331` | A hora em que foi emitido o token, representado em tempo época. |
| Tempo de expiração | `exp` | `1452289231` | A hora em que o token torna-se inválida, representado em tempo época.  A aplicação deve utilizar este pedido para confirmar a validade da duração do token.  |
| Não antes de | `nbf` | `1452285331` |  A hora em que se torna o token de válido, representado em tempo época. Normalmente, é igual a hora de emissão.  A aplicação deve utilizar este pedido para confirmar a validade da duração do token.  |
| Versão | `ver` | `2.0` | A versão do id_token, conforme definido pelo Azure AD.  Para o ponto final 2.0, o valor será `2.0`. |
| Id de inquilino | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Um guid que representa o Azure AD inquilino que é o utilizador a partir de.  Para contas de trabalho e profissional, o guid será o ID do inquilino imutáveis da organização que o utilizador pertence.  Para contas pessoais, o valor será `9188040d-6c67-4c5b-b112-36a304b66dad`.  O `profile` âmbito for necessário para receber este pedido. |
| Código Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash código é incluído no id_tokens apenas quando o id_token é emitido juntamente com um código de autorização OAuth 2.0.  Pode ser utilizado para validar a autenticidade de um código de autorização.  Consulte [especificação OpenID ligar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como efectuar esta validação. |
| Hash Token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash token de acesso é incluído no id_tokens apenas quando o id_token é emitido juntamente com um token de acesso OAuth 2.0.  Pode ser utilizado para validar a autenticidade de um token de acesso.  Consulte [especificação OpenID ligar-se](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como efectuar esta validação. |
| Nonce | `nonce` | `12345` | O nonce é uma estratégia para mitigar ataques de repetição token.  A aplicação, pode especificar um nonce num pedido de autorização, utilizando o `nonce` parâmetro de consulta.  O valor fornecer no pedido de vai ser emitido no id_token `nonce` afirmação, não modificada.  Isto permite a sua aplicação verificar se o valor em relação ao valor-especificado no pedido, associa sessão na aplicação de um determinado id_token.  A aplicação deverá executar esta validação durante o processo de validação id_token. |
| Nome | `name` | `Babe Ruth` | A afirmação de nome fornece um valor legível humano que identifica o assunto do token de. Este valor não há garantia de ser exclusivos, é mutáveis e foi concebido para ser utilizado apenas para fins de visualização.  O `profile` âmbito for necessário para receber este pedido. |
| Mensagem de correio electrónico | `email` | `thegreatbambino@nyy.onmicrosoft.com` | O endereço de e-mail principal associado a conta de utilizador, caso exista alguma.  O valor é mutáveis e poderá alterar para um determinado utilizador ao longo do tempo.  O `email` âmbito for necessário para receber este pedido. |
| Nome de utilizador preferido | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | O nome de utilizador principal que é utilizado para representar o utilizador no ponto final 2.0.  É possível um endereço de e-mail, número de telefone ou um nome de utilizador genérico sem um formato especificado.  O valor é mutáveis e poderá alterar para um determinado utilizador ao longo do tempo.  O `profile` âmbito for necessário para receber este pedido. |
| Assunto | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | O capital sobre o qual o token afirma informações, como o utilizador de uma aplicação. Este valor é imutáveis e não é possível reatribuir ou reutilizados, pelo que pode ser utilizada para executar verificações de autorização em segurança, tal como quando o token é utilizado para aceder a um recurso. Uma vez que o assunto é sempre presente nos tokens os problemas do Azure AD, é recomendável utilizar este valor num sistema de autorização de objectivo geral. |
| ID de objecto | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | O Id da conta escolar ou profissional no sistema de Azure AD objeto.  Esta afirmação não será emitida para contas pessoais do Microsoft.  O `profile` âmbito for necessário para receber este pedido. |


## <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso emitidos pelo ponto final 2.0 só são consumíveis pelo Microsoft Services neste ponto específico.  As suas aplicações não devem ter de executar qualquer validação ou inspeção de tokens de acesso para qualquer um dos cenários atualmente suportados.  Pode são processados os tokens de acesso como completamente opaca - são basta cadeias que a sua aplicação pode passar para a Microsoft nos pedidos HTTP.

No futuro próximo, o ponto final 2.0 irá apresentar a capacidade para a sua aplicação receber tokens de acesso de outros clientes.  Nesse momento, esta informação será atualizada com as informações na que sua aplicação precisa para efetuar outras tarefas semelhantes e validação token de acesso.

Quando pedir um token de acesso a partir do ponto final 2.0, o ponto final 2.0 também devolve alguns metadados sobre o token de acesso para consumo da sua aplicação.  Estas informações incluem o tempo de validade do token de acesso e os âmbitos para o qual é válida.  Esta opção permite-sua aplicação para efetuar a colocação em cache inteligente de tokens de acesso sem ter de analisar abrir o token de acesso própria.

## <a name="refresh-tokens"></a>Atualizar tokens

Atualizar tokens são tokens de segurança que pode utilizar a aplicação para adquirir novas aceder tokens de um fluxo de OAuth 2.0.  Permitir a aplicação alcançar a longo prazo acesso aos recursos em nome de um utilizador sem necessidade de interação pelo utilizador.

Atualização tokens são múltiplos recursos.  Isto é que um token de atualização recebido durante um pedido de token para um recurso pode ser reembolsadas para tokens de acesso a um recurso completamente diferente.

Para receberem uma atualização numa resposta token, a sua aplicação terá de pedir e lhe ser concedida a `offline_acesss` âmbito.   Para saber mais sobre o `offline_access` âmbito, consulte o [artigo consentimento & âmbitos aqui](active-directory-v2-scopes.md).

Atualizar tokens são e será sempre, completamente opaca para a sua aplicação.  Estes são emitidos pelo ponto final do Azure AD 2.0 e podem apenas ser seja inspeccionadas e interpretados pelo ponto final 2.0.  São longa vida, mas a sua aplicação não deve ser escrita esperar que irá durar um token de atualização durante qualquer período de tempo.  Tokens de atualização podem ser invalidados momento qualquer várias razões.  É a única forma para a sua aplicação saber se um token de atualização é válido tentar resgatá-lo ao efetuar um pedido de token para o ponto final 2.0.

Quando um token de atualização para um novo token de acesso resgatar (e se a sua aplicação tivesse sido concedida a `offline_access` âmbito), receberá um novo token de atualização na resposta token.  Deverá guardar o token de atualização recentemente emitido, substituindo a que utilizou no pedido de.  Isto irá garante que seu tokens de atualização permanecem válidos para tão longas quanto possível.

## <a name="validating-tokens"></a>Validar tokens

Neste ponto no momento, a validação token apenas que as suas aplicações devem ser necessário efetuar está a validar id_tokens.  Para poder validar um id_token, a aplicação deverá validar assinatura a id_token e as afirmações na id_token.

<!-- TODO: Link -->
Fornecemos bibliotecas e exemplos de código que mostram como lidar facilmente validação token - o abaixo informações simplesmente é fornecido para aqueles que pretender para compreender o processo subjacente.  Existem várias bibliotecas de abrir origem festa 3º disponíveis para validação JWT - existe pelo menos uma opção para quase todas as plataforma & nesse idioma.

#### <a name="validating-the-signature"></a>Validar a assinatura
Um JWT contém três segmentos, separados pelo `.` caráter.  O primeiro segmento é conhecido como **cabeçalho**, o segundo como o **corpo**e a terceira como a **assinatura**.  O segmento de assinatura pode ser utilizado para validar a autenticidade da id_token para que pode ser considerado fidedigno pela sua aplicação.

Id_Tokens são assinados utilizem indústria algoritmos de encriptação assimétricos padrão, tais como RSA 256. O cabeçalho da id_token contém informações sobre o método de encriptação e chave utilizado para assinar o token de:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar do token, enquanto o `kid` afirmação indica a chave pública específica que foi utilizada para assinar o token.

Em qualquer dado momento, o ponto final 2.0 pode assinar uma id_token utilizando qualquer uma de um determinado conjunto de pares de chaves privadas público.  O ponto final 2.0 roda o conjunto possível de teclas numa base periódica, para que a sua aplicação deve ser escrita para processar automaticamente essas alterações chaves.  Uma frequência razoável para verificar a existência de actualizações para as chaves públicas utilizadas pelo ponto final 2.0 é de cerca de 24 horas.

Pode adquirir os assinatura principais dados necessários para validar a assinatura utilizando o documento de metadados OpenID ligar-se que se encontra no:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Experimente este URL num browser!

Este documento de metadados é um objeto JSON que contém várias partes úteis de informações, tais como a localização dos vários pontos finais necessários para realizar autenticação OpenID ligar.  

Também inclui uma `jwks_uri`, que dá a localização do conjunto de chaves públicas utilizado para assinar tokens.  O documento JSON que se encontra na `jwks_uri` contém todas as informações da chave públicas utilizado nesse momento específico.  Pode utilizar a aplicação a `kid` reclamar no cabeçalho JWT para selecionar qual chave pública neste documento foi utilizado para assinar um nomeadamente token.  -Lo, em seguida, pode executar a validação de assinatura com a chave pública correta e o algoritmo indicado.

Executar a validação de assinatura está fora do âmbito deste documento - existem muitos bibliotecas de abrir origem para o ajudar a fazê-lo, se necessário.

#### <a name="validating-the-claims"></a>Validar as em afirmações
Quando a aplicação recebe um id_token após o início de sessão do utilizador,-deve também executar alguns controlos contra a violação de id_token.  Estes incluem mas não estão limitados a:

- A **audiência** afirmação - Certifique-se de que o id_token foi concebido para ser fornecidas para a sua aplicação.
- As **Não antes** e **Tempo de expiração de** afirmações - para verificar se o id_token não expirou.
- A **emissor** afirmação - verificar se o token de facto foi emitido para a sua aplicação pelo ponto final 2.0.
- O **Nonce** - como uma mitigação de ataque token de reprodução.
- e muito mais...

Para uma lista completa de validações afirmação que deve efetuar a sua aplicação, consulte a [especificação OpenID ligar](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalhes dos valores esperados para estes em afirmações são incluídos acima, na [secção id_token](#id_tokens).


## <a name="token-lifetimes"></a>Durações tokens

As seguintes durações tokens são fornecidas puramente para sua compreender, tal como o podem ajudar na programação e depuração de aplicações.  As suas aplicações não devem ser escritas esperar qualquer um dos seguintes durações para que permaneçam constantes - estes possam e irão alterar em qualquer altura determinada altura.

| Token | Durante a vida. | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (contas escolar ou profissional) | 1 hora | Id_Tokens são normalmente válidas para uma hora.  A aplicação web do pode utilizar este duração da mesma em manter a sua própria sessão com o utilizador (recomendado) ou selecione uma duração da sessão completamente diferente.  Se tem a aplicação de obter um novo id_token, simplesmente necessita de fazer um novo início de sessão no pedido para o 2.0 autorizar ponto final.  Se o utilizador tem uma sessão de browser válido com o ponto final 2.0, não poderá ser necessário introduzir novamente as suas credenciais. |
| Id_Tokens (contas pessoais) | 24 horas | Id_Tokens para contas pessoais são normalmente válidos 24 horas.  A aplicação web do pode utilizar este duração da mesma em manter a sua própria sessão com o utilizador (recomendado) ou selecione uma duração da sessão completamente diferente.  Se tem a aplicação de obter um novo id_token, simplesmente necessita de fazer um novo início de sessão no pedido para o 2.0 autorizar ponto final.  Se o utilizador tem uma sessão de browser válido com o ponto final 2.0, não poderá ser necessário introduzir novamente as suas credenciais. |
| Tokens de acesso (contas escolar ou profissional) | 1 hora | Indicada na respostas tokens como parte dos metadados token. |
| Tokens de acesso (contas pessoais) | 1 hora | Indicada na respostas tokens como parte dos metadados token.  Access_tokens emitido em nome de contas pessoais poderá estar configurado para uma duração diferentes, mas uma hora é normalmente as maiúsculas/minúsculas. |
| Atualizar Tokens (conta escolar ou profissional) | Até 14 dias | Um token de atualização única seja válido para um máximo de 14 dias.  No entanto, o token de atualização pode tornar-se inválidas em qualquer altura para qualquer número de razões, para que a sua aplicação deve continuar a experimentar e utilizar um token de atualização até a falhar ou a aplicação substitui-la por um novo token de atualização.  Um token de atualização também irão tornar-se inválidas se tiver sido cerca de 90 dias uma vez que o utilizador tenha introduzido as respetivas credenciais. |
| Atualizar Tokens (contas pessoais) | Até 1 ano | Um token de atualização única seja válido para um máximo de 1 ano.  No entanto, o token de atualização pode tornar-se inválidas em qualquer altura para qualquer número de razões, para que a sua aplicação deve continuar a tentar e utilize um token de atualização até falhar. |
| Códigos de autorização (contas escolar ou profissional) | 10 minutos | Códigos de autorização estão mais curto e devem ser reembolsados imediatamente para access_tokens e refresh_tokens quando são recebidas. |
| Códigos de autorização (contas pessoais) | 5 minutos | Códigos de autorização estão mais curto e devem ser reembolsados imediatamente para access_tokens e refresh_tokens quando são recebidas.  Códigos de autorização emitidos em nome de contas pessoais também são utilização única. |

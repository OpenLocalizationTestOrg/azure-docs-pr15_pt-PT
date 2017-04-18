<properties
    pageTitle="Muda para o ponto final do Azure AD 2.0 | Microsoft Azure"
    description="Uma descrição das alterações efetuadas a ser para os protocolos de pré-visualização público de 2.0 de modelo de aplicação."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Atualizações importantes para os protocolos de autenticação 2.0
Programadores de atenção! Sobre as duas semanas seguintes, podemos estarão a fazer as atualizações alguns a nossa protocolos de autenticação de 2.0 poderão vermelha força alterações para qualquer as aplicações que escreveu durante nosso período de pré-visualização.  

## <a name="who-does-this-affect"></a>Quem é que Isto afeta?
De aplicações que foi escritas para utilizar o 2.0 convergiu ponto final de autenticação

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Obter mais informações sobre o ponto final 2.0 podem ser encontradas [aqui](active-directory-appmodel-v2-overview.md).

Se já tiver criado uma aplicação com o ponto final 2.0 por codificação diretamente para o protocolo 2.0, utilizar qualquer um dos nossos middlewares web OpenID ligar ou OAuth ou outras bibliotecas de festa 3º a utilizar para efetuar a autenticação, deve ser preparado para testar os seus projetos e efetuar alterações, se necessário.

## <a name="who-doesnt-this-affect"></a>Quem não Isto afeta?
De aplicações que foram escritas contra o ponto final autenticação de produção Azure AD,

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Este protocolo é definido no pedra e não vai estar com as alterações.

Além disso, se a sua aplicação **apenas** utiliza os nossos biblioteca ADAL para efectuar a autenticação, não tem de alterar alguma coisa.  ADAL tem protegidas a sua aplicação a partir das alterações.  

## <a name="what-are-the-changes"></a>Quais são as alterações?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Remover o valor de x5t de cabeçalhos JWT
O ponto final 2.0 utiliza JWT tokens extensivamente, que contêm uma secção de parâmetros de cabeçalho com relevantes metadados sobre o token.  Se codificar o cabeçalho de um dos nossos JWTs atuais, teria de localizar algo parecido com:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Onde ambas as "x5t" "crianças" propriedades e identificam a chave pública que deve ser utilizada para validar assinatura do token, tal como obtidos a partir do ponto final de metadados OpenID ligar.

A alteração que estamos a tornar aqui é remover a propriedade "x5t".  Pode continuar a utilizar os mesmos mecanismos para validar os tokens, mas deve depender apenas a propriedade "crianças" para obter a chave pública correta, conforme especificado no protocolo OpenID ligar. 

> [AZURE.IMPORTANT] **A tarefa: Certifique-se a sua aplicação não dependem a existência do valor x5t.**

### <a name="removing-profileinfo"></a>Remover profile_info
Anteriormente, o ponto final 2.0 tem sido devolver um objeto JSON codificada base64 nas respostas tokens denominadas `profile_info`.  Quando pedir um token de acesso a partir do ponto final 2.0 ao enviar um pedido para:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

A resposta seria como o objeto JSON seguinte:
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O `profile_info` valor contido obter informações sobre o utilizador que iniciou sessão no aplicação - respetivo nome a apresentar, nome próprio, apelido, endereço de e-mail, identificador e assim sucessivamente.  Basicamente, a `profile_info` foi utilizado para a colocação em cache token e apresentar os efeitos.

Agora está a ser removido o `profile_info` valor – mas não se preocupe, estamos a fornecer ainda estas informações para os programadores num local ligeiramente diferente.  Em vez de `profile_info`, o ponto final 2.0 agora irá devolver uma `id_token` em cada resposta token:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Poderá codificar e analisar id_token para obter as mesmas informações que tenha recebido através de profile_info.  O id_token é uma JSON Web Token (JWT), com o conteúdo conforme especificado pelo OpenID ligar.  O código para fazê-lo por isso, deve ser muito semelhante – basta necessários para extrair o segmento meio (o corpo) a id_token e base64 codificá-lo para aceder ao objecto JSON dentro.

Sobre as duas semanas seguintes, deverá código sua aplicação para obter as informações de utilizador a partir de um a `id_token` ou `profile_info`; consoante o que se encontra presente.  Dessa forma quando a alteração for feita, sua aplicação de forma totalmente integrada pode processar a transição a partir do `profile_info` para `id_token` sem interrupções.

> [AZURE.IMPORTANT] **A tarefa: Certifique-se a sua aplicação não dependem a existência do `profile_info` valor.**

### <a name="removing-idtokenexpiresin"></a>Remover id_token_expires_in
Semelhante ao `profile_info`, também está a ser removido o `id_token_expires_in` parâmetro de respostas.  Anteriormente, o ponto final 2.0 deve devolver um valor para `id_token_expires_in` juntamente com cada resposta id_token, por exemplo, numa resposta autorizar:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou numa resposta token:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O `id_token_expires_in` valor seria indicar o número de segundos a id_token seria permanecem válidos para.  Agora, vamos está a remover o `id_token_expires_in` valor completamente.  Em vez disso, pode utilizar a norma OpenID ligar `nbf` e `exp` afirmações para examinar a validade de um id_token.  Consulte a [referência de token 2.0](active-directory-v2-tokens.md) para obter mais informações sobre estes afirmações.

> [AZURE.IMPORTANT] **A tarefa: Certifique-se a sua aplicação não dependem a existência do `id_token_expires_in` valor.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Alterar as afirmações devolvidas por âmbito = openid
Esta alteração será mais significativo – de facto, irá afectar quase todas as aplicações que utiliza o ponto final 2.0.  Muitas aplicações enviarem pedidos para o ponto final de 2.0 utilizando o `openid` âmbito, como:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Hoje, quando o utilizador concede consentimento para o `openid` âmbito, a sua aplicação recebe vastos de informações sobre o utilizador a id_token resultante.  Estes afirmações podem incluir o nome da pessoa, nome de utilizador preferido, endereço de e-mail, ID do objeto e mais.

Nesta actualização, podemos está a alterar as informações que o `openid` âmbito oferecem o acesso de aplicação, a melhor comform com a especificação OpenID ligar.  O `openid` âmbito só irá permitir a sua aplicação iniciar sessão de utilizador no e receber um identificador de aplicação específicas para o utilizador na `sub` reclamar do id_token.  As afirmações numa id_token com apenas a `openid` âmbito concedido será isento de qualquer informação identificativa.  Exemplo id_token afirmações são:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Se pretender obter informação identificativa (PII) sobre o utilizador na sua aplicação, a sua aplicação terão de pedir permissões adicionais do utilizador.  Vamos são introdução ao suporte para duas âmbitos de novos a partir das especificações OpenID ligar – a `email` e `profile` âmbitos – que lhe permitem fazê-lo.

- O `email` âmbito é bastante simples – permitir o acesso de aplicação para o endereço de e-mail principal do utilizador através de `email` reclamar na id_token.  Tenha em atenção que a `email` afirmação não sempre estarão presente no id_tokens – -lo só será incluído se disponível no perfil do utilizador.
- O `profile` âmbito oferecem o acesso de aplicação a todas as outras informações básicas sobre o utilizador – em seu nome, nome de utilizador preferido, ID do objecto etc.

Esta opção permite-lhe código a sua aplicação de uma forma de mínimas divulgação – pode perguntar ao utilizador para apenas o conjunto de informações que a sua aplicação requer fazer a sua tarefa.  Se quiser continuar a obter o conjunto completo de informações do utilizador que a aplicação está atualmente a receber, deve incluir todos os âmbitos de três nos pedidos de autorização:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Pode começar a sua aplicação de enviar a `email` e `profile` âmbitos imediatamente e o ponto final 2.0 irão aceitar estes dois âmbitos e comece a pedir permissões de utilizadores conforme necessário.  No entanto, a alteração de interpretação da `openid` âmbito não terão efeito para alguns semanas.

> [AZURE.IMPORTANT] **A tarefa: adicionar o `profile` e `email` âmbitos se a sua aplicação requer informações sobre o utilizador.**  Tenha em atenção que ADAL irá incluir ambas as permissões nos pedidos por predefinição. 

### <a name="removing-the-issuer-trailing-slash"></a>Remover o emissor barra em branco à direita.
Anteriormente, o valor de emissor que aparece no tokens a partir do ponto final 2.0 demorou o formulário

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Onde o guid foi tenantId do inquilino Azure AD que emitiu o token.  Com estas alterações, o valor de emissor torna-se

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

em ambos os tokens e no documento de deteção OpenID ligar.

> [AZURE.IMPORTANT] **A tarefa: Certifique-se a sua aplicação aceita o valor de emissor com e sem uma barra final durante a validação de emissor.**

## <a name="why-change"></a>Porque é que alterar?
A motivação principal de introduzirem estas alterações é estar em conformidade com a especificação de padrão OpenID ligar.  Por que está a ser OpenID ligar-se em conformidade, podemos Espero que minimizar as diferenças entre a integração com os serviços de identidade da Microsoft e com outros serviços de identidade da indústria.  Pretendemos permitir que os programadores utilizar bibliotecas de autenticação seus favoritos abrir origem sem ter de alterar as bibliotecas para acomodar diferenças da Microsoft.

## <a name="what-can-you-do"></a>O que pode fazer?
Data de hoje, pode começar a efetuar todas as alterações descritas acima.  Deve imediatamente:

1.  **Remover todas as dependências de `x5t` parâmetro de cabeçalho.**
2.  **Processar a transição a partir de forma adequada `profile_info` para `id_token` nas respostas tokens.**
3.  **Remover todas as dependências de `id_token_expires_in` parâmetro de resposta.**
3.  **Adicionar o `profile` e `email` âmbitos para a sua aplicação se a sua aplicação necessita de informações de utilizador básica.**
4.  **Aceite emissor valores em tokens com e sem uma barra final.**

Nossa [documentação do protocolo 2.0](active-directory-v2-protocols.md) já foi atualizado para refletir estas alterações, para que poderão utilizá-la como referência para ajudar a atualizar o seu código.

Se tiver questões adicionais sobre o âmbito das alterações, volte à vontade para comunique-no Twitter na @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Com que frequência serão ocorrem as alterações de protocolo?
Não prever a qualquer força ainda mais muda para os protocolos de autenticação.  Vamos intencionalmente são unir estas alterações para um lançamento para que não tenha seguir este tipo de processo de atualização novamente sempre mais rapidamente.  Obviamente, vamos continuar adicionar funcionalidades ao serviço de autenticação de 2.0 convergentes pode tirar partido das, mas essas alterações devem ser aditivo e não quebra existentes código.

Por fim, gostaríamos diga agradecimento para experimentar coisas durante o período de pré-visualização.  As informações e experiências dos nossos vanguarda tem sido inestimável extremidade, pelo que pedimos consiste e que continuará a partilhar o seu opiniões e ideias.

Codificação satisfeito!

A divisão de identidade da Microsoft

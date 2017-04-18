<properties 
    pageTitle="Como delegado de subscrição de registo e produto de utilizador" 
    description="Saiba como delegado de subscrição de registo e produto de utilizador para um terceiro na gestão de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegado de subscrição de registo e produto de utilizador

Delegação permite-lhe utilizar o seu Web site existente para processamento de programador início de sessão-no/sign-valorização e a subscrição de produtos em vez de utilizar a funcionalidade incorporada no portal do programador. Permite que o seu Web site para proprietário os dados de utilizador e executar a validação destes passos de uma forma personalizada.

## <a name="delegate-signin-up"> </a>Delegar programador iniciar sessão e inscrição

Delegar programador iniciar sessão e inscrever-se para o seu Web site existente terá de criar um ponto final de delegação especiais no seu site que age como ponto de entrada para essa pedido de iniciada a partir do portal de Programador de gestão de API.

O fluxo de trabalho final será da seguinte forma:

1. Programador cliques na ligação da sessão ou inscrição no portal do Programador de gestão de API
2. Browser é redirecionado para o ponto final de delegação
3. Ponto final de delegação em troca redireciona para ou apresenta IU perguntar do utilizador para iniciar sessão ou inscrição
4. Em caso de êxito, o utilizador é redirecionado para a página portal de gestão de API para programadores iniciados a partir de


Para começar, vamos primeiro gestão de API de configuração da compilação para encaminhar pedidos de através do seu ponto final de delegação. No portal do publisher API gestão, clique em **segurança** e, em seguida, clique no separador **delegação** . Clique na caixa de verificação para ativar 'Delegate início de sessão no & inscrição'.

![Página de delegação][api-management-delegation-signin-up]

* Decida o que o URL do seu ponto final de delegação especial serão e introduzi-lo no campo **URL de ponto final de delegação** . 

* No campo de **chave de autenticação de delegação** introduza uma palavra-passe que será utilizada para calcular uma assinatura fornecida para verificação para se certificar de que o pedido verdade está a chegar a partir do Azure API gestão. Pode clicar no botão **Gerar** ter API Managemnet aleatoriamente gerar uma chave para si.

Agora, precisa de criar o **ponto final de delegação**. Tem de executar um número de ações:

1. Receba um pedido de no seguinte formato:

    > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {URL da página de origem} & sal = {cadeia} & assinatura = {cadeia}*

    Parâmetros de consulta para o caso de início de sessão no / inscrição:
    - **operação**: identifica encontra-se que tipo de delegação pedi-lo - só pode ser **início de sessão** neste caso
    - **returnUrl**: o URL da página onde o utilizador clicou uma ligação de início de sessão no ou inscrição
    - **sal**: uma cadeia de sal especial utilizada para calcular um hash de segurança
    - **assinatura**: um hash segurança calculado para ser utilizada para comparação para o seu próprio hash calculado

2. Certifique-se de que o pedido vem da gestão de API do Azure (opcional, mas vivamente recomendado para segurança)

    * Calcule um hash HMAC SHA512 de uma cadeia com base em **returnUrl** e **sal** parâmetros de consulta ([código de exemplo fornecido abaixo]):
        > HMAC (**sal** + '\n' + **returnUrl**)
         
    * Compare o hash calculado acima para o valor do parâmetro de consulta de **assinatura** . Se os dois hashes corresponderem, avance para o próximo passo, caso contrário, recusar o pedido.

2. Certifique-se de que está a receber um pedido de início de sessão no/início de sessão-telefónico: o parâmetro de consulta de **operação** será definido como "**início de sessão**".

3. Apresentar o utilizador com IU para iniciar sessão ou inscrição

4. Se o utilizador estiver a iniciar sessão de segurança tem de criar uma conta correspondente para-los na gestão de API. [Criar um utilizador] com a gestão de API REST API. Ao fazê-lo, certifique-se de que o utilizador defina o ID de utilizador para o mesmo é no arquivo de utilizador ou para um ID que pode manter um registo das.

5. Quando o utilizador é autenticado com êxito:

    * [Pedir um token de sessão único (SSO)] através de gestão de API REST API

    * acrescente um parâmetro de consulta returnUrl para o URL de SSO recebeu da chamada API acima:
        > Por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * redirecionar o utilizador para o URL produzido acima

Para além da operação de **início de sessão** , também pode executar a gestão de contas ao seguir os passos anteriores e utilizar uma das seguintes operações.

-   **ChangePassword**
-   **ChangeProfile**
-   **CloseAccount**

Tem de passar os seguintes parâmetros de consulta para operações de gestão de conta.

-   **operação**: identifica qual o tipo de pedido de delegação é (ChangePassword, ChangeProfile ou CloseAccount)
-   **ID de utilizador**: o id de utilizador da conta para gerir
-   **sal**: uma cadeia de sal especial utilizada para calcular um hash de segurança
-   **assinatura**: um hash segurança calculado para ser utilizada para comparação para o seu próprio hash calculado

## <a name="delegate-product-subscription"> </a>Delegar a subscrição do produto

Delegar a subscrição do produto funciona de forma semelhante a delegar utilizador início de sessão no/de segurança. O fluxo de trabalho final seria da seguinte forma:

1. Programador seleciona um produto no portal do Programador de gestão de API e clica no botão subscrever
2. Browser é redirecionado para o ponto final de delegação
3. Ponto final de delegação execute os passos de subscrição de produtos necessários - trata-se até e pode implicar redirecionar para outra página para pedir informações de faturação, colocar questões adicionais, ou simplesmente armazenar as informações e não que precisam de qualquer ação do utilizador


Para ativar a funcionalidade, na página de **delegação** , clique em **subscrição de produto do delegado**.

Em seguida, certifique-se que o ponto final de delegação efetua as seguintes ações:


1. Receba um pedido de no seguinte formato:

    > *http://www.yourwebsite.com/apimdelegation?operation= {operação} & Iddoproduto = {produto para subscrever} & ID = {utilizador efetuar pedido} & sal = {cadeia} & assinatura = {cadeia}*

    Parâmetros de consulta para o caso de subscrição de produto:
    - **operação**: identifica qual o tipo de pedido de delegação é. Para a subscrição do produto pedidos das opções válidas são:
        - "Subscrever": um pedido de subscrever o utilizador a um determinado produto com fornecidos ID (ver abaixo)
        - "Anular a subscrição": um pedido para anular a subscrição de um utilizador a partir de um produto
        - "Renovar": um pedido para renovar uma subscrição (por exemplo, que pode ser expirar)
    - **Iddoproduto**: o ID do produto pedido ao utilizador para subscrever
    - **ID de utilizador**: o ID do utilizador para o qual o pedido for feito
    - **sal**: uma cadeia de sal especial utilizada para calcular um hash de segurança
    - **assinatura**: um hash segurança calculado para ser utilizada para comparação para o seu próprio hash calculado


2. Certifique-se de que o pedido vem da gestão de API do Azure (opcional, mas vivamente recomendado para segurança)

    * Calcule uma SHA512 HMAC de uma cadeia com base nos parâmetros de consulta **Iddoproduto**, **ID de utilizador** e **sal** :
        > HMAC (**sal** + '\n' + **Iddoproduto** + '\n' + **ID de utilizador**)
         
    * Compare o hash calculado acima para o valor do parâmetro de consulta de **assinatura** . Se os dois hashes corresponderem, avance para o próximo passo, caso contrário, recusar o pedido.
    
3. Execute qualquer processamento de subscrição do produto com base no tipo de operação pedida numa **operação** - por exemplo, de faturação, ainda mais perguntas, etc.

4. Com êxito ao subscrever ao utilizador para o produto no seu lado, subscreva ao utilizador para o produto de gestão de API ao [contactar o REST API para a subscrição do produto].

## <a name="delegate-example-code"></a> Código de exemplo ##

Estes exemplos de código mostram como tirar a *chave de validação de delegação*, que está definida no ecrã de delegação do portal do publisher, para criar um HMAC que, em seguida, é utilizado para validar a assinatura que prova a validade do returnUrl passado. O mesmo código funciona para Iddoproduto e ID de utilizador com ligeira modificação.

**Código c# para gerar hash do returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Código de NodeJS para gerar hash do returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a delegação, consulte o vídeo seguinte.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[pedir um token de sessão único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[criar um utilizador]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[chamar REST API para a subscrição do produto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
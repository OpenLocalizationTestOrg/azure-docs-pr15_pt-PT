###<a name="server-auth"></a>Como: autenticar com um fornecedor (fluxo de servidor)

Para que as aplicações móveis gerir o processo de autenticação na sua aplicação, tem de registar a sua aplicação com o seu fornecedor de identidade. Em seguida, no seu serviço de aplicação do Azure, tem de configurar o ID da aplicação e o segredo fornecido pelo seu fornecedor.
Para obter mais informações, consulte o tutorial [Adicionar autenticação para a sua aplicação].

Depois de ter registado o seu fornecedor de identidade, basta chame o método de .login() com o nome do seu fornecedor. Por exemplo, para iniciar sessão com Facebook, utilize o seguinte código.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Se estiver a utilizar um fornecedor de identidades diferente de Facebook, altere o valor passado para o método de início de sessão acima para um dos seguintes: `microsoftaccount`, `facebook`, `twitter`, `google`, ou `aad`.

Neste caso, o serviço de aplicação do Azure gere o fluxo de autenticação OAuth 2.0 ao apresentar a página de início de sessão do fornecedor seleccionado e gerar um token de autenticação de aplicação de serviço após bem sucedido início de sessão com o fornecedor de identidade. A função de início de sessão, quando estiver concluído, devolve um objeto JSON (utilizador) expõe o ID de utilizador e a aplicação de serviço de token de autenticação nos campos ID de utilizador e authenticationToken, respetivamente. Este token pode ser em cache e reutilizada até que expire.

###<a name="client-auth"></a>Como: autenticar com um fornecedor (fluxo de cliente)

A aplicação pode também independentemente de contactar o fornecedor de identidade e, em seguida, fornecer o token devolvido no seu serviço de aplicação para a autenticação. Este fluxo de cliente permite-lhe para fornecer uma experiência de início de sessão único para utilizadores ou para obter dados de utilizador adicionais do fornecedor de identidade.

#### <a name="social-authentication-basic-example"></a>Exemplo de básica autenticação social

Este exemplo utiliza Facebook cliente SDK para autenticação:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Este exemplo assume que o token fornecido pelo fornecedor de respetivos SDK está armazenado na variável de token.

#### <a name="microsoft-account-example"></a>Exemplo de Account Microsoft

O exemplo seguinte utiliza o Live SDK, que suporta único-sign-on para aplicações da loja Windows utilizando Account Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Este exemplo obtém um token da Live ligar, que é fornecido no seu serviço de aplicação ao contactar o suporte a função de início de sessão.

###<a name="auth-getinfo"></a>Como: obter informações sobre o utilizador autenticado

As informações de autenticação para o utilizador actual podem ser obtidas a partir de `/.auth/me` ponto final utilizando qualquer método AJAX.  Certifique-se de que definir o `X-ZUMO-AUTH` cabeçalho ao seu token de autenticação.  O token de autenticação é armazenado na `client.currentUser.mobileServiceAuthenticationToken`.  Por exemplo, para utilizar a API obtenção:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Obtenção está disponível como um pacote de npm ou para browser poderá transferir CDNJS. Pode também utilizar jQuery ou outra AJAX API para obter as informações.  Os dados serão recebidos como um objeto JSON.

<properties
   pageTitle="Trabalhar com baseada em afirmações identidades nas aplicações multi-inquilino | Microsoft Azure"
   description="Como uma utilização afirmações para validação emissor e autorização"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Trabalhar com baseada em afirmações identidades nas aplicações multi-inquilino

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo faz [parte de uma série]. Também existe uma [aplicação de exemplo] concluída que acompanha nesta série.

## <a name="claims-in-azure-ad"></a>Em afirmações no Azure AD

Quando um utilizador inicia sessão, Azure AD envia um token de ID que contém um conjunto de afirmações sobre o utilizador. Uma afirmação é simplesmente uma peça de informação, expressa como um par de chave/valor. Por exemplo, `email` = `bob@contoso.com`.  Em afirmações tem um emissor &mdash; neste caso, Azure AD &mdash; qual é a entidade que autentica o utilizador e cria as afirmações. Em que confia as afirmações porque confia o emissor. (Por outro lado, se não confiar o emissor, não confiar as afirmações!)

De alto nível:

1.  O utilizador autentica.
2.  O IDP envia um conjunto de afirmações.
3.  A aplicação normaliza ou aumenta as afirmações (opcionais).
4.  A aplicação utiliza as afirmações para tomar decisões de autorização.

No OpenID ligar, o conjunto de afirmações que obtém é controlado pelo [parâmetro de âmbito] do pedido de autenticação. No entanto, Azure AD emite um conjunto de afirmações através de OpenID ligar; limitado consulte o artigo [Token suportados e os tipos de afirmações]. Se pretender obter mais informações sobre o utilizador, terá de utilizar a API do Azure AD Graph.

Aqui estão alguns dos pedidos de AAD uma aplicação poderá interessam normalmente:

Tipo de afirmação num token de ID |    Descrição
-----------------------|--------------
sociedade mista | Quem o token foi emitido para. Este será ID cliente. da aplicação Em geral, não deva tem de se preocupar esta afirmação, porque o software intermédio o valida automaticamente. Exemplo:`"91464657-d17a-4327-91f3-2ed99386406f"`
grupos   | Uma lista dos grupos AAD de que o utilizador é um membro. Exemplo:`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | O [emissor] do OIDC token. Exemplo:`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
nome    | Nome a apresentar do utilizador. Exemplo:`"Alice A."`
OID | O identificador de objeto do utilizador no AAD. Este valor é o identificador imutáveis e não reutilizável do utilizador. Utilizar este valor, e-mail, como um identificador exclusivo para os utilizadores; podem alterar endereços de e-mail. Se utilizar a API do Azure AD Graph na sua aplicação, o ID do objeto é esse valor utilizado para informações de perfil de consulta. Exemplo:`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
funções   | Uma lista de funções de aplicação para o utilizador. Exemplo:`["SurveyCreator"]`
TID | ID de inquilino. Este valor é um identificador exclusivo para o inquilino no Azure AD. Exemplo:`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique_name | Um nome a apresentar legível humanos do utilizador. Exemplo:`"alice@contoso.com"`
UPN | Nome principal de utilizador. Exemplo:`"alice@contoso.com"`

Esta tabela lista os tipos de afirmação como são apresentados no token de ID. ASP.NET Core 1.0, o software intermédio OpenID ligar converte alguns dos tipos de afirmação quando preenche o coleção de afirmações para o utilizador principal:

-   OID >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   TID >`http://schemas.microsoft.com/identity/claims/tenantid`
-   unique_name >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Em afirmações transformações

Durante o fluxo de autenticação, poderá pretender modificar as afirmações que obtém do IDP. No ASP.NET Core 1.0, pode efetuar em afirmações transformação dentro do evento **AuthenticationValidated** a partir do software intermédio OpenID ligar. (Consulte [eventos de autenticação].)

Quaisquer afirmações que adicionar durante **AuthenticationValidated** são armazenadas no cookie de autenticação da sessão. Não obter seguia novamente para Azure AD.

Eis alguns exemplos de transformação de afirmações:

-   **Normalização em afirmações**ou em afirmações façam consistentes em utilizadores. Este é particularmente relevante se está a obter em afirmações a partir de várias IDPs, que poderão utilizar tipos de afirmações diferentes para obter informações semelhantes.
Por exemplo, o Azure AD envia um pedido de "upn" que contém o e-mail do utilizador. Outros IDPs poderão enviar uma afirmação "e-mail". O código seguinte converte a afirmação "upn" uma afirmação "enviar por correio eletrónico":

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Adicionar **valores de afirmações predefinido** para afirmações que não estão presentes &mdash; atribuir por exemplo, um utilizador para uma função predefinida. Em alguns casos, este pode simplificar lógica de autorização.
- Adicione **tipos de afirmações personalizadas** com informações específicas da aplicação sobre o utilizador. Por exemplo, pode armazenar algumas informações sobre o utilizador numa base de dados. É possível adicionar uma afirmação personalizada com estas informações para a permissão de autenticação. A afirmação é armazenada num cookie, pelo que só necessita de pôr a partir da base de dados de uma vez por sessão de início de sessão. Por outro lado, também pretende evitar a criação de cookies demasiado grandes, pelo que necessita de ter em consideração o compromisso entre tamanho cookie versus pesquisas de base de dados.   

Depois do fluxo de autenticação estiver concluído, as afirmações estão disponíveis no `HttpContext.User`. Nesse momento, deverá tratá-los como uma colecção de só de leitura &mdash; por exemplo, utilizá-los para tomar decisões de autorização.

## <a name="issuer-validation"></a>Validação de emissor
Em ligar OpenID, a afirmação emissor ("iss") identifica o IDP que emitiu o token de ID. Parte do fluxo de autenticação OIDC é confirmar que a afirmação emissor corresponde ao emissor real. O software OIDC intermédio trata isto por si.

No Azure AD, o valor de emissor é exclusivo por inquilino AD (`https://sts.windows.net/<tenantID>`). Por conseguinte, uma aplicação deve fazer uma verificação adicional, para se certificar de que o emissor representa um inquilino que é permitido para iniciar sessão aplicação.

Para uma aplicação de inquilino único, apenas pode verificar que o emissor é o seu próprio inquilino. Na verdade, o software OIDC intermédio faz isto automaticamente por predefinição. Numa aplicação com várias inquilino, tem de permitir vários emitentes, correspondente a inquilinos diferentes. Eis uma abordagem geral para utilizar:

-   Nas opções de software intermédio OIDC, defina **ValidateIssuer** como false. Esta opção desative a verificação automática.
-   Quando inicia um inquilino para cima, armazene o inquilino e o emissor na sua base de dados do utilizador.
-   Sempre que um utilizador inicia sessão, procure o emissor na base de dados. Se o emissor não for encontrado, significa que não inscreveu esse inquilino. Poderá redirecioná-los para um sinal de página para cima.
-  Também pode blacklist determinados inquilinos; Por exemplo, para os clientes que não pagar a sua subscrição.

Para obter informações mais detalhadas, consulte o artigo [inscrição e ativação numa aplicação multi-inquilino de inquilinos][signup].

## <a name="using-claims-for-authorization"></a>Reclamações por autorização a utilizar

Com afirmações, identidade de um utilizador já não se encontra uma entidade monolítico. Por exemplo, um utilizador poderá ter um endereço de correio eletrónico, número de telefone, aniversário, género, etc. Talvez IDP o utilizador armazena todas estas informações. Mas ao autenticar o utilizador, normalmente, obterá um subconjunto dos seguintes procedimentos como em afirmações. Neste modelo, a identidade do utilizador é simplesmente um conjunto de afirmações. Quando se decisões de autorização sobre um utilizador, irá procurar determinados conjuntos de afirmações. Por outras palavras, a pergunta "Pode utilizador X executar ações Y", finalmente, torna-se "tem de X afirmação do utilizador Z".

Aqui estão alguns padrões básicas para verificar em afirmações.

-  Para verificar que o utilizador tem uma determinada afirmação com um determinado valor:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Este código verifica se o utilizador tem uma afirmação função com o valor "Administrador". Esta trata corretamente as maiúsculas/minúsculas onde o utilizador tem sem afirmação função ou vários reclamações de função.

    A classe de **ClaimTypes** define constantes para os tipos de afirmações utilizadas frequentemente. No entanto, pode utilizar qualquer valor de cadeia para o tipo de afirmação.

-   Para obter um valor único para um tipo de afirmação, quando o que esperar exista mais por um valor:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Para obter todos os valores para um tipo de afirmação:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Para mais informações, consulte [autorização baseadas no recurso e baseado em funções nas aplicações multi-inquilino][authorization].

## <a name="next-steps"></a>Próximos passos

- Leia o seguinte artigo nesta série: [inscrição e ativação numa aplicação multi-inquilino de inquilinos][signup]
- Mais informações sobre [autorização baseada em afirmações] na documentação do ASP.NET Core 1.0

<!-- Links -->
[parte de uma série]: guidance-multitenant-identity.md
[parâmetro de âmbito]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Token suportado e os tipos de afirmações]: ../active-directory/active-directory-token-and-claims.md
[emissor]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Eventos de autenticação]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Autorização baseada em afirmações]: https://docs.asp.net/en/latest/security/authorization/claims.html
[aplicação de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md

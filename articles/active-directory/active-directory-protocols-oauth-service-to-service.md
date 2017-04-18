<properties
    pageTitle="Azure AD serviços Auth utilizando OAuth2.0 | Microsoft Azure"
    description="Este artigo descreve como utilizar mensagens HTTP para implementar a autenticação de serviço para utilizar o fluxo de conceder OAuth2.0 cliente credenciais."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="service-to-service-calls-using-client-credentials"></a>Serviço para chamadas de serviço utilizando credenciais do cliente

OAuth 2.0 cliente credenciais conceder fluxo de permite que um serviço web (um *cliente confidencial*) para utilizar a suas própria credenciais para autenticar durante a chamada de outro serviço web, em vez de um utilizador a representar. Neste cenário, o cliente é normalmente um serviço web camada, um serviço de daemon ou o web site.

## <a name="client-credentials-grant-flow-diagram"></a>Diagrama de fluxo de conceder de credenciais do cliente

O diagrama seguinte explica como as credenciais do cliente conceder fluxo funciona no Azure Active Directory (Azure AD).

![OAuth2.0 cliente credenciais conceder fluxo](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. A aplicação de cliente autentica para o ponto final de emissão de tokens de Azure AD e os pedidos de um token de acesso.
2. O ponto final de emissão de tokens de Azure AD problemas o token de acesso.
3. O token de acesso é utilizado para autenticar ao recurso seguro.
4. São devolvidos dados a partir do recurso seguro para a aplicação web.

## <a name="register-the-services-in-azure-ad"></a>Registar os serviços no Azure AD

Registe-se o serviço de chamada e o serviço de recepção no Azure Active Directory (Azure AD). Para obter instruções detalhadas, consulte o artigo [Adicionar, atualizar e remover uma aplicação](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Pedir um Token de acesso

Para pedir um token de acesso, utilize uma mensagem de HTTP ao inquilino-específico ponto final do Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Pedido de token de acesso de serviço de serviço

Um pedido de token de acesso ao serviço contém seguintes parâmetros.

| Parâmetro | | Descrição |
|-----------|------|------------|
| response_type | obrigatório | Especifica o tipo de resposta pedida. Num fluxo conceder de credenciais de cliente, o valor tem de ser **client_credentials**.|
| client_id | obrigatório | Especifica o id de cliente do Azure AD do serviço web chamada. Para localizar o ID de cliente a aplicação de chamada, no Portal de gestão do Azure, clique em **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar**.|
| client_secret | obrigatório |  Introduza uma chave registada para o serviço web chamada no Azure AD. Para criar uma chave no Portal de gestão do Azure, clique em **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar**. |
| recurso | obrigatório | Introduza o URI de ID da aplicação do serviço web receção. Para localizar a aplicação ID URI, no Portal de gestão do Azure, clique em **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar**. |

## <a name="example"></a>Exemplo

A seguinte mensagem de HTTP pedidos um token de acesso para o serviço web de https://service.contoso.com/. O `client_id` identifica o serviço web que solicita o token de acesso.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Resposta Token de acesso de serviço de serviço

Uma resposta de sucesso contém uma resposta JSON OAuth 2.0 com os seguintes parâmetros.

| Parâmetro   | Descrição |
|-------------|-------------|
|access_token |O token de acesso pedida. O serviço web chamada pode utilizar este token para autenticar o serviço web receção. |
|access_type  | Indica o valor de tipo de token. O único tipo que suporte do Azure AD é o **tipo de ligação**. Para mais informações sobre tokens de tipo de ligação, consulte o artigo o [OAuth 2.0 autorização Framework: a utilização Token de ligação (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | Quanto tempo o token de acesso é válido (em segundos).|
|expires_on   |O tempo quando expira o token de acesso. A data é representada como o número de segundos a partir do 1970-01-01T0:0:0Z UTC até o tempo de expiração. Este valor é utilizado para determinar a duração de tokens em cache. |
|recurso     | O URI de ID da aplicação do serviço web receção. |

## <a name="example"></a>Exemplo

O exemplo seguinte mostra uma resposta de sucesso a um pedido para um token de acesso a um serviço web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Consulte também

* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)

<properties
    pageTitle="Azure AD 2.0 protocolos | Microsoft Azure"
    description="Um guia para protocolos suportados pelo ponto final do Azure AD 2.0."
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

# <a name="v20-protocols---oauth-20--openid-connect"></a>Ligar de protocolos - OAuth 2.0 & OpenID 2.0

O ponto final 2.0 pode utilizar o Azure AD para identidade-como-a-service com protocolos padrão da indústria, ligue o OpenID e OAuth 2.0.  Enquanto o serviço é compatível com normas, pode ser ténue diferenças entre as duas implementações destes protocolos.  As informações aqui será útil se optar por escrever o seu código enviando diretamente e processamento de pedidos de HTTP ou utilizar um 3 festa abrir a biblioteca de origens, em vez de utilizar um dos nossos bibliotecas abrir origem.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas
Quase todos os OAuth & OpenID ligar fluxos de, existem quatro partes envolvidos no exchange:

![Funções de OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- O **Servidor de autorização** é o ponto final 2.0.  É responsável por assegurar a identidade do utilizador, conceder e revogar o acesso aos recursos e emitir tokens.  É também conhecida como o fornecedor de identidade - esta forma segura trata alguma coisa para fazer com as informações do utilizador, o seu acesso e as relações entre as partes de um fluxo de fidedignidade.
- O **Proprietário do recurso** é normalmente o utilizador final.  É a parte que é o proprietário de dados e tem a potência para permitir que terceiros aceder a esse dados ou recurso.
- O **Cliente OAuth** é a aplicação, identificada pelo seu ID de aplicação.  É normalmente a pessoa que o utilizador final interage com e os pedidos tokens do servidor de autorização.  O cliente tem de conceder permissão para aceder ao recurso pelo proprietário recurso.
- O **Servidor de recursos** está onde residem o recurso ou os dados.  Confia o servidor de autorização de forma segura autenticar e autorizar o cliente OAuth e utiliza portadores access_tokens para garantir que pode ser concedido acesso a um recurso.


## <a name="app-registration"></a>Registo de aplicação
Cada aplicação que utiliza o ponto final 2.0 terá de ser registado na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) antes de-pode interagir com OAuth ou OpenID ligar.  O processo de registo de aplicação irá recolher e atribuir alguns valores para a sua aplicação:

- Um **Id da aplicação** que identifica exclusivamente a sua aplicação
- Um **URI redirecionar** ou **Identificador de pacote** que podem ser utilizados para direcionar respostas para a sua aplicação
- Alguns outros valores de cenário específicos.

Para obter mais detalhes, saiba como [registar uma aplicação](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Pontos finais
Depois de registar, a aplicação comunica com Azure AD ao enviar pedidos para o ponto final 2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde o `{tenant}` pode execute uma das quatro diferentes valores:

| Valor | Descrição |
| ----------------------- | ------------------------------- |
| `common` | Permite aos utilizadores com contas pessoais do Microsoft e contas de trabalho/escola a partir do Azure Active Directory para iniciar sessão na aplicação. |
| `organizations` | Permite que apenas os utilizadores com contas de trabalho/escola a partir do Azure Active Directory para iniciar sessão na aplicação. |
| `consumers` | Permite que apenas os utilizadores com contas pessoais do Microsoft (MSA) para iniciar sessão na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`ou`contoso.onmicrosoft.com` | Permite que apenas os utilizadores com contas de trabalho/escola a partir de um inquilino do Azure Active Directory específico para iniciar sessão na aplicação.  O nome de domínio amigável do inquilino Azure AD ou identificador de guid do inquilino pode ser utilizado.  |

Para mais informações sobre como interagir com estes pontos finais, escolha um tipo de aplicação em particular abaixo.

## <a name="tokens"></a>Tokens
A aplicação 2.0 OAuth 2.0 e ligar OpenID utilizar extenso tokens de tipo de ligação, incluindo tokens portadores representados como JWTs. Uma ligação OAuth é um token de segurança lightweight que concede o acesso de "ligação" para um recurso protegido. Neste sentido, "portadores" são qualquer uma das partes que pode apresentar o token. Apesar de uma festa tem autenticar-se primeiro com o Azure AD para receber a ligação OAuth, se não forem tomados os passos necessários para proteger o token na transmissão e armazenamento, pode ser interceptada e utilizado por das partes indesejada. Enquanto alguns tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas utilizá-los, tokens de tipo de ligação não têm este mecanismo e devem ser transportados num canal seguro, tal como a segurança da camada de transporte (HTTPS). Se uma ligação OAuth é transmitido em claro, um homem-in o nome do meio ataque pode ser utilizado por uma festa maliciosa para adquirir o token e utilizá-lo para um acesso não autorizado a um recurso protegido. Aplicam os mesmos princípios de segurança quando armazenar ou colocação em cache tokens de tipo de ligação para utilizar posteriormente. Certifique-se sempre que a sua aplicação transmite e armazena tokens de tipo de ligação de uma forma segura. Para obter mais considerações de segurança no tokens de tipo de ligação, consulte o artigo [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Mais detalhes dos diferentes tipos de tokens utilizados no ponto final 2.0 está disponível na [referência de token de ponto final 2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocolos

Se estiver pronto para ver alguns pedidos de exemplo, começar com um da abaixo tutoriais.  Cada um deles corresponde a um cenário de autenticação específico.  Se precisar de ajuda para determinar qual é o caudal direita por si, consulte o artigo [os tipos de aplicações que pode ser criados com o 2.0](active-directory-v2-flows.md).

- [Construir Mobile e aplicação nativa com OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Criar Web ligar as aplicações com o ID aberta](active-directory-v2-protocols-oidc.md)
- [Criar aplicações única página com o fluxo de implícito OAuth 2.0](active-directory-v2-protocols-implicit.md)
- [Compilar Daemons ou processos de lado Server com as credenciais do cliente OAuth 2.0 fluxo](active-directory-v2-protocols-oauth-client-creds.md)
- Obter tokens numa API Web com a OAuth 2.0 em nome de um fluxo de (brevemente)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 

<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Como criar aplicações diretamente ao utilizar os protocolos suportados pelo Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protocolos de autenticação

Azure Active Directory (Azure AD) B2C fornece identidade como um serviço para as suas aplicações por duas protocolos de padrão da indústria de suporte: Ligue o OpenID e OAuth 2.0. O serviço é compatível com normas, mas qualquer duas implementações destes protocolos têm diferenças ténue.  As informações neste guia será útil se escrever o seu código diretamente enviando e processamento de pedidos de HTTP, em vez de utilizar uma biblioteca de abrir origem. Recomendamos que leia esta página antes de começar para os detalhes de cada protocolo específico. Mas, se já estiver familiarizado com o Azure AD B2C, pode ir diretamente para [as guias de referência de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Noções básicas
Cada aplicação que utiliza o Azure AD B2C tem de estar registado no seu diretório B2C no [portal do Azure](https://portal.azure.com). O processo de registo de aplicação recolhe e atribui alguns valores para a sua aplicação:

- Um **ID da aplicação** que identifica exclusivamente a sua aplicação.
- Um **URI redirecionar** ou **identificador de pacote** que podem ser utilizados para direcionar respostas para a sua aplicação.
- Alguns outros valores de cenário específicos. Para obter mais informações, saiba [como registar a sua aplicação](active-directory-b2c-app-registration.md).

Depois de registar a sua aplicação, comunica com o Azure AD ao enviar pedidos para o ponto final 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Em quase todos os fluxos de OAuth e ligar-se OpenID, quatro partes são envolvidas no exchange:

![Funções de OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- O **servidor de autorização** é o ponto final do Azure AD 2.0. Em segurança trata qualquer elemento relacionado com acesso e informações do utilizador. Este também processa as relações entre as partes num fluxo de fidedignidade. É responsável por verificar a identidade do utilizador, conceder e revogar o acesso aos recursos e emitir tokens. Também conhecido como é o fornecedor de identidade.
- O **proprietário do recurso** é normalmente o utilizador final. É a parte que é o proprietário de dados e tiver power para permitir que terceiros aceder a esse dados ou de recurso.
- O **cliente OAuth** é a sua aplicação. É identificada pelo seu ID de aplicação. É, normalmente, a parte que os utilizadores finais interagem com. Também os pedidos tokens do servidor de autorização. O proprietário do recurso tem de conceder a permissão de cliente para aceder ao recurso.
- O **servidor de recursos** está onde residem o recurso ou os dados. Confia o servidor de autorização para autenticar e autorizar o cliente OAuth de forma segura. Também utiliza tokens de acesso de tipo de ligação para se certificar de que pode ser concedido acesso a um recurso.

## <a name="policies"></a>Políticas
Pode, políticas do Azure AD B2C são as funcionalidades mais importantes do serviço. Azure AD B2C expande os protocolos de OAuth 2.0 e ligar OpenID padrão introduzindo políticas. Estas permitem que Azure AD B2C efetuar muito mais do que simples autenticação e autorização. Políticas de totalmente descrevem consumidor identidade experiências, incluindo a inscrição, iniciar sessão e edição de perfil. Políticas de podem ser definidas numa IU administrativa. Estes podem ser executadas por utilizar um parâmetro de consulta especial no pedidos de autenticação de HTTP. Políticas não estão funcionalidades padrão do token 2.0 e OpenID ligar, pelo que deverá tomar o tempo para compreendê-los. Para obter mais informações, consulte o [Guia de referência do Azure AD B2C política](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokens
A aplicação Azure AD B2C OAuth 2.0 e ligar OpenID faz extensa utilização do tokens de tipo de ligação, incluindo tokens de tipo de ligação que são representados como tokens de web JSON (JWTs). Uma ligação OAuth é um token de segurança lightweight que concede o acesso de "ligação" para um recurso protegido. A ligação é qualquer uma das partes que pode apresentar o token. Azure AD tem autenticar-se primeiro uma festa antes de-pode receber uma ligação OAuth. Mas, se não forem tomados os passos necessários para proteger o token na transmissão e armazenamento, pode ser interceptada e utilizado por das partes indesejada.

Alguns tokens de segurança têm um mecanismo incorporado que impede que partes não autorizadas utilizá-los, mas tokens de tipo de ligação não têm este mecanismo. Devem ser transportados num canal seguro, tal como a segurança da camada de transporte (HTTPS). Se uma ligação OAuth é transmitido fora de um canal seguro, uma festa maliciosa pode utilizar um homem no meio ataque para adquirir o token e utilizá-la para obter acesso não autorizado a um recurso protegido. Aplicam os mesmos princípios de segurança quando tokens portadores são armazenados ou em cache para utilização posterior. Certifique-se sempre que a sua aplicação transmite e armazena tokens de tipo de ligação de uma forma segura.

Considerações de segurança token portadores adicionais, consulte o artigo [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Obter mais detalhes sobre os diferentes tipos de tokens utilizados no Azure AD B2C estão disponíveis na [referência token Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocolos

Quando estiver pronto para rever alguns pedidos de exemplo, pode começar com um dos seguintes tutoriais. Cada um deles corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual fluem são ideal para si, consulte o artigo [tipos de aplicações que pode ser criados utilizando o Azure AD B2C](active-directory-b2c-apps.md).

- [Criar aplicações móveis e nativas utilizando o OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Criar aplicações web utilizando a ligação OpenID](active-directory-b2c-reference-oidc.md)

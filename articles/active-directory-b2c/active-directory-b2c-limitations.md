<properties
    pageTitle="Azure B2C diretório ativos: As restrições e limitações | Microsoft Azure"
    description="Uma lista de restrições com o Azure Active Directory B2C e limitações"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active B2C de diretório: As restrições e limitações

Existem várias funcionalidades e funcionalidades do Azure Active Directory (Azure AD) B2C que ainda não são suportadas. Muitos destes problemas conhecidos e limitações serão endereçados passará, mas deverá conhecê-las se estiver a criar aplicações do consumidor destinado utilizando o Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemas durante a criação de inquilinos do Azure AD B2C

Se se deparar com problemas durante a [criação de um inquilino do Azure AD B2C](active-directory-b2c-get-started.md), consulte o artigo [criar um inquilino do Azure AD ou um inquilino do Azure AD B2C – problemas e resoluções](active-directory-b2c-support-create-directory.md) para obter orientações.

Tenha em atenção que existem conhecidos problemas quando eliminar um inquilino do B2C existente e criar novamente com o mesmo nome de domínio. Tem de criar um inquilino B2C com um nome de domínio diferente.

## <a name="note-about-b2c-tenant-quotas"></a>Tenha em atenção sobre quotas de inquilino B2C

Por predefinição, o número de utilizadores num inquilino B2C é limitado a 50.000 utilizadores. Se precisar de aumentar a quota do seu inquilino B2C, deverá contactar o suporte.

## <a name="branding-issues-on-verification-email"></a>Problemas no e-mail de verificação de imagem corporativa

O e-mail de verificação predefinido contém a imagem corporativa do Microsoft. Vamos vai removê-lo no futuro. Por agora, pode removê-lo ao utilizar a [funcionalidade de imagem corporativa da empresa](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Restrições sobre aplicações

Atualmente, não são suportados os seguintes tipos de aplicações no Azure AD B2C. Para obter uma descrição dos tipos de aplicações suportados, consulte [Azure AD B2C: tipos de aplicações](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Única página de aplicações (JavaScript)

Muitas aplicações modernas ter uma única página aplicação (SPA) front-end escrito principalmente JavaScript e utiliza frequentemente um quadro SPA como AngularJS, Ember.js, Durandal, etc. Este fluxo ainda não está disponível no Azure AD B2C.

### <a name="daemons--server-side-applications"></a>Daemons / lado do servidor aplicações

Aplicações que contenham processos de execução longa ou que funcionem sem a presença de um utilizador também precisa de uma maneira de aceder a recursos seguros, tal como APIs da Web. Estas aplicações podem autenticar e obter tokens utilizando a identidade da aplicação (em vez de identidade delegada um consumidor) no [fluxo de credenciais de cliente OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Este fluxo ainda não está disponível no Azure AD B2C, para por agora, aplicações podem receber tokens apenas depois de ter ocorrido um fluxo de início de sessão no consumidor interativos.

### <a name="standalone-web-apis"></a>APIs Web autónomo

Na B2C de AD Azure, tem a capacidade de [criar uma API Web que está protegida utilizando tokens OAuth 2.0](active-directory-b2c-apps.md#web-apis). No entanto, esse Web API apenas poderão receber tokens a partir de um cliente que partilha o ID de aplicação do mesmo. Construir uma API de Web é acedida a partir de vários clientes diferentes não é suportada.

### <a name="web-api-chains-on-behalf-of"></a>Cadeias de API da Web (em nome de)

Muitos arquitecturas incluem uma API Web que necessita para ligar a outra descendentes Web API, ambos protegidos por Azure AD B2C. Este cenário é comuns em nativos clientes que têm uma Web API back-end, que por sua vez chama um serviço online da Microsoft como a API do Azure AD Graph.

Este cenário Web API interligado pode ser suportado utilizando a conceder OAuth 2.0 Jwt portadores credenciais, caso contrário, conhecida como o fluxo da do nome do sucessivamente. No entanto, o fluxo em nome de não está atualmente implementado B2C de AD o Azure.

## <a name="restriction-on-libraries-and-sdks"></a>Restrição em bibliotecas e SDK

O conjunto de bibliotecas do Microsoft suportada que funcionam Azure AD B2C é muito limitado neste momento. Temos suporte para aplicações web do .NET com base e serviços, bem como NodeJS web aplicações e serviços.  Também temos uma biblioteca de cliente do pré-visualização .NET conhecida como MSAL que pode ser utilizado com Azure AD B2C no Windows e outras aplicações do .NET.

Vamos não tiver atualmente biblioteca suporte noutros idiomas ou plataformas, incluindo iOS e Android.  Se pretender criar uma plataforma diferentes daqueles mencionados acima, recomendamos que utilize uma origem de abrir SDK, que fazem referência a nossa [OAuth 2.0 e a referência de protocolo de ligar OpenID](active-directory-b2c-reference-protocols.md) conforme necessário.  Azure AD B2C implementa OAuth & OpenID ligar, que torna possível a utilizar uma biblioteca de OAuth ou ligar OpenID genérica para integração.

Os nossos iOS e Android guia tutoriais utilizam bibliotecas de abrir origem que testámos para compatibilidade com o Azure AD B2C.  Todos os tutoriais nossos Guia estão disponíveis na nossa secção [Introdução](active-directory-b2c-overview.md#getting-started) .

## <a name="restriction-on-protocols"></a>Restrição protocolos

Azure AD B2C suporta OpenID ligar e OAuth 2.0. No entanto, nem todas as funcionalidades e capacidades de cada protocolo foram implementadas. Para melhor compreenda o âmbito da funcionalidade de protocolo suportado no Azure AD B2C, leia o nosso [OpenID ligar e de referência de protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md). Suporte do protocolo SAML e WS Fed não está disponível.

## <a name="restriction-on-tokens"></a>Restrição de tokens

Muitos dos tokens emitidos por Azure AD B2C são implementados como JSON Web Tokens ou JWTs. No entanto, nem todas as informações contidas no JWTs (conhecidas como "em afirmações") são bastante que deve ser ou em falta. Alguns exemplos incluem "sub" e as afirmações "preferred_username".  Como os valores, o formato ou o significado de alteração de afirmações ao longo do tempo, tokens para as políticas existentes permanecerá afetados - que pode confiar nos respetivos valores nas aplicações de produção.  Como alteram valores, podemos irá dar-lhe a oportunidade de configurar essas alterações para cada uma das suas políticas.  Para compreender melhor os tokens atualmente emitidos pelo serviço Azure AD B2C, leia a nossa [token referência](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restrição no grupos aninhados

Os membros do grupo interno aninhado não são suportados no inquilinos do Azure AD B2C. Não planeamos adicionar esta funcionalidade.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restrição à funcionalidade de consulta diferença num API do Azure AD Graph

A [funcionalidade de consulta diferença num API do Azure AD gráfico](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) não é suportada no inquilinos do Azure AD B2C. Este é no nosso guia a longo prazo.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemas com a gestão de utilizadores no portal do Azure clássico

Funcionalidades de B2C são acessíveis no portal do Azure. No entanto, pode utilizar o portal clássico Azure para aceder a outras funcionalidades de inquilino, incluindo gestão de utilizadores. Atualmente, existem algumas dos problemas conhecidos com gestão de utilizadores (o separador de **utilizadores** ) no portal do Azure clássico:

- Para um utilizador da conta local (ou seja, um consumidor quem se inscreve com um endereço de e-mail e palavra-passe, ou um nome de utilizador e palavra-passe), o campo **Nome de utilizador** não corresponde ao início de sessão no identificador (endereço de e-mail ou nome de utilizador) que foi utilizado durante a inscrição. Este é porque o campo apresentado no portal do Azure clássico é realmente o utilizador nome Principal (UPN), que não é utilizada em cenários B2C. Para ver o identificador de início de sessão da conta local, localize o objeto de utilizador no [Explorador do gráfico](https://graphexplorer.cloudapp.net/). Irá encontrar o mesmo problema com uma conta de rede social de utilizador (por exemplo, um consumidor quem se inscreve com Facebook, Google +, etc.), mas nesse caso, não existe nenhum identificador de início de sessão para falar de.

    ![Conta local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para um utilizador da conta local, pode não conseguirá editar qualquer um dos campos e guardar alterações no separador **perfil** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemas com iniciado pelo administrador palavra-passe Repor no portal do Azure clássico

Se repor a palavra-passe para um local consumidor conta baseada no portal de clássico Azure (o comando **Repor palavra-passe** no separador **utilizadores** ), esse consumidor não poderá alterar dele palavra-passe o seguinte início de sessão, se utilizar o início de sessão para cima ou política, início de sessão e será bloqueada terminar as suas aplicações. Como solução, utilize a [API do Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) a palavra-passe do consumidor (sem expiração de palavra-passe) ou utilize um política em vez de um sinal de início de sessão para cima ou política de início de sessão.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemas com a criação de um atributo personalizado

Um [atributo personalizado adicionado no portal do Azure](active-directory-b2c-reference-custom-attr.md) não é criada imediatamente no seu inquilino B2C. Terá de utilizar o atributo personalizado de pelo menos uma das suas políticas para a mesma para são criados no seu inquilino B2C e ficam disponíveis através da API do gráfico.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemas com a verificar um domínio no portal do Azure clássico

Atualmente não consegue verificar um domínio com êxito no [Azure portal clássica](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemas com a sessão com a política de MFA em Safari browsers

Pedidos de políticas de início de sessão (com MFA ativada) falhas intermitentemente em browsers, Safari com erros de HTTP 400 (pedido incorrecto). Este é devidos limites de tamanho de baixa cookie do Safari. Existem algumas soluções para este problema:

- Utilize a "inscrição ou início de sessão no política" em vez da "início de sessão no política".
- Reduza o número de **afirmações da aplicação** que está a ser pedido na sua política.

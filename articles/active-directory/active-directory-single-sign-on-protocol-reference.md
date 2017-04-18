<properties
    pageTitle="Azure Single Sign-On SAML protocolo | Microsoft Azure"
    description="Este artigo descreve o protocolo de início de sessão único no SAML no Azure Active Directory"
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

# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML início de sessão único

Este artigo abrange os pedidos de autenticação do SAML 2.0 e as respostas que suporta o Azure Active Directory (Azure AD) para Single Sign-On.

O diagrama de protocolo abaixo descreve a sequência de início de sessão única. O serviço de nuvem (o fornecedor de serviços) utiliza um enlace HTTP redirecionar para transmitir uma `AuthnRequest` elemento (pedido de autenticação) para Azure AD (o fornecedor de identidade). Em seguida, Azure AD utiliza uma mensagem HTTP vincular a publicar uma `Response` elemento para o serviço de nuvem.

![Single Sign-On fluxo de trabalho](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Para pedir um método de autenticação de utilizador, na nuvem dos serviços de enviar um `AuthnRequest` elemento a Azure AD. Um exemplo SAML 2.0 `AuthnRequest` poderia este aspeto:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| ID | obrigatório | Azure AD utiliza este atributo para preencher a `InResponseTo` atributo da resposta devolvido. ID de não tem de começar com um número, pelo que é uma estratégia de comuns anexar como prefixo uma cadeia tal como "id" para a representação de cadeia de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é um ID válido. |
| Versão | obrigatório | Isto deve ser **2.0**.|
| IssueInstant | obrigatório | Esta é uma cadeia de data/hora com um valor de UTC e [Formatar ida e volta ("t")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD espera um valor DateTime deste tipo, mas não avaliar ou utilize o valor. |
| AssertionConsumerServiceUrl | opcional | Se tiver fornecido, este tem de corresponder a `RedirectUri` do serviço na nuvem no Azure AD. |
| ForceAuthn | opcional | Se tiver fornecido, este deve ser falso. Qualquer outro valor provoca um erro.|
| IsPassive | opcional | Se tiver fornecido, este deve ser falso. Qualquer outro valor provoca um erro. |  

Todos os outros `AuthnRequest` atributos, tais como consentimento, destino, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignorados**.

Azure AD também ignora as `Conditions` elemento `AuthnRequest`.

### <a name="issuer"></a>Emissor

O `Issuer` elemento uma `AuthnRequest` deve corresponder exatamente a um da **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isto é definido para a **Aplicação ID URI** especificado durante o registo de aplicação.

Um exemplo SAML excerto que contém o `Issuer` elemento tem o seguinte aspeto:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Este elemento os pedidos de um formato de ID do nome em particular na resposta e é opcional no `AuthnRequest` elementos enviados para o Azure AD.

Um exemplo `NameIdPolicy` elemento tem o seguinte aspeto:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` é fornecido, pode incluir respetivo opcional `Format` atributo. O `Format` atributo só pode ter um dos seguintes valores; qualquer outro valor resulta num erro.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory problemas a afirmação NameID como um identificador par.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory problemas a afirmação NameID no formato de endereço de correio electrónico.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Este valor permite Azure Active Directory para selecionar o formato de afirmação. Azure Active Directory problemas a NameID como um identificador par.

Não inclua a `SPNameQualifer` atributo. Azure AD ignora as `AllowCreate` atributo.

### <a name="requestauthncontext"></a>RequestAuthnContext

O `RequestedAuthnContext` elemento Especifica os métodos de autenticação pretendido. É opcional no `AuthnRequest` elementos enviados para o Azure AD. Azure AD suporta apenas uma `AuthnContextClassRef` valor: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Controlo do âmbito

O `Scoping` elemento, que inclui uma lista de fornecedores de identidades, é opcional no `AuthnRequest` elementos enviados para o Azure AD.

Se tiver fornecido, não inclua a `ProxyCount` atributo, `IDPListOption` ou `RequesterID` elemento, como não são suportadas.

### <a name="signature"></a>Assinatura

Não inclua um `Signature` elemento `AuthnRequest` elementos, não suporta o Azure AD assinado pedidos de autenticação.

### <a name="subject"></a>Assunto

Azure AD ignora as `Subject` elemento do `AuthnRequest` elementos.

## <a name="response"></a>Resposta

Quando um pedida início de sessão no é concluída com êxito, o Azure AD regista uma resposta para o serviço de nuvem. Uma resposta de exemplo para uma tentativa de início de sessão bem sucedida tem o seguinte aspeto:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Resposta

O `Response` elemento inclui o resultado do pedido de autorização. Azure AD conjuntos de `ID`, `Version` e `IssueInstant` os valores na `Response` elemento. Também define os seguintes atributos:

- `Destination`: Quando o início de sessão é concluída com êxito, está definido para o `RedirectUri` do fornecedor de serviços (serviço na nuvem).
- `InResponseTo`: Esta está definida para o `ID` atributo da `AuthnRequest` elemento que iniciou a resposta.

### <a name="issuer"></a>Emissor

Azure AD conjuntos de `Issuer` elemento a `https://login.microsoftonline.com/<TenantIDGUID>/` onde <TenantIDGUID> é o ID de inquilino do inquilino Azure AD.

Por exemplo, uma resposta de exemplo com elemento emissor poderia o seguinte aspeto:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado

O `Status` elemento concede o sucesso ou o fracasso do início de sessão. Inclui o `StatusCode` elemento, que contém um código ou um conjunto de códigos aninhados que representam o estado do pedido. Também inclui a `StatusMessage` elemento, que contém as mensagens de erro personalizadas que são geradas durante o processo de início de sessão.

<!-- TODO: Add a authentication protocol error reference -->

Segue-se uma resposta SAML a uma tentativa de início de sessão sem êxito.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Declaração

Para além de `ID`, `IssueInstant` e `Version`, Azure AD define os seguintes elementos na `Assertion` elemento da resposta.

#### <a name="issuer"></a>Emissor

Esta é definida como `https://sts.windows.net/<TenantIDGUID>/`onde <TenantIDGUID> é o ID de inquilino do inquilino Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

Azure AD assina afirmação em resposta a um bem sucedida início de sessão. O `Signature` elemento contém uma assinatura digital que pode utilizar o serviço de nuvem para autenticar a origem de verificar a integridade da declaração.

Para gerar a assinatura digital, Azure AD utiliza a chave de assinatura na `IDPSSODescriptor` elemento do seu documento de metadados.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Assunto

Especifica o capital que é o assunto das instruções na declaração. Contém uma `NameID` elemento, que representa o utilizador autenticado. O `NameID` valor é um identificador de destino que é direcionado apenas para o fornecedor de serviços que está a audiência para o token. É persistente - pode ser revogado, mas nunca é reatribuída. Também é opaco, nesse-não revelar nada sobre o utilizador não pode ser utilizado como um identificador para consultas de atributo.

O `Method` atributo da `SubjectConfirmation` elemento sempre está definido para `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Este elemento Especifica as condições que definem a utilização aceitável de declarações de SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

O `NotBefore` e `NotOnOrAfter` atributos especificam o intervalo durante o qual a declaração é válida.

- O valor da `NotBefore` atributo for igual ou ligeiramente (menos de um segundo) mais tarde do que o valor de `IssueInstant` atributo da `Assertion` elemento. Azure AD não conta para qualquer diferença de tempo entre si próprio e o serviço de nuvem (fornecedor de serviços) e não adiciona qualquer memória intermédia para este período de tempo.
- O valor da `NotOnOrAfter` atributo é mais tarde do que o valor da 70 minutos a `NotBefore` atributo.

#### <a name="audience"></a>Audiência

Este contém um URI que identifica um público-alvo. Azure AD define o valor deste elemento para o valor de `Issuer` elemento da `AuthnRequest` que iniciou o início de sessão. Para avaliar a `Audience` valor, utilize o valor da `App ID URI` que foi especificado durante o registo de aplicação.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o `Issuer` valor, o `Audience` valor deve corresponder exatamente a um dos nomes principais de serviço que representa o serviço de nuvem no Azure AD. No entanto, se o valor da `Issuer` elemento não for um valor URI, o `Audience` valor na resposta é o `Issuer` valor com o prefixo `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Este contém em afirmações sobre o assunto ou utilizador. O excerto seguinte contém uma amostra `AttributeStatement` elemento. Nas reticências indica que o elemento pode incluir várias atributos e valores de atributo.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **Afirmação de nome** : O valor da `Name` atributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o nome principal de utilizador do utilizador autenticado, tais como `testuser@managedtenant.com`.
- **ObjectIdentifier afirmação** : O valor da `ObjectIdentifier` atributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é a `ObjectId` do objeto diretório que representa o utilizador autenticado no Azure AD. `ObjectId`é um imutáveis globalmente exclusivo e reutilização seguro identificador do utilizador autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Este elemento afirma que o assunto da declaração foi autenticado por um determinado meio num momento específico.

- O `AuthnInstant` atributo Especifica a hora em que o utilizador autenticado com Azure AD.
- O `AuthnContext` elemento Especifica o contexto de autenticação utilizado para autenticar o utilizador.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

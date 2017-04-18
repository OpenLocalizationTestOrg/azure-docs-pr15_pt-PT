<properties
    pageTitle="Início de sessão único Azure saída SAML protocolo | Microsoft Azure"
    description="Este artigo descreve o protocolo de SAML Sign-Out única no Azure Active Directory"
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


# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML terminar sessão único

Suporta a Azure Active Directory (Azure AD) SAML 2.0 web único perfil terminar sessão de browser. Para terminar sessão único para funcionar corretamente, Azure AD tem de registar o URL de metadados durante o registo de aplicação. Azure AD obtém o URL de termine a sessão e a chave de assinatura do serviço de nuvem dos metadados. Azure AD utiliza a chave de assinatura para verificar a assinatura na LogoutRequest recebida e utiliza o LogoutURL para redirecionar utilizadores depois de que tem sessão iniciadas.

Se o serviço de nuvem não suportar um ponto final de metadados, após a aplicação é registada, o programador tem contacte o suporte da Microsoft para fornecer o URL de termine a sessão e a chave de assinatura.

Este diagrama mostra o fluxo de trabalho do Azure AD única terminar processo de.

![Terminar o fluxo de trabalho de sessão único](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

O envia de serviço de nuvem um `LogoutRequest` mensagem para Azure AD para indicar que uma sessão foi terminada. O excerto seguinte mostra um exemplo `LogoutRequest` elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

O `LogoutRequest` elemento enviado para o Azure AD requer os seguintes atributos:

- `ID`: Identifica o pedido de terminar sessão. O valor de `ID` não deverá começar com um número. Exercícios práticos típico são acrescentar **id** para a representação de cadeia de um GUID.

- `Version`: Defina o valor deste elemento para **2.0**. Este valor é necessário.

- `IssueInstant`: Este é um `DateTime` cadeia com um valor de hora Universal coordenar (UTC) e o [formato ida e volta ("t")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD espera um valor deste tipo, mas não impor.

- O `Consent`, `Destination`, `NotOnOrAfter` e `Reason` atributos são ignorados se estão incluídos num `LogoutRequest` elemento.

### <a name="issuer"></a>Emissor

O `Issuer` elemento um `LogoutRequest` deve corresponder exatamente a um da **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isto é definido para a **Aplicação ID URI** especificado durante o registo de aplicação.

### <a name="nameid"></a>NameID

O valor da `NameID` elemento tem de corresponder exatamente o `NameID` do utilizador que está a ser assinado saída.
## <a name="logoutresponse"></a>LogoutResponse

Azure AD envia uma `LogoutResponse` em resposta a um `LogoutRequest` elemento. O excerto seguinte mostra um exemplo `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Azure AD conjuntos de `ID`, `Version` e `IssueInstant` os valores na `LogoutResponse` elemento. Também define o `InResponseTo` elemento para o valor da `ID` atributo da `LogoutRequest` que caso a resposta.

### <a name="issuer"></a>Emissor

Azure AD define este valor `https://login.microsoftonline.com/<TenantIdGUID>/` onde <TenantIdGUID> é o ID de inquilino do inquilino Azure AD.

Para avaliar o valor da `Issuer` elemento, utilize o valor do **Aplicação ID URI** fornecido durante o registo de aplicação.

### <a name="status"></a>Estado

Azure AD utiliza o `StatusCode` elemento o `Status` elemento para indicar o sucesso ou o fracasso do terminar sessão. Quando a tentativa de terminar sessão falhar, o `StatusCode` elemento também pode conter mensagens de erro personalizadas.

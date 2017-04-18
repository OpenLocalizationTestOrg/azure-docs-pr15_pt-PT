<properties
    pageTitle="Metadados do Azure AD Federação | Microsoft Azure"
    description="Este artigo descreve o documento de metadados de Federação do Active Directory Azure publica para serviços aceitar tokens Azure Active Directory."
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


# <a name="federation-metadata"></a>Federação metadados

Azure Active Directory (Azure AD) publica um documento de metadados de Federação para serviços configurado para aceitar os tokens de segurança Azure AD problemas. O formato de documento de metadados de Federação é descrito na [versão 1.2 da Web Serviços de Federação Language (WS Federação)](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que se expande [metadados para a organização de normalização segurança afirmação Markup Language (SAML) 2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Específicas do inquilino e os pontos finais de metadados do inquilino independente

Azure AD publica os pontos finais específicas do inquilino e inquilino independente.

Os pontos finais de específicas do inquilino foram concebidos para um inquilino específico. Os metadados de Federação específicas do inquilino incluem informações sobre o inquilino, incluindo informações específicas do inquilino de emissor e ponto final. As aplicações que restringir o acesso a um único inquilino utilizam os pontos finais de específicas do inquilino.

Os pontos finais de inquilino independente fornecem informações que são comuns a todos os Azure AD inquilinos. Esta informação aplica-se a inquilinos alojados *login.microsoftonline.com* e podem é partilhada por inquilinos. Os pontos finais de inquilino independente recomenda-se para as aplicações do inquilinos com várias, uma vez que não estão associadas a qualquer inquilino específico.

## <a name="federation-metadata-endpoints"></a>Pontos finais de metadados da Federação

Azure AD publica metadados de Federação na `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **os pontos finais de inquilino específicos**, o `TenantDomainName` pode ser um dos seguintes tipos de:

- Um nome de domínio registado de um Azure AD de inquilinos, tais como: `contoso.onmicrosoft.com`.
- O imutáveis inquilinos ID do domínio, tal como `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **os pontos finais de inquilino independente**, o `TenantDomainName` é `common`. Este documento lista apenas os elementos de metadados de Federação comuns a todos os inquilinos do Azure AD que estão alojados login.microsoftonline.com.

Por exemplo, poderá ser um ponto final de específicas do inquilino `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto final de inquilino independente é [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Pode ver o documento de metadados de Federação ao escrever este URL num browser.

## <a name="contents-of-federation-metadata"></a>Conteúdos de Federação metadados

A secção seguinte fornece informações necessárias aos serviços consumam os tokens emitidos por Azure AD.

### <a name="entity-id"></a>ID da entidade

O `EntityDescriptor` elemento contém um `EntityID` atributo. O valor da `EntityID` atributo representa o emissor, ou seja, o token serviço de segurança (STS) que emitiu o token. É importante validar o emissor quando recebe um token.

Os metadados que se segue mostra um exemplo específicas do inquilino `EntityDescriptor` elemento com um `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Pode substituir o ID de inquilino o ponto final de inquilino independente com o seu ID de inquilino para criar uma inquilino específicas `EntityID` valor. O valor resultante serão os mesmos como o emissor de tokens. A estratégia permite uma aplicação do inquilino com várias validar o emissor para um determinado inquilino.

Os metadados que se segue mostra um exemplo inquilino independente `EntityID` elemento. Tenha em atenção que a `{tenant}` é um literal, não um marcador de posição.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura de tokens

Quando um serviço recebe um token que é emitido por um inquilino do Azure AD, tem de ser validada a assinatura do token de com uma chave de assinatura é publicada no documento de metadados Federação. Os metadados de Federação incluem a parte dos certificados que utilizam os inquilinos para assinatura de tokens pública. Os bytes observou certificado aparecem no `KeyDescriptor` elemento. O token de certificado de assinatura seja válido para assinatura apenas quando o valor da `use` atributo é `signing`.

Um documento de metadados de Federação publicado pelo Azure AD pode ter vários chaves de assinatura, tal como quando Azure AD está a preparar para atualizar o certificado de assinatura. Quando um documento de metadados de Federação inclui mais do que um certificado, um serviço que está a validar os tokens deverá suportar todos os certificados no documento.

Os metadados que se segue mostra um exemplo `KeyDescriptor` elemento com uma chave de assinatura.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

O `KeyDescriptor` elemento aparece em dois locais no documento de metadados Federação; na secção específicas de Federação de WS e a secção SAML específicas. Os certificados publicados em ambas as secções serão os mesmos.

Na secção WS Federação-específicas, um leitor de metadados WS Federation seria ler os certificados a partir de um `RoleDescriptor` elemento com o `SecurityTokenServiceType` tipo.

Os metadados que se segue mostra um exemplo `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Na secção SAML específicas, um leitor de metadados WS Federation seria ler os certificados a partir de um `IDPSSODescriptor` elemento.

Os metadados que se segue mostra um exemplo `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não existem diferenças no formato de certificados específicas do inquilino e inquilino independente.

### <a name="ws-federation-endpoint-url"></a>URL de ponto final de Federação WS

Os metadados de Federação incluem o URL que utiliza Azure AD para o início de sessão único e terminar sessão no protocolo WS Federation único. Este ponto final é apresentado na `PassiveRequestorEndpoint` elemento.

Os metadados que se segue mostra um exemplo `PassiveRequestorEndpoint` elemento para um ponto final de específicas do inquilino.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto final de inquilino independente, o URL de Federação WS aparece no ponto final de Federação WS, conforme mostrado no seguinte exemplo.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de ponto final de protocolo SAML

Os metadados de Federação incluem o URL que utiliza o Azure AD para o início de sessão único e terminar sessão no SAML 2.0 protocolo único. Estes pontos finais aparecem no `IDPSSODescriptor` elemento.

Os URLs de início de sessão e terminar sessão aparecem no `SingleSignOnService` e `SingleLogoutService` elementos.

Os metadados que se segue mostra um exemplo `PassiveResistorEndpoint` para um ponto final de específicas do inquilino.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Do mesmo modo os pontos finais para os pontos finais protocolo comuns do SAML 2.0 são publicados nos metadados de Federação do inquilino independente, conforme mostrado no seguinte exemplo.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

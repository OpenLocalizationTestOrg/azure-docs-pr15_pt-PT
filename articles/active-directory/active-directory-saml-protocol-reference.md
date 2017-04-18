<properties
    pageTitle="Referência de protocolo do Azure AD SAML | Microsoft Azure"
    description="Este artigo fornece uma descrição geral dos perfis Single Sign-On e único Sign-Out SAML no Azure Active Directory."
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
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Como o Azure Active Directory utiliza o protocolo SAML

Azure Active Directory (Azure AD) utiliza SAML 2.0 protocolo permitem que as aplicações fornecer uma experiência de início de sessão único aos seus utilizadores. Os perfis de [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) e [Terminar sessão único](active-directory-single-sign-out-protocol-reference.md) SAML do Azure AD explicam como SAML declarações, protocolos e enlaces são utilizados no serviço de fornecedor de identidade.

Protocolo de SAML requer o fornecedor de identidade (Azure AD) e o fornecedor de serviços (a aplicação) para obter informações sobre si próprios do exchange.

Quando uma aplicação é registada com Azure AD, o programador da aplicação regista informações relacionadas com a Federação com Azure AD. Isto inclui a **Redirecionar URI** e **URI de metadados** da aplicação.

Azure AD utiliza o **URI de metadados** do serviço em nuvem para obter a chave de assinatura e a termine a sessão URI do serviço na nuvem. Se a aplicação não suportar um metadados URI, o Programador de deve contactar o suporte da Microsoft para fornecer a termine a sessão URI e chave de assinatura.

Azure Active Directory expõe específicas do inquilino e comuns (inquilino independente) únicos início de sessão e únicos terminar sessão pontos finais. Estes URLs representam localizações endereçáveis – não estiverem apenas uma identificadores – para que pode ir para o ponto final para ler os metadados.

 - O ponto final de inquilino específicas está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  O <TenantDomainName> marcador de posição representa um nome de domínio registado ou TenantID GUID de um inquilino do Azure AD. Por exemplo, os metadados de Federação do inquilino contoso.com são na: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- O ponto final de inquilino independente está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Este endereço de ponto final, **comuns** é apresentada, em vez de um nome de domínio do inquilino ou ID.

Para obter informações sobre os documentos de Federação metadados que publica do Azure AD, consulte o artigo [Metadados de Federação](active-directory-federation-metadata.md).

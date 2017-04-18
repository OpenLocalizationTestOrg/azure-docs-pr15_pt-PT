<properties
    pageTitle="Algoritmo de hash de assinatura de alteração de fidedignidade do Office 365 responder festa | Microsoft Azure"
    description="Esta página fornece diretrizes para alterar o algoritmo SHA para fidedignidade de federação com o Office 365"
    keywords="SHA1, SHA256, Office 365, Federação, aadconnect, adfs, o ad fs, alterar sha, fidedignidade de Federação, depender fidedignidade terceiros"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Alterar o algoritmo de hash de assinatura do Office 365 responder fidedignidade festa

## <a name="overview"></a>Descrição geral

Azure Active Directory Federation Services (AD FS) assina respectivos tokens ao Microsoft Azure Active Directory para se certificar de que não pode ser indevidamente com. Esta assinatura pode ser baseada no SHA1 ou SHA256. Azure Active Directory suporta agora tokens tiver sessão iniciadas com um algoritmo de SHA256 e, recomendamos que a definição do algoritmo de assinatura de tokens para SHA256 para o mais alto nível de segurança. Este artigo descreve os passos necessários para definir o algoritmo de assinatura de tokens para o mais segura SHA256 nível.

## <a name="change-the-token-signing-algorithm"></a>Alterar o algoritmo de assinatura de tokens

Após ter configurado o algoritmo de assinatura com um dos dois processos abaixo, o AD FS assina os tokens para o Office 365 depender festa fidedignidade com SHA256. Não precisa de efetuar alterações à configuração extra e esta alteração não tem nenhum impacto na sua capacidade para aceder ao Office 365 ou outras aplicações do Azure AD.

### <a name="ad-fs-management-console"></a>Consola de gestão do AD FS

1. Abra o AD FS management console no servidor ADFS principal.
2. Expanda o nó do AD FS e clique em **Confiar fidedignidades de terceiros**.
3. Botão direito do rato sua confiança dependente de terceiros do Office 365/Azure e selecione **Propriedades**.
4. Selecione o separador **Avançadas** e selecione o algoritmo de hash seguro SHA256.
5. Clique em **OK**.

![Algoritmo de assinatura SHA256 – MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets do AD FS PowerShell

1. Em qualquer servidor do AD FS, abra o PowerShell em privilégios de administrador.
2. Defina o algoritmo hash seguro, utilizando o cmdlet **Set-AdfsRelyingPartyTrust** .

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leia também

* [Reparar fidedignidade do Office 365 com ligação do Azure AD](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

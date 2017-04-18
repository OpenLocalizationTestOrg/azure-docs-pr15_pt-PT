<properties 
    pageTitle="Começar a trabalhar com autenticação de certificados com base no Android | Microsoft Azure" 
    description="Saiba como configurar a autenticação de certificados com base nas soluções com dispositivos Android" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-android---public-preview"></a>Começar a trabalhar com autenticação de certificados com base no Android - Public Preview  

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Este tópico mostra-lhe como configurar e utilizar a autenticação de certificados com base (CBA) num dispositivo Android para utilizadores de inquilinos nos planos do Office 365 Enterprise, empresas e educação. 

CBA permite-lhe ser autenticada pelo Azure Active Directory com um certificado de cliente num dispositivo Android ou iOS ao ligar a sua conta do Exchange online para: 

- Aplicações móveis do Office como o Microsoft Outlook e o Microsoft Word   
- Clientes do Exchange ActiveSync (EAS) 

Configurar esta funcionalidade elimina a necessidade para introduzir uma combinação de nome de utilizador e palavra-passe para determinados impressão em série e aplicações do Microsoft Office no seu dispositivo móvel. 
 

## <a name="supported-scenarios-and-requirements"></a>Cenários suportados e requisitos  



### <a name="general-requirements"></a>Requisitos gerais 


Para todos os cenários neste tópico, são necessárias as seguintes tarefas:  

- Acesso a authority(s) certificado para emitir certificados de cliente.  

- Os certificados authority(s) tem de ser configurado no Azure Active Directory. Pode encontrar passos detalhados sobre como concluir a configuração na secção [Introdução](#getting-started) .  

- Autoridade de certificação de raiz e qualquer autoridades de certificação intermédia tem de ser configuradas no Azure Active Directory.  

- Autoridade de certificação tem de ter uma lista de revogação de certificados (CRL) que pode ser referenciada através de um opostas URL da Internet.  

- O certificado de cliente deve ser emitido para autenticação de cliente.  


- Para clientes do Exchange ActiveSync, o certificado de cliente tem de ter o endereço do utilizador encaminháveis e-mail no Exchange online o nome Principal ou o valor de RFC822 nome do campo nome do assunto alternativo. Conjunto mapas do Azure Active Directory o valor de RFC822 para o atributo de endereço Proxy no diretório.  



### <a name="office-mobile-applications-support"></a>Suporte para aplicações móveis do Office 

| Aplicações                      | Suporte      |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![Verificação][1]  |
| OneNote                   | Brevemente  |
| OneDrive                  | ![Verificação][1]  |
| Outlook                   | ![Verificação][1]  |
| Do Yammer                    | ![Verificação][1]  |
| Skype para empresas        | ![Verificação][1]  |


### <a name="requirements"></a>Requisitos de  

A versão do SO do dispositivo tem de ser 5.0 Android (raqueta) e superior. 

Um servidor de federação tem de ser configurado.  


Para o Azure Active Directory revogar um certificado de cliente, o token ADFS tem de ter as seguintes afirmações:  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(O número de série o certificado de cliente) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(Cadeia para o emissor do certificado de cliente) 

Azure Active Directory adiciona destas afirmações ao token de atualização se estiverem disponíveis no token de ADFS (ou outros token SAML). Quando o token de atualização necessita de ser validada, estas informações são utilizadas para verificar revogação. 

Como prática recomendada, deverá actualizar as páginas de erro ADFS com instruções sobre como obter um certificado de utilizador. 

Para obter mais detalhes, consulte o artigo [Personalizar as páginas AD FS iniciar sessão](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Suporte de clientes do Exchange ActiveSync 


Algumas aplicações do Exchange ActiveSync no Android 5.0 (raqueta) ou posterior são suportadas. Para determinar se é a sua aplicação de correio eletrónico suporta esta funcionalidade, contacte o seu programador de aplicação. 



## <a name="getting-started"></a>Introdução 


Para começar, precisa de configurar as autoridades de certificação no Azure Active Directory. Para cada autoridade de certificação, carregar o seguinte procedimento: 

- A parte do certificado, no formato *. cer* pública 

- Internet opostas URLs onde residem os as listas de revogação de certificados (CRLs)
 

Segue-se o esquema de uma autoridade de certificação: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Para carregar as informações, pode utilizar o módulo Azure AD através do Windows PowerShell.  
Abaixo estão exemplos para adicionar, remover ou modificar uma autoridade de certificação. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configurar o inquilino do Azure AD para certificado de autenticação baseada em 

1. Inicie o Windows PowerShell com privilégios de administrador. 

2. Instale o módulo Azure AD. Tem de instalar a versão [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) ou superior.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Ligar ao seu inquilino de destino: 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Adicionar uma nova autoridade de certificação

1. Definir propriedades de vários de autoridade de certificação e adicioná-la para o Azure Active Directory: 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obter as autoridades de certificação: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Obter as autoridades de certificação de lista

Obter as autoridades de certificação atualmente armazenadas no Azure Active Directory do seu inquilino: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Remover uma autoridade de certificação

1.  Obter as autoridades de certificação: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Remova o certificado para autoridade de certificação: 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying uma autoridade de certificação 

1.  Obter as autoridades de certificação: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Modificar as propriedades no autoridade de certificação: 

        $c[0].AuthorityType=1 

3. Defina a **autoridade de certificação**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Testar as aplicações móveis do Office  

Para testar a autenticação de certificados na sua aplicação móvel do Office: 

1.  No seu dispositivo de teste, instale uma aplicação móvel do Office (por exemplo, OneDrive) a partir da Google Play Store.

2.  Certifique-se de que o certificado de utilizador ter sido aprovisionado para o seu dispositivo de teste. 

3.  Inicie a aplicação. 

4.  Introduza o seu nome de utilizador e, em seguida, selecione o certificado de utilizador que pretende utilizar. 

Deve ser com êxito sessão iniciada. 





## <a name="testing-exchange-activesync-client-applications"></a>Testar aplicações de cliente do Exchange ActiveSync

Para aceder ao Exchange ActiveSync através de autenticação de certificado baseado, tem de ser à aplicação um perfil do EAS que contém o certificado de cliente. O perfil EAS tem de conter as seguintes informações:

- O certificado para ser utilizado para a autenticação de utilizador 

- O ponto final do EAS tem de ser office365 (tal como esta funcionalidade é suportada atualmente apenas no ambiente de inquilino com várias online do Exchange)

Um perfil do EAS pode ser configurado e colocado no dispositivo através da utilização de um MDM, como o Intune ou colocando manualmente o certificado no perfil do EAS no dispositivo.  


### <a name="testing-eas-client-applications-on-android"></a>Testar aplicações de cliente do EAS no Android 

Para testar a autenticação de certificado de com uma aplicação no Android 5.0 (raqueta) ou posterior, execute os passos abaixo:  

1. Configure um perfil do EAS na aplicação que satisfaz os requisitos acima.  


2. Depois do perfil está configurado corretamente, abra a aplicação e certifique-se de que o correio está a sincronizar. 




## <a name="revocation"></a>Revogação de certificados

Para revogar um certificado de cliente, o Azure Active Directory obtém a lista de revogação de certificados (CRL) a partir de URLs carregá-las como parte das informações de certificação de certificado e coloca-lo em cache. Publicar a última data/hora (**Data efetiva** propriedade) no LCR é utilizada para assegurar o LCR ainda estiver válido. O LCR periodicamente é referenciado para revogar o acesso a certificados que fazem parte da lista.

Se um revogação mais instantânea for necessária (por exemplo, se um utilizador perde um dispositivo), pode ser invalidado o token de autorização do utilizador. Invalidar o token de autorização, defina o campo de **StsRefreshTokenValidFrom** para este utilizador específico através do Windows PowerShell. Tem de atualizar o campo **StsRefreshTokenValidFrom** para cada utilizador que pretende revogar o acesso para.
 
Para se certificar de que a revogação persistir, tem de definir a **Data efetiva** do LCR a uma data após o valor definido pelo **StsRefreshTokenValidFrom** e certifique-se o certificado em questão é no LCR.
 
Os passos seguintes descrevem o processo de atualização e invalidem o token de autorização pela definição do campo **StsRefreshTokenValidFrom** . 

1. Ligar-se com as credenciais de administrador para o serviço MSOL: 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Obter o valor de StsRefreshTokensValidFrom atual para um utilizador: 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Configure um novo valor StsRefreshTokensValidFrom para a igual de utilizador para o carimbo de hora atual: 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


A data que definir tem de ser no futuro. Se não estiver a data no futuro, não está definida a propriedade **StsRefreshTokensValidFrom** . Se a data for no futuro, **StsRefreshTokensValidFrom** é definido como a hora atual (não da data indicada pelo comando conjunto-MsolUser). 


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
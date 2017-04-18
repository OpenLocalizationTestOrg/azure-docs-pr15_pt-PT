<properties
    pageTitle="Ligação do Azure AD e Federação | Microsoft Azure"
    description="Esta página é a localização central para toda a documentação relativamente ao operações de AD FS utilizando a ligação do Azure AD"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Ligação do Azure AD e Federação

Ligação do Azure AD permite-lhe configurar a Federação com no local AD FS e Azure AD. Com Federação início de sessão, pode ativar a utilizadores para iniciar sessão Azure AD com base serviços com palavra-passe no local e, enquanto na rede da empresa, sem ser necessário introduzir novamente a palavra-passe. A opção de federação com o AD FS permite-lhe implementar uma nova ou especificar um AD FS existente do Windows Server 2012 R2 farm.

Este tópico se a base para obter informações sobre Federação relacionadas com funcionalidades para ligações de Azure AD Connect e listas para todos os outros tópicos relacionados com a mesma. Para estabelecer ligações a ligação do Azure AD, consulte o artigo integrar a suas identidades no local com o Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Ligação do Azure AD - tópicos de Federação

| Tópico | O que abrange e quando a leitura |
|:------|:-----------|
| **Azure AD Connect início de sessão no opções de utilizador** ||
| [Noções sobre opções de início de sessão de utilizador](active-directory-aadconnect-user-signin.md) | Descrição dos vários início de sessão no opções de utilizador e como afetam a experiência de utilizador de início de sessão no Azure |
| **Instalação do AD FS utilizando a ligação do Azure AD**||
| [Pré-requisitos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Pré-requisitos para uma instalação de AD FS com êxito através da ligação do Azure AD|
| [Configurar o AD FS farm](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Como instalar uma nova farm do AD FS utilizando a ligação do Azure AD |
| **Modificar a configuração do AD FS** | |
| [Reparar a fidedignidade](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Como reparar atual fidedignidade entre no local AD FS e no Office 365 / Azure |
| [Adicionar um novo servidor do AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Expandir o farm do AD FS com o AD FS servidor mensagem inicial instalação adicionais |
| [Adicionar um novo servidor do AD FS WAP](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Expandir o farm do AD FS com WAP servidor mensagem inicial instalação adicionais |
| [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Adicionar outro domínio Federado com Azure AD |
|**Registar tarefas de instalação**||
| [Adicionar o logótipo da empresa personalizado / ilustração](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modificar a experiência de início de sessão no especificando o logótipo personalizado que será apresentado na página de início de sessão do AD FS |
| [Adicionar descrição de início de sessão](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Alterar a descrição de início de sessão na página de início de sessão do AD FS | 
| [Modificar o AD FS reclamar regras](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modificar / adicionar regras de afirmação no AD FS correspondente à configuração da sincronização de ligação do Azure AD |


## <a name="additional-resources"></a>Recursos adicionais

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
* [Implementação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Implementação de FS elevada disponibilidade AD cruz geográficos no Azure com o Gestor de tráfego Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)



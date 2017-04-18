<properties
    pageTitle="Configure o seu dispositivo Windows 10 com o Azure AD a partir das definições | Microsoft Azure"
    description="Explica como podem aderir utilizadores para Azure AD através do menu definições."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configure o seu dispositivo Windows 10 com o Azure AD a partir das definições
Se já estiver a utilizar o Windows 7 ou Windows 8 e tem foi atualizado o seu computador ou dispositivo para o Windows 10, pode participar Azure Active Directory (Azure AD) através do menu definições.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Para associar ao Azure AD a partir do menu Definições


1. A partir do menu **Iniciar** , prima o atalho **Definições** .
2. Em **Definições**, selecione **sistema**->**sobre**->**participar Azure AD**.
<center>
![Participar Azure AD a partir do menu definições](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Clique em **continuar** na janela de mensagem Azure AD participar.
<center>
![Janela de mensagem de associação Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Forneça as credenciais de início de sessão. Esta experiência de início de sessão no irá incluir todos os passos que são necessários para a autenticação concluída. Se que fazem parte de um inquilino federado, o administrador irá fornecer-lhe com a experiência de Federação está alojada pela sua organização.
<center>
![Fornecer as credenciais de início de sessão no](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Se a sua organização tiver configurado o Azure a autenticação Multifator para participar para Azure AD, fornece o segundo factor antes de continuar.
6. No ecrã de **Permitir que este dispositivo ser gerido** , clique em **Aceitar** .
7. Deverá ver a mensagem "o seu dispositivo agora está ligado à sua organização no Azure AD".


## <a name="additional-information"></a>Informações adicionais
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticação identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)

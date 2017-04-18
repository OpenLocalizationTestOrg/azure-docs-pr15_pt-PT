

<properties
    pageTitle="Aderir a um dispositivo pessoal para a sua organização | Microsoft Azure"
    description="Explica como os utilizadores podem registar os seus dispositivos Windows 10 pessoais à sua rede da empresa e fornece os passos de implementação de um cenário BYOD."
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

# <a name="join-a-personal-device-to-your-organization"></a>Aderir a um dispositivo pessoal para a sua organização

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Para aderir a um dispositivo Windows 10 para a sua organização

1.  No menu **Iniciar** , selecione **Definições**.
2.  Selecione **contas**e, em seguida, clique na **sua conta**.
3.  Clique em **Adicionar conta profissional ou escolar**e, em seguida, escreva na sua conta institucional.
4.  Na página Iniciar sessão para a sua organização, introduza o seu nome de utilizador e palavra-passe e, em seguida, clique em **OK**.
5.  Será pedido para um desafio autenticação multifator. (Este desafio é configurável por um administrador de TI).
6.  Azure Active Directory (Azure AD) verifica se o dispositivo requer a inscrição de gestão do dispositivo móvel.
7.  O Windows regista o dispositivo no diretório da organização no Azure AD e inscreve-lo na gestão de dispositivos móveis, se apropriado.
8.  Se for um utilizador gerido, o Windows leva-o no ambiente de trabalho através da automática iniciar sessão.
9.  Se for um utilizador federado, será redirecionado para um ecrã de início de sessão no Windows para introduzir as suas credenciais.

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)

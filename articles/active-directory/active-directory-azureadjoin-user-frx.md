<properties
    pageTitle="Configurar um novo dispositivo com o Azure AD durante a configuração | Microsoft Azure"
    description="Um tópico que explica como os utilizadores podem configurar participar do Azure AD durante a sua primeira experiência executar."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Configurar um novo dispositivo com o Azure AD durante a configuração

No Windows 10, podem aderir utilizadores os seus dispositivos para o Azure Active Directory (Azure AD) na primeira execução experiência (FRX). Isto permite que as organizações distribuir dispositivos retráctil aos seus empregados ou estudantes ou informá-los de escolher os seus próprios dispositivos (CYOD).
Se o Windows 10 Professional ou o Windows 10 Enterprise edições estiver instalado num dispositivo, a experiência assume a predefinição o processo de configuração de dispositivos que sejam propriedade da empresa.

## <a name="to-join-a-device-to-azure-ad"></a>Para associar um dispositivo ao Azure AD


1. Quando ativar o seu novo dispositivo e iniciar o processo de configuração, deverá ver a mensagem **Preparar-se** . Siga os avisos para configurar o seu dispositivo.
2. Comece por personalização do seu região e idioma. Em seguida, aceite os termos de licenciamento de Software Microsoft.
![Personalizar para a sua região](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Selecione a rede que pretende utilizar para ligar à Internet.
4. Selecione se estiver a utilizar um dispositivo pessoal ou um dispositivo de propriedade da empresa. Se for proprietário de empresa, clique neste **dispositivo pertence à minha organização**. Esta ação inicia a experiência do Azure AD participar. Segue-se um ecrã que irá ver se está a utilizar o Windows 10 Professional.
<center>
![Quem é o proprietário deste ecrã do PC](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Introduza as credenciais que foram fornecidas pela sua organização.
<center>
![Início de sessão no ecrã](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Depois de ter introduzido o seu nome de utilizador, um inquilino correspondente está localizado no Azure AD. Se estiver num domínio federado, serão redirecionados para o seu servidor de seguro Token serviço (STS) no local – por exemplo, Active Directory Federation Services (AD FS).
7. Se for um utilizador num domínio não federado, introduza as suas credenciais diretamente na página à alojado de AD do Azure. Se a marca da empresa foi configurada, também irá ver o logótipo da sua organização e suporta texto.
8.  Lhe for pedido para um desafio autenticação multifator. Este desafio é configurável por um administrador de TI.
9.  Azure AD verifica se este utilizador/dispositivo requer inscrição de gestão de dispositivos móveis.
10. O Windows regista o dispositivo no diretório da organização no Azure AD e inscreve-lo na gestão de dispositivos móveis, se apropriado.
11. Se for um utilizador gerido, Windows leva-o no ambiente de trabalho durante o processo de início de sessão automático.
12. Se for um utilizador federado, for instruído para o ecrã de início de sessão no Windows para introduzir as suas credenciais.

> [AZURE.NOTE] Aderir a um domínio do Active Directory do Windows Server no local na experiência de fora da caixa do Windows não é suportada. Por isso, se planeia aderir a um computador para um domínio, deverá de selecione a ligação **configurar o Windows com uma conta local** como alternativa. Em seguida, pode participar o domínio a partir das definições no seu computador à medida que efetuou antes.

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)

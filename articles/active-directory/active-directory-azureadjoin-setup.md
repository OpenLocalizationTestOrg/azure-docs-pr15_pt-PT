<properties
    pageTitle="Configurar o Azure AD participar para os seus utilizadores | Microsoft Azure"
    description="Explica como os administradores podem configurar participar do Azure AD de diretório no local e registo do dispositivo."
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>Configurar o Azure AD participar na sua organização

Antes de configurar o Azure Active Directory participar (Azure AD participar), é necessário sincronizar o seu diretório no local de utilizadores na nuvem ou criar manualmente contas geridas no Azure AD.

Instruções detalhadas sobre como sincronizar os seus utilizadores no local para Azure AD é abrangido no [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).


Para criar e gerir utilizadores no Azure AD manualmente, consulte [Gestão de utilizadores no Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurar o registo de dispositivo
1. Inicie sessão no portal do Azure como administrador.
2. No painel esquerdo, selecione **Do Active Directory**.
3. No separador do **diretório** , selecione o seu diretório.
4. Selecione o separador de **Configurar** .
5. Aceda à secção **dispositivos** .
6. No separador **dispositivos** , defina o seguinte:  
   * **Máximo número de dispositivos por utilizador**: selecione o número máximo de dispositivos que um utilizador pode ter Azure AD.  Se um utilizador atingir este quota, estes não conseguir adicionar dispositivos adicionais até um ou mais dos seus dispositivos existentes são removidos.
   * **Exigir AUTH de MULTIFATOR para dispositivos associação**: definir se os utilizadores terão para fornecer um segundo fator de autenticação para associar o seu dispositivo ao Azure AD. Para mais informações sobre a autenticação Multifator de Azure, consulte o artigo [Introdução ao Azure a autenticação Multifator na nuvem](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **Os utilizadores poderão dispositivos de associação do AZURE AD**: selecione os utilizadores e grupos que são permitidos para associar dispositivos ao Azure AD.
   * **Adicionais administradores ON AZURE AD associadas dispositivos**: com Azure AD Premium ou o conjunto de aplicações de mobilidade Enterprise (EMS), pode escolher quais os utilizadores que são concedidos direitos de administrador local para o dispositivo. Os administradores globais e proprietários de dispositivo são concedidos direitos de administrador local por predefinição.

<center>![Configurar o dispositivo regisration](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Depois de configurar participar do Azure AD para os seus utilizadores, eles podem ligar ao Azure AD através dos seus dispositivos de pessoais ou empresariais.

Seguem-se a três cenários que pode utilizar para permitir que os utilizadores configurar o Azure AD participar:

- Um dispositivo pertencentes à empresa aderir utilizadores diretamente para o Azure AD.
- Os utilizadores domínio-associação um dispositivo pertencentes à empresa ao Active Directory no local e, em seguida, expandir o dispositivo para Azure AD.
- Os utilizadores adicionam contas escolar ou profissional ao Windows num dispositivo pessoal

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)

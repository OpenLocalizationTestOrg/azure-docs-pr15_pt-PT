<properties
    pageTitle="Descrição geral do registo de dispositivo do Active Directory Azure | Microsoft Azure"
    description="é foundation para cenários de acesso condicional com base no dispositivo. Quando um dispositivo é registado, o Azure Active Directory dispositivo registo Aprovisiona o dispositivo de com uma identidade utilizada para autenticar o dispositivo, quando o utilizador inicia sessão."
    services="active-directory"
    keywords="registo de dispositivo, ativar dispositivo registo, o registo de dispositivo e MDM"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="Markvi"/>

# <a name="get-started-with-azure-active-directory-device-registration"></a>Introdução ao Azure Active Directory dispositivo registo

Azure Active Directory dispositivo registo é foundation para cenários de acesso condicional com base no dispositivo. Quando um dispositivo está registado, o Azure Active Directory dispositivo registo fornece o dispositivo de com uma identidade utilizada para autenticar o dispositivo, quando o utilizador inicia sessão. O dispositivo autenticado e os atributos do dispositivo, em seguida, podem ser utilizados para impor políticas de acesso condicional para as aplicações que são alojadas na nuvem e no local.

Quando o combinado com uma solução de management(MDM) do dispositivo móvel como o Microsoft Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Esta opção permite-lhe criar regras de acesso condicional impõem access a partir de dispositivos para cumprir os padrões de segurança e conformidade. Para mais informações sobre a inscrição de dispositivos no Microsoft Intune, consulte o artigo [Inscrever dispositivos para a gestão de Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Cenários ativados por Azure Active Directory dispositivo registo

Azure Active Directory dispositivo registo inclui suporte para iOS, Android e Windows dispositivos. Os cenários individuais que utilizam o registo do Azure AD dispositivo podem ter mais específicas requisitos e a plataforma de suporte. Estes cenários são os seguintes:

- **Condicional aceder a aplicações que são alojado no local**: pode utilizar dispositivos registados com as políticas de acesso para aplicações que estão configuradas para utilizar o AD FS com o Windows Server 2012 R2. Para mais informações sobre como configurar o acesso condicional para no local, consulte o artigo [configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo](active-directory-conditional-access-on-premises-setup.md).

- **Acesso condicional para aplicações do Office 365 com o Microsoft Intune** : administradores IT podem aprovisionar o políticas de dispositivo de acesso condicional para proteger os recursos da empresa, enquanto ao mesmo tempo permitindo que os técnicos de informação no compatível com dispositivos para aceder os serviços. Para mais informações, consulte o artigo [Condicional políticas de dispositivo de acesso para serviços do Office 365](active-directory-conditional-access-device-policies.md).

##<a name="setting-up-azure-active-directory-device-registration"></a>Configurar o Azure Active Directory dispositivo registo

Tem de ativar registo Azure AD dispositivo no Portal do Azure modo a que dispositivos móveis podem detetar o serviço procurando pelo famoso registos DNS. Tem de configurar sua empresa DNS para que dispositivos Windows 10, Windows 8.1, Windows 7, Android e iOS podem descobrir e utilizar o serviço.
Pode ver e ativar/desativar registados dispositivos utilizando o Portal de administrador no Azure Active Directory.

>[AZURE.NOTE]
 Para mais recentes instruções sobre como configurar o registo de dispositivo automático Consulte, [como configurar o registo automático de domínio do Windows associadas dispositivos com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

### <a name="enable-azure-active-directory-device-registration-service"></a>Ativar o serviço de registo de dispositivo do Azure Active Directory

1. Inicie sessão no portal do Microsoft Azure como administrador.
2. No painel esquerdo, selecione **Do Active Directory**.
3. No separador do **diretório** , selecione o seu diretório.
4. Selecione o separador de **Configurar** .
5. Desloque-se para a secção denominada **dispositivos**.
6. Selecione **todas as** para **os utilizadores poderão dispositivos de associação do local de trabalho**.
7. Selecione o número máximo de dispositivos que pretende autorizar por utilizador.

>[AZURE.NOTE]
>Inscrição com Microsoft Intune ou gestão de dispositivos móveis do Office 365 necessita de participar local de trabalho. Se tiver configurado um destes serviços, todos está selecionado e o botão NONE está desativado.

Por predefinição, a autenticação de dois fatores não estiver ativada para o serviço. No entanto, autenticação de dois fatores é recomendada quando registar um dispositivo.

- Antes de ser necessária a autenticação de dois fatores para este serviço, deve configurar um fornecedor de autenticação de dois fatores no Azure Active Directory e configurar as suas contas de utilizador para autenticação Multifator, consulte o artigo [Adicionar autenticação Multifator para o Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Se estiver a utilizar o AD FS com o Windows Server 2012 R2, tem de configurar um módulo de autenticação de dois fatores no AD FS, consulte o artigo [Utilizando a autenticação Multifator com os serviços de Federação do Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurar a identificação de registo de dispositivo do Azure Active Directory
Dispositivos Windows 7 e Windows 8.1 irão descobrir o serviço de registo de dispositivo, combinando o nome de conta de utilizador com um nome de servidor de registo de dispositivo conhecido.

Tem de criar um registo CNAME de DNS que aponta para o registo associado ao seu serviço de registo de dispositivo do Azure Active Directory. O registo CNAME tem de utilizar o enterpriseregistration prefixo famoso seguido sufixo UPN utilizado pelas contas de utilizador na sua organização. Se a sua organização utiliza vários sufixos UPN, vários registos CNAME têm de ser criados no DNS.

Por exemplo, se utilizar dois sufixos UPN na sua organização com o nome @contoso.com e @region.contoso.com, irá criar os seguintes registos DNS.

| Entrada                                     | Tipo  | Endereço                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | CNAME | enterpriseregistration.Windows.NET |
| enterpriseregistration.Region.contoso.com | CNAME | enterpriseregistration.Windows.NET |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Ver e gerir objectos de dispositivo no Azure Active Directory
1. A partir do portal do administrador do Azure, pode ver, bloquear e desbloquear dispositivos. Um dispositivo que está bloqueado já não terá acesso às aplicações que estão configuradas para permitir apenas registados dispositivos.
2. Inicie sessão no Portal do Microsoft Azure como administrador.
3. No painel esquerdo, selecione **Do Active Directory**.
4. Selecione o seu diretório.
5. Selecione o separador de **utilizadores** . Em seguida, selecione um utilizador para ver os seus dispositivos
6. Selecione o separador **dispositivos** .
7. Selecione **Registered dispositivos** a partir do menu pendente.
8. Aqui pode ver, bloquear ou desbloquear os dispositivos registados utilizadores.

## <a name="additional-topics"></a>Tópicos adicionais

Pode registar os seus dispositivos Windows 7 e Windows 8.1 domínio associadas com o registo do Azure AD dispositivo. Os tópicos seguintes fornece mais informações sobre as pré-requisitos e os passos necessários para configurar o registo de dispositivo no Windows 7 e Windows 8.1 dispositivos.

- [Registo de dispositivo automático com o Azure Active Directory para Windows façam parte de um domínio dispositivos](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar registo de dispositivo automático para dispositivos de domínio associado do Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurar registo de dispositivo automático para dispositivos de domínio associado Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registo de dispositivo automático com dispositivos façam parte de um domínio do Azure Active Directory para o Windows 10](active-directory-azureadjoin-devices-group-policy.md)

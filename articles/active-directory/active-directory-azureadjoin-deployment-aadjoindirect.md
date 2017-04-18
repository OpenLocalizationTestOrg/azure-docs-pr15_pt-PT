<properties
    pageTitle="Cenários de utilização e considerações de implementação do Azure AD participar | Microsoft Azure"
    description="Explica como os administradores podem configurar participar do Azure AD para os seus utilizadores finais (empregados, os estudantes, outros utilizadores). Também fala sobre os diferentes cenários reais para utilizar o Azure AD participar."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< etiquetas ms.service= "active directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/27/2016"

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Cenários de utilização e considerações de implementação para participar do Azure AD

## <a name="usage-scenarios-for-azure-ad-join"></a>Cenários de utilização para participar do Azure AD
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Cenário 1: Empresas em grande medida na nuvem

Azure Active Directory participar (Azure AD participar) podem ajudá-lo se atualmente operar e gestão de identidades para a sua empresa na nuvem ou está a mover para a nuvem mais rapidamente. Pode utilizar uma conta que tenha criado no Azure AD para iniciar sessão no Windows 10. Durante [o processo de experiência executar (FRX) primeiro](active-directory-azureadjoin-user-frx.md)ou ao associar Azure AD a partir do [menu Definições](active-directory-azureadjoin-user-upgrade.md), os utilizadores podem aderir respectivas máquinas para Azure AD.  Os utilizadores também podem desfrutar de início de sessão (SSO) acesso único para recursos de nuvem, como o Office 365, os respetivos browsers ou em aplicações do Office.

### <a name="scenario-2-educational-institutions"></a>Cenário 2: Estabelecimentos de ensino

Estabelecimentos de ensino normalmente têm dois tipos de utilizador: corpo docente e estudantes. Membros de corpo docente são considerados como a longo prazo membros da organização. Criar contas no local para os mesmos é conveniente. Mas estudantes são shorter-term membros da organização e as respetivas contas podem ser geridas no Azure AD. Isto significa que pode ser seguia escala de diretório para a nuvem em vez de a ser armazenado no local. Também significa que os estudantes poderão iniciar sessão no Windows com as respetivas contas do Azure AD e obter acesso aos recursos do Office 365 nas aplicações do Office.

### <a name="scenario-3-retail-businesses"></a>Cenário 3: Empresas de revenda

Empresas de revenda têm sazonal e empregados a longo prazo. Normalmente, criar contas no local e utilizar o domínio máquinas para empregados a tempo inteiro longo prazo. Mas sazonal é shorter-term membros da organização e é conveniente para gerir as respetivas contas onde licenças de utilizador podem ser mais facilmente mudadas. Quando cria as respetivas contas de utilizador na nuvem com licenças do Office 365, estes utilizadores obtém os benefícios de iniciar sessão no Windows e o Office aplicações com uma conta do Azure AD, enquanto que manter mais flexibilidade com as suas licenças após deixarem.

### <a name="scenario-4-additional-scenarios"></a>Cenário 4: Cenários adicionais

Juntamente com os benefícios debatidos anteriormente, beneficiem das vantagens dos ter os seus utilizadores associar os respetivos dispositivos ao Azure AD devido a uma experiência de unir simplificada, gestão de dispositivos eficientes, a inscrição de gestão do dispositivo móvel automática e serviço single sign-on para Azure AD e no local recursos.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Considerações de implementação para participar do Azure AD

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permitir que os utilizadores aderir a um dispositivo pertencentes à empresa diretamente para o Azure AD


Empresas podem fornecer contas apenas na nuvem para empresas de parceiro e organizações. Estes parceiros, em seguida, podem aceder facilmente aplicações da empresa e recursos com o início de sessão único. Este cenário é aplicável aos utilizadores que acesso aos recursos principalmente na nuvem, como as aplicações do Office 365 ou SaaS que dependem do Azure AD para autenticação.

### <a name="prerequisites"></a>Pré-requisitos
**Ao nível da empresa (administrador)**

*   Azure subscrição com o Azure Active Directory  

**Ao nível de utilizador**

*   Windows 10 (edições Professional e Enterprise)

### <a name="administrator-tasks"></a>Tarefas de administrador
* [Configurar o registo de dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tarefas do utilizador
* [Configurar um novo dispositivo Windows 10 com o Azure AD durante a configuração](active-directory-azureadjoin-user-frx.md)
* [Configure o seu dispositivo Windows 10 com o Azure AD a partir do menu Definições](active-directory-azureadjoin-user-upgrade.md)
* [Aderir a um dispositivo pessoal do Windows 10 para a sua organização](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Ativar BYOD na sua organização para o Windows 10
Pode configurar os seus utilizadores e colaboradores para utilizar os seus dispositivos com Windows pessoais (BYOD) para aceder à empresa aplicações e recursos. Os utilizadores podem adicionar as respetivas contas do Azure AD (contas escolar ou profissional) para um dispositivo pessoal do Windows para aceder aos recursos, de forma segura e em conformidade.

### <a name="prerequisites"></a>Pré-requisitos
**Ao nível da empresa (administrador)**

*   Subscrição do Azure AD

**Ao nível de utilizador**

*   Windows 10 (edições Professional e Enterprise)


### <a name="administrator-tasks"></a>Tarefas de administrador

* [Configurar o registo de dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tarefas do utilizador
* [Aderir a um dispositivo pessoal do Windows 10 para a sua organização](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)

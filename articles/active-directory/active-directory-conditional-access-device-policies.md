<properties
    pageTitle="Políticas de dispositivo de acesso condicional para serviços do Office 365 | Microsoft Azure"
    description="Para obter detalhes sobre como dispositivo com base em condições controlam o acesso aos serviços do Office 365. Enquanto os técnicos de informação (IWs) pretende serviços de aceder ao Office 365 como o Exchange e SharePoint Online no trabalho ou na escola a partir dos respetivos dispositivos pessoais, o seu administrador de TI quer o acesso a ser secure.IT administradores podem aprovisionar o políticas de dispositivo de acesso condicional para proteger os recursos da empresa, enquanto ao mesmo tempo permitindo IWs em compatível com dispositivos para aceder os serviços."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Políticas de dispositivo de acesso condicional para serviços do Office 365

O termo "condicional acesso" tem muitas condições associadas como o utilizador autenticado multifator, autenticado dispositivo, dispositivo compatível com etc. Este tópico focusses principalmente no dispositivo com base em condições para controlar o acesso aos serviços do Office 365. Enquanto os técnicos de informação (IWs) pretende serviços de aceder ao Office 365 como o Exchange e SharePoint Online no trabalho ou na escola a partir dos respetivos dispositivos pessoais, o seu administrador de TI quer o acesso a ser segura. Administradores IT podem aprovisionar o políticas de dispositivo de acesso condicional para proteger os recursos da empresa, enquanto ao mesmo tempo permitindo IWs em compatível com dispositivos para aceder os serviços. Políticas de acesso condicional para o Office 365 podem ser configuradas a partir do portal do Microsoft Intune acesso condicional.

Azure Active Directory impõe acesso condicional as políticas para proteger o acesso a serviços do Office 365. Um administrador inquilino pode criar uma política de acesso condicional bloqueia um utilizador num dispositivo não compatível com a partir de aceder a um serviço do Office 365. O utilizador deve estar em conformidade com as políticas de dispositivos da empresa antes de pode ser concedido acesso ao serviço. Em alternativa, o administrador também pode criar uma política que requer que os utilizadores apenas inscrevam os respetivos dispositivos para obter acesso a um serviço do Office 365. Políticas de podem ser aplicadas a todos os utilizadores de uma organização, ou limitadas a alguns grupos de destino e melhoradas ao longo do tempo para incluir grupos-alvo adicionais.

É um pré-requisito para impor políticas de dispositivos para que os utilizadores registar os seus dispositivos com o serviço de registo de dispositivo do Azure Active Directory. Pode optar por ativar a autenticação multifator (MFA) para o registo de dispositivos com o serviço de registo de dispositivo do Azure Active Directory. Recomenda-se para o serviço do Azure Active Directory dispositivo registo MFA. Quando MFA está ativado, os utilizadores registar os seus dispositivos com o serviço de Azure Active Directory dispositivo registo são pedidos para a segunda autenticação fator.

##<a name="how-does-conditional-access-policy-work"></a>Como funciona a política de acesso condicional?

Quando um utilizador solicita acesso ao serviço do Office 365 a partir de uma plataforma do dispositivo suportado, Azure Active Directory autentica o utilizador e o dispositivo a partir do qual o utilizador inicia o pedido; e concede acesso ao serviço apenas quando o utilizador está em conformidade com a política definida para o serviço. Utilizadores que não possui o seu dispositivo registado são fornecidos instruções reparação sobre como inscrever-se e tornam-se em conformidade para aceder aos serviços empresariais do Office 365. Utilizadores em dispositivos iOS e Android serão exigidos a que inscrevam os respetivos dispositivos utilizando a aplicação Portal da empresa. Quando um utilizador inscreve o seu dispositivo, o dispositivo está registado com o Azure Active Directory e registado para gestão de dispositivos e conformidade. Os clientes tem de utilizar o serviço de Azure Active Directory dispositivo registo em conjunto com o Microsoft Intune para ativar a gestão de dispositivos móveis do serviço do Office 365. Inscrição do dispositivo é uma pré-necessários para os utilizadores dos serviços de aceder ao Office 365 quando são impostas políticas de dispositivos.

Quando um utilizador inscreve o seu dispositivo com êxito, o dispositivo fica fidedigno. Azure Active Directory fornece Single-Sign-On para aceder a aplicações da empresa e impõe a política de acesso condicional para conceder acesso a uma hora de serviço não apenas o primeiro o utilizador pedir acesso, mas sempre que o utilizador pedir para renovar o access. O utilizador será negado o acesso a serviços quando as credenciais de início de sessão no são alteradas, dispositivo é perdida/roubado ou a política não for cumprida no momento da pedido para renovação.

## <a name="deployment-considerations"></a>Considerações de implementação:
Tem de utilizar o serviço do Azure Active Directory dispositivo registo para o registo de dispositivo.

Quando os utilizadores autenticados no local, Active Directory Federation Services (AD FS) (1.0 e acima) é necessário. Autenticação Multifator para participar no local de trabalho irá falhar quando o fornecedor de identidades não for capaz de autenticação multifator. Por exemplo, o AD FS 2.0 não é multifator capazes de autenticação. Administrador de inquilinos do tem de garantir que no local o AD FS é MFA capaz e um método MFA válido está ativado, antes de ativar MFA serviço de registo de dispositivo do Azure Active Directory. Por exemplo, o AD FS no Windows Server 2012 R2 tem capacidades MFA. Também tem de ativar um método de autenticação válido adicionais (MFA) no servidor ADFS antes de ativar MFA serviço de registo de dispositivo do Azure Active Directory. Para mais informações sobre métodos MFA suportados no AD FS, consulte o artigo configurar métodos de autenticação adicionais para o AD FS.

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

P: qual é a política de exclusão predefinida para as plataformas de dispositivo não suportadas?

R: neste momento, as políticas de acesso condicional seletivamente são impostas utilizadores em dispositivos iOS e Android. Aplicações noutras plataformas de dispositivo são, por predefinição, afetada pela política de acesso condicional para dispositivos iOS e Android. No entanto, poderá, selecione administrador de inquilinos do substituir a política global para não permitir o acesso aos utilizadores plataformas não suportadas.
É no guia para alargar a política de acesso condicional aos utilizadores outras plataformas, incluindo as janelas.

P: quando será política de acesso condicional para serviços do Office 365 alargada às aplicações baseado no Browser (por exemplo, OWA, SharePoint baseada no browser).

R: neste momento, acesso condicional aos serviços do Office 365 está limitado a aplicações completas no dispositivo. É no guia para alargar a política de acesso condicional aos utilizadores aceder a serviços da browsers.

<properties
    pageTitle="Utilizar dispositivos Windows 10 no seu local de trabalho | Microsoft Azure"
    description="Fornece um instantâneo das capacidades para os utilizadores e IT, contraste as diferentes maneiras de um dispositivo pode ser aprovisionado e utilizado numa empresa com o Windows 10."
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
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>Utilizar o Windows 10 dispositivos no seu local de trabalho

Aplica-se ao: PCs para o Windows 10

Windows 10 oferece três modelos para as organizações que permitem aos utilizadores aceder a recursos de trabalho de uma forma segura e conveniente.

- **Aderir a Azure Active Directory** (Azure AD associação), para os trabalhadores que acesso aos recursos, tais como o Office 365 principalmente na nuvem. Azure AD associação é trabalho personalizado aprovisionamento experiência que há de nova no Windows 10.
- **Associação do domínio**, para as organizações que tenham investimentos em aplicações no local e recursos. Associação de domínio oferece uma experiência melhorada no Windows 10 quando estiver ligado a Azure AD.
- **Uma nova experiência BYOD simplificada**, para os utilizadores que pretende adicionar uma conta profissional ou escolar ao Windows facilmente aceder e recursos em dispositivos pessoais.

A tabela seguinte apresenta um instantâneo das capacidades de utilizadores e administradores de TI, contraste as diferentes maneiras de um dispositivo pode ser aprovisionado e utilizado numa empresa com o Windows 10:

|                                                                                                                                                                 | Associação de domínio     | Associação do Azure AD | Dispositivo pessoal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows dispositivo iniciar sessão para contas escolar ou profissional.                                                                                                                      | Sim             | Sim           | N              |
| Utilizador sessão único (SSO) para aplicações do Office 365 e Azure AD. SSO é a capacidade de início de sessão apenas uma vez aceder a recursos organizacionais. | Sim             | Sim           | Sim             |
| Utilizador SSO às aplicações Kerberos/NTLM.                                                                                                                                  | Sim             | Limitado       | Sim, através de VPN         |
| Autorização forte e conveniente sessão para contas escolar ou profissional com o Microsoft Passport e Olá do Windows.                                                                   | Sim             | Sim           | Sim             |
| Acesso à empresa da loja Windows, com uma conta escolar ou profissional (não uma conta Microsoft).                                                                                    | Sim             | Sim           | Sim             |
| Compatível com Enterprise roaming das definições do utilizador em todos os dispositivos com contas escolar ou profissional.                                                                                 | Sim             | Sim           | Sim             |
| A capacidade de restringir o acesso às aplicações organizacionais para dispositivos que são compatíveis com as políticas de dispositivos organizacional.                                                      | Sim             | Sim           | Sim             |
| Utilizador reposição personalizada de aprovisionamento de dispositivos para "trabalho a partir de qualquer lugar."                                                                                                | N              | Sim           | Sim             |
| Capacidade para gerir dispositivos.                                                                                                                                       | Sim, através de GP/SCCM | Sim           | Sim             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Aderir a utilizar dispositivos de propriedade de trabalho com Azure AD associação e o domínio no Windows 10

Windows 10 oferece duas formas para que sejam propriedade trabalho dispositivos aceder a recursos de trabalho:

- Associação do Azure AD
- Associação de domínio

 Ambos podem ser opções válidas, dependendo das suas necessidades e requisitos de uma organização. Em alguns casos, podem beneficiar organizações com a ativação ambos os métodos de implementação.

## <a name="when-to-use-azure-active-directory-join"></a>Quando utilizar participar Azure Active Directory

Azure AD associação é uma novo trabalho personalizada aprovisionamento experiência no Windows 10.  -Está atribuído a trabalhadores que acesso aos recursos de trabalho, tais como o Office 365 principalmente na nuvem. É uma forma lightweight para configurar computadores, tablets e telemóveis para a empresa. Dispositivos são geridos através de gestão de dispositivos móveis, utilizando controlos consistentes ao longo de plataformas do Windows.

**Utilização do Azure AD participar para qualquer um dos seguintes motivos**:

- Pretende ativar a gestão personalizada de aprovisionamento de dispositivos para os trabalhadores esteja onde estiver.
- Fornecer aos utilizadores com dispositivos móveis propriedade de trabalho como tablets e telemóveis.
- Pretende gerir um conjunto de utilizadores no Azure AD em vez de no Active Directory, como os trabalhadores sazonais, contratantes ou estudantes.
- Pretende fornecem capacidades de junção para os trabalhadores das filiais remote com infraestrutura limitadas no local.
- Não possui um Active Directory no local.

Algumas organizações irão utilizar Azure AD participar como a forma principal para implementar o proprietário de trabalho dispositivos, especialmente à medida que eles migrarem a maior parte ou a totalidade dos seus recursos para a nuvem. Híbrido as organizações com o Active Directory e Azure AD também podem optar por implementar um método ou noutra consoante o utilizador ou departamento.

Distritos de escola e universidades, por exemplo, poderão gerir docentes no Active Directory e estudantes Azure AD. Algumas empresas poderão pretender gerir escritórios remotos ou os departamentos de vendas no Azure AD. Azure AD associação e métodos de associação de domínio, podem ser utilizados dentro de uma organização híbrido. Associação do Azure AD pode ser complementar ótima para associação de domínio para implementar dispositivos num ambiente de trabalho.

**Se for mais usual acesso aos recursos de empresa através na nuvem, a sua organização poderá desfrutar benefícios adicionais se**:

- Pode remover dependências de infraestrutura de identidade no local.
- Pode simplificar o modelo de implementação de dispositivos, introdução afastando-o de soluções de processamento de imagens ao permitir a configuração de gestão personalizada.
- Pode utilizar a gestão de dispositivos móveis para gerir todos os seus dispositivos em diferentes plataformas.

Para mais informações sobre Azure AD participar, consulte o artigo [funcionalidades de nuvem de expandir a dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Quando utilizar a associação de domínio (ou manter utilizá-lo)

Para o último 15 anos, muitas organizações tenham utilizado associação de domínio para ligar dispositivos de trabalho. -Permite aos utilizadores iniciar sessão para os seus dispositivos com o trabalho do Active Directory ou escola contas. Associação de domínio também lhe permite IT para centralmente e totalmente gerir nestes dispositivos. Organizações normalmente dependem imagens métodos para aprovisionar dispositivos e utilizam frequentemente System Centro de configuração do Gestor (SCCM) ou política de grupo (GP) para geri-los.

A **sua empresa deve utilizar a associação de domínio (ou manter utilizá-lo) para qualquer uma das seguintes razões**:

- Tiver aplicações Win32 implementadas nestes dispositivos que utilizam NTLM/Kerberos.
- Exigir GP ou SCCM/DCM gerir dispositivos.
- Pretende continuar a utilizar imagens soluções para configurar os dispositivos para os seus funcionários.

A **associação de domínio no Windows 10 também fornece-lhe as seguintes vantagens quando estiver ligado a Azure AD**:

- Autenticação do dispositivo vinculadas forte e conveniente sessão para contas escolar ou profissional com o Microsoft Passport e Olá do Windows.
- Acesso à empresa da loja Windows para dispositivos que utilizem o trabalho ou escola contas (sem conta Microsoft necessária).
- Compatível com Enterprise roaming das definições do utilizador em todos os dispositivos que utilizam contas escolar ou profissional (sem conta Microsoft necessária).
- A capacidade de restringir o acesso às aplicações organizacionais para dispositivos que são compatíveis com as políticas de dispositivos organizacional.

Para mais informações sobre Azure AD participar, consulte o artigo [funcionalidades de nuvem de expandir a dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Ativar a participar dos dispositivos pessoalmente qual é o proprietário para o trabalho ou escola

Para suportar BYOD na empresa, o Windows 10 dá o utilizador a capacidade de adicionar uma conta escolar ou profissional ao seu computador, tablet ou telemóvel. Depois do utilizador adiciona uma conta escolar ou profissional, o dispositivo está registado com a Azure AD e, opcionalmente, inscrito no sistema de gestão de dispositivos móveis que tenha configurado a organização. Diretório mostrará nestes dispositivos como 'Registado' vs. 'Azure AD associadas'. IT administraors pode aplicar diferentes políticas com base nesta informação, fornecendo um toque mais claro em pessoalmente qual é o proprietário dispositivos que em dispositivos que sejam propriedade trabalho se pretender.

Os utilizadores podem adicione um escolar ou profissional conta para o seu dispositivo pessoal muito convenientemente. Ele pode fazer isto quando acede a uma aplicação de trabalho pela primeira vez ou podem fazê-lo manualmente através do menu definições. Esta conta irá fornecer SSO para recursos organizacionais.

Para mais informações sobre Azure AD participar, consulte o artigo [ligar dispositivos de domínio para Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Ativar a associação de domínio ou uma associação do Azure AD

Todos os métodos de descrito anteriormente (associação de domínio, Azure AD participar e adicionar trabalhar ou conta escolar) tem pontos de entrada na experiência de utilizador do Windows 10. No entanto, todas as necessitam de um administrador de TI ativar a funcionalidade de infraestrutura do antes da experiência irá funcionar.

## <a name="requirements-for-deploying-azure-ad-join"></a>Requisitos para implementação participar do Azure AD

Para implementar o Azure AD participar para qualquer conjunto de utilizadores precisa do seguinte:

- Uma subscrição do Azure AD.
- Uma subscrição do Azure AD Premium, como o dispositivo móvel gestão inscrição automática, se necessitar de mais funcionalidades.
- Gestão de dispositivos móveis – por exemplo, uma subscrição do Microsoft Intune, gestão de dispositivos móveis para o Office 365 ou qualquer um dos fornecedores de gestão de dispositivos móveis parceiro que se integram no Azure AD. (Consulte a [secção FAQ](#frequently-asked-questions) junto ao fim deste artigo para obter mais informações).

Se instalações híbrido, recomendamos vivamente que implementar ligação do Azure AD para expandir o diretório no local ao Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Requisitos para utilizar o domínio participar com o Azure AD

Associação de domínio continua a funcionar como tem sempre. No entanto, para obter os benefícios do Azure AD terá o seguinte procedimento:

- Uma subscrição do Azure AD.
- Uma implementação de ligação do Azure AD para expandir o diretório no local ao Azure AD.
- Uma política que permite que os dispositivos façam parte de um domínio ter acesso condicional para Azure AD.
- Uma política que permita aceder a dispositivos "associarem ao domínio", se pretender que possam restringir o acesso para alguns dispositivos.
- Gestor de configuração do Centro de sistema versão 1509 para pré-visualização técnica, para ativar regras para que exige o compatível com dispositivos. (Consulte a documentação do TechNet e a mensagem do blogue).

Para mais informações sobre a associação de domínio no Windows 10, consulte o artigo [experiências ligar dispositivos de domínio para Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Requisitos de utilização BYOD e "Adicionar uma conta escolar ou profissional"

Para ativar "trazer o seu próprio dispositivo" (BYOD) com contas escolar ou profissional, precisa do seguinte:

- Uma subscrição do Azure AD.
- Uma subscrição do Azure AD Premium, como o dispositivo móvel gestão inscrição automática, se necessitar de mais funcionalidades.

## <a name="requirements-for-using-microsoft-passport"></a>Requisitos para utilizar o Microsoft Passport

Para permitir que o Microsoft Passport, terá o seguinte procedimento:

- Uma pública key infrastructure () para suporte de autenticação baseada em certificado que utiliza o Microsoft Passport.
- Uma subscrição do Intune para suporte de autenticação baseada em certificados que utiliza o Microsoft Passport para participar do Azure AD e contas escolar ou profissional.
- Gestor de configuração do sistema Centro versão 1509 para pré-visualização técnica (consulte a documentação do TechNet e a mensagem do blogue) para suporte de autenticação baseada em certificado que utiliza o Microsoft Passport para associação de domínio.
- Uma política para activar o Microsoft Passport na organização.

Como alternativa à utilização de uma PKI, pode ativar a Microsoft Passport baseada em chaves ao efetuar o seguinte:

- Implemente o Windows Server 2016 "Produção pré-visualização 1" CDs (sem necessidade de domínio ou floresta níveis funcionais; várias CDs para que servem redundância que cada site do Active Directory bastará).
- Definir a política para activar o Microsoft Passport na organização.

Para mais informações sobre o Microsoft Passport e Windows Olá no Windows 10, consulte o artigo < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Que produtos de gestão do dispositivo móvel parceiro integram com o Azure AD?

Os seguintes produtos de fornecedor integram com o Azure AD para inscrição unificada e acesso condicional no Windows 10:

- AirWatch por VMware
- Citrix Xenmobile
- Gestor de móvel Lightspeed
- Gestão de dispositivos móveis SOTI no local
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>O que sobre local de trabalho aderir no Windows 10?
Associação do local de trabalho foi utilizada no Windows 8.1 para activar BYOD. No Windows 10, BYOD é activado através de "Adicione uma escolar ou profissional conta" como é explicado anterior neste documento. Para organizações que não integram a sua gestão de dispositivos móveis com o Azure AD, os utilizadores podem inscrever o dispositivo na gestão manualmente através de **Definições** > **contas** > **acesso de trabalho**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>Os utilizadores ligar respetiva conta Microsoft à sua conta de domínio no Windows 10
Não no Windows 10. No Windows 8.1, os utilizadores façam parte de um domínio dispositivos poderiam "ligar" respetiva conta Microsoft (por exemplo, Hotmail, Live, Outlook, Xbox, etc.) à sua conta de domínio para ativar determinadas experiências como SSO para serviços direto, a utilização da loja Windows e roaming das definições do utilizador em todos os dispositivos. No Windows 10, a conta Microsoft "ligar" funcionalidade foi retirada. O utilizador pode adicionar um ou mais contas Microsoft como contas adicionais para ativar o SSO para serviços do consumidor como da loja Windows. Isto é feito nas **Definições** > **contas** > **sua conta**.

Os utilizadores que estiver a atualizar a partir de dispositivos de domínio do Windows 8.1 e que tinha respetiva conta Microsoft ligado, este tem automaticamente a respetiva conta Microsoft ligada adicionada à lista de contas adicionais que utilizam.


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)

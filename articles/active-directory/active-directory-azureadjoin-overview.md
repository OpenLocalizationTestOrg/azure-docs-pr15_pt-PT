<properties
    pageTitle="Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar | Microsoft Azure"
    description="Fornece uma descrição detalhada de como podem utilizar o Windows 10 dispositivos Azure AD participar para se registar no Azure Active Directory."
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

# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar

## <a name="what-is-azure-active-directory-join"></a>O que é o Azure Active Directory participar?
Azure Active Directory participar (Azure AD participar) é a funcionalidade que regista um dispositivo que sejam propriedade da empresa no Azure Active Directory para ativar a gestão centralizada do dispositivo. Se torna possível para utilizadores como empregados e estudantes para ligar à nuvem de empresa através do Azure Active Directory. Isto permite simplificadas implementações do Windows e o acesso a organizacionais aplicações e recursos a partir de qualquer dispositivo Windows, ambos empresarial pertence e pessoalmente propriedade (BYOD).

Associação do Azure AD destina-se para as empresas que estão na nuvem-primeiro/apenas na nuvem – normalmente pequenas e médias empresas que não possui uma infraestrutura do Active Directory do Windows Server no local. Que referido, Azure associação de AD pode e será também ser utilizado por grandes organizações em dispositivos com incapacidade de efetuar uma associação de domínio tradicional (dispositivos móveis, por exemplo), ou para utilizadores que principalmente precisam de aceder ao Office 365 ou outras aplicações do Azure AD SaaS.

Apesar da associação de domínio tradicional ainda oferece que uma experiência do melhor no local em dispositivos que são capazes de domínio participar, Azure AD associação é adequada para dispositivos que não é possível associação de domínio. Associação do Azure AD também é adequada para gerir os utilizadores na nuvem. Faz-lo utilizando as capacidades da gestão do dispositivo móvel em vez de utilizando ferramentas de gestão de domínio tradicional como política de grupo e o sistema Center Configuration Manager (SCCM).

![Descrição geral de dispositivos da empresa e dispositivos pessoais com o Active Directory no local e o Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Por que motivo devem empresas adotar o Azure AD participar?

* **Empresas que estão em grande medida na nuvem**: se foram descontinuadas ou está a mover a um modelo na qual são redução sua ambiental no local e pretende trabalhar com mais na nuvem, participar do Azure AD poderiam beneficiar. Talvez foram criadas contas do Azure AD manualmente ou através de sincronizar o Active Directory no local. Qualquer forma, tiver uma conta no Azure AD e pode utilizá-lo para iniciar sessão no Windows 10. Os utilizadores podem associar os respetivos computadores a Azure AD através de um da experiência de out of box (OOBE) ou através do menu definições. Depois de aderir, os utilizadores irão Desfrute de único início de sessão (SSO) acesso aos recursos na nuvem como o Office 365, os respetivos browsers ou em aplicações do Office.

* **Estabelecimentos de ensino**: um dos cenários podemos ouvir sobre com frequência é que, estabelecimentos de ensino tem dois tipos de utilizador: corpo docente e estudantes. Membros de corpo docente são considerados como longo prazo membros da organização, pelo que criar contas no local para os mesmos é conveniente. Mas estudantes são shorter-term membros da organização e, por conseguinte, podem ser geridos no Azure AD. Isto significa que pode ser seguia escala de diretório para a nuvem em vez de armazenados no local. Também significa que os estudantes podem iniciar sessão no Windows com as respetivas contas do Azure AD e obter acesso aos recursos do Office 365, os respetivos browsers ou em aplicações do Office.

* **Empresas de revenda**: outro cenário seguem acerca de clientes é a sua vontade para gerir mais facilmente sazonal.  Novamente, contas para funcionários de longo prazo, Full-time normalmente são criadas como contas de no domínio máquinas no local. Mas sazonal é shorter-term membros da organização, por isso, é aconselhável geri-los onde licenças de utilizador podem ser mais facilmente mudadas. Criar nestas contas de utilizador na nuvem com licenças do Office 365 permite que os utilizadores obter os benefícios de iniciar sessão no Windows e aplicações do Office com uma conta do Azure AD. Entretanto, manter mais flexibilidade com as suas licenças após deixarem.
* **Outras empresas**: Embora manter os utilizadores no Active Directory no local, que poderia ainda beneficiar tendo utilizadores ser Azure AD associadas. Se ao facto Azure AD oferece uma experiência de unir simplificada, gestão de dispositivos eficaz, a inscrição de gestão do dispositivo móvel automática e capacidade de início de sessão única para o Azure AD e no local recursos.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Que funcionalidades participar do Azure AD oferta?
Com o Azure AD participar, obtém o seguinte procedimento:

* **Personalizada aprovisionamento de dispositivos da empresa que sejam propriedade**: com o Windows 10, os utilizadores podem configurar um dispositivo de novo, retráctil na experiência out of box, sem participação IT.


* **Suporte para factores de forma moderna**: Azure AD associação funciona em dispositivos que não têm o domínio tradicional participar capacidades.  


* **Suporte para contas organizacionais existentes**: os utilizadores já não têm de criar e manter um uma conta Microsoft pessoal para obter a melhor experiência no dispositivos emitido da empresa, à medida que tinham com o Windows 8. Podem utilizar em vez disso, as respetivas contas de trabalho existente no Azure AD. Para organizações muitos, que mostra basicamente Isto significa que os utilizadores podem configurar e iniciar sessão no Windows com as mesmas credenciais que utilizar para aceder ao Office 365.


* **Inscrição de gestão do dispositivo móvel automáticas**: dispositivos podem ser automaticamente inscrito na gestão de dispositivos móveis quando estiver ligado a Azure AD. Este processo funciona com o Microsoft Intune e parceiro soluções de gestão de dispositivos móveis. Quando concluir o processo gestão de dispositivos com Intune, os administradores de TI podem monitor/gerir dispositivos Azure AD-associadas ao longo dos dispositivos façam parte de um domínio na consola de gestão de SCCM.


* **Serviço Single sign-on para recursos da empresa**: os utilizadores desfrutam single sign-on do ambiente de trabalho do Windows para aplicações e recursos na nuvem, como o Office 365 e milhares de aplicações empresariais que dependem de Azure AD para a autenticação através de [ligação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md). Propriedade empresarial dispositivos que estão associados a Azure AD Desfrute também SSO para recursos no local quando o dispositivo está numa rede de empresa e, a partir de qualquer lugar quando estes recursos são expostos através do [Azure AD para o Proxy de aplicação](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Roaming de estado do sistema operativo**: as definições de acessibilidade, sites públicos, Wi-Fi as palavras-passe e outras definições são sincronizadas em todos os dispositivos empresarial propriedade sem necessidade de uma conta Microsoft pessoal.


* **Pronto para Enterprise da loja Windows**: O da loja Windows suporta aquisição da aplicação e licenciamento com contas do Azure AD. As organizações podem aplicações de licença de volume e os disponibilizar para os utilizadores na sua organização.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Como funcionam os dispositivos com o Azure AD participar?

| Dispositivo empresarial (aderido ao domínio no local)                                                                                                                                                                                         | Dispositivo empresarial (associado na nuvem)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Dispositivo pessoal                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Os utilizadores podem iniciar sessão no Windows com as credenciais de trabalho (tal como hoje em dia).                                                                                                                                                                        | Os utilizadores podem iniciar sessão para Windows com as credenciais de trabalho que são geridas no Azure AD. Isto é relevante para dispositivos empresariais em três casos: 1) a organização não tem o Active Directory no local (por exemplo, uma pequenas empresas). 2) a organização não cria todas as contas de utilizador no Active Directory (por exemplo, contas para os estudantes, consultores ou sazonal não forem criadas no Active Directory). 3) a organização tiver dispositivos empresariais que não podem ser associados a um domínio (no local), como telefones ou tablets com um SKU Mobile (por exemplo, um secundário dispositivo redirecionado para um piso fábrica/revenda). Associação do Azure AD suporta participar de dispositivos da empresa para organizações geridos e federados. | Os utilizadores inicie sessão no Windows com as respetivas credenciais de conta Microsoft pessoais (sem alteração).                                                |
| Os utilizadores têm acesso às definições do guardado no servidor e a empresa da loja Windows. Estes serviços trabalharem com contas de trabalho e não necessitam de uma conta Microsoft pessoal. Isto requer que as organizações para ligar os respetivos no local Active Directory ao Azure AD.                                        | Os utilizadores podem fazer a configuração de gestão personalizada. Podem percorrer a experiência de primeira (FRX) através da respetiva conta do trabalho como alternativa à tendo aprovisionar IT os dispositivos, apesar de ambos os métodos são suportados.                                                                                                                                                                                                                                                                                                                                                                             | Os utilizadores podem adicionar facilmente uma conta de trabalho que é gerida no Active Directory ou Azure AD.                                                      |
| Os utilizadores têm a capacidade SSO do ambiente de trabalho de trabalhar aplicações, Web sites e recursos – incluindo recursos no local e apps na nuvem que utilizar Azure AD para autenticação.                                                                                                            | Os dispositivos estão registados automaticamente no diretório enterprise (Azure AD) e automaticamente inscrito na gestão de dispositivos móveis. (Função azure AD Premium).                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Os utilizadores têm a capacidade SSO através de aplicações e para Web sites/recursos com esta conta do trabalho.                                              |
| Os utilizadores podem adicionar as respetivas contas pessoais do Microsoft para aceder à sua imagens pessoais e ficheiros sem que afetam os dados de empresa. (Definições de roaming continuarem a trabalhar com as respetivas contas do trabalho.) A conta Microsoft permite SSO e já não unidades roaming das definições.  | Os utilizadores podem fazer a reposição personalizada de palavra-passe (personalizada SSPR) no início de sessão, que significa que podem repor uma palavra-passe esquecida. (Função azure AD Premium).                                                                                                                                                                                                                                                                                                                                                                                                                                   | Os utilizadores têm acesso à empresa da loja Windows para que estes possam adquirir e utilizar aplicações de linha de negócio nos seus dispositivos pessoais. |                                                               |


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)

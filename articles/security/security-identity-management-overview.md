<properties
   pageTitle="Descrição geral de segurança de gestão de identidades Azure | Microsoft Azure"
   description=" Acesso e identidades gestão soluções ajuda do Microsoft IT proteger o acesso a aplicações e recursos na horizontal o Centro de dados da empresa e para a nuvem, permitindo-níveis de validação, tal como a autenticação multifator e políticas de acesso condicional adicionais. Este artigo fornece uma descrição geral das principais funcionalidades de segurança Azure que ajudam com a gestão de identidades. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Descrição geral de segurança de gestão de identidades Azure

Acesso e identidades gestão soluções ajuda do Microsoft IT proteger o acesso a aplicações e recursos na horizontal o Centro de dados da empresa e para a nuvem, permitindo-níveis de validação, tal como a autenticação multifator e políticas de acesso condicional adicionais. Monitorizar actividade suspeita através de segurança avançada de elaboração de relatórios, auditoria e alertar ajuda-o a mitigar potenciais problemas de segurança. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) fornece serviço single sign-on para milhares de nuvem (SaaS) aplicações e aceder às aplicações web executar no local.

Vantagens de segurança do Azure Active Directory (AD) incluem a capacidade de:

- Criar e gerir uma identidade única para cada utilizador em toda a empresa híbrido, mantendo a utilizadores, grupos e dispositivos sincronizadas
- Fornecer acesso de início de sessão único para as suas aplicações, incluindo milhares de aplicações de SaaS previamente integradas
- Ativar a segurança do access aplicação por impor com base em regras de autenticação Multifator para ambas no local e na nuvem aplicações
- Aprovisionar um acesso remoto seguro para as aplicações de web no local através de Proxy de aplicação do Azure AD

O objetivo deste artigo é fornecer uma descrição geral das principais funcionalidades de segurança Azure que ajudam com a gestão de identidades. Fornecemos também ligações para artigos que irão dar-detalhes de cada funcionalidade, de modo que pode obter mais informações.  

O artigo foca-se nas capacidades de gestão de identidades do Azure core seguintes:

- O início de sessão único
- Proxy inverso
- Autenticação multifator
- Monitorização de segurança, alertas e relatórios baseados em formação do máquina
- Gestão de identidade e de acesso do consumidor
- Registo de dispositivo
- Gestão de identidades privilegiados
- Proteção de identidade
- Gestão de identidades híbrido

## <a name="single-sign-on"></a>O início de sessão único

Início de sessão (SSO) meio único ser capaz de aceder a todas as aplicações e recursos de que precisa de fazer empresas, ao iniciar sessão de uma só vez, utilizando uma única conta de utilizador. Assim que iniciar sessão, pode aceder a todas as aplicações de que precisa sem ser necessário para autenticar (por exemplo, escreva uma palavra-passe) uma segunda vez.

Muitas organizações dependem software como um aplicações de serviço (SaaS) como o Office 365, caixa e Salesforce para produtividade do utilizador final. Historicamente, a equipa de TI necessário para individualmente criar e actualizar contas de utilizador em cada aplicação SaaS e utilizadores tinham Lembre-se uma palavra-passe para cada aplicação SaaS.

Azure AD se prolonga do Active Directory no local para a nuvem, permitindo aos utilizadores utilizar a respetiva conta institucional principal não só iniciar sessão para os seus dispositivos façam parte de um domínio e recursos da empresa, mas também todos os web e aplicações SaaS necessários para a sua tarefa.

Não só os utilizadores não possui que gerir vários conjuntos de nomes de utilizador e palavras-passe, o acesso a aplicação pode ser automaticamente aprovisionados ou anular aprovisionados grupos organizacionais com base no e o estado de um funcionário. Azure AD apresenta segurança e aceder a controlos de governação que permitem-lhe gerir centralmente acesso dos utilizadores através de SaaS aplicações.

Saiba mais:

- [Descrição geral do Single Sign-On](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Integrar o Azure Active Directory serviço single sign-on SaaS aplicações](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy inverso

Proxy de aplicação do Azure AD permite-lhe publicar aplicações no local, como sites [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [O Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)e [IIS](http://www.iis.net/)-baseado aplicações dentro da sua rede privada e oferece um acesso seguro para os utilizadores fora da sua rede. Proxy de aplicação fornece acesso remoto e o início de sessão único (SSO) para vários tipos de aplicações de web no local com milhares de SaaS aplicações que suporte do Azure AD. Os empregados podem iniciar sessão para as suas aplicações a partir de casa nos seus próprios dispositivos e autenticar através deste proxy baseado na nuvem.

Saiba mais:

- [Ativar o Proxy de aplicação do Azure AD](../active-directory/active-directory-application-proxy-enable.md)
- [Publicar aplicações utilizando o Proxy de aplicação do Azure AD](../active-directory/active-directory-application-proxy-publish.md)
- [Single-sign-on com Proxy de aplicação](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Trabalhar com o access condicional](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Autenticação multifator
Azure autenticação multifator (MFA) é um método de autenticação que necessita da utilização de mais do que um método de confirmação e adiciona uma segunda crítica camada de segurança para suplementos de início de sessão do utilizador e operações. MFA o ajuda a salvaguardar informações acesso a dados e as aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião. Fornece-forte autenticação através de um intervalo de opções de verificação — chamada telefónica, mensagem de texto ou aplicação móvel notificação ou verificação de código e 3 festa OAuth tokens.

Saiba mais:

- [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [O que é o Azure a autenticação Multifator?](../multi-factor-authentication/multi-factor-authentication.md)
- [Como funciona a autenticação Multifator de Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorização de segurança, alertas e relatórios baseados em formação do máquina

Monitorização de segurança e alertas e relatórios baseados na formação de máquina identificam padrões de acesso inconsistente podem ajudar a proteger a sua empresa. Pode utilizar o access do Azure Active Directory e relatórios de utilização para ganham visibilidade sobre a integridade e a segurança do diretório da sua organização. Com esta informação, um administrador de diretório melhor pode determinar onde possíveis riscos de segurança podem situar-se para que possam adequadamente planear a mitigar esses riscos.

No portal do Azure clássico, relatórios são categorizados das seguintes formas:

- Os relatórios anomalia – contêm eventos que encontrámos para ser anómalo de início de sessão. Nosso objetivo é torná-lo em mente essa actividade e permite-lhe possam fazer uma imagem sobre se um evento é suspeito.
- Relatórios de aplicação integrados – fornecem informações para como nuvem aplicações estão a ser utilizadas na sua organização. Azure Active Directory oferece a integração com milhares de aplicações na nuvem.
- Relatórios de erro – indicar erros que podem ocorrer quando Aprovisiona o contas para aplicações externas.
- Os relatórios específicas do utilizador – apresentam dispositivo/início de sessão nos dados de atividade para um utilizador específico.
- Registos de atividade – contêm um registo de todos os eventos auditados na últimas 24 horas, últimos 7 dias, ou últimos 30 dias, bem como alterações de atividade de grupo e atividade de reposição e registo de palavra-passe.

Saiba mais:

- [Ver relatórios acesso e utilização](../active-directory/active-directory-view-access-usage-reports.md)
- [Introdução ao Azure Active Directory elaboração de relatórios](../active-directory/active-directory-reporting-getting-started.md)
- [Guia de elaboração de relatórios do Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gestão de identidade e de acesso do consumidor

Azure Active B2C de diretório é um serviço de gestão de identidades altamente disponível, global, para aplicações do consumidor destinado que se adapta a centenas de milhões de identidades. Pode ser integrado nos mobile e web plataformas. Os utilizadores poderão pode iniciar sessão para todas as suas aplicações através de experiências personalizáveis utilizando as respetivas contas de rede sociais existentes ou através da criação de novas credenciais.

No passado, os programadores de aplicações que pretendiam para inscrever-se e inicie sessão no consumidores nas suas aplicações seriam criaram os seus próprios código. E se seria tiver utilizado ou sistemas de bases de dados no local para armazenar os nomes de utilizador e palavras-passe. Azure Active B2C de diretório oferece a sua organização uma melhor forma para integrar o aplicações com a ajuda de uma plataforma segura baseados em normas e um conjunto maior de extensible políticas de gestão de identidades do consumidor.

Quando utiliza o Azure Active Directory B2C, os utilizadores poderão pode inscrever-se para as suas aplicações utilizando as respetivas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou através da criação de novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e palavra-passe).

Saiba mais:

- [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Pré-visualização do Azure Active Directory B2C: iniciar sessão para cima e inicie sessão consumidores nas suas aplicações](../active-directory-b2c/active-directory-b2c-overview.md)
- [Pré-visualização do Azure Active Directory B2C: Tipos de aplicações](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registo de dispositivo

Registo de dispositivo do Azure AD é foundation para cenários baseada no dispositivo [acesso condicional](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Quando um dispositivo está registado, o Azure Active Directory dispositivo registo fornece o dispositivo de com uma identidade utilizada para autenticar o dispositivo, quando o utilizador inicia sessão. O dispositivo autenticado e os atributos do dispositivo, em seguida, podem ser utilizados para impor políticas de acesso condicional para as aplicações que são alojadas na nuvem e no local.

Quando o combinado com uma solução de gestão (MDM) do dispositivo móvel como o Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Esta opção permite-lhe criar regras de acesso condicional impõem access a partir de dispositivos para cumprir os padrões de segurança e conformidade.

Saiba mais:

- [Introdução ao Azure Active Directory dispositivo registo](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [Configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Registo de dispositivo automático com dispositivos façam parte de um domínio do Azure Active Directory para Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Gestão de identidades privilegiados
Azure Active Directory (AD) privilegiados da gestão de identidades permite-lhe gerir, controlar e monitorizar as identidades privilegiadas e acesso aos recursos no Azure AD, bem como de outros serviços onlinehttps da Microsoft como o Office 365 ou o Microsoft Intune.

Por vezes, os utilizadores têm de efectuar operações privilegiadas nas recursos Azure ou do Office 365 ou outras aplicações SaaS. Isto significa frequentemente organizações tem de conceder-lhes acesso privilegiado permanente no Azure AD. Este é um crescente risco de segurança para recursos alojado na nuvem porque organizações suficientemente não é possível monitorizar o que os utilizadores estão a fazer com os respetivos privilégios de administrador. Para além disso, se uma conta de utilizador com o access privilegiados está comprometida, essa violação de um pode afetar a sua segurança nuvem geral. Gestão de identidades do Azure AD privilegiados ajuda para resolver este risco.

Gestão de identidades do Azure AD privilegiados permite-lhe:

- Ver quais os utilizadores que estão os administradores do Azure AD
- Ativar a pedido, "apenas em hora" acesso administrativo ao Microsoft Online Services como o Office 365 e o Intune
- Obter relatórios sobre o histórico de acesso de administrador e alterações nas atribuições de administrador
- Receber alertas sobre o acesso a uma função privilegiado

Saiba mais:

- [Gestão de identidades do Azure AD privilegiados](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Funções de gestão de identidades do Azure AD privilegiados](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure AD privilegiados gestão de identidades: Como adicionar ou remover uma função de utilizador](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade
Proteção do Azure AD identidade é um serviço de segurança que fornece uma vista consolidada em eventos de risco e potenciais vulnerabilidades afetar identidades da sua organização. Proteção de identidade tira partido das capacidades de detecção de anomalia do existente Azure Active Directory (disponíveis através de relatórios de atividade discordantes Azure AD) e apresenta os novos tipos de evento de risco podem detetar anomalias em tempo real.

Saiba mais:

- [Proteção de identidade do Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canal 9: Azure AD e a apresentação de identidade: pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gestão de identidades híbrido

Abordagem da Microsoft para identidade abrange no local e na nuvem, criar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente de localização.

Saiba mais:

- [Híbrido identidade técnico](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Blogue da equipa do Active Directory](https://blogs.technet.microsoft.com/ad/)

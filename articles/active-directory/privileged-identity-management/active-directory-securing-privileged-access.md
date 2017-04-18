<properties
    pageTitle="Proteger o acesso privilegiado no Azure AD | Microsoft Azure"
    description="Um tópico que explica as abordagens para proteger o acesso privilegiado nos Azure, Azure Active Directory e Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Proteger o acesso privilegiado no Azure AD

Proteger o acesso privilegiado é um primeiro passo crítico para ajudar a proteger ativos empresariais numa organização Moderno. Contas privilegiadas são aqueles que administrar e gerir sistemas de TI. Cyber intrusos nestas contas para obter acesso aos dados de uma organização e sistemas de destino. Para proteger o acesso privilegiado, deverá isolar os sistemas do risco de expostos a um utilizador malicioso e contas.

Mais utilizadores estão a ser iniciadas obter um acesso privilegiado através dos serviços na nuvem. Isto pode incluir os administradores globais do Office 365, subscrição Azure administradores e utilizadores que têm acesso administrativo no VMs ou na SaaS aplicações.

A Microsoft recomenda a que seguir estas informações gerais sobre como [Proteger privilegiados acesso](https://technet.microsoft.com/library/mt631194.aspx).

Para clientes utilizando o Azure Active Directory para gerir o acesso a Azure, Office 365, ou outros serviços da Microsoft e aplicações, estes princípios aplicam-se de contas de utilizador são geridas e autenticadas ao Active Directory ou no Azure Active Directory. As secções seguintes fornecem mais informações sobre as funcionalidades do Azure AD para suportar a proteger o acesso privilegiado.

## <a name="multi-factor-authentication"></a>Autenticação multifator

Para aumentar a segurança de autenticação de administrador, deve exigir autenticação multifator (MFA) antes de conceder privilégios. MFA é um método para verificar quem é o utilizador que necessita da utilização de mais do que apenas um nome de utilizador e palavra-passe. Fornece uma segunda camada de segurança para suplementos de início de sessão do utilizador e operações.

Azure autenticação Multifator ajuda-o a salvaguardar informações o acesso a dados e as aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião. Fornece-forte autenticação através de um intervalo de opções de verificação fácil incluindo chamadas telefónicas, mensagens de texto, notificações da aplicação móvel, ou código de verificação e 3º tokens de JURAMENTO de terceiros.

Para obter uma descrição geral de como funciona a autenticação Multifator de Azure, consulte o vídeo seguinte.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Para obter mais detalhes, consulte o artigo [MFA para Office 365 e MFA para Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilégios de vínculo de hora

Algumas organizações, poderão constatar que tenham demasiados utilizadores em funções altamente privilegiadas. Um utilizador poderá ter sido adicionado à função para uma determinada actividade, como o para se inscrever para um serviço, mas não utiliza esses privilégios frequentemente mais tarde.

Para diminuir o tempo de exposição de privilégios e aumentar a sua visibilidade para a sua utilização, limitar os utilizadores a tomar apenas nos seus privilégios Just no tempo (JIT), sempre que necessário executar uma tarefa. Para Azure Active Directory e Serviços Online da Microsoft, pode utilizar [a gestão de identidades privilegiados do Azure AD (PIM)](http://aka.ms/AzurePIM).


![Dashboard PIM][2]


## <a name="attack-detection"></a>Ataque deteção

[Azure Active Directory identidade Protection](../active-directory-identityprotection.md) fornece uma vista consolidada em eventos de risco e potenciais vulnerabilidades afetar identidades da sua organização. Proteção de identidade com base em eventos de risco, calcula a um nível de risco de utilizador para cada utilizador, permitindo-lhe configurar com base em risco políticas para proteger automaticamente as identidades da sua organização. Estas políticas, juntamente com outros controlos de acesso condicional fornecidas pela Azure Active Directory e EMS, podem bloquear o utilizador ou oferecer sugestões que incluam de palavra-passe e imposição de autenticação multifator automaticamente.

![Proteção de identidade do Azure AD][3]

## <a name="conditional-access"></a>Acesso condicional

Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando autenticar um utilizador, antes de permissão do acesso a uma aplicação. Assim que forem cumpridas destas condições, o utilizador autenticado e permissão de acesso para a aplicação.


![Definir regras de acesso condicional com MFA][4]


## <a name="related-articles"></a>Artigos relacionados

- Ativar a [autenticação Multifator Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Ativar a [Gestão de identidades do Azure AD privilegiados](../active-directory-privileged-identity-management-configure.md)
- Ativar a [proteção de identidade do Azure AD](../active-directory-identityprotection.md)
- Ativar [controlos de acesso condicional](../active-directory-conditional-access.md)


Para mais informações sobre a criação de um quadro de referência de segurança completo, consulte a secção "responsabilidades do cliente e informações gerais" do documento [Microsoft Cloud Security para arquitectura empresarial](http://aka.ms/securecustomer) . Para mais informações sobre envolventes serviços da Microsoft para ajudar com a qualquer um destes tópicos, contacte o representante da Microsoft ou visite a nossa [página de soluções Cybersecurity](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png

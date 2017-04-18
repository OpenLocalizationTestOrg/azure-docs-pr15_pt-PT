<properties
    pageTitle="Notificações de elaboração de relatórios do Azure Active Directory"
    description="Como utilizar o Azure Active Directory relatórios as notificações de início de sessão suspeita ins."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Notificações de elaboração de relatórios do Azure Active Directory

## <a name="what-reports-generate-email-notifications"></a>O que relatórios geram notificações de correio eletrónico

Neste momento, só o Irregular sessão accionadores de relatório de atividade e-mail notificações.

## <a name="what-is-an-irregular-sign-in"></a>O que é um "Irregular início de sessão"?

Início de sessão-ins irregulares são aqueles que tenham sido identificadas por nossa algoritmos, com base numa condição "viagem impossibilita" combinado com uma localização de início de sessão anómalo e do dispositivo de aprendizagem automática. Isto pode indicar que um hacker tenha sido tentar iniciar sessão utilizando esta conta.

## <a name="who-receives-the-email-notifications"></a>Quem recebe as notificações de correio eletrónico?

O e-mail é enviado para todos os administradores globais que foi atribuídos uma licença do Active Directory Premium. Para assegurar que seja entregue, podemos enviá-la para administradores endereço de E-mail alternativo, assim. Os administradores devem incluir aad-alerts-noreply@mail.windowsazure.com na sua lista de remetentes seguros para que estes não perder o e-mail.

## <a name="how-often-are-these-emails-sent"></a>Com que frequência são estes e-mails enviados?

O correio eletrónico é enviado se ocorrerem 10 novas irregulares início de sessão no atividades nos últimos 30 dias ou desde a última mensagem foi enviada, consoante o que é menor.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Como posso aceder ao relatório mencionado na mensagem de correio electrónico?

Quando clica na ligação, serão redirecionados para a página de relatório no interior do Azure portal clássica. Para poder aceder ao relatório, tem de ser ambos:

- Um administrador ou co-administrador da sua subscrição do Azure

- Um administrador global no diretório e atribuída uma licença do Active Directory Premium. Para mais informações, consulte o artigo [edições do Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Posso desativar estas mensagens de correio eletrónico?

Sim, desativar as notificações relacionados com suplementos de início de sessão discordantes no interior do Azure portal clássico, clique em **Configurar**e, em seguida, selecione **desativado** na secção **notificações** .

## <a name="whats-next"></a>O que se segue
- Curioso que relatórios de segurança, auditoria e atividade estão disponíveis? Dar saída [de segurança do Azure AD, auditoria e relatórios de atividade](active-directory-view-access-usage-reports.md)
- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar a imagem corporativa ao seu iniciar sessão e o painel de acesso a páginas de empresa](active-directory-add-company-branding.md)

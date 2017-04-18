<properties
    pageTitle="Notificações de proteção de identidade do Active Directory Azure | Microsoft Azure"
    description="Saiba como as notificações de suportem de atividades de inquérito."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, deteção de aplicação de nuvem, gerir aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-notifications"></a>Notificações de proteção de identidade do Active Directory Azure 


Proteção de identidade AD Azure envia dois tipos de notificação de automatizada e-mails para o ajudar a gerir o risco de utilizador e eventos de risco:

- Utilizador comprometida alerta de correio eletrónico

- E-mail de resumo semanal

## <a name="user-compromised-alert-email"></a>Utilizador comprometida alerta de correio eletrónico

Alerta de correio eletrónico comprometida de utilizador é gerado quando proteção de identidade do Azure AD identifica uma conta, como comprometida. O e-mail inclui uma ligação para os utilizadores sinalizadas para relatório de risco no dashboard de proteção de identidade. Recomendamos que imediatamente investigar as notificações de comprometida.


## <a name="weekly-digest-email"></a>E-mail de resumo semanais

O e-mail de resumo semanal contém um resumo dos novos eventos de risco.<br>
Inclui:

- Utilizadores risco
- Actividades suspeitas
- Vulnerabilidades detectadas
- Ligações para os relatórios relacionados em proteção de identidade


<br>
![Remediação](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

Pode alternar enviar um e-mail de resumo semanal.
<br><br>
![Riscos de utilizador](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**Para abrir a caixa de diálogo relacionados com a configuração**:

1. No pá **Azure AD identidade proteção** , clique em **Definições**.
<br><br>
![Política de risco de utilizador](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. Na secção **Geral** , clique em **notificações**.
<br><br>
![Política de risco de utilizador](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md) 


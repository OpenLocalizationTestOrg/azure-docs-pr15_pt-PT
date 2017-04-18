<properties
    pageTitle="Início de sessão no experiências com proteção de identidade do Azure AD | Microsoft Azure"
    description="Fornece uma descrição geral da experiência do utilizador quando proteção de identidade tem atenuada ou remediated um utilizador ou quando é exigida o por uma política de autenticação multifator."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, deteção de aplicação de nuvem, gerir aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Início de sessão no experiências com a proteção de identidade do Azure AD

Com o Azure Active Directory identidade proteção, pode:

- exigir que os utilizadores para se registar no autenticação multifator

- processar arriscados-ins do início de sessão e utilizadores comprometidos

A resposta do sistema para estes problemas de ter um impacto experiência de início de sessão de um utilizador porque apenas diretamente iniciar sessão ao fornecer um nome de utilizador e uma palavra-passe não será possível deixem. Passos adicionais são necessários para obter um utilizador com segurança para o business.

Este tópico fornece uma descrição geral da experiência de início de sessão de um utilizador para todos os casos que podem ocorrer.

**Autenticação multifator**

- Registo de autenticação multifator



**Inicie sessão no risco**

- Recuperação de início de sessão arriscada

- Arriscado início de sessão no bloqueados

- Registo de autenticação multifator durante uma sessão arriscada
 

**Utilizador risco**

- Recuperação de conta comprometida

- Conta comprometida bloqueada




## <a name="multi-factor-authentication-registration"></a>Registo de autenticação multifator

A melhor experiência de utilizador em ambos os casos, o caudal de recuperação de conta comprometida e arriscado caudal iniciar sessão, é quando o utilizador pode recuperar personalizada. Se os utilizadores estão registados para a autenticação multifator, já tem um número de telefone associado à sua conta que pode ser utilizada para transmitir desafios de segurança. Sem participação de secretária ou administrador de ajuda é necessário para recuperar a partir de comprometer a conta. Assim sendo, é vivamente recomendado os seus utilizadores registados para a autenticação multifator. 

Os administradores podem:

- Defina uma política que requer que os utilizadores configurar as respetivas contas para verificação de segurança adicional. 
- permitir a ignorar registo de autenticação multifator até 30 dias, no caso pretendem dar aos utilizadores um período de tolerância antes de registar.

**O registo de autenticação multifator tem três passos:**

1. Primeiro passo, o utilizador obtém uma notificação sobre os requisitos para definir a conta para autenticação multifator. 

    ![Remediação] (./media/active-directory-identityprotection-flows/140.png "Remediação")


2. Para configurar a autenticação multifator, é necessário permitir que o sistema de que forma pretende ser contactado.

    ![Remediação] (./media/active-directory-identityprotection-flows/141.png "Remediação")
 
3. O sistema submete um desafio para si e precisa de responder.

    ![Remediação] (./media/active-directory-identityprotection-flows/142.png "Remediação")

 



## <a name="risky-sign-in-recovery"></a>Recuperação de início de sessão arriscada

Quando um administrador tiver configurado uma política de início de sessão no riscos, os utilizadores afetados são notificados quando tentarem iniciar sessão. 

**O fluxo de início de sessão arriscado tem dois passos:** 

1. O utilizador é informado algo invulgar foi detetado sobre a sua sessão, tal como iniciar sessão a partir de uma nova localização, dispositivo ou aplicação. 

    ![Remediação] (./media/active-directory-identityprotection-flows/120.png "Remediação")

2. O utilizador é necessário para provar a sua identidade por um desafio de segurança de resolução. Se o utilizador está registado para autenticação multifator precisam de viagem de volta um código de segurança para o número de telefone. Uma vez que este é um apenas um arriscado início de sessão e não uma conta comprometida, o utilizador não tem de alterar a palavra-passe neste fluxo de. 

    ![Remediação] (./media/active-directory-identityprotection-flows/121.png "Remediação")



 
## <a name="risky-sign-in-blocked"></a>Arriscado início de sessão no bloqueados
Os administradores também podem optar por definir uma política de início de sessão no risco para bloquear utilizadores relativamente a iniciar sessão dependendo do nível de risco. Para obter desbloqueados, os utilizadores finais terá de contactar um administrador ou suporte técnico ou pode tentarem iniciar sessão a partir de uma localização familiar ou dispositivo. Recuperar personalizada por autenticação multifator de resolução não é uma opção neste caso.

![Remediação] (./media/active-directory-identityprotection-flows/200.png "Remediação")




## <a name="compromised-account-recovery"></a>Recuperação de conta comprometida

Quando tiver sido configurada uma política de segurança de risco de utilizador, os utilizadores que cumprem o utilizador especificado na política de nível do risco (e, consequentemente, são assumido comprometida) tem de percorrer o fluxo de recuperação do utilizador comprometer antes de estes possam iniciar sessão. 

**O fluxo de recuperação de compromisso do utilizador tem três passos:**

1. O utilizador é informado de que os respetivos segurança da conta é risco devido a atividade suspeita ou fuga credenciais.

    ![Remediação] (./media/active-directory-identityprotection-flows/101.png "Remediação")

2.  O utilizador é necessário para provar a sua identidade por um desafio de segurança de resolução. Se o utilizador está registado para autenticação multifator personalizada pode recuperar de ser comprometida. Terá de lhes viagem de volta um código de segurança para o número de telefone. 

    ![Remediação] (./media/active-directory-identityprotection-flows/110.png "Remediação")


3.  Por fim, o utilizador for forçado para alterar a respetiva palavra-passe uma vez que outra pessoa tenha acesso à sua conta. Capturas de ecrã desta experiência estão abaixo.
 
    ![Remediação] (./media/active-directory-identityprotection-flows/111.png "Remediação")



## <a name="compromised-account-blocked"></a>Conta comprometida bloqueada 

Para obter um utilizador que foi bloqueado por uma política de segurança de risco de utilizador desbloqueada, o utilizador tem contacte um administrador ou suporte técnico. Recuperar personalizada por autenticação multifator de resolução não é uma opção neste caso.


![Remediação] (./media/active-directory-identityprotection-flows/104.png "Remediação")



 
## <a name="reset-password"></a>Repor palavra-passe

Se os utilizadores comprometidos estão bloqueados relativamente a iniciar sessão, um administrador pode gerar uma palavra-passe temporária para os mesmos. Os utilizadores terão que alterar a respetiva palavra-passe durante uma sessão seguinte.

![Remediação] (./media/active-directory-identityprotection-flows/160.png "Remediação")


 




 

## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md) 
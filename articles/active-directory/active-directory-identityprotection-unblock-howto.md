<properties
    pageTitle="Azure Active Directory protecção de identidade - como desbloquear utilizadores | Microsoft Azure"
    description="Obter informações sobre como desbloquear utilizadores que tenham sido bloqueados por uma política de protecção de identidade do Azure Active Directory."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, desbloquear utilizador"
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
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory protecção de identidade - como desbloquear utilizadores

Com o Azure Active Directory identidade proteção, pode configurar as políticas para impedir que os utilizadores se as condições configuradas são correspondidas. Normalmente, um utilizador bloqueados contactos suporte técnico para se tornar desbloqueados. Este tópicos explica os passos que pode efetuar para desbloquear um utilizador bloqueado.


## <a name="determine-the-reason-for-blocking"></a>Determinar a razão para bloquear

Como primeiro passo para desbloquear um utilizador, tem de determinar o tipo de política que bloqueou o utilizador porque os seus próximos passos são cálculo consoante a forma-lo. Com o Azure Active Directory identidade proteção, um utilizador pode ser bloqueado ou por uma política de início de sessão no risco ou uma política de risco de utilizador. 

Pode obter o tipo de política que bloqueou a um utilizador a partir do cabeçalho na caixa de diálogo que foi apresentado ao utilizador durante uma tentativa de início de sessão:

|Política | Caixa de diálogo utilizador|
|--- | --- |
|Início de sessão no risco | ![O início de sessão bloqueado](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Risco de utilizador | ![Conta bloqueada](./media/active-directory-identityprotection-unblock-howto/104.png) |


Um utilizador que está bloqueado por:

- Uma política de início de sessão no risco é também conhecida como suspeita iniciar sessão
- Uma política de risco de utilizador é também conhecida como uma conta de risco

 
## <a name="unblocking-suspicious-sign-ins"></a>Desbloquear suplementos de início de sessão suspeitos

Para desbloquear uma suspeita iniciar sessão, tem as seguintes opções:

1. **Iniciar sessão a partir de uma localização familiar ou dispositivo** - um motivo comum para início de sessão suspeitas bloqueados-ins estão tentativas de início de sessão a partir de localizações não está familiarizadas ou dispositivos. Os utilizadores rapidamente podem determinar se esta é a razão bloqueio ao tentar iniciar sessão a partir de uma localização familiar ou dispositivo.


3. **Excluir da política** - se achar que a configuração atual da sua política de início de sessão no está a causar problemas para utilizadores específicos, pode excluir os utilizadores a partir dos mesmos. Consulte a [política de início de sessão no risco](active-directory-identityprotection.md#sign-in-risk-policy) para obter mais detalhes.
 
4. **Desativar política** - se achar que a configuração de política está a prejudicar para todos os seus utilizadores, pode desativar a política. Consulte a [política de início de sessão no risco](active-directory-identityprotection.md#sign-in-risk-policy) para obter mais detalhes.


## <a name="unblocking-accounts-at-risk"></a>Desbloquear contas risco

Para desbloquear uma conta do risco, tem as seguintes opções:

1. **Repor palavra-passe** - pode repor a palavra-passe de utilizador. Consulte o artigo [Repor a palavra-passe segura manual](active-directory-identityprotection.md#manual-secure-password-reset) para obter mais detalhes.

2. **Dispensar todos os eventos de risco** - a política de risco de utilizador bloqueia um utilizador se atingiu o nível de risco de utilizador configurado para bloquear o acesso. Pode reduzir um utilizador do nível de risco fechando manualmente comunicado eventos de risco. Para obter mais detalhes, consulte o artigo [eventos de risco de fecho manualmente](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Excluir da política** - se achar que a configuração atual da sua política de início de sessão no está a causar problemas para utilizadores específicos, pode excluir os utilizadores a partir dos mesmos. Consulte a [política de risco de utilizador](active-directory-identityprotection.md#user-risk-policy) para obter mais detalhes.
 
4. **Desativar política** - se achar que a configuração de política está a prejudicar para todos os seus utilizadores, pode desativar a política. Consulte a [política de risco de utilizador](active-directory-identityprotection.md#user-risk-policy) para obter mais detalhes.




## <a name="next-steps"></a>Próximos passos

 Pretende saber mais sobre proteção de identidade do Azure AD? Consulte o artigo [Protecção de identidade do Azure Active Directory](active-directory-identityprotection.md).
 


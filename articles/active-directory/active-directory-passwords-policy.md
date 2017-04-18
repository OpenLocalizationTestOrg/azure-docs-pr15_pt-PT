<properties
    pageTitle="Políticas de palavra-passe e restrições no Azure Active Directory | Microsoft Azure"
    description="Descreve as políticas que se aplicam a palavras-passe no Azure Active Directory, incluindo os caracteres permitidos, comprimento e expiração"
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas de palavra-passe e restrições no Azure Active Directory

Este artigo descreve as políticas de palavra-passe e os requisitos de complexidade associados armazenadas no seu diretório do Azure AD de contas de utilizador.

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de UserPrincipalName que se aplicam a todas as contas de utilizador

Todas as contas de utilizador que tem de iniciar sessão para o sistema de autenticação do Azure AD tem de ter um valor do atributo utilizador exclusivo nome principal (UPN) associado a essa conta. A tabela seguinte destaques as políticas que aplicam-se para ambas no local Contas de utilizador de origem do Active Directory (sincronizado na nuvem) e para contas de utilizador apenas na nuvem.

|   Propriedade           |     Requisitos de UserPrincipalName  |
|   ----------------------- |   ----------------------- |
|  Carateres permitidos    |  <ul> <li>A-Z</li> <li>um - z </li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Não são permitidos caracteres  | <ul> <li>Qualquer '@' caráter que não é separar o nome de utilizador do domínio.</li> <li>Não pode conter um caráter período '.' imediatamente anterior a '@' símbolo</li></ul> |
| Restrições de comprimento  |       <ul> <li>Comprimento total não podem exceder 113 carateres</li><li>64 carateres antes do ‘@’ símbolo</li><li>48 caracteres após a ‘@’ símbolo</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Políticas de palavra-passe que se aplicam apenas para contas de utilizador na nuvem

A tabela seguinte descreve as definições de políticas de palavra-passe disponíveis que podem ser aplicadas às contas de utilizador que são criadas e geridas no Azure AD.

|  Propriedade       |    Requisitos de          |
|   ----------------------- |   ----------------------- |
|  Carateres permitidos   |   <ul><li>A-Z</li><li>um - z </li><li>0 – 9</li> <li>@# $ % ^ & * - _ ! + = {[]} & #124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Não são permitidos caracteres   |       <ul><li>Carateres Unicode</li><li>Espaços</li><li> **Apenas palavras-passe fortes**: não pode conter um caráter de ponto '.' imediatamente anterior a '@' símbolo</li></ul> |
|   Restrições de palavra-passe | <ul><li>8 carateres mínimos e máximo de 16 caracteres</li><li>**Apenas palavras-passe fortes**: 3 requer terminar 4 dos seguintes procedimentos:<ul><li>Caracteres em minúsculas</li><li>Caracteres em maiúsculas</li><li>Números (0-9)</li><li>Símbolos (consulte o artigo restrições de palavra-passe acima)</li></ul></li></ul> |
| Duração de expiração de palavra-passe      | <ul><li>Valor predefinido: **90** dias </li><li>Valor é configurável utilizando o cmdlet Set-MsolPasswordPolicy a partir do Azure Active Directory módulo para Windows PowerShell.</li></ul> |
| Notificação de expiração de palavra-passe |  <ul><li>Valor predefinido: **14** dias (antes que expire a palavra-passe)</li><li>Valor é configurável utilizando o cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiração de palavra-passe |  <ul><li>Valor predefinido: dias **Falso** (indica que expiração de palavra-passe está ativada) </li><li>Valor pode ser configurado para contas de utilizador individual, utilizando o cmdlet Set-MsolUser. </li></ul> |
|  Histórico de palavra-passe  | Última palavra-passe não pode ser utilizado novamente. |
|  Duração do histórico de palavra-passe | Uma eternidade |
|  Bloqueio de conta | Após 10 tentativas sem êxito início de sessão no (palavra-passe incorreta), o utilizador será bloqueado para um minuto. Ainda mais início de sessão no tentativas falhadas bloqueará saída ao utilizador para aumentar as durações. |


## <a name="next-steps"></a>Próximos passos

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Gerir palavras-passe a partir de qualquer lugar](active-directory-passwords.md)
* [Como funciona a gestão de palavra-passe](active-directory-passwords-how-it-works.md)
* [Introdução à gestão de palavra-passe](active-directory-passwords-getting-started.md)
* [Personalizar a gestão de palavra-passe](active-directory-passwords-customize.md)
* [Práticas recomendadas de gestão de palavra-passe](active-directory-passwords-best-practices.md)
* [Como obter informações operacionais com relatórios de gestão de palavra-passe](active-directory-passwords-get-insights.md)
* [Gestão de palavras-passe perguntas mais frequentes](active-directory-passwords-faq.md)
* [Resolver problemas de gestão de palavra-passe](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)

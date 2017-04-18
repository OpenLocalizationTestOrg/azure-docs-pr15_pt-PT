<properties
    pageTitle="Definir políticas de expiração de palavra-passe no Azure Active Directory | Microsoft Azure"
    description="Saiba como consultar as políticas de expiração ou alterar expiração de palavra-passe de utilizador individualmente ou em massa para Azure Active directory as palavras-passe"
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


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Definir políticas de expiração de palavra-passe no Azure Active Directory

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Como um administrador global para um serviço de nuvem da Microsoft, pode utilizar o Microsoft Azure Active Directory módulo para Windows PowerShell para configurar as palavras-passe de utilizador não a expirar. Também pode utilizar cmdlets do Windows PowerShell para remover o nunca-expira a configuração, ou para ver qual o utilizador palavras-passe estão configuradas não expirar. Este artigo fornece ajuda para serviços em nuvem, como o Microsoft Intune e Office 365, depender do Microsoft Azure Active Directory para serviços de identidade e diretório.

  > [AZURE.NOTE] Apenas as palavras-passe para contas de utilizador que não estão a ser sincronizadas através da sincronização de diretórios podem ser configuradas não a expirar. Para mais informações acerca da sincronização de diretórios, consulte a lista de tópicos no [mapa do diretório de sincronização](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Para utilizar cmdlets do Windows PowerShell, primeiro tem de instalá-los.

## <a name="what-do-you-want-to-do"></a>O que pretende fazer?

- [Como verificar a política de expiração para uma palavra-passe](#how-to-check-expiration-policy-for-a-password)

- [Definir uma palavra-passe para expirar](#set-a-password-to-expire)

- [Definir uma palavra-passe para que não expirará](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Como verificar a política de expiração para uma palavra-passe

1.  Ligar ao Windows PowerShell com as suas credenciais de administrador da empresa.

2.  Efetue um dos seguintes procedimentos:

    - Para ver se palavra-passe um único utilizador está configurada para nunca expirar, execute o cmdlet seguinte utilizando o nome principal de utilizador (UPN) (por exemplo, aprilr@contoso.onmicrosoft.com) ou o ID de utilizador do utilizador que pretende verificar:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - Para ver a definição "Palavra-passe nunca expirar" para todos os utilizadores, execute o cmdlet seguinte:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Definir uma palavra-passe para expirar

1.  Ligar ao Windows PowerShell com as suas credenciais de administrador da empresa.

2.  Efetue um dos seguintes procedimentos:

    - Para definir a palavra-passe de um utilizador para que a palavra-passe irá expirar, execute o cmdlet seguinte utilizando o nome principal de utilizador (UPN) ou o ID de utilizador do utilizador:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - Para definir as palavras-passe de todos os utilizadores na organização para que expirem irá, utilize o cmdlet seguinte:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Definir uma palavra-passe para nunca expirar

1. Ligar ao Windows PowerShell com as suas credenciais de administrador da empresa.

2.  Efetue um dos seguintes procedimentos:

    - Para definir a palavra-passe de um utilizador para nunca expirar, execute o seguinte cmdlet, utilizando o nome principal de utilizador (UPN) ou o ID de utilizador do utilizador:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - Para definir as palavras-passe de todos os utilizadores na organização para nunca expirar, execute o cmdlet seguinte:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Próximos passos

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

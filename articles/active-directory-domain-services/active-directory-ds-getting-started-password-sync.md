<properties
    pageTitle="Serviços de domínio do Azure AD: Ativar a sincronização de palavra-passe | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Ativar a sincronização de palavra-passe para serviços de domínio do Azure AD
Em tarefas anteriores, ativada serviços de domínio do Azure AD do seu inquilino do Azure AD. A tarefa seguinte é ativar hashes credenciais necessárias para a autenticação NTLM e Kerberos sincronizar os serviços de domínio do Azure AD. Assim que a sincronização de credenciais está configurada, os utilizadores podem iniciar sessão domínio gerido utilizando as respetivas credenciais da empresa.

Os passos que envolvem são diferentes consoante se a sua organização tem um apenas na nuvem Azure AD de inquilinos ou estiver definido para sincronizar com o seu diretório no local com a ligação do Azure AD.

<br>

> [AZURE.SELECTOR]
- [Apenas na nuvem Azure AD de inquilinos](active-directory-ds-getting-started-password-sync.md)
- [Sincronizou o inquilino do Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarefa 5: Ativar a sincronização de palavra-passe para serviços de domínio AAD para um Azure apenas na nuvem inquilino AD
Serviços de domínio do AD Azure necessita de credenciais hashes num formato adequado para a autenticação NTLM e Kerberos, para autenticar os utilizadores no domínio gerido. A menos que ativar serviços de domínio do AAD do seu inquilino, Azure AD não gerar ou armazenar credenciais hashes no formato requerido para autenticação NTLM ou Kerberos. Óbvios por motivos de segurança, Azure AD também não armazenar quaisquer credenciais no formulário de texto simples. Por conseguinte, Azure AD não tem uma forma para gerar estes hashes credencial NTLM ou Kerberos com base nas credenciais existente dos utilizadores.

> [AZURE.NOTE] Se a sua organização tiver um apenas na nuvem inquilino do Azure AD, os utilizadores que têm de utilizar os serviços de domínio do Azure AD tem de alterar a palavra-passe.

Este processo de alterar palavra-passe faz com que a credencial hashes obrigatório pelos serviços de domínio do Azure AD para a autenticação Kerberos e NTLM a serem gerados no Azure AD. Quer pode expirar palavras-passe para todos os utilizadores no inquilino de que precisa de utilizar os serviços de domínio do Azure AD ou dar instruções a estes utilizadores para alterar a palavra-passe.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Ativar NTLM e Kerberos credencial hash. ª geração para um Azure apenas na nuvem inquilino de AD
Seguem-se instruções que precisa fornecer os utilizadores finais, para que estes possam alterar palavra-passe:

1. Navegue para a página de painel de acesso do Azure AD para a sua organização na [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selecione o separador **perfil** nesta página.

3. Clique no mosaico de **Alterar palavra-passe** nesta página.

    ![Crie uma rede virtual para serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Se não vir a opção **Alterar palavra-passe** na página do painel de acesso, certifique-se de que a sua organização tenha configurado [Gestão de palavras-passe no Azure AD](../active-directory/active-directory-passwords-getting-started.md).

4. Na página **Alterar palavra-passe** , escreva a palavra-passe existente (antiga) e, em seguida, escreva uma nova palavra-passe e confirmar. Clique em **Submeter**.

    ![Crie uma rede virtual para serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Após alterar a palavra-passe, a nova palavra-passe será utilizável nos serviços de domínio do Azure AD em breve. Depois de alguns minutos (normalmente, cerca de 20 minutos), pode iniciar sessão para computadores aderidos ao domínio gerido utilizando a palavra-passe alterada recentemente.

<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Como atualizar a sua própria palavra-passe](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Introdução à gestão de palavras-passe no Azure AD](../active-directory/active-directory-passwords-getting-started.md).

- [Ativar a sincronização de palavra-passe para serviços de domínio AAD para um Azure sincronizado inquilino AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrar um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Aderir a uma máquina de virtual do Windows para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aderir a uma máquina de virtual vermelho chapéu Enterprise Linux para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

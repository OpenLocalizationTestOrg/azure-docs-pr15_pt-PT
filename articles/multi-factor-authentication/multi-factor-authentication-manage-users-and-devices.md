<properties 
    pageTitle="Relatórios de autenticação Multifator Azure"
    description="Este exemplo descreve como alterar as definições de utilizador como forçar os utilizadores para que execute novamente o processo de prova de segurança."
    documentationCenter=""
    services="multi-factor-authentication"
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerir definições do utilizador com a autenticação Multifator do Azure na nuvem

Como administrador, pode gerir as seguintes definições de utilizador e do dispositivo.  

- [Exigir que os utilizadores selecionados fornecer novamente métodos de contacto](#require-selected-users-to-provide-contact-methods-again)
- [Eliminar utilizadores existentes palavras-passe de aplicação](#delete-users-existing-app-passwords)
- [Restaurar MFA em todos os dispositivos suspensos para um utilizador](#restore-mfa-on-all-suspended-devices-for-a-user)






Isto é útil se um computador ou dispositivo for perdido ou roubado ou tem de remover o acesso de utilizadores.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Exigir que os utilizadores selecionados fornecer novamente métodos de contacto

Isto vai definição força o utilizador para concluir o processo de registo novamente quando inicia a sessão. Tenha em atenção que não baseadas no browser aplicações irão continuar a trabalhar se o utilizador tiver palavras-passe de aplicação para os mesmos.  Pode eliminar as palavras-passe de aplicação de utilizadores, também selecionando **Eliminar todos os aplicação palavras-passe existentes geradas por utilizadores selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como exigir que os utilizadores de fornecer novamente métodos de contacto




1. Início de sessão no portal do Azure clássica.
2. No lado esquerdo, clique em do Active Directory.
3. Em, clique em diretório no diretório para o utilizador que pretende para exigir o registo para fornecer novamente o seu método de contacto.
4. Na parte superior, clique em utilizadores.
5. Na parte inferior da página, clique em Gerir autenticação Multifator Isto irá abrir a página de autenticação multifator.
6. Localize o utilizador que pretende gerir e coloque uma marca de verificação na caixa localizada junto ao nome. Poderá ter alterar a vista na parte superior.
7. Isto irá ativar a ligação **Gerir definições de utilizador** no lado direito. Clique na mesma.
8. Coloca uma marca de verificação **exigir que os utilizadores selecionados fornecer novamente métodos de contacto**.
![Fornecer métodos de contacto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Clique em Guardar.
11. Clique em Fechar

## <a name="delete-users-existing-app-passwords"></a>Eliminar utilizadores existentes palavras-passe de aplicação

Este procedimento elimina todas as palavras-passe de aplicação que um utilizador tenha criado. Aplicações não baseado no browser que foram associadas com estas palavras-passe de aplicação deixarão de funcionar até é criada uma nova palavra-passe de aplicação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como eliminar utilizadores existentes palavras-passe de aplicação

1. Início de sessão no portal do Azure clássica.
2. No lado esquerdo, clique em do Active Directory.
3. Em, clique em diretório no diretório para o utilizador que pretende eliminar palavras-passe de aplicação para.
4. Na parte superior, clique em utilizadores.
5. Na parte inferior da página, clique em Gerir autenticação Multifator Isto irá abrir a página de autenticação multifator.
6. Localize o utilizador que pretende gerir e coloque uma marca de verificação na caixa localizada junto ao nome. Poderá ter alterar a vista na parte superior.
7. Isto irá ativar a ligação **Gerir definições de utilizador** no lado direito. Clique na mesma.
8. Coloca uma marca de verificação **Eliminar todos os aplicação palavras-passe existentes geradas por utilizadores selecionados**.
![Eliminar palavras-passe de aplicação](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Clique em Guardar.
10. Clique em Fechar.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar MFA em todos os dispositivos passados para um utilizador

Os administradores têm a capacidade de restaurar a autenticação Multifator no utilizadores dispositivos e browsers. Quando este procedimento, Isto removerá a lembrar MFA a partir de todos os dispositivos do utilizador e browsers e o utilizador vai ser necessário utilizar MFA ao iniciar sessão na próxima vez.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como restaurar MFA em todos os dispositivos suspensos para um utilizador

1. Início de sessão no portal do Azure clássica.
2. No lado esquerdo, clique em do Active Directory.
3. Em, clique em diretório no diretório para o utilizador que pretende restaurar mfa no.
4. Na parte superior, clique em utilizadores.
5. Na parte inferior da página, clique em Gerir autenticação Multifator Isto irá abrir a página de autenticação multifator.
6. Localize o utilizador que pretende gerir e coloque uma marca de verificação na caixa localizada junto ao nome. Poderá ter alterar a vista na parte superior.
7. Isto irá ativar a ligação **Gerir definições de utilizador** no lado direito. Clique na mesma.
8. Colocar uma marca de verificação em **restaurar a autenticação multifator em todos os dispositivos passadas**
![eliminar palavras-passe de aplicação](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Clique em Guardar.
10. Clique em Fechar.

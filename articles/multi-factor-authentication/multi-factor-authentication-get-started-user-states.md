<properties 
    pageTitle="Estados de utilizador do Microsoft Azure Multifator autenticação"
    description="Saiba mais sobre os Estados de utilizador no Azure MFA."
    services="multi-factor-authentication"
    documentationCenter=""
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

# <a name="user-states-in-azure-multi-factor-authentication"></a>Estados de utilizador no Azure autenticação Multifator

Contas de utilizador no Azure a autenticação Multifator tem os seguintes Estados distintos três:

Estado | Descrição |Aplicações de browser não afectadas| Notas
:-------------: | :-------------: |:-------------: |:-------------: |
Desativado | O estado de predefinido para um novo utilizador não inscrito na autenticação multifator.|N|O utilizador não está a utilizar autenticação multifator.
Ativado |O utilizador foi registado na autenticação multifator.|Não.  Eles continuam a funcionar até que o processo de registo seja concluído.|O utilizador está ativado, mas não concluiu o processo de registo. Será pedido para concluir o processo na próxima iniciar sessão.
Impostas|O utilizador foi registado e tem de concluir o processo de registo para utilizar a autenticação multifator.|Sim.  As aplicações requerem palavras-passe de aplicação. | O utilizador poderá ou poderá não ter sido concluída registo. Se terem concluído o processo de registo, em seguida, estão a utilizar autenticação multifator. Caso contrário, será pedido ao utilizador para concluir o processo na próxima iniciar sessão.

## <a name="changing-a-user-state"></a>Alterar o estado de um utilizador
Alterações de estado de utilizadores consoante permissão ou não foi configuração para MFA e se o utilizador tem concluir o processo.  Quando ativar MFA para um utilizador, os utilizadores irão alterar estado de desativado para ativado.  Quando o utilizador, cujo estado ter sido alterado para ativado, a iniciar sessão e concluir o processo, o seu estado será alterado para imposta.  

### <a name="to-view-a-users-state"></a>Para ver o estado de um utilizador
--------------------------------------------------------------------------------
1.  Inicie sessão no **portal clássica Azure** como administrador.
2.  No lado esquerdo, clique em **Do Active Directory**.
3.  Em, clique em **diretório** no diretório para o utilizador que pretende ativar.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **utilizadores**.
5.  Na parte inferior da página, clique em **Gerir Auth Multifator**.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isto vai abrir num novo separador do browser.  Será capaz de ver o estado de utilizadores.
![Clique no diretório](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Para alterar o estado de desativado para ativado
1.  Inicie sessão no **portal clássica Azure** como administrador.
2.  No lado esquerdo, clique em **Do Active Directory**.
3.  Em, clique em **diretório** no diretório para o utilizador que pretende ativar.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **utilizadores**.
5.  Na parte inferior da página, clique em **Gerir Auth Multifator**.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isto vai abrir num novo separador do browser.  Localize o utilizador que pretende ativar para autenticação multifator. Poderá ter alterar a vista na parte superior. Certifique-se de que o estado é **desativado.** 
 ![Permitir que o utilizador](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque um **Verificar** na caixa junto ao respetivo nome.
7.  No lado direito, clique em **Ativar**.
![Permitir que o utilizador](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Ativar a autenticação multifator**.
![Permitir que o utilizador](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Deverá Repare que o estado do utilizador mudou de **desativado** para **ativado**.
![Permitir que os utilizadores](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Depois de ter ativado os seus utilizadores, é aconselhável notificar por correio eletrónico.  -Lo também deve informá-los como pode utilizar as aplicações não baseadas no browser para evitar a ser bloqueada.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Para alterar o estado de ativado/impostas para desativado
1.  Inicie sessão no **portal clássica Azure** como administrador.
2.  No lado esquerdo, clique em **Do Active Directory**.
3.  Em, clique em **diretório** no diretório para o utilizador que pretende ativar.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **utilizadores**.
5.  Na parte inferior da página, clique em **Gerir Auth Multifator**.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isto vai abrir num novo separador do browser.  Localize o utilizador que pretende desativar. Poderá ter alterar a vista na parte superior. Certifique-se de que o estado é um dos **ativado** ou **impostas.**
7.  Coloque um **Verificar** na caixa junto ao respetivo nome.
7.  No lado direito, clique em **desativar**.
![Desativar o utilizador](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Vai ser-lhe para o confirmar.  Clique em **Sim**.
![Desativar o utilizador](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Em seguida, deverá ver que foi efetuada com êxito.  Clique em **feche.** 
 ![Desativar utilizador](./media/multi-factor-authentication-get-started-user-states/userstate4.png)

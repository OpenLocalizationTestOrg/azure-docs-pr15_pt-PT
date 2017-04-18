<properties
    pageTitle="Ligação do Azure AD: Os passos seguintes e como gerir a ligação do Azure AD | Microsoft Azure"
    description="Saiba como pode aumentar a configuração predefinida e tarefas operacionais para ligação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Próximos passos e como gerir a ligação do Azure AD
São avançadas operacionais tópicos que lhe permitem personalizar Azure Active Directory ligar para satisfazer necessidades e requisitos da sua organização.  

## <a name="add-additional-sync-administrators"></a>Adicionar administradores de sincronização adicionais
Por predefinição apenas o utilizador que efetuou a instalação e administradores local poderão gerir o motor de sincronização instalada. Para mais pessoas possam aceder e gerir o motor de sincronização, localize o grupo com o nome ADSyncAdmins no servidor local e adicioná-los para este grupo.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Atribuir licenças a utilizadores do Azure AD Premium e mobilidade Enterprise

Agora que os utilizadores tenham sido sincronizados na nuvem, terá de atribua-lhes uma licença para que pode começar a utilizar com aplicações na nuvem como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para atribuir uma Azure AD Premium ou licença do Enterprise mobilidade conjunto de aplicações
--------------------------------------------------------------------------------
1. Início de sessão no portal do Azure como administrador.
2. No lado esquerdo, selecione **Do Active Directory**.
3. Na página do Active Directory, faça duplo clique no directório de que tem os utilizadores que pretende ativar.
4. Na parte superior da página diretório, selecione **licenças**.
5. Na página licenças, selecione o Active Directory Premium ou para o conjunto de aplicações do Enterprise mobilidade e, em seguida, clique em **atribuir**.
6. Na caixa de diálogo, selecione os utilizadores que pretende atribuir licenças a e, em seguida, clique no ícone de marca de verificação para guardar as alterações.


## <a name="verifying-the-scheduled-synchronization-task"></a>Verificar a tarefa de sincronização agendada
Se pretender verificar o estado de uma sincronização que pode fazê-lo ao selecionar no portal do Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para verificar a tarefa de sincronização agendada
--------------------------------------------------------------------------------
1. Início de sessão no portal do Azure como administrador.
2. No lado esquerdo, selecione **Do Active Directory**.
3. Na página do Active Directory, faça duplo clique no directório de que tem os utilizadores que pretende ativar.
4. Na parte superior da página diretório, selecione **Integração de diretórios**.
5. Na integração com nota do local active directory a hora da última sincronização.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Iniciar uma tarefa de sincronização agendada
Se o que precisa para executar uma tarefa de sincronização pode fazê-lo executando o Assistente de ligação do Azure AD novamente.  Terá de fornecer as suas credenciais do Azure AD.  No Assistente de, selecione a tarefa de **Personalizar as opções de sincronização** e clique em seguinte através do assistente. No final, certifique-se de que a caixa de **Iniciar o processo de sincronização assim que a configuração inicial conclui** está selecionada.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para obter mais informações sobre a ligação do Azure AD sync: Scheduler, consulte o artigo [Azure AD ligar Scheduler](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis na ligação do Azure AD
Após a sua instalação inicial da ligação do Azure AD, pode sempre iniciar o assistente novamente a partir do Azure AD Connect iniciar página ou ambiente de trabalho do atalho.  Vai notar que aceder novamente o assistente fornece alguns novas opções no formulário de tarefas adicionais.  

A tabela seguinte fornece um resumo destas tarefas e uma breve descrição em cada um deles.

![Aderir a regra](./media/active-directory-aadconnect-whats-next/addtasks.png)


Tarefa adicional | Descrição
------------- | ------------- |
Ver o cenário seleccionado  |Permite-lhe ver a sua solução de ligação do Azure AD atual.  Isto inclui definições gerais, directórios sincronizados, as definições de sincronização, etc.
Personalizar as opções de sincronização | Permite-lhe alterar a configuração atual, incluindo a adição de florestas do Active Directory adicionais para a configuração ou activação de sincronização de opções, tais como o utilizador, grupo, dispositivo ou palavra-passe de escrita anterior.
Ativar o modo de transição |  Esta opção permite-lhe a informação de fase mais tarde será sincronizada mas nada será exportado Azure AD ou do Active Directory.  Esta opção permite-lhe pré-visualizar as sincronizações antes de ocorrerem.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).

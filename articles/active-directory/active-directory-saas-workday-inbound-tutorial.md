<properties 
    pageTitle="Tutorial: Configuração do dia de trabalho para a sincronização de entrada | Microsoft Azure" 
    description="Saiba como utilizar DIATRABALHO como origem de dados de identidade do Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Tutorial: Configuração do dia de trabalho para a sincronização de entrada


Objectivo deste tutorial é mostrar-lhe os passos que precisa para efetuar no dia de trabalho e Azure AD para importar as pessoas do dia de trabalho para o Azure AD. 

O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure AD Premium válida
-   Um inquilino no dia de trabalho
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1. Ativar a integração de aplicação para o dia de trabalho 


2. Criação de um utilizador do sistema de integração 


3. Criar um grupo de segurança 


4. Atribuir o utilizador da integração de sistema para o grupo de segurança 


5. Configurar opções de grupo de segurança 


6. Ativar as alterações de política de segurança 


7. Configurar o utilizador a importação no Azure AD 



##<a name="enabling-the-application-integration-for-workday"></a>Ativar a integração de aplicação para o dia de trabalho
  
O objectivo desta secção é destacar como ativar a integração de aplicação para o dia de trabalho.

### <a name="steps"></a>Passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Adicionar aplicação")

  
5. Na caixa de pesquisa, escreva **DIATRABALHO**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Adicionar uma aplicação a partir do gallerry")

6. No painel de resultados, selecione DIATRABALHO e, em seguida, clique em Concluir para adicionar a aplicação.

    ![Galeria de aplicação] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galeria de aplicação")





## <a name="creating-an-integration-system-user"></a>Criação de um utilizador do sistema de integração

### <a name="steps"></a>Passos:


1. Na **DIATRABALHO estar**, introduza criar utilizador na caixa de pesquisa e, em seguida, clique em **Criar integração com o sistema de utilizador**. 

    ![Para criar o utilizador] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Para criar o utilizador")



2. Conclua a tarefa de **Criar integração com o sistema de utilizador** , fornecendo um nome de utilizador e palavra-passe para um novo utilizador do sistema de integração.  Deixe o exigir nova palavra-passe na próxima iniciar sessão opção desmarcado, uma vez que este utilizador irá iniciar sessão através de programação. Deixe os minutos de tempo limite de sessão com o valor predefinido de 0, o que irá impedir sessões o utilizador de temporização saída prematuramente. 

    ![Criar utilizador da integração de sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Criar utilizador da integração de sistema")
 




## <a name="creating-a-security-group"></a>Criar um grupo de segurança

Para o cenário descrito neste tutorial, tem de criar um grupo de segurança do sistema de integração sem restrições e atribuir ao utilizador.

### <a name="steps"></a>Passos:

1. Introduza criar grupo de segurança na caixa de pesquisa e, em seguida, clique em **Criar grupo de segurança**. 

    ![Grupo CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Grupo CreateSecurity")
 

2. Conclua a tarefa de criar o grupo de segurança.  Grupo de segurança do sistema de integração selecione — sem restrições na lista pendente Tipo de grupo de segurança em tenant, para criar um grupo de segurança a que os membros serão explicitamente adicionados. 

    ![Grupo CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Grupo CreateSecurity")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Atribuir o utilizador da integração de sistema para o grupo de segurança

### <a name="steps"></a>Passos:


1. Introduza Editar grupo de segurança na caixa de pesquisa e, em seguida, clique em **Editar grupo de segurança**. 

    ![Editar grupo de segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Editar grupo de segurança")
 
 

2. Procure e selecione o novo grupo de segurança de integração pelo nome. 

    ![Editar grupo de segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Editar grupo de segurança")

 

3. Adicione o novo utilizador da integração de sistema para o novo grupo de segurança. 

    ![Grupo de segurança do sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Grupo de segurança do sistema")  




## <a name="configuring-security-group-options"></a>Configurar opções de grupo de segurança

Neste passo, conceder as novas permissões de grupo de segurança para **obter** e **colocar** operações em objetos protegidos pelas seguintes políticas de segurança do domínio:

- Aprovisionamento de conta externa

- Dados de trabalho: Relatórios de trabalho público

- Dados de trabalho: Todas as posições

- Dados de trabalho: Corrente informações de pessoal

- Dados de trabalho: Empresas título no perfil de trabalho

 
### <a name="steps"></a>Passos:

1. Introduza as políticas de segurança de domínio na caixa de pesquisa e, em seguida, clique na ligação, políticas de segurança do domínio para área funcional.  

    ![Políticas de segurança do domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Políticas de segurança do domínio")  
 

2. Procurar em sistema e selecionar a área funcional do **sistema** .  Clique em **OK**.  

    ![Políticas de segurança do domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Políticas de segurança do domínio")  


3. Na lista de políticas de segurança para a área funcional do sistema, expanda a administração de segurança e selecione a política de segurança de domínio externo aprovisionamento de conta.  

    ![Políticas de segurança do domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Políticas de segurança do domínio")  


4. Clique em **Editar permissões**e, em seguida, na página **Editar permissões de**caixa de diálogo, adicione o novo grupo de segurança para a lista de grupos de segurança com permissões de integração de **obter** e **colocar** . 

    ![Permissões de edição] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Permissões de edição")  

 

5. Repita o passo 1, acima, para regressar ao ecrã para selecionar áreas funcionais, e desta vez, procure pessoal, selecionar a área funcional Staffing e clique em **OK**.

    ![Políticas de segurança do domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Políticas de segurança do domínio")  
 

6. Na lista de políticas de segurança para a área funcional Staffing, expanda trabalhador dados: Staffing e repita o passo 4 acima para cada um destes restante políticas de segurança:

     - Dados de trabalho: Relatórios de trabalho público

     - Dados de trabalho: Todas as posições

     - Dados de trabalho: Corrente informações de pessoal

     - Dados de trabalho: Empresas título no perfil de trabalho


    ![Políticas de segurança do domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Políticas de segurança do domínio")  







## <a name="activating-security-policy-changes"></a>Ativar as alterações de política de segurança

### <a name="steps"></a>Passos:

1. Introduza ativar na caixa de pesquisa e, em seguida, clique na ligação, ativar alterações de política de segurança pendentes. 

    ![Ativar] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Ativar") 
 

2. Começar a tarefa de ativar alterações de política de segurança pendentes introduzindo um comentário para efeitos de auditoria e, em seguida, clique em **OK**. 

    ![Ativar pendentes segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Ativar pendentes segurança")   
 

3. Concluir a tarefa no ecrã seguinte clicando na caixa de verificação rotulada confirmar e, em seguida, clique em **OK**. 

    ![Ativar pendentes segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Ativar pendentes segurança")  





## <a name="configuring-user-import-in-azure-ad"></a>Configurar o utilizador a importação no Azure AD

O objectivo desta secção é destacar como configurar o Azure AD para importar as pessoas do dia de trabalho.


### <a name="steps"></a>Passos:


1. Na página de integração de aplicação **DIATRABALHO** , clique em **Importar de utilizador de configurar** para abrir a caixa de diálogo **Configurar aprovisionamento** .


2. Na página de **definições e as credenciais de administrador** , execute os seguintes passos e, em seguida, clique em **seguinte**: 

    ![Definições e as credenciais de administrador] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Definições e as credenciais de administrador")  
 
    um. Na DIATRABALHO administração utilizador nome caixa de texto, escreva o nome do utilizador ter criado a criar uma secção de utilizador do sistema de integração de.

    b. Na caixa de DIATRABALHO administração palavra-passe texto, escreva a palavra-passe do utilizador ter criado a criar uma secção de utilizador do sistema de integração de.

    c. Na DIATRABALHO inquilino URL caixa de texto, escreva o URL ou o inquilino do dia de trabalho.


3. Na página **testar a ligação** , clique em **Iniciar o teste** para confirmar a conectividade e, em seguida, clique em **seguinte**. 

    ![Testar ligação] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Testar ligação")  
 

4. Na página de opções de **aprovisionamento** , clique em **seguinte**. 

    ![Opções de aprovisionamento] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opções de aprovisionamento")


5. Na caixa de diálogo **Iniciar o aprovisionamento** , clique em **Concluir**. 

    ![Iniciar o aprovisionamento] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Iniciar o aprovisionamento")
 

Agora pode aceda à secção **utilizadores** e verifique se o utilizador DIATRABALHO tenha sido importado.



## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

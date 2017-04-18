<properties 
    pageTitle="Tutorial: Configuração do dia de trabalho para a sincronização de entrada | Microsoft Azure" 
    description="Saiba como utilizar a sincronização de entrada com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Tutorial: Configuração do dia de trabalho para a sincronização de entrada
>[AZURE.NOTE]Azure Active Directory (AD) Premium está disponível para os clientes na China que utilizam a nível mundial instância do Azure AD.    
Azure AD Premium atualmente não é suportada no serviço Microsoft Azure operado pela 21Vianet na China.    

Objectivo deste tutorial é mostrar-lhe os passos que precisa para efetuar no dia de trabalho e no Microsoft Azure AD para importar as pessoas do dia de trabalho para a Microsoft Azure AD.    
 O cenário descrito neste tutorial assume que já tem os seguintes itens:  

-   Uma subscrição do Azure válida  
-   Um inquilino no dia de trabalho  

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:  

1.  Ativar a integração de aplicação para o dia de trabalho  
2.  Criação de um utilizador do sistema de integração  
3.  Criar um grupo de segurança  
4.  Atribuir o utilizador da integração de sistema para o grupo de segurança  
5.  Configurar opções de grupo de segurança  
6.  Ativar as alterações de política de segurança  
7.  Configurar o utilizador a importação no Microsoft Azure AD  

##<a name="enabling-the-application-integration-for-workday"></a>Ativar a integração de aplicação para o dia de trabalho

O objectivo desta secção é destacar como ativar a integração de aplicação para a equipa de vendas.    

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Para ativar a integração da aplicação para DIATRABALHO, execute os seguintes passos:

1.  No Portal de gestão do Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.    

    ![O Active Directory] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "O Active Directory")  

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.    

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.    

    ![Aplicações] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Aplicações")  

4.  Para abrir a **Galeria de aplicação**, clique em **Adicionar uma aplicação**e, em seguida, clique em **Adicionar uma aplicação para a minha organização utilizar**.    

    ![o que pretende fazer?] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "o que pretende fazer?")  

5.  Na **caixa de pesquisa**, escreva **DIATRABALHO**.    

    ![DIATRABALHO] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "DIATRABALHO")  

6.  No painel de resultados, selecione **DIATRABALHO**e, em seguida, clique em **Concluir** para adicionar a aplicação.    

    ![DIATRABALHO] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "DIATRABALHO")  

##<a name="creating-an-integration-system-user"></a>Criação de um utilizador do sistema de integração

1.  Na **DIATRABALHO estar**, introduza a **Criar utilizador** na caixa de pesquisa e, em seguida, clique na ligação, **Criar integração com o sistema de utilizador**.     

    ![Criar utilizador] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "Criar utilizador")  

2.  Conclua a tarefa de criar utilizador do sistema de integração, fornecendo um nome de utilizador e palavra-passe para um novo utilizador do sistema de integração.  Deixe o exigir nova palavra-passe na próxima iniciar sessão opção desmarcado, uma vez que este utilizador irá iniciar sessão através de programação.    
    Deixe os minutos de tempo limite de sessão com o valor predefinido de 0, o que irá impedir sessões o utilizador de temporização saída prematuramente.    

    ![Criar utilizador da integração de sistema] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Criar utilizador da integração de sistema")  

##<a name="creating-a-security-group"></a>Criar um grupo de segurança

Para o cenário descrito neste tutorial, tem de criar um grupo de segurança do sistema de integração sem restrições e atribuir ao utilizador.    

1.  Introduza criar grupo de segurança na caixa de pesquisa e, em seguida, clique na ligação, criar grupo de segurança.     

    ![Grupo CreateSecurity] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "Grupo CreateSecurity")  

2.  Conclua a tarefa de criar o grupo de segurança.  Grupo de segurança do sistema de integração selecione — sem restrições na lista pendente Tipo de grupo de segurança em tenant, para criar um grupo de segurança a que os membros serão explicitamente adicionados.     

    ![Grupo CreateSecurity] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "Grupo CreateSecurity")  

##<a name="assigning-the-integration-system-user-to-the-security-group"></a>Atribuir o utilizador da integração de sistema para o grupo de segurança

1.  Introduza Editar grupo de segurança na caixa de pesquisa e, em seguida, clique na ligação, **Editar grupo de segurança**.     

    ![Editar grupo de segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Editar grupo de segurança")  

2.  Procurar e selecione o novo grupo de segurança de integração pelo nome    

    ![Editar grupo de segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Editar grupo de segurança")  

3.  Adicione o novo utilizador da integração de sistema para o novo grupo de segurança.       

    ![Grupo de segurança do sistema] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "Grupo de segurança do sistema")  

##<a name="configuring-security-group-options"></a>Configurar opções de grupo de segurança

Neste passo, conceder as novas permissões de grupo de segurança para operações de obter e colocar sobre os objectos protegidos pelas seguintes políticas de segurança do domínio:  

-   Aprovisionamento de conta externa  
-   Dados de trabalho: Relatórios de trabalho público  
-   Dados de trabalho: Todas as posições  
-   Dados de trabalho: Corrente informações de pessoal  
-   Dados de trabalho: Empresas título no perfil de trabalho  

&nbsp;  

1.  Introduza as políticas de segurança de domínio na caixa de pesquisa e, em seguida, clique na ligação, políticas de segurança do domínio para área funcional.     

    ![Políticas de segurança do domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Políticas de segurança do domínio")  

2.  Procurar em sistema e selecionar a área funcional do sistema.  Clique no botão rotulado, OK.     

    ![Políticas de segurança do domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Políticas de segurança do domínio")  

3.  Na lista de políticas de segurança para a área funcional do sistema, expanda a administração de segurança e selecione a política de segurança de domínio externo aprovisionamento de conta.     

    ![Políticas de segurança do domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Políticas de segurança do domínio")  

4.  Clique no botão Editar permissões e, em seguida, no ecrã editar permissões, adicionar o novo grupo de segurança para a lista de grupos de segurança com permissões de integração de Get e colocar.     

    ![Permissões de edição] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Permissões de edição")  

5.  Repita o passo 1, acima, para regressar ao ecrã para selecionar áreas funcionais, e desta vez, procure pessoal, selecione a área de funcional Staffing e clique no botão OK rotulado.    

    ![Políticas de segurança do domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Políticas de segurança do domínio")  

6.  Na lista de políticas de segurança para a área funcional Staffing, expanda trabalhador dados: Staffing e repita o passo 4 acima para cada um destes restante políticas de segurança:    

    -   Dados de trabalho: Relatórios de trabalho público  
    -   Dados de trabalho: Todas as posições  
    -   Dados de trabalho: Corrente informações de pessoal  
    -   Dados de trabalho: Empresas título no perfil de trabalho    

    ![Políticas de segurança do domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Políticas de segurança do domínio")  

##<a name="activating-security-policy-changes"></a>Ativar as alterações de política de segurança

1.  Introduza ativar na caixa de pesquisa e, em seguida, clique na ligação, ativar alterações de política de segurança pendentes.    

    ![Ativar] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Ativar")  

2.   Começar a tarefa de ativar alterações de política de segurança pendentes introduzindo um comentário para efeitos de auditoria e, em seguida, clicando no botão rotulado, OK.      

    ![Ativar pendentes segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Ativar pendentes segurança")  

3.  Conclua a tarefa no ecrã seguinte clicando na caixa de verificação rotulada confirmar e clicar no botão rotulado, OK.     

    ![Ativar pendentes segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Ativar pendentes segurança")  

##<a name="configuring-user-import-in-microsoft-azure-ad"></a>Configurar o utilizador a importação no Microsoft Azure AD

O objectivo desta secção é destacar como configurar o Microsoft Azure AD para importar as pessoas do dia de trabalho.    

###<a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>Para configurar a importação do utilizador no Microsoft Azure AD, execute os seguintes passos:

1.  Na página de integração de aplicação **DIATRABALHO** , clique em **Importar de utilizador de configurar** para abrir a caixa de diálogo **Configurar aprovisionamento** .    

2.  Na página de **definições e as credenciais de administrador** , execute os seguintes passos e, em seguida, clique em seguinte:    

    ![Definições e as credenciais de administrador] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Definições e as credenciais de administrador")    

    1.  Na caixa de texto **nome de utilizador de administração do dia de trabalho** , escreva o nome do utilizador que criou na secção de [criação de um utilizador do sistema de integração](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    2.  Na caixa de texto **palavra-passe de administrador de DIATRABALHO** , escreva a palavra-passe do utilizador que criou na secção de [criação de um utilizador do sistema de integração](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    3.  Na caixa de texto **DIATRABALHO inquilino URL** , escreva o URL ou o inquilino do dia de trabalho.    

3.  Na página **testar a ligação** , clique em **Iniciar o teste** para confirmar a conectividade e, em seguida, clique em **seguinte**.    

    ![Testar ligação] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Testar ligação")  

4.  Na página de **Opções de aprovisionar** , clique em **seguinte**.    

    ![Opções de aprovisionamento] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Opções de aprovisionamento")  

5.  Na caixa de diálogo **Iniciar o aprovisionamento** , clique em **Concluir**.    

    ![Iniciar o aprovisionamento] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Iniciar o aprovisionamento")  

Agora pode aceda à secção **utilizadores** e verifique se o utilizador DIATRABALHO tenha sido importado.    

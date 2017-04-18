<properties
    pageTitle="Introdução com a sincronização de dados de bases de dados SQL"
    description="Neste tutorial ajuda-na começar a trabalhar com a sincronização de dados do SQL Azure (pré-visualização)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Introdução com a sincronização de dados do Azure SQL (pré-visualização)
Neste tutorial, aprendeu os conceitos básicos da sincronização de dados SQL Azure através do Portal clássica Azure.

Neste tutorial assume mínima experiência prévia com o SQL Server e base de dados do SQL Azure. Neste tutorial, criar um grupo de sincronização híbrido (SQL Server e instâncias de base de dados SQL) totalmente configurada e sincronizar na agenda que definiu.

> [AZURE.NOTE] A documentação técnica concluída definido para a sincronização de dados do SQL Azure, anteriormente localizados no MSDN, está disponível como um PDF. Transferi-lo [aqui](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Passo 1: Ligar à base de dados Azure SQL

1. Inicie sessão no [Portal clássica](http://manage.windowsazure.com).

2. No painel esquerdo, clique em **Bases de dados SQL** .

3. Clique em **SINCRONIZAR** na parte inferior da página. Quando clica em SINCRONIZAR, é apresentada uma lista que mostra as coisas que pode adicionar - **Novo grupo de sincronização** e **Novo agente de sincronização**.

4. Para iniciar o Assistente do agente de sincronização de dados SQL novo, clique em **Novo agente de sincronização**.

5. Se não tiver adicionado um agente anteriormente, **clique em transferi-lo aqui**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Passo 2: Adicionar um agente do cliente
Este passo é necessário apenas se vai ter uma base de dados no local do SQL Server incluído no seu grupo de sincronização. Se o seu grupo de sincronização tem apenas as instâncias de base de dados SQL, avance para o passo 4.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Passo 2: Instale o software necessário
Certifique-se de que tem os seguintes componentes instalados no computador onde instalar o agente de cliente.

- **.NET framework 4.0**

 Instale o .NET Framework 4.0 a partir de [aqui](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 CLR tipos de sistema (x86)**

 Instalar os Microsoft SQL Server 2008 R2 SP1 sistema CLR tipos (x86) a partir de [aqui](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 partilhado objectos de gestão de (x86)**

 Instalar os Microsoft SQL Server 2008 R2 SP1 partilhado gestão objetos (x86) a partir de [aqui](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Passo 2b: instalar uma nova agente de cliente

Siga as instruções em [instalar um agente do cliente (sincronização de dados SQL)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) para instalar o agente.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Passo 2c: concluir o Assistente de novo agente de sincronização de dados SQL

1.  Regressar ao Assistente de novo agente de sincronização de dados SQL.
2.  Atribua o agente de um nome significativo.
3.  Na lista pendente, selecione a **região** (Centro de dados) a este agente do anfitrião.
4.  Na lista pendente, selecione a **subscrição** para este agente do anfitrião.
5.  Clique na seta para a direita.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Passo 3: Registar uma base de dados do SQL Server com o agente de cliente

Após ter instalado o agente de cliente, registe-se cada base de dados do SQL Server no local que pretende incluir num grupo de sincronização com o agente de.
Para registar uma base de dados com o agente de, siga as instruções em [Registe-se uma base de dados do SQL Server com um agente de cliente](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## <a name="step-4-create-a-sync-group"></a>Passo 4: Criar um grupo de sincronização


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Passo 4: iniciar o Assistente de novo grupo de sincronização

1.  Regressar ao [Portal clássica](http://manage.windowsazure.com).
2.  Clique em **bases de dados SQL**.
3.  Clique em **Adicionar SINCRONIZAR** na parte inferior da página, em seguida, selecione novo grupo de sincronização a partir do nível.

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Passo 4. oB: introduza as definições básicas


1.  Introduza um nome significativo para o grupo de sincronização.
2.  Na lista pendente, selecione a **região** (Centro de dados) para alojar deste grupo de sincronização.
3. Clique na seta para a direita.

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Passo 4c: definir o concentrador de sincronização

1. Na lista pendente, selecione a instância de base de dados SQL para servir como o concentrador de grupo de sincronização.
2. Introduza as credenciais para esta instância de base de dados SQL - **CONCENTRADOR de nome de utilizador** e **Palavra-passe do CONCENTRADOR**.
3. Aguarde para a sincronização de dados SQL confirmar o nome de utilizador e palavra-passe. Irá ver uma marca de verificação verde aparecem à direita da palavra-passe quando as credenciais são confirmadas.
4. Na lista pendente, selecione a política de **Resolução de conflitos** .

 **Concentrador Wins** - qualquer alteração escrita para a base de dados escrita concentrador as bases de dados de referência, substituir alterações na mesma referenciam o registo de base de dados. Funcional, isto significa que a primeira alteração escrita ao concentrador é propagado às outras bases de dados.


 **Cliente Wins** - as alterações do escritas ao concentrador são substituídos por alterações nas bases de dados de referência. Funcional, isto significa que a última alteração escrita ao concentrador é aquele mantidos e propagado às outras bases de dados.

5.  Clique na seta para a direita.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Passo 4d: adicionar uma base de dados de referência


Repita este passo para cada base de dados adicional que pretende adicionar ao grupo de sincronização.

1. Na lista pendente, selecione a base de dados para adicionar.

    Bases de dados na lista pendente incluem ambas as bases de dados do SQL Server que foram registadas com o agente e instâncias de base de dados SQL.
2.  Introduza as credenciais para esta base de dados - **nome de utilizador** e **palavra-passe**.
3.  Na lista pendente, selecione a **DIREÇÃO de SINCRONIZAÇÃO** para esta base de dados.

    **Bidirecional** - alterações na base de dados referência são escritas à base de dados concentrador e as alterações à base de dados concentrador são escritas à base de dados de referência.

    **Sincronização do concentrador** - a base de dados recebe atualizações da concentrador. Não envia alterações ao concentrador.

    **Sincronizar com o concentrador** - a base de dados envia atualizações para o concentrador. Alterações no centro do não são escritas para esta base de dados.

4.  Para concluir a criação do grupo de sincronização, clique na marca de verificação no canto inferior direito do assistente. Aguarde que a sincronização de dados SQL confirmar as credenciais. Uma marca de verificação verde indica que as credenciais são confirmadas.

5.  Clique na marca de verificação uma segunda vez. Desta forma, regressa à página de **SINCRONIZAÇÃO** em bases de dados SQL. Agora está listado deste grupo de sincronização com o seu grupos de sincronização e agentes.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Passo 5: Definir os dados para sincronizar

Sincronização de dados SQL Azure permite-lhe selecionar tabelas e colunas para sincronizar. Se também pretende filtrar uma coluna para que apenas linhas com valores específicos (idade, tais como > = 65) sincronizar, utilize o portal de sincronização de dados do SQL Azure e a documentação sobre em selecione a tabelas, colunas e linhas para sincronizar para definir os dados para sincronizar.

1.  Regressar ao [Portal clássica](http://manage.windowsazure.com).
2.  Clique em **bases de dados SQL**.
3.  Clique no separador **SINCRONIZAÇÃO** .
4.  Clique no nome deste grupo de sincronização.
5.  Clique no separador **Regras de SINCRONIZAÇÃO** .
6.  Selecione a base de dados que quer fornecer o esquema do grupo de sincronização.
7.  Clique na seta para a direita.
8.  Clique em **esquema de ATUALIZAÇÃO**.
9.  Para cada tabela na base de dados, selecione as colunas para incluir as sincronizações.
    - Não não possível selecionar colunas com tipos de dados não suportadas.
    - Se estiverem selecionadas sem colunas numa tabela, a tabela não está incluída no grupo de sincronização.
    - Para selecionar/anular a seleção de todas as tabelas, clique em SELECIONAR na parte inferior do ecrã.
10. Clique em **Guardar**e, em seguida, aguarde para o grupo de sincronização concluir o aprovisionamento.
11. Para voltar para a página principal de sincronização de dados, clique na seta anterior no canto superior esquerdo do ecrã (por cima do nome do grupo sincronização).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Passo 6: Configurar o seu grupo de sincronização

Pode sempre sincronizar um grupo de sincronização, clicando em SINCRONIZAR na parte inferior da página de destino de sincronização de dados.
Para sincronizar com base numa agenda, pode configura o grupo de sincronização.

1.  Regressar ao [Portal clássica](http://manage.windowsazure.com).
2.  Clique em **bases de dados SQL**.
3.  Clique no separador **SINCRONIZAÇÃO** .
4.  Clique no nome deste grupo de sincronização.
5.  Clique no separador **Configurar** .
6.  **SINCRONIZAÇÃO AUTOMÁTICA**
    - Para configurar o grupo de sincronização para sincronizar com uma conjunto de frequência, clique em **Sucessivamente**. Ainda pode sincronizar a pedido ao clicar em SINCRONIZAR.
    - Clique em **desligado** para configurar o grupo de sincronização para sincronizar apenas quando clica em SINCRONIZAR.
7.  **FREQUÊNCIA DE SINCRONIZAÇÃO**
    - Se estiver a SINCRONIZAÇÃO automática no, defina a frequência de sincronização. A frequência deve estar entre 5 minutos e 1 mês.
8.  Clique em **Guardar**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Parabéns. Ter criado um grupo de sincronização que inclui uma instância de base de dados SQL e uma base de dados do SQL Server.

## <a name="next-steps"></a>Próximos passos
Para obter informações adicionais sobre a base de dados SQL e sincronização de dados do SQL, consulte:

* [Transferir a documentação técnica de sincronização de dados do SQL concluída](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Descrição geral de base de dados SQL](sql-database-technical-overview.md)
* [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
 

 

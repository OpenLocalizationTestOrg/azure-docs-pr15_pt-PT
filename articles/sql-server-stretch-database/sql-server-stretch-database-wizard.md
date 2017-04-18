<properties
    pageTitle="Introdução ao executar a base de dados ativar para o Assistente de esticar | Microsoft Azure"
    description="Saiba como configurar uma base de dados para esticar base de dados ao executar a base de dados ativar para esticar assistente."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Introdução ao executar a base de dados ativar para esticar Assistente

Para configurar uma base de dados para esticar base de dados, execute a base de dados ativar para esticar assistente.  Este tópico descreve as informações que tem de introduzir e as escolhas que tem a fazer no assistente.

Para saber mais sobre esticar base de dados, consulte o artigo [Esticar base de dados](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Mais tarde, se desativar a base de dados de opções esticar, lembre-se de que a desativação esticar base de dados para uma tabela ou para uma base de dados não elimina o objeto remoto. Se quiser eliminar a tabela remota ou base de dados remota, tem de largue-a utilizando o portal de gestão Azure. Os objetos remotos continuam a implicam custos Azure até eliminá-los manualmente. 

## <a name="launch-the-wizard"></a>Iniciar o Assistente

1.  No SQL Server Management Studio, no Explorador do objeto, selecione a base de dados no qual pretende ativar esticar.

2.  Direita\-clique em e selecione **tarefas**e, em seguida, selecione **Esticar**e, em seguida, selecione **Ativar** para iniciar o assistente.

## <a name="Intro"></a>Introdução
Reveja o objetivo do assistente e os pré-requisitos.

Os pré-requisitos importantes incluem o seguinte:

-   Tem de ser um administrador para alterar as definições de base de dados.
-   Tem de ter uma subscrição do Microsoft Azure.
-   O SQL Server tem de ser capaz de comunicar com o servidor remoto Azure.

![Página de introdução do assistente esticar base de dados][StretchWizardImage1]

## <a name="Tables"></a>Selecione tabelas
Selecione as tabelas que pretende ativar para esticar.

Tabelas com muitas filas e colunas são apresentadas na parte superior da lista ordenada. Antes do assistente apresenta a lista de tabelas, analisa-os para tipos de dados que não são atualmente suportados pela esticar base de dados.

![Selecione a página de tabelas do assistente esticar base de dados][StretchWizardImage2]

|Coluna|Descrição|
|----------|---------------|
|(sem título)|Selecione a caixa de verificação nesta coluna para ativar a tabela selecionada para esticar.|
|**Nome**|Especifica o nome da coluna na tabela.|
|(sem título)|Um símbolo nesta coluna pode representar um aviso de que não\'t impedi-lo de ativar a tabela selecionada para esticar. Também podem representar o problema de bloqueio o impede de ativar a tabela selecionada para esticar \- por exemplo, uma vez que a tabela que utiliza um tipo de dados não suportadas. Paire sobre o símbolo para apresentar uma descrição mais informações. Para obter mais informações, consulte [limitações para esticar base de dados](sql-server-stretch-database-limitations.md).|
|**For esticada**|Indica se a tabela já está ativada para esticar.|
|**Migrar**|Pode migrar uma tabela inteira (**Tabela inteira**) ou pode especificar um filtro numa coluna existente na tabela. Se pretender utilizar uma função de filtro diferentes para selecionar linhas para migrar, executar a instrução ALTER TABLE para especificar a função filter depois de sair do assistente. Para obter mais informações sobre a função de filtro, consulte o artigo [Selecionar linhas para migrar utilizando uma função de filtro](sql-server-stretch-database-predicate-function.md). Para obter mais informações sobre como aplicar a função, consulte o artigo [Ativar esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Linhas**|Especifica o número de linhas na tabela.|
|**Tamanho (KB)**|Especifica o tamanho da tabela em KB.|

## <a name="Filter"></a>Opcionalmente, forneça um filtro de linha

Se quiser fornecer uma função de filtro para selecionar linhas para migrar, efetue as seguintes coisas na página **Selecionar tabelas** .

1.  Na lista **Selecione as tabelas que pretende esticar** , clique em **Toda a tabela** na linha para a tabela. Abre a caixa de diálogo **Seleccionar linhas para esticar** .

    ![Definir uma função de filtro][StretchWizardImage2a]

2.  Na caixa de diálogo **Seleccionar linhas que pretende esticar** , selecione **Escolher linhas**.

3.  No **campo nome**, forneça um nome para a função filter.

4.  Para a cláusula **Where** , selecione uma coluna a partir da tabela, selecione um operador e fornecer um valor.

5. Clique em **Verificar** para testar a função. Se a função devolve resultados a partir da tabela - ou seja, se existirem linhas para migrar que satisfaçam a condição - o teste relatórios **com êxito**.

    >   [AZURE.NOTE] A caixa de texto que apresenta a consulta de filtro é só de leitura. Não é possível editar a consulta na caixa de texto.

6.  Clique em concluído para regressar à página **selecionadas tabelas** .

A função filter é criada no SQL Server apenas quando concluir o assistente. Até lá, poderá regressar à página **Selecione tabelas** para alterar ou mudar o nome a função filter.

![Selecione página tabelas depois de definir uma função de filtro][StretchWizardImage2b]

Se pretender utilizar um tipo diferente da função de filtro para selecionar linhas para migrar, efetue um dos seguintes procedimentos.  

-   Sair do assistente e execute a instrução ALTER TABLE para ativar Esticar para a tabela e para especificar uma função de filtro. Para obter mais informações, consulte o artigo [Ativar esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md).  

-   Execute a instrução ALTER TABLE para especificar uma função de filtro depois de sair do assistente. Para obter os passos necessários, consulte o artigo [Adicionar uma função de filtro após executar o assistente](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurar a implementação do Azure

1.  Inicie sessão no Microsoft Azure com uma conta Microsoft.

    ![Inicie sessão no Azure - assistente esticar base de dados][StretchWizardImage3]

2.  Selecione a subscrição Azure existente para utilizar para esticar base de dados.

3.  Selecione uma região Azure.
    -   Se criar um novo servidor, o servidor é criado neste região.  
    -   Se tiver servidores existentes na região selecionada, o assistente lista-los quando escolhe **servidor existente**.

    Para minimizar a latência, escolha o Azure região onde se encontra o SQL Server. Para mais informações sobre regiões, consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/).

4.  Especifique se pretende utilizar um servidor existente ou criar um novo servidor Azure.

    Se o Active Directory no SQL Server for Federado com Azure Active Directory, opcionalmente, pode utilizar uma conta de serviço federados para SQL Server para comunicar com o servidor remoto Azure. Para obter mais informações sobre os requisitos para esta opção, consulte o artigo [Alterar opções base de dados definido (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Criar novo servidor**

        1.  Crie um ID de utilizador e palavra-passe para o administrador do servidor.

        2.  Opcionalmente, utilize uma conta de serviço federados para SQL Server para comunicar com o servidor remoto Azure.

        ![Criar novo server Azure - assistente esticar base de dados][StretchWizardImage4]

    -   **Servidor existente**

        1.  Selecione o servidor Azure existente.

        2.  Selecione o método de autenticação.

            -   Se selecionar **Autenticação do SQL Server**, forneça o início de sessão do administrador e a palavra-passe.

            -   Selecione **Autenticação integrado do Active Directory** para utilizar uma conta de serviço federados para SQL Server para comunicar com o servidor remoto Azure. Se o servidor seleccionado não está integrado com o Azure Active Directory, esta opção não aparecer.

        ![Selecione server Azure existente - assistente esticar base de dados][StretchWizardImage5]

## <a name="Credentials"></a>Credenciais seguras
Tem de ter uma chave de modelo global de base de dados para proteger as credenciais que utiliza esticar base de dados para ligar à base de dados remota.  

Se já existe uma chave de modelo global de base de dados, introduza a palavra-passe para a mesma.  

![Proteger as credenciais página do assistente esticar base de dados][StretchWizardImage6b]

Se a base de dados não tiver uma chave de modelo global de existente, introduza uma palavra-passe segura para criar uma chave de modelo global de base de dados.  

![Proteger as credenciais página do assistente esticar base de dados][StretchWizardImage6]

Para obter mais informações sobre a chave de modelo global de base de dados, consulte o artigo [criar o modelo global de chave (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [criar uma chave de modelo global de base de dados](https://msdn.microsoft.com/library/aa337551.aspx). Para mais informações sobre a credencial que o assistente cria, consulte o artigo [Criar base de dados no âmbito das CREDENCIAIS (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selecione o endereço IP
Utilize o intervalo de endereços IP de sub-rede (recomendado) ou o endereço IP público do seu servidor de SQL, para criar uma regra de firewall no Azure que permite do SQL Server comunicar com o servidor remoto Azure.

O endereço ou endereços IP fornecidas nesta página indicam ao servidor Azure para permitir que os dados a receber, consultas e operações de gestão de iniciado por SQL Server para passar através da firewall do Azure. O assistente não altera nada nas definições da firewall do SQL Server.

![Selecione a página de endereço IP do assistente esticar base de dados][StretchWizardImage7]

## <a name="Summary"></a>Resumo
Reveja os valores que introduziu e as opções que selecionou no assistente e os custos estimados no Azure. Em seguida, selecione **Concluir** para ativar as opções esticar.

![Página de resumo do assistente esticar base de dados][StretchWizardImage8]

## <a name="Results"></a>Resultados
Reveja os resultados.

Para monitorizar o estado da migração de dados, consulte o artigo [Monitor e resolver problemas de migração de dados (base de dados esticar)](sql-server-stretch-database-monitor.md).

![Página de resultados do assistente esticar base de dados][StretchWizardImage9]

## <a name="KnownIssues"></a>O Assistente de resolução de problemas
**O Assistente de base de dados esticar falhou.**
Se a base de dados esticar ainda não é activado ao nível do servidor e executar o assistente sem o sistema de permissões de administrador para ativá-lo, o Assistente de falha. Solicite ao administrador de sistema para ativar o esticar base de dados a instância de servidor local e, em seguida, execute o assistente novamente. Para obter mais informações, consulte o artigo [pré-requisito: permissão para activar esticar base de dados no servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Próximos passos
Ative tabelas adicionais para esticar base de dados. Monitorizar a migração de dados e gerir esticar\-com permissão para bases de dados e tabelas.

-   [Ativar opções esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md) para ativar a tabelas adicionais.

-   [Monitor e resolver problemas de migração de dados](sql-server-stretch-database-monitor.md) para ver o estado da migração de dados.

-   [Interromper e retomar esticar base de dados](sql-server-stretch-database-pause.md)

-   [Gerir e resolver problemas esticar base de dados](sql-server-stretch-database-manage.md)

-   [Cópia de segurança com capacidade de esticar as bases de dados](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Consulte também

[Ativar opções esticar base de dados para uma base de dados](sql-server-stretch-database-enable-database.md)

[Ativar opções esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png

<properties
    pageTitle="Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o Portal do Azure"
    description="Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o Portal do Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Arquivar uma base de dados do Azure SQL para um ficheiro BACPAC utilizando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artigo fornece instruções para o arquivo a base de dados do Azure SQL para um ficheiro BACPAC (armazenado no armazenamento de Blobs do Azure) utilizando o [Azure portal](https://portal.azure.com).

Quando precisar de criar um arquivo de uma base de dados do Azure SQL, pode exportar o esquema da base de dados e os dados para um ficheiro BACPAC. Um ficheiro BACPAC é simplesmente um ficheiro ZIP com uma extensão de BACPAC. Um ficheiro BACPAC mais tarde pode ser armazenado em armazenamento de Blobs do Azure ou em armazenamento local numa localização no local e mais tarde importados anterior para a base de dados do SQL Azure ou para SQL Server no local instalação. 

***Considerações sobre***

- Para um arquivo ser consistentes, tem de se certificar que quer sem escrever atividade está a ocorrer durante a exportação ou que esteja a exportar a partir de uma [cópia consistente](sql-database-copy.md) da base de dados Azure SQL.
- O tamanho máximo de um ficheiro BACPAC arquivado ao armazenamento de Blobs do Azure é 200 GB. Para arquivar um ficheiro BACPAC maior para o armazenamento local, utilize o utilitário de linha de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com Visual Studio e do SQL Server. Também pode [Transferir](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente das ferramentas de dados do SQL Server para obter este utilitário.
- Arquivar ao armazenamento de Azure premium utilizando um ficheiro BACPAC não é suportada.
- Se a operação de exportação exceder 20 horas, pode ser cancelada. Para aumentar o desempenho durante a exportação, pode:
 - Aumente temporariamente o seu nível de serviço.
 - Cessar todos ler e escrever atividade durante a exportação.
 - Utilize um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos sobre todas as tabelas grandes. Sem índices agrupados, uma exportação poderá falhar se demora mais de 6 a 12 horas. Isto acontece porque o serviço de exportação necessitam de ser uma pesquisa da tabela para tentar exportar a tabela inteira. É uma boa forma de saber se as suas tabelas estão otimizadas para exportar executar **DBCC SHOW_STATISTICS** e certifique-se de que o *RANGE_HI_KEY* não é nulo e o valor tem boa distribuição. Para obter detalhes, consulte o artigo [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] Não BACPACs destinam-se para ser utilizada para cópia de segurança e restaurar operações. Base de dados SQL Azure cria automaticamente cópias de segurança para cada base de dados do utilizador. Para obter detalhes, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md).

Para concluir este artigo precisa do seguinte:

- Uma subscrição do Azure.
- Uma base de dados do Azure SQL. 
- Uma [conta de armazenamento do Windows Azure padrão](../storage/storage-create-storage-account.md) com um contentor blob para armazenar a BACPAC no armazenamento padrão.

## <a name="export-your-database"></a>Exportar a sua base de dados

Abra o pá de base de dados SQL para a base de dados que pretende exportar.

> [AZURE.IMPORTANT] Para garantir a um ficheiro BACPAC consistente deve primeiro, [crie uma cópia da base de dados](sql-database-copy.md) e, em seguida, exporte a cópia da base de dados. 

1.  Aceda ao [Azure portal](https://portal.azure.com).
2.  Clique em **bases de dados SQL**.
3.  Clique em base de dados para arquivar.
4.  Em pá base de dados SQL, clique em **Exportar** para abrir o pá **Exportar base de dados** :

    ![botão Exportar][1]

5.  Clique em **armazenamento** e selecione a sua conta e blob contentor de armazenamento de onde o BACPAC será armazenado:

    ![Exportar base de dados][2]

6. Selecione o tipo de autenticação. 
7.  Introduza as credenciais de autenticação adequado para o servidor do Azure SQL que contém a base de dados que esteja a exportar.
8.  Clique em **OK** para arquivar a base de dados. Clicar em **OK** , cria um pedido de base de dados de exportação e submete-a para o serviço. O comprimento do tempo que irá demorar a exportação depende o tamanho e complexidade da sua base de dados e o seu nível de serviço. Receberá uma notificação.

    ![notificação de exportação][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorizar o progresso da operação de exportação

1.  Clique em **servidores SQL**.
2.  Clique no servidor que contém a base de dados (origem) original que apenas arquivados.
3.  Desloque para baixo até operações.
4.  No SQL pá server, clique em **Histórico de importar/exportar**:

    ![importar o histórico de exportação][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Verifique o BACPAC se encontra no contentor de armazenamento

1.  Clique em **contas de armazenamento**.
2.  Clique na conta de armazenamento onde armazenou o arquivo BACPAC.
3.  Clique em **contentores** e selecione o contentor que exportou a base de dados para obter detalhes (pode transferir e guardar BACPAC a partir daqui).

    ![Detalhes do ficheiro .bacpac][5]  

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre como importar um BACPAC para uma base de dados do SQL Azure, consulte o artigo [Importar um BACPCAC para uma base de dados do Azure SQL](sql-database-import.md)
- Para obter informações sobre como importar um BACPAC para uma base de dados do SQL Server, consulte o artigo [Importar um BACPCAC para uma base de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png


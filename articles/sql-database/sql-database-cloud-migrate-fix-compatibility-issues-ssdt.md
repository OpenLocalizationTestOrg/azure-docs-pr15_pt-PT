<properties
   pageTitle="Corrigir problemas de compatibilidade de base de dados do SQL Server antes da migração para a base de dados SQL | Microsoft Azure"
   description="Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade, o Assistente de migração do SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Migrar uma base de dados do SQL Server para a base de dados do Azure SQL com ferramentas de dados do SQL Server para Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Classificação de actualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Neste artigo, saiba detetar e corrigir problemas de compatibilidade de base de dados do SQL Server utilizando as ferramentas de dados do SQL Server para Visual Studio antes da migração para a base de dados do SQL Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Utilizar ferramentas de dados do SQL Server para Visual Studio

Utilize ferramentas de dados do SQL Server para Visual Studio ("SSDT") para importar o esquema da base de dados para um projeto de base de dados do Visual Studio para análise. Para analisar, especifique a plataforma de destino para o projeto como V12 de base de dados do SQL e, em seguida, criar projeto. Se a compilação é efetuada com êxito, a base de dados é compatível. Se a compilação falhar, pode resolver os erros em SSDT (ou uma das outras ferramentas de outros fabricantes referidas neste tópico). Assim que o project cria com êxito, pode publicá-lo novamente como uma cópia da base de dados de origem. Em seguida, pode utilizar a funcionalidade de comparação de dados no SSDT para copiar os dados a partir da base de dados de origem para a base de dados compatível V12 de SQL Azure. Em seguida, pode migrar esta base de dados atualizado. Para utilizar esta opção, transfira a [versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migração VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se for necessária uma migração de esquema, o esquema pode ser publicado diretamente a partir do Visual Studio diretamente para a base de dados do SQL Azure. Utilize este método quando o esquema da base de dados requer mais alterações que poderá ser resolvido pelo Assistente de migração.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Detetar problemas de compatibilidade com ferramentas de dados do SQL Server para Visual Studio
   
1.  Abra o **Explorador de objeto do SQL Server** no Visual Studio. Utilize **Adicionar SQL Server** para ligar a instância do SQL Server que contém a base de dados ser migrada. Localize a base de dados no Explorador do objeto, com o botão direito na base de dados e selecione **Criar novo projeto...**     
    
    ![Novo projeto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Configure as definições de importação para **Importar objectos confinados de aplicação**. Desmarque as opções para importar o seguinte: referenciados inícios de sessão, permissões e definições de base de dados.    

    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Clique em **Iniciar** para importar a base de dados e criar o projeto que contém um ficheiro de script T-SQL para cada objeto da base de dados. Os ficheiros de script são aninhados nas pastas do projeto.    

    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  No Explorador de solução do Visual Studio, com o botão direito do projeto de base de dados e selecione propriedades. Na página **Definições do Project** , configure a plataforma de destino para V12 de base de dados do Microsoft Azure SQL.    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Com o botão direito do projeto e selecione **Criar** para criar o projeto.    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  A **Lista de erros** apresenta cada incompatibilidade. Neste caso, o nome de utilizador NT \ serviço é incompatível. Uma vez que é incompatível, pode comentá-la ou removê-la (e endereço as implicações de remover esta início de sessão e a função da solução de base de dados).     
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Corrigir problemas de compatibilidade com ferramentas de dados do SQL Server para Visual Studio

1.  Faça duplo clique sobre o primeiro script para abrir o script numa janela de consulta e comenta o script e, em seguida, execute o script.     
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Repita este processo para cada script que contém incompatibilidades até não permanecem nenhum erro.    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Quando a base de dados está isento de erros, com o botão direito do projeto e selecione **Publicar**. Uma cópia da base de dados de origem for criada e publicada (recomenda-se vivamente para utilizar uma cópia, pelo menos inicialmente).     
 - Antes de publicar, dependendo da versão do SQL Server de origem (anteriores ao SQL Server 2014), poderá ter para repor a plataforma de destino do projeto para ativar a implementação.     
 - Se estiver a migrar uma base de dados mais antiga do SQL Server, não apresentar qualquer funcionalidades para o projeto que não são suportadas na origem do SQL Server até migrar a base de dados para uma versão mais recente do SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  No Explorador de objeto do SQL Server, com o botão direito a base de dados de origem e clique em **Comparação de dados**. Comparar o projeto à base de dados original ajuda-o a perceber quais as alterações foram efetuadas pelo assistente. Selecione a sua versão do Azure SQL V12 da base de dados e, em seguida, clique em **Concluir**.    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Reveja as diferenças detetadas e, em seguida, clique em **Atualizar destino** para migrar dados a partir da base de dados de origem para a base de dados do Azure SQL V12.     
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Escolha um método de implementação. Consulte o artigo [migrar uma base de dados do SQL Server compatível com a base de dados do SQL.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)

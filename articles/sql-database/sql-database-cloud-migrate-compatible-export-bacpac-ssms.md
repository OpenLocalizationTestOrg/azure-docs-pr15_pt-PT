
<properties
   pageTitle="Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SQL Server Management Studio | Microsoft Azure"
   description="Microsoft Azure base de dados SQL, migração de base de dados, exportar base de dados, exportar BACPAC Assistente de ficheiro, exportar aplicação de camadas de dados"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Este artigo mostra como exportar uma base de dados do SQL Server para um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizando o Assistente Exportar dados camada de aplicação no SQL Server Management Studio. 

1. Certifique-se de que tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal para permanecer sincronizado com atualizações ao portal do Azure.

     > [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e ligue a sua base de dados de origem no Explorador do objeto.

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Botão direito do rato a base de dados de origem no Explorador de objeto, aponte para **tarefas**e clique em **Exportar dados de aplicação de camadas...**

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No Assistente de exportação, configure a exportação para guardar o ficheiro BACPAC quer uma localização de disco local ou para um Azure blob. O BACPAC exportado sempre inclui o esquema da base de dados completa e, por predefinição, os dados a partir de todas as tabelas. Se pretende excluir dados das algumas ou todas as tabelas, utilize o separador Avançadas. Pode, por exemplo, escolher exportar apenas os dados para tabelas de referência em vez de todas as tabelas.

***Importante*** Quando exporta um BACPAC ao armazenamento de Blobs do Azure, utilize o armazenamento padrão. Importar um BACPAC a partir do armazenamento premium não é suportada.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importar um BACPAC a base de dados do SQL Azure com SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importar um BACPAC para SqlPackage de base de dados do Azure SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importar um BACPAC portal Azure de base de dados do SQL Azure](sql-database-import.md)
- [Importar um BACPAC PowerShell de base de dados do Azure SQL](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)

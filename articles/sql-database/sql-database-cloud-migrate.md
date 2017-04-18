<properties
   pageTitle="Migração de base de dados do SQL Server para a base de dados SQL | Microsoft Azure"
   description="Saiba mais sobre como no local do SQL Server da base de dados migração para base de dados do SQL Azure na nuvem. Utilize ferramentas de migração de base de dados para testar a compatibilidade antes de migração de base de dados."
   keywords="base de dados de migração, migração de base de dados do sql server, ferramentas de migração de base de dados, migrar a base de dados, migrar a base de dados sql"
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

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migração de base de dados do SQL Server para a base de dados do SQL na nuvem

Neste artigo, saiba como migrar no local SQL Server 2005 ou posterior base de dados para a base de dados do SQL Azure. Este processo de migração da base de dados, que migra o esquema e os dados da base de dados do SQL Server no seu ambiente atual para a base de dados SQL. Ser concluída com êxito, a base de dados existente tem primeiro de passar um teste de compatibilidade. Com [V12 de base de dados do SQL](sql-database-v12-whats-new.md), existem alguns restante problemas de compatibilidade, que não seja o problema relacionado com o nível do servidor e base de dados em várias operações. Bases de dados e aplicações que dependam [parcialmente ou não suportada funções](sql-database-transact-sql-information.md) precisa de algumas reformulação corrigir incompatibilidade antes da base de dados do SQL Server pode ser migrado.

Para migrar, seguem-se os passos lhe tirar:

- **Teste para compatibilidade**: validar compatibilidade de base de dados com [V12 de base de dados do SQL](sql-database-v12-whats-new.md). 
- **Corrigir problemas de compatibilidade, se existirem**: se falhas de validação, tem de corrigir os erros de validação.  
- **Executar a migração** Assim que a sua base de dados for compatível, pode utilizar um ou vários métodos para efetuar a migração. 

SQL Server fornece vários métodos para efetuar cada uma das seguintes tarefas. Este artigo fornece uma descrição geral dos métodos disponíveis para cada tarefa. O diagrama seguinte ilustra os passos e os métodos.

  ![Diagrama de migração VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Para migrar uma base de dados do SQL de Server, incluindo o Microsoft Access, Sybase, MySQL Oracle e DB2 a base de dados SQL Azure, consulte o artigo [Migração Assistente do SQL Server](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Ferramentas de migração de base de dados testar a compatibilidade de base de dados do SQL Server com base de dados SQL

Para testar a existência de problemas de compatibilidade de base de dados SQL antes de iniciar o processo de migração de base de dados, utilize um dos seguintes métodos:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Classificação de actualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [Ferramentas de dados do SQL Server para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT utiliza as regras de compatibilidade mais recentes para detetar incompatibilidades V12 de base de dados do SQL. Se forem detetadas incompatibilidades, corrigir problemas detectados diretamente nesta ferramenta. Este método é o método recomendado para testar e resolver problemas de compatibilidade V12 de base de dados do SQL. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage é um utilitário da linha de comandos que testa para problemas de compatibilidade e gera um relatório que contém os problemas de compatibilidade detectado. Se utilizar esta ferramenta, certifique-se de que utilizar a versão mais recente para utilizar as regras de compatibilidade mais recentes. Se for detetado nenhum, tem de utilizar outra ferramenta para resolver quaisquer problemas de compatibilidade detectado - SSDT é recomendado.  
- [Assistente de aplicação de exportar dados de camadas no SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): Este assistente detetar e relatar erros ao ecrã. Se não for detetado nenhum, pode continuar e concluir a migração para a base de dados SQL. Se for detetado nenhum, tem de utilizar outra ferramenta para resolver quaisquer problemas de compatibilidade detectado - SSDT é recomendado.
- [O Microsoft SQL Server atualizar Advisor pré-visualização 2016](http://www.microsoft.com/download/details.aspx?id=48119): esta ferramenta autónomo, que esteja atualmente na pré-visualização, Deteta e gera um relatório de incompatibilidades V12 de base de dados do SQL. Esta ferramenta ainda não tem as regras de compatibilidade mais recentes. Se não for detetado, pode continuar e concluir a migração para a base de dados SQL. Se for detetado nenhum, tem de utilizar outra ferramenta para resolver quaisquer problemas de compatibilidade detectado - SSDT é recomendado. 
- [Assistente de migração do SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW é uma ferramenta de codeplex que utiliza as regras de compatibilidade V11 de base de dados do SQL Azure para detetar incompatibilidades V12 de base de dados do SQL Azure. Se forem detetadas incompatibilidades, alguns problemas podem ser corrigidos diretamente nesta ferramenta. Esta ferramenta poderá encontrar incompatibilidades que não necessita de ser corrigido. -Foi a primeira base de dados do Azure SQL assistência ferramenta de migração disponível e ativamente é suportado pela Comunidade do SQL Server. Além disso, esta ferramenta pode concluir a migração de dentro da ferramenta de própria. 

## <a name="fix-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração de base de dados

Se forem detetados problemas de compatibilidade, tem de as corrigir antes de prosseguir com a migração de base de dados do SQL Server. Existem uma grande variedade de problemas de compatibilidade que poderá encontrar, dependendo ambos na versão do SQL Server na base de dados de origem e a complexidade da base de dados, quer esteja a migrar. As versões mais antigas do SQL Server tem mais problemas de compatibilidade. Utilize os seguintes recursos, para além de uma pesquisa de Internet alvo utilizando o seu motor de busca de opções:

- [Funcionalidades de base de dados do SQL Server não suportadas na base de dados do SQL Azure](sql-database-transact-sql-information.md)
- [Funcionalidade do motor da base de dados descontinuadas no SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidade do motor da base de dados descontinuadas no SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidade do motor da base de dados descontinuadas no SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funcionalidade do motor da base de dados descontinuadas no SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funcionalidade do motor da base de dados descontinuadas no SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Para além de procurar na Internet e utilizar estes recursos, utilize o [fóruns da Comunidade do MSDN do SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou [StackOverflow](http://stackoverflow.com/).

Utilize uma das seguintes ferramentas de migração de base de dados para corrigir os problemas de detetado:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Utilizar [As ferramentas de dados do SQL Server para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para utilizar SSDT, pode importar o esquema da base de dados para as ferramentas de dados do SQL Server para Visual Studio "SSDT") e crie o projeto para uma implementação V12 de base de dados do SQL. Em seguida, corrigir todos os problemas de compatibilidade detectado no SSDT. Quando estiver concluído, sincronizar as alterações novamente para a base de dados de origem (ou uma cópia da base de dados de origem. SSDT atualmente é o método recomendado para testar e resolver problemas de compatibilidade V12 de base de dados do SQL. Siga a hiperligação para um [guia passo a passo utilizando SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Utilizar o [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para utilizar SSMS, execute comandos Transact-SQL para corrigir os erros detetados utilizar outra ferramenta. Este método é principalmente para utilizadores avançados modificar o esquema da base de dados diretamente na base de dados de origem. 
- Utilizar o [Assistente de migração do SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para utilizar SAMW, gerar um script Transact-SQL da base de dados de origem. O assistente transforma o script, sempre que possível, para tornar o esquema compatível com o V12 de base de dados SQL. Quando estiver concluído, SAMW pode ligar a V12 de base de dados SQL para executar o script. Esta ferramenta também analisa os ficheiros de rastreio para determinar problemas de compatibilidade. O script pode ser gerado com esquema apenas ou pode incluir dados no formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrar uma base de dados compatível com SQL Server para a base de dados SQL

Para migrar uma base de dados compatível com SQL Server, a Microsoft fornece vários métodos de migração para vários cenários. O método que escolher depende da sua tolerância para o tempo de inatividade, o tamanho e complexidade da sua base de dados do SQL Server e a conectividade na nuvem da Microsoft Azure.  

> [AZURE.SELECTOR]
- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar do ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Para escolher o seu método de migração, a primeira pergunta pedir é se que pode perder de forma alguma tomar a base de dados fora de produção durante a migração. Migrar uma base de dados enquanto transações ativas estão a ocorrer pode resultar em inconsistências de base de dados e danos possíveis da base de dados. Existem vários métodos para colocar em repouso uma base de dados, a partir de desativar a conectividade de cliente para criação de um [instantâneo de base de dados](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar com indisponibilidade mínima, utilize [replicação do SQL Server da transação](sql-database-cloud-migrate-compatible-using-transactional-replication.md) se a base de dados cumpra os requisitos para replicação transaccional. Se o pode perder de forma alguma algum tempo de inatividade ou estiver a executar uma migração de teste de uma base de dados de produção para migração posterior, considere um dos seguintes três métodos:

- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): para pequenas a bases de dados médios, migrar um compatível com SQL Server 2005 ou posterior da base de dados é tão simple como executar a [Implementar o base de dados para o Assistente de base de dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) no SQL Server Management Studio.
- [Exportar para ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e, em seguida, [Importar do ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Se tiver dificuldades de conectividade (sem conectividade de largura de banda baixa problemas ou tempo limite) e para médio para grandes bases de dados, utilizar um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Com este método, exportar o esquema do SQL Server e os dados para um ficheiro BACPAC. Em seguida, importar o ficheiro BACPAC numa base de dados SQL utilizando o Assistente de exportação dados camada de aplicação no SQL Server Management Studio ou o utilitário de linha de comandos de [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
- Utilizar BACPAC e BCP em conjunto: utilizar um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e [BCP](https://msdn.microsoft.com/library/ms162802.aspx) bases de dados muito maiores para alcançar maior parallelization para aumenta o desempenho, embora com maior complexidade. Com este método, migre o esquema e os dados em separado.
 - [Exportar o esquema apenas para um ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importar o esquema apenas a partir do ficheiro de BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) na base de dados SQL.
 - Utilize [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extrair os dados em ficheiros simples e, em seguida, [paralela carga](https://technet.microsoft.com/library/dd425070.aspx) estes ficheiros para a base de dados do SQL Azure.

     ![SQL Server migração de base de dados - migrar a base de dados SQL na nuvem.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Próximos passos

- [Pré-visualização do Microsoft SQL Server 2016 classificação de actualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados do SQL](sql-database-v12-whats-new.md)
[Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)

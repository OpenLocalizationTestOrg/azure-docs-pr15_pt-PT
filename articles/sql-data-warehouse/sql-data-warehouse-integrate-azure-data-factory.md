<properties
   pageTitle="Utilizar dados Azure fábrica com armazém de dados SQL | Microsoft Azure"
   description="Sugestões para usar o Azure dados fábrica (ADF) no armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utilizar dados Azure fábrica com armazém de dados SQL

Azure dados fábrica fornece um método totalmente gerido para orquestrar a transferência de dados e execução dos procedimentos armazenados no armazém de dados SQL.  Isto permite-lhe mais facilmente configuração da compilação e agenda complexa extrair transformar e carga (ETL) procedimentos com armazém de dados do SQL. Para obter uma descrição mais completa da fábrica de dados do Azure, consulte a [documentação do Azure fábrica de dados][].

## <a name="data-movement"></a>Movimentação de dados

Azure dados fábrica permite movimento de dados entre origens no local e diferentes serviços do Azure.  Geral, atual integração com o Azure dados fábrica suporta movimento de dados para e a partir das seguintes localizações:

+ Armazenamento de Blobs do Azure
+ Base de dados Azure SQL
+ No local SQL Server
+ SQL Server no IaaS

Para obter informações sobre como configurar um dados cópia atividade consulte o artigo [copiar dados com a fábrica de dados do Azure][]

## <a name="stored-procedures"></a>Procedimentos armazenados
 Da mesma forma que pode ser utilizada para agendar a transferência de dados, Azure dados fábrica podem também ser utilizada para orquestrar a execução de procedimentos armazenados.  Isto permite mais complexas tubagens seja criado e expande a capacidade de tirar partido da utilizaria do armazém de dados do SQL Azure Data Factory.

## <a name="next-steps"></a>Próximos passos
Para obter uma descrição geral da integração, consulte o artigo [Descrição geral da integração do armazém de dados SQL][].
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].

<!--Image references-->

<!--Article references-->

[Copiar dados com a fábrica de dados do Azure]: ../data-factory/data-factory-data-movement-activities.md
[Descrição geral do desenvolvimento armazém de dados SQL]: ./sql-data-warehouse-overview-develop.md
[Descrição geral da integração do armazém de dados SQL]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentação do Azure fábrica de dados]:https://azure.microsoft.com/documentation/services/data-factory/


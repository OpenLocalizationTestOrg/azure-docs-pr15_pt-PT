<properties
   pageTitle="Gerir power cluster no Azure SQL dados armazém (REST) | Microsoft Azure"
   description="Tarefas de Transact-SQL (T-SQL) para o desempenho de escala de saída, ajustando DWUs. Guarde os custos ao dimensionamento durante o que não sejam de pico vezes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gerir power cluster no armazém de dados do SQL Azure (T SQL)

> [AZURE.SELECTOR]
- [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Desempenho de escala por escalar para fora de calcular recursos e da memória para satisfazer os pedidos de alteração da sua carga de trabalho. Guarde os custos por recursos anterior dimensionamento durante as horas de que não sejam ponta ou colocar em pausa cluster completamente. 

Este conjunto de tarefas utiliza T-SQL para:

- Ver definições de DWU atuais
- Alterar cluster recursos, ajustando DWUs

Para interromper ou retomar uma base de dados, selecione uma das outras opções de plataforma na parte superior deste artigo.

Para saber mais sobre isto, consulte o artigo [Gerir calcular descrição geral do power][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Ver definições de DWU atuais

Para ver as definições de DWU atuais para as bases de dados:

1. Abra o Explorador de objeto do SQL Server no Visual Studio 2015.
2. Ligar à base de dados principal associado ao servidor de base de dados SQL lógico.
2. Selecione a partir da vista de gestão de dinâmica sys.database_service_objectives. Eis um exemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Cluster de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os DWUs:


1. Ligar à base de dados principal associada com o seu servidor de base de dados SQL lógico.
2. Utilize a instrução de TSQL [ALTER DATABASE][] . O exemplo seguinte define o objectivo de nível de serviço para DW1000 para a base de dados MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximos passos

Para outras tarefas de gestão, consulte o artigo [Descrição geral da gestão][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Descrição geral da gestão]: ./sql-data-warehouse-overview-manage.md
[Gerir a descrição geral do power cluster]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTERAR A BASE DE DADOS]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

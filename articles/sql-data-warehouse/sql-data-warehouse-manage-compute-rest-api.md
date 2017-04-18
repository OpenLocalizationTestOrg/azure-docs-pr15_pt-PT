<properties
   pageTitle="Gerir power cluster no Azure SQL dados armazém (REST) | Microsoft Azure"
   description="Tarefas do PowerShell para gerir calcular power. Recursos de cluster de escala, ajustando DWUs. Ou, interromper e retomar cluster recursos poupar nos custos."
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

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Gerir power cluster no Azure SQL dados armazém (REST)

> [AZURE.SELECTOR]
- [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Desempenho de escala por escalar para fora de calcular recursos e da memória para satisfazer os pedidos de alteração da sua carga de trabalho. Guarde os custos por recursos anterior dimensionamento durante as horas de que não sejam ponta ou colocar em pausa cluster completamente. 

Este conjunto de tarefas utiliza o portal do Azure para:

- Cluster de escala
- Cluster de colocar em pausa
- Cluster de currículo

Para saber mais sobre isto, consulte o artigo [Gerir calcular descrição geral][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Power cluster de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os DWUs, utilize o REST API [criar ou actualizar base de dados][] . O exemplo seguinte define o objectivo de nível de serviço para DW1000 para a base de dados MySQLDW que está alojado num servidor omeuservidor. O servidor está num grupo de recursos Azure denominada ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster de colocar em pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para interromper uma base de dados, utilize a API do resto de [Colocar em pausa a base de dados][] . O exemplo seguinte interrompe a uma base de dados denominada Database02 alojado num servidor chamado Server01. O servidor está num grupo de recursos Azure denominada ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cluster de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar uma base de dados, utilize a [Base de dados do currículo][] REST API. O exemplo seguinte inicia uma base de dados denominada Database02 alojado num servidor chamado Server01. O servidor está num grupo de recursos Azure denominada ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximos passos

Para outras tarefas de gestão, consulte o artigo [Descrição geral da gestão][].

<!--Image references-->

<!--Article references-->
[Descrição geral da gestão]: ./sql-data-warehouse-overview-manage.md
[Gerir a descrição geral de cluster]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Colocar em pausa base de dados]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Base de dados do currículo]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Criar ou actualizar a base de dados]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

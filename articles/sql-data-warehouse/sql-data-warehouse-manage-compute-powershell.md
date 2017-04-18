<properties
   pageTitle="Gerir power cluster no armazém de dados do SQL Azure (PowerShell) | Microsoft Azure"
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
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gerir power cluster no armazém de dados do SQL Azure (PowerShell)

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


## <a name="before-you-begin"></a>Antes de começar

### <a name="install-the-latest-version-of-azure-powershell"></a>Instalar a versão mais recente do PowerShell do Azure

> [AZURE.NOTE]  Para utilizar o Azure PowerShell com armazém de dados do SQL, precisa de Azure PowerShell versão 1.0.3 ou superior.  Para verificar a sua versão atual, execute o comando **módulo Get - ListAvailable-nome Azure**. Pode instalar a versão mais recente do [Microsoft Web plataforma Installer][].  Para mais informações, consulte o artigo [como instalar e configurar o Azure PowerShell][].

### <a name="get-started-with-azure-powershell-cmdlets"></a>Começar a trabalhar com os cmdlets do PowerShell do Azure

Para começar a utilizar:

1. Abra o Azure PowerShell. 
2. Na linha de comandos do PowerShell, execute estes comandos para iniciar sessão para o Gestor de recursos do Azure e selecione a sua subscrição.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Power cluster de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os DWUs, utilize o cmdlet do PowerShell [AzureRmSqlDatabase conjunto][] . O exemplo seguinte define o objectivo de nível de serviço para DW1000 para a base de dados MySQLDW que está alojado num servidor omeuservidor. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster de colocar em pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para interromper uma base de dados, utilize o cmdlet [AzureRmSqlDatabase suspender][] . O exemplo seguinte interrompe a uma base de dados denominada Database02 alojado num servidor chamado Server01. O servidor está num grupo de recursos Azure denominada ResourceGroup1. 

> [AZURE.NOTE] Note que, se o servidor está foo.database.windows.net, utilize "foo" como nomedoservidor - os cmdlets do PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, neste exemplo seguinte obtém a base de dados para o objeto $database. -Tubos, em seguida, o objeto para [Suspender AzureRmSqlDatabase][]. Os resultados são armazenados no resultDatabase objeto. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cluster de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar uma base de dados, utilize o cmdlet [AzureRmSqlDatabase currículo][] . O exemplo seguinte inicia uma base de dados denominada Database02 alojado num servidor chamado Server01. O servidor está num grupo de recursos Azure denominada ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, neste exemplo seguinte obtém a base de dados para o objeto $database. Em seguida, tubos o objeto para [Currículo AzureRmSqlDatabase][] e armazena os resultados na $resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximos passos

Para outras tarefas de gestão, consulte o artigo [Descrição geral da gestão][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Descrição geral da gestão]: ./sql-data-warehouse-overview-manage.md
[Como instalar e configurar PowerShell do Azure]: ./powershell-install-configure.md
[Gerir a descrição geral de cluster]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Currículo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[AzureRmSqlDatabase suspender]: https://msdn.microsoft.com/library/mt619337.aspx
[Definir AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web plataforma Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/

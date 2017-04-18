<properties 
    pageTitle="Alterar o nível de camadas e o desempenho do serviço de uma base de dados do Azure SQL através do PowerShell | Microsoft Azure" 
    description="Alterar a camada de serviço e nível de desempenho de uma base de dados do Azure SQL mostra como dimensionar a base de dados do SQL para cima ou para baixo com o PowerShell. Alterar a camada comparar da base de dados Azure SQL com PowerShell." 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Alterar o serviço camada e desempenho nível (preços camada) de uma base de dados SQL com PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Camadas de serviços e níveis de desempenho descrevem as funcionalidades e recursos disponíveis para a base de dados do SQL e podem ser atualizados como as necessidades da sua alteração de aplicação. Para obter detalhes, consulte o artigo [Camadas de serviços](sql-database-service-tiers.md).

Tenha em atenção que alterar a camada de serviço e/ou cria uma réplica da base de dados original ao nível de desempenho do novo nível de desempenho de uma base de dados e, em seguida, muda ligações ao longo para a réplica. Dados não são perdidos durante este processo mas durante o breve momento em que quando podemos mudar para a réplica, as ligações para a base de dados estão desactivadas, para que algumas operações em voos poderão ser revertidas. Esta janela varia, mas é média em 4 segundos e mais de 99% de casos é menor que 30 segundos. Muito raramente, especialmente se não existirem grandes números de transações na voo nas ligações de momento em que estão desativados, esta janela pode ser maior.  

A duração de todo o processo de escala de segurança depende camada tamanho e service da base de dados antes e depois da alteração. Por exemplo, uma base de dados de 250 GB que está a mudar para a partir de dentro de uma camada de serviço padrão, deverá concluída dentro 6 horas. Para uma base de dados do mesmo tamanho que está a alterar níveis de desempenho dentro da camada de serviço Premium, deve concluir dentro de 3 horas.


- Para alterar uma base de dados, a base de dados deve ser mais pequeno do que o tamanho máximo permitido a camada de serviço de destino. 
- Ao atualizar uma base de dados com a [Replicação Geo](sql-database-geo-replication-portal.md) ativado, primeiro tem de actualizar suas bases de dados secundárias para a camada de desempenho desejado antes de atualizar a base de dados principal.
- Quando mudar a partir de uma camada de serviço Premium, primeiro tem de terminar todas as relações de replicação Geo. Pode seguir os passos descritos no tópico [recuperar a partir de uma falha](sql-database-disaster-recovery.md) para parar o processo de replicação entre a página principal e as bases de dados secundárias ativos.
- As ofertas de serviço de restaurar são diferentes para as várias camadas de serviço. Se está a mudar podem perder a capacidade de restaurar a um ponto no tempo ou se tiver um período de retenção de cópia de segurança inferior. Para mais informações, consulte [o cópia de segurança da base de dados do SQL Azure e restaurar](sql-database-business-continuity.md).
- As novas propriedades da base de dados não são aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo precisa do seguinte:**

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure simplesmente clique **Conta gratuita** na parte superior desta página e, em seguida, regresse para concluir deste artigo.
- Uma base de dados do Azure SQL. Se não tiver uma base de dados do SQL, crie uma seguir os passos neste artigo: [criar a primeira base de dados do Azure SQL](sql-database-get-started.md).
- Azure PowerShell.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Alterar o nível de camadas e o desempenho do serviço da base de dados SQL

Executar [conjunto-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet e configurar o **-RequestedServiceObjectiveName** para o nível de desempenho da camada comparar pretendida; por exemplo *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Exemplo de script PowerShell para alterar o nível de camadas e o desempenho do serviço da base de dados SQL

Substituir ```{variables}``` com os valores (não inclui as chavetas).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Próximos passos

- [Dimensionar a saída e no](sql-database-elastic-scale-get-started.md)
- [Ligar e uma base de dados SQL com SSMS de consulta](sql-database-connect-query-ssms.md)
- [Exportar uma base de dados do Azure SQL](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- [Documentação de base de dados SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlets da base de dados azure SQL] (http://msdn.microsoft.com/library/azure/mt574084https :/ / msdn.microsoft.com/biblioteca/azure/mt619433(v=azure.300\).aspx.aspx)
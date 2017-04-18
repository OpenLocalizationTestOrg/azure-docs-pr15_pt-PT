<properties
    pageTitle="Replicação de bases de dados global DocumentDB | Microsoft Azure"
    description="Saiba como gerir a replicação global da sua conta DocumentDB através do portal do Azure."
    services="documentdb"
    keywords="base de dados global, replicação"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Como efetuar replicação de bases de dados global DocumentDB através do portal Azure

Saiba como utilizar o portal do Azure para criar uma réplica dados em várias regiões para global disponibilidade de dados no Azure DocumentDB.

Para obter informações sobre a base de dados global como funciona a replicação no DocumentDB, consulte o artigo [dados distribuir globalmente com DocumentDB](documentdb-distribute-data-globally.md). Para obter informações acerca da realização replicação de bases de dados global através de programação, consulte o artigo [em desenvolvimento com contas de DocumentDB com várias região](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Distribuição global DocumentDB bases de dados é ativada automaticamente para todas as contas de DocumentDB recentemente criadas e ficará disponível. Estamos a trabalhar para ativar a distribuição global em todas as contas existentes, mas entretanto, se pretender que a distribuição globais ativada na sua conta, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e podemos irá ativá-lo para agora.

## <a id="addregion"></a>Adicionar regiões de base de dados global

DocumentDB está disponível na maioria das [regiões Azure] [azureregions]. Após selecionar o nível de consistência predefinido para a sua conta de base de dados, pode associar uma ou mais regiões (dependendo da sua escolha necessidades de distribuição globais e de nível de consistência predefinido).

1. No [portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas de DocumentDB**.
2. No pá **DocumentDB conta** , selecione a conta de base de dados para modificar.
3. Na pá conta, clique em **criar uma réplica globalmente dados** a partir do menu.
4. Na pá **replica globalmente dados** , selecione as regiões para adicionar ou remover e, em seguida, clique em **Guardar**. Existe um custo a adição de regiões, consulte o artigo [preços página](https://azure.microsoft.com/pricing/details/documentdb/) ou o artigo de [dados de distribuir globalmente com DocumentDB](documentdb-distribute-data-globally.md) para obter mais informações.

    ![Clique em regiões no mapa para adicionar ou removê-los][1]

### <a name="selecting-global-database-regions"></a>Selecionar regiões de base de dados global

Quando configurar duas ou mais regiões, recomenda-se que regiões estão selecionados, com base nos pares de região descritos a [empresas continuidade e falhas recuperação (BCDR): Azure emparelhadas regiões]  [ bcdr] artigo.

Especificamente, quando configurar para várias regiões, certifique-se selecionar o mesmo número de regiões (+ /-1, ímpar/par) de cada uma das colunas região pares. Por exemplo, se pretender implementar quatro regiões de EUA, selecione duas áreas dos e.u.a. a partir de coluna da esquerda e dois da direita. Por isso, a seguinte seria um conjunto adequado: ocidental dos EUA, Leste dos EUA, América do Norte Central-nos e Sul Central-nos.

Estas orientações são importante para seguir quando regiões apenas dois estão configuradas para cenários de recuperação de falhas. Para mais de duas regiões, seguir estas orientações é aconselhável, mas não críticas, desde que algumas das regiões selecionadas adiram este emparelhar.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Próximos passos

Saiba como gerir a consistência da sua conta de replicadas globalmente ao ler o artigo [níveis de consistência no DocumentDB](documentdb-consistency-levels.md).

Para obter informações sobre a base de dados global como funciona a replicação no DocumentDB, consulte o artigo [dados de distribuir globalmente com DocumentDB](documentdb-distribute-data-globally.md). Para obter informações sobre a replicação através de programação de dados em várias regiões, consulte o artigo [em desenvolvimento com contas de DocumentDB com várias região](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/

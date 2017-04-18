<properties 
    pageTitle="Configurar a replicação de Geo para base de dados do SQL Azure com o portal do Azure | Microsoft Azure" 
    description="Configurar a replicação de Geo para base de dados do SQL Azure através do portal Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurar a replicação de Geo para base de dados do SQL Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra-lhe como configurar a replicação do Geo Active para base de dados com o [Azure portal](http://portal.azure.com)SQL.

Para iniciar activação pós-falha com o portal do Azure, consulte o artigo [Iniciar planeado ou não planeado activação pós-de uma falha, para a base de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Geo-a replicação do Active (secundários legíveis) está agora disponível para todas as bases de dados em todas as camadas de serviço. Na de 2017 de Abril de, vai ser foi removido o tipo de secundário que não sejam legíveis e bases de dados que não sejam legíveis existentes serão automaticamente atualizadas para secundários legíveis.

Para configurar a replicação-Geo através do portal Azure, tem o seguinte recurso:

- Azure base de dados SQL - a base de dados principal ao qual pretende criar uma réplica para uma região geográfica diferente.

## <a name="add-secondary-database"></a>Adicionar base de dados secundária

Os passos seguintes criam uma nova base de dados secundária numa parceria de replicação Geo.  

Para adicionar um secundário, tem de ser o proprietário da subscrição ou coproprietário. 

A base de dados secundária terão o mesmo nome que a base de dados principal e, por predefinição, terá o mesmo nível de serviço. A base de dados secundária pode ser uma base de dados única ou uma base de dados flexível. Para mais informações, consulte o artigo [Camadas de serviços](sql-database-service-tiers.md).
Depois de secundária é criada e propagada, dados começarão a replicação da base de dados principal para a nova base de dados secundária. 

> [AZURE.NOTE] Se a base de dados do parceiro já existe (por exemplo - estatístico como resultado da terminar uma relação de replicação Geo anterior) o comando falhará.

### <a name="add-secondary"></a>Adicionar secundário

1. No [portal do Azure](http://portal.azure.com), navegue para a base de dados que pretende configurar para a replicação Geo.
2. Na página de base de dados SQL, selecione **Geo replicação**e, em seguida, selecione a região para criar a base de dados secundária. Pode selecionar qualquer região diferente da região que aloja a base de dados principal, mas recomenda-se a [região par](../best-practices-availability-paired-regions.md) .

    ![configurar a replicação Geo](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Selecione ou configurar o servidor e comparar camada para a base de dados secundária.

    ![configurar o secundário](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Opcionalmente, pode adicionar uma base de dados secundária para um conjunto de dados da base de dados flexível:

 Para criar a base de dados secundária num conjunto de dados, clique em **conjunto de base de dados flexível** e selecione um agrupamento no servidor de destino. Um conjunto de dados tem de existir no servidor de destino como este fluxo de trabalho não cria um conjunto de dados.

6. Clique em **Criar** para adicionar secundária.
 
6. A base de dados secundária é criado e começa o processo de propagação. 
 
    ![configurar o secundário](./media/sql-database-geo-replication-portal/seeding0.png)

7. Quando o processo de propagação estiver concluído, a base de dados secundária apresenta o respetivo estado.

    ![Propagar concluída](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Remover a base de dados secundária

A operação permanentemente termina a replicação à base de dados secundária e a função dos secundária para uma base de dados de leitura / escrita normal. Se a ligação à base de dados secundária for interrompida, o comando é concluída com êxito mas o irá secundário não tornam-se leitura / escrita até após a conectividade é restaurado.  

1. No [portal do Azure](http://portal.azure.com), navegue para a base de dados principal a parceria de replicação Geo.
2. Na página de base de dados SQL, selecione **Geo replicação**.
3. Na lista de **secundários** , selecione a base de dados que pretende remover da parceria de replicação Geo.
4. Clique em **Parar a replicação**.

    ![remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Clicar em **Parar replicação** abre uma janela de confirmação por isso, clique em **Sim** para remover a base de dados da parceria de replicação Geo (configurá-lo a uma base de dados de leitura / escrita não faz parte de qualquer replicação).


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre Geo-replicação do Active, consulte o artigo - [Geo-a replicação do Active](sql-database-geo-replication-overview.md)
- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)


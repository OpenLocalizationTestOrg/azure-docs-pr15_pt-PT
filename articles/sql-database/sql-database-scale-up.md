<properties
    pageTitle="Alterar o nível de camadas e o desempenho do serviço de uma base de dados do Azure SQL | Microsoft Azure"
    description="Alterar a camada de serviço e nível de desempenho de uma base de dados do Azure SQL mostra como dimensionar a base de dados do SQL para cima ou para baixo. Alterar a camada comparar da base de dados Azure SQL."
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


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Alterar o serviço camada e desempenho nível (preços camada) de uma base de dados do SQL Azure no portal


> [AZURE.SELECTOR]
- [**Portal do Azure**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Camadas de serviços e níveis de desempenho descrevem as funcionalidades e recursos disponíveis para a base de dados do SQL e podem ser atualizados como as necessidades da sua alteração de aplicação. Para obter detalhes, consulte o artigo [Camadas de serviços](sql-database-service-tiers.md).

Tenha em atenção que alterar a camada de serviço e/ou cria uma réplica da base de dados original ao nível de desempenho do novo nível de desempenho de uma base de dados e, em seguida, muda ligações ao longo para a réplica. Dados não são perdidos durante este processo mas durante o breve momento em que quando podemos mudar para a réplica, as ligações para a base de dados estão desactivadas, para que algumas operações em voos poderão ser revertidas. Esta janela varia, mas é média em 4 segundos e mais de 99% de casos é menor que 30 segundos. Muito raramente, especialmente se não existirem grandes números de transações na voo nas ligações de momento em que estão desativados, esta janela pode ser maior.  

A duração de todo o processo de escala de segurança depende camada tamanho e service da base de dados antes e depois da alteração. Por exemplo, uma base de dados de 250 GB que está a mudar para a partir de dentro de uma camada de serviço padrão, deverá concluída dentro 6 horas. Para uma base de dados do mesmo tamanho que está a alterar níveis de desempenho dentro da camada de serviço Premium, deve concluir dentro de 3 horas.


Utilize as informações nas [camadas de serviços de base de dados de SQL Azure e níveis de desempenho](sql-database-service-tiers.md) para determinar o nível de camadas e o desempenho do serviço adequado para a base de dados do SQL Azure.

- Para alterar uma base de dados, a base de dados deve ser mais pequeno do que o tamanho máximo permitido a camada de serviço de destino. 
- Ao atualizar uma base de dados com a [Replicação Geo](sql-database-geo-replication-overview.md) ativado, primeiro tem de actualizar suas bases de dados secundárias para a camada de desempenho desejado antes de atualizar a base de dados principal.
- Quando mudar uma camada de serviço, primeiro tem de terminar todas as relações de replicação Geo. 
- As ofertas de serviço de restaurar são diferentes para as várias camadas de serviço. Se está a mudar podem perder a capacidade de restaurar a um ponto no tempo ou se tiver um período de retenção de cópia de segurança inferior. Para mais informações, consulte [o cópia de segurança da base de dados do SQL Azure e restaurar](sql-database-business-continuity.md).
- Alterar a sua base de dados preços camadas não altera o tamanho máximo da base de dados. Para alterar a sua base de dados o tamanho máximo utilize [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- As novas propriedades da base de dados não são aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo precisa do seguinte:**

- Uma base de dados do Azure SQL. Se não tiver uma base de dados do SQL, crie uma seguir os passos neste artigo: [criar a primeira base de dados do Azure SQL](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Alterar o nível de camadas e o desempenho do serviço da base de dados


Abra o pá de base de dados SQL para a base de dados que quer Dimensionar para cima ou para baixo:

1.  No [portal do Azure](https://portal.azure.com), clique em **mais serviços** > **bases de dados SQL**.
2.  Clique em da base de dados que pretende alterar.
3.  No pá a **base de dados SQL** clique **preços camada (escala DTUs)**:

    ![preços de camadas][1]

1.  Selecione uma nova camada e clique em **Seleccionar**:

    Clicar em **Selecione** submete um pedido de escala para alterar a definição de preços camada. Dependendo do tamanho da base de dados a operação de escala pode demorar algum tempo a concluir (consulte as informações na parte superior deste artigo).

    > [AZURE.NOTE] Alterar a sua base de dados preços camadas não altera o tamanho máximo da base de dados. Para alterar a sua base de dados o tamanho máximo utilize [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Selecione a camada comparar][2]

3.  Clique no ícone de notificação (sino) no canto superior direito:

    ![notificações][3]

    Clique no texto de notificação para abrir o painel de detalhes onde pode ver o estado do pedido.




## <a name="next-steps"></a>Próximos passos

- Altere o tamanho máximo de base de dados utilizando [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Dimensionar a saída e no](sql-database-elastic-scale-get-started.md)
- [Ligar e uma base de dados SQL com SSMS de consulta](sql-database-connect-query-ssms.md)
- [Exportar uma base de dados do Azure SQL](sql-database-export.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- [Documentação de base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

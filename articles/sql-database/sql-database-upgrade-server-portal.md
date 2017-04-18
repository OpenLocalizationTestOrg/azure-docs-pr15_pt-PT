<properties
    pageTitle="Atualização para o V12 de base de dados do SQL Azure através do portal Azure | Microsoft Azure"
    description="Explica como atualizar para V12 de base de dados do SQL Azure, incluindo como atualizar as bases de dados Web e empresas e como atualizar um servidor de V11 migrar as suas bases de dados diretamente para um agrupamento de base de dados flexível utilizando o portal do Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Atualização para o V12 de base de dados do SQL Azure através do portal Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de dados do SQL é a versão mais recente, por isso atualizar servidores existentes para V12 de base de dados do SQL é recomendado.
V12 de base de dados do SQL tem várias [vantagens em relação à versão anterior](sql-database-v12-whats-new.md) incluindo:

- Maior compatibilidade com o SQL Server.
- Desempenho melhorado premium e novos níveis de desempenho.
- [Agrupamentos de base de dados flexível](sql-database-elastic-pool.md).

Este artigo fornece instruções para atualizar os servidores de SQL V11 de base de dados existentes e bases de dados para o V12 de base de dados do SQL.

Durante o processo de atualizar para o V12 irá atualizar quaisquer bases de dados Web e empresas para uma nova camada de serviço para que as indicações para atualizar as bases de dados Web e empresas são incluídas.

Além disso, migrar para um [conjunto de base de dados flexível](sql-database-elastic-pool.md) pode ser mais custo eficaz que atualizar para níveis de desempenho individuais (preços camadas) para bases de dados simples. Conjuntos de dados também simplificam a gestão de base de dados porque apenas necessárias para gerir as definições de desempenho para o conjunto em vez de gerir separadamente os níveis de desempenho das bases de dados individuais. Se tiver bases de dados em múltiplos servidores considere movê-las no mesmo servidor e tomar partido colocá-los num conjunto de dados. Pode facilmente [Migrar automática bases de dados de servidores V11 diretamente na agrupamentos de base de dados flexível através do PowerShell](sql-database-upgrade-server-powershell.md). Também pode utilizar o portal de migrar V11 bases de dados para um conjunto de dados, mas no portal do já tem de ter um servidor de V12 para criar um conjunto de dados. Indicações são fornecidas neste artigo para criar o conjunto de após a atualização do servidor, se tiver [bases de dados que podem beneficiar a partir de um conjunto de dados](sql-database-elastic-pool-guidance.md).

Tenha em atenção que as bases de dados irão permanecer online e continuar a trabalhar em toda a operação de atualização. No momento da transição real para o novo nível de desempenho temporário largando das ligações à base de dados pode ocorrer durante uma duração muito pequena que é normalmente cerca de 90 segundos mas podem ser quanto 5 minutos. Se a aplicação tiver [breves falhas de processamento fontes de ligação](sql-database-connectivity-issues.md) , é suficiente para proteger contra ligações interrompidas no final da atualização.

Atualizar para o SQL V12 de base de dados não pode ser anulada. Após uma atualização no servidor não pode ser revertido para V11.

Após atualizar para V12, [recomendações de camada de serviço](sql-database-service-tier-advisor.md) e [Considerações sobre o desempenho agrupamento flexível](sql-database-elastic-pool-guidance.md) não imediatamente estará disponíveis até que o serviço tenha tempo para avaliar a sua das cargas de trabalho no novo servidor. Histórico de recomendação V11 server não se aplica aos servidores V12 para que não é retida.

## <a name="prepare-to-upgrade"></a>Preparar a atualizar

- **Atualizar todas as bases de dados Web e empresas**: consulte o artigo [Atualizar todas as bases de dados Web e empresas](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) abaixo da secção ou consulte o artigo [Monitor e gerir um agrupamento de base de dados flexível (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Rever e suspender Geo replicação**: se a base de dados do Azure SQL estiver configurado para a replicação Geo deve documentar sua configuração atual e [Parar a replicação Geo](sql-database-geo-replication-portal.md#remove-secondary-database). Após a atualização for concluída reconfigure a base de dados para a replicação Geo.
- **Abra as seguintes portas se tiver clientes numa VM Azure**: se o programa cliente liga-se ao V12 de base de dados do SQL enquanto o cliente é executada numa máquina virtual Azure (VM), terá de abrir intervalos de porta 11000 11999 e 14000-14999 na VM. Para obter mais detalhes, consulte [portas para V12 de base de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Iniciar a atualização

1. Em Procurar o [Azure portal](https://portal.azure.com/) no servidor que pretende atualizar ao selecionar **Procurar** > **SQL servers**e selecionando o servidor de 2.0 que pretende atualizar.
2. Selecione a **Atualização mais recente de base de dados de SQL**, em seguida, selecione **actualizar este servidor**.

      ![actualizar o servidor][1]

3. Atualizar um servidor para a atualização mais recente de base de dados SQL é permanentes e irreversíveis. Para confirmar a atualização, escreva o nome do seu servidor e clique em **OK**.

## <a name="upgrade-all-web-and-business-databases"></a>Atualizar todas as bases de dados Web e empresas

Se o servidor tiver quaisquer bases de dados Web ou empresas tem de actualizá-los. Durante o processo de atualizar para o V12 de base de dados do SQL irá atualizar todas as bases de dados Web e empresas para uma nova camada de serviço.    

Para ajudá-lo com atualizar, o serviço de base de dados SQL recomenda um adequado camada e desempenho do nível de serviço (preços camada) para cada base de dados. O serviço recomenda a camada melhor para executar a carga de trabalho do sua base de dados existente através da análise de utilização de histórica para a base de dados.

3. Na pá **actualizar este servidor** Selecione cada base de dados para rever e selecione preços recomendadas camada atualizar para. Pode sempre procurar as camadas comparar disponíveis e selecione aquele que melhor se adequa ao seu ambiente.


     ![bases de dados][2]


7. Depois de clicar na camada sugerida será apresentada com a pá **Escolher a camada comparar** onde pode selecionar uma camada e, em seguida, clique no botão **Selecionar** para alterar para essa camada. Selecione uma nova camada para cada base de dados Web ou para empresas.

    O que é importante ter em atenção é que a base de dados do SQL não está bloqueado para qualquer serviço específico camada ou desempenho nível, o modo como os requisitos da sua alteração de base de dados pode alterar facilmente entre as camadas de serviços disponíveis e os níveis de desempenho. Na verdade, Basic, padrão e bases de dados do SQL Premium são faturada por hora e tiver a capacidade de dimensionar cada base de dados para cima ou para baixo 4 vezes dentro de um período de 24 horas.

    ![recomendações][6]


Depois de todas as bases de dados no servidor são elegíveis está pronto para iniciar a atualização

## <a name="confirm-the-upgrade"></a>Confirmar a atualização

3. Quando todas as bases de dados no servidor são elegíveis para a atualização é necessário para **Escrever o nome do servidor** para confirmar que pretende executar a atualização e, em seguida, clique em **OK**.

    ![Verifique se a atualização][3]


4. A atualização é iniciado e apresenta o progresso no notificação. O processo de atualização é iniciado. Consoante os detalhes das suas bases de dados específicos atualizar para o V12 pode demorar algum tempo. Durante este período de tempo todas as bases de dados no servidor vai permanecer online mas servidor e acções de gestão de base de dados serão restritas.

    ![Atualizar o progresso da][4]

    No momento da transição real para o desempenho novo nível de temporário largando das ligações à base de dados pode ocorrer durante uma duração muito pequena (normalmente medida em segundos). Se uma aplicação tiver processamento de falhas breves (lógica de repetição) para fontes de ligação, é suficiente para proteger contra ligações interrompidas no final da atualização.

5. Depois de concluída a operação de atualização pá a **Atualização mais recente** irá apresentar **ativado**.

    ![V12 activado][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Mover as bases de dados para um agrupamento de base de dados flexível

No [portal do Azure](https://portal.azure.com/) navegue para o servidor V12 e clique em **Adicionar conjunto**.

- ou -

Se vir uma mensagem a informar **clique aqui para ver os conjuntos de base de dados flexível recomendado para este servidor**, clique na mesma para criar um conjunto que está optimizado para bases de dados do seu servidor facilmente. Para obter detalhes, consulte o artigo [preço e desempenho considerações para um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md).

![Adicionar agrupamento a um servidor][7]

Siga as instruções no artigo [criar um agrupamento de base de dados flexível](sql-database-elastic-pool.md) para concluir a criação do seu conjunto.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Monitorizar bases de dados após atualizar para o V12 de base de dados SQL

>[AZURE.IMPORTANT] Atualizar para a versão mais recente do SQL Server Management Studio (SSMS) para tirar partido das capacidades de v12 novo. [Transferir o SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Depois de atualizar, ativamente monitorizar a base de dados para garantir que aplicações estão em execução no desempenho pretendido e, em seguida, optimize as definições conforme necessário.

Para além de monitorizar bases de dados individuais, pode monitorizar agrupamentos de base de dados flexível [Monitor, gerir e tamanho de um conjunto de base de dados flexível com o portal do Azure](sql-database-elastic-pool-manage-portal.md) ou com o [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Dados consumo de recursos:** Para bases de dados Basic, padrão e Premium dados consumo de recursos estão disponíveis através de [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) das DMV da base de dados do utilizador. Fornece das DMV junto à informação de consumo de recursos em tempo real no granularidade segunda 15 de hora anterior de operação. O consumo de percentagem DTU por um intervalo é calculado como o consumo de percentagem máxima das dimensões CPU, IO e registo. Eis uma consulta para calcular o média consumo de percentagem DTU através de última hora:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informações de monitorização adicionais:

- [Base de dados do SQL azure desempenho seta de quatro pontas única bases de dados](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerações sobre preço e desempenho para um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md).
- [Base de dados do SQL Azure utilizando vistas dinâmicas de gestão de monitorização](sql-database-monitoring-with-dmvs.md)




**Alertas:** Configure o 'Alertas' no portal do Azure para o notifique quando o consumo DTU para uma base de dados atualizado abordagens determinado alto nível. Alertas de base de dados podem ser o programa de configuração no portal do Azure para várias métricas de desempenho como DTU, CPU, IO e registo. Navegue até à sua base de dados e selecione **regras alertas** na pá **Definições** .

Por exemplo, pode configurar um alerta de correio eletrónico "DTU percentagem" se o valor médio de percentagem DTU exceder 75% sobre os últimos 5 minutos. Referir-se a [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) para saber mais sobre como configurar as notificações de alerta.





## <a name="next-steps"></a>Próximos passos

- [Verificar existência de agrupamento de recomendações e criar um conjunto de dados](sql-database-elastic-pool-create-portal.md).
- [Alterar o nível de camadas e o desempenho do serviço da base de dados](sql-database-scale-up.md).



## <a name="related-links"></a>Ligações relacionadas

- [O que há de novo no V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [Planear e preparar-se para atualizar para o V12 de base de dados SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

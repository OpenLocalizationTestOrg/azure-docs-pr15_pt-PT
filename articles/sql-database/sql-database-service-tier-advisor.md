<properties 
   pageTitle="Preços recomendações de camada para a base de dados do SQL Azure" 
   description="Quando alterar preços camadas no portal do Azure, preços recomendações camada são desde que recomendar a camada que melhor se adequada para executar a carga de trabalho do Azure SQL base de dados existente. Comparar camadas descrevem o nível de camadas e o desempenho do serviço de uma base de dados do SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>Recomendações de camada comparar base de dados SQL

 Comparar camada recomendações sugerem o nível de camadas e desempenho de serviço que é mais adequado para a execução de carga de trabalho do Azure SQL base de dados existente.

> [AZURE.NOTE] Comparar camada recomendações só estão disponíveis para bases de dados Web e empresas e agrupamentos de base de dados flexível – e só está disponível no [portal do Azure](https://portal.azure.com/).


Obter preços recomendações camada durante as seguintes tarefas:

- [Alterar o serviço camada e desempenho nível (preços camada) de uma base de dados SQL](sql-database-scale-up.md)
- [Atualização Azure SQL server para V12](sql-database-upgrade-server-portal.md)
- Navegue até ao seu servidor V12. Consulte o artigo [da base de dados do SQL preços recomendações de camadas](sql-database-service-tier-advisor.md).
- [Criar um conjunto de dados da base de dados flexível](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Descrição geral

O serviço de base de dados SQL analisa desempenho atual e requisitos de funcionalidade por avaliação da utilização de recursos históricas para uma base de dados do SQL. Além disso, a camada de serviço aceitável mínimo é determinada com base em tamanho da base de dados e funcionalidades ativadas [continuidade do negócio](sql-database-business-continuity.md) . 

Esta informação está analisada e o nível de camadas e desempenho de serviço que melhor se adequada para executar a carga de trabalho normal a base de dados e manutenção de é atual conjunto de funcionalidades é recomendado.

- O serviço examina 15 a 30 dias anteriores dos dados de histórico (utilização de recursos, o tamanho da base de dados e atividade de base de dados) e executa uma comparação entre a quantidade de recursos consumidas e as limitações reais dos níveis de desempenho e camadas de serviços encontra-se disponível.
- Dados são analisados em intervalos segundo 15 e conjunto de resultados de cada intervalo estiver categorizado para a camada de serviço existente e nível de desempenho que melhor se adequadas para o processamento de carga de trabalho do conjunto de resultados.
- Estes 15 segundos de amostras, em seguida, são agregadas a análise de 15-30 dias maior e o nível de camadas e desempenho de serviço ideal pode processar 95% da carga de trabalho histórica é recomendado.

### <a name="recommendations"></a>Recomendações

Com base em utilização da sua base de dados, existem atualmente 2 categorias de recomendações que podem ser encontradas:


| Recomendação | Descrição |
| :--- | :--- |
| Atualização | Atualize para uma nova camada. |
| Indisponível | Uma base de dados requer uma mínima carga de trabalho ou aproximadamente 35 dias após a atividade. Não é suficiente dados para fornecer uma recomendação válida. |

## <a name="getting-pricing-tier-recommendations"></a>Introdução a preços recomendações de camadas

Obter preços recomendações camada ao selecionar Web ou empresas base de dados existente, clique em **todas as definições**e, em seguida, clique em **níveis de preços (escala DTUs)**. (Comparar camada recomendações também estão disponíveis quando que [atualizar o Azure SQL server para V12](sql-database-upgrade-server-portal.md).)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar** > **bases de dados SQL**.
4. Em pá **bases de dados SQL** , clique em base de dados que pretende ver uma recomendação para:

    ![Selecione a base de dados][1]

5. No pá da base de dados, selecione **todas as definições** , em seguida, selecione a **camada de preços (escala DTUs)**.


7. Abra o **recomendado preços camadas** onde pode clicar a camada sugerida e, em seguida, clique no botão **Selecionar** para alterar para essa camada.

    ![Inscrever-se para a pré-visualização][4]

8. Opcionalmente, clique em **Ver detalhes de utilização** para abrir o pá **Preços detalhes de recomendação camada** onde pode ver a camada recomendada para a base de dados, uma funcionalidade comparação entre camadas atuais e recomendadas e um gráfico de análise de utilização de recursos histórico.

    ![Inscrever-se para a pré-visualização][5]



## <a name="summary"></a>Resumo

Comparar camada recomendações fornecem uma experiência automatizada para recolher dados de telemetria para cada base de dados do SQL e recomendar a melhor serviço camada/desempenho nível combinação com base nas necessidades de desempenho real de uma base de dados e requisitos de funcionalidade. Na pá definições clique em **níveis de preços (escala DTUs)** para ver os preços recomendações de camadas para quaisquer bases de dados Web e empresas.



## <a name="next-steps"></a>Próximos passos

Consoante os detalhes da base de dados específico, efetuar uma atualização ou redução normalmente não ocorrer instantaneamente. O portal irá fornecer notificações como as transições de base de dados para a camada novo ou que pode monitorizar o estado de actualização consultando a vista de [sys.dm_operation_status (base de dados do SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) na base de dados principal a base de dados do SQL Server.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<properties 
   pageTitle="Conhecimentos aprofundados de desempenho de base de dados do Azure SQL | Microsoft Azure" 
   description="A base de dados do SQL Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas que podem melhorar o desempenho da consulta atual." 
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
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>Conhecimentos aprofundados de desempenho de base de dados SQL

Base de dados SQL Azure fornece ferramentas de desempenho para ajudá-lo a identificar e melhorar o desempenho das suas bases de dados, fornecendo ações de sintonização inteligentes e recomendações. 

1. Navegue para a base de dados no [Portal do Azure](http://portal.azure.com) e clique em **todas as definições de** > **Desempenho **  >  **Descrição geral** para abrir a página de **Desempenho** . 


2. Clique em **recomendações** para abrir a [Classificação de base de dados do SQL](#sql-database-advisor)e clique em **consultas** para abrir [Conhecimentos aprofundados de desempenho de consulta](#query-performance-insight).

    ![Desempenho da vista](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Descrição geral do desempenho

Clicar em **Descrição geral** ou no mosaico do **Desempenho** será direcionado para o dashboard de desempenho para a sua base de dados. Esta vista fornece um resumo do desempenho da base de dados e ajuda-o com e Otimização do desempenho de resolução de problemas. 

![Desempenho](./media/sql-database-performance/performance.png)

- O mosaico de **recomendações** fornece uma divisão de sintonização recomendações para a base de dados (recomendações de início 3 são apresentadas se existem mais). Clicar neste mosaico leva-o a **Classificação de base de dados do SQL**. 
- O mosaico de **optimização da atividade** fornece um resumo em curso e concluídas optimização ações para a base de dados, que lhe dá uma vista rápida para o histórico de sintonização atividades. Clicar neste mosaico leva-o para a vista completa do histórico optimização da base de dados.
- O mosaico **optimização automática** mostra a configuração automática de sintonização para a base de dados (as ações de sintonização estão configuradas para ser aplicado automaticamente a sua base de dados). Se clicar neste mosaico abre a caixa de diálogo de configuração de automatização.
- O mosaico de **consultas de base de dados** mostra o resumo do desempenho da consulta para a base de dados (geral DTU a utilização e a parte superior recurso consumir consultas). Clicar neste mosaico leva-o para **Conhecimentos aprofundados de desempenho de consulta**.



## <a name="sql-database-advisor"></a>Advisor de base de dados SQL


[Classificação de base de dados do SQL](sql-database-advisor.md) fornece inteligentes recomendações de sintonização do que o podem ajudar a melhorar o desempenho da sua base de dados. 

- Recomendações sobre qual índices para criar ou largar (e uma opção para aplicar as recomendações de índice remissivo automaticamente sem qualquer interação de utilizador e automaticamente a reverter recomendações tem um impacto negativo no desempenho).
- Recomendações para quando estão identificados problemas de esquema da base de dados.
- Recomendações para quando consultas beneficiar parametrizadas.




## <a name="query-performance-insight"></a>Conhecimentos aprofundados de desempenho de consulta

[Conhecimentos aprofundados de desempenho de consulta](sql-database-query-performance.md) permite-lhe perder menos tempo a resolução de problemas de desempenho de base de dados, fornecendo:

- Mais aprofundada visão o consumo de recursos (DTU) de bases de dados. 
- A CPU superior consumir consultas, o que podem ser potencialmente optimizadas para um desempenho melhorado. 
- A capacidade de desagregar os detalhes de uma consulta. 


## <a name="additional-resources"></a>Recursos adicionais

- [Orientação de desempenho de base de dados SQL Azure para bases de dados simples](sql-database-performance-guidance.md)
- [Quando deve ser utilizado um agrupamento de base de dados flexível?](sql-database-elastic-pool-guidance.md)
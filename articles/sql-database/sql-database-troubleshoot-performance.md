<properties
    pageTitle="Sugestões de sintonização de desempenho de base de dados SQL | Microsoft Azure"
    description="Sugestões para optimização da base de dados do SQL Azure através de avaliação e melhoria de desempenho."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="desempenho SQL Otimização do desempenho de bases de dados Otimização do desempenho sql optimização sugestões, ajuste de desempenho de base de dados do sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>Sugestões de sintonização de desempenho de base de dados SQL
Pode alterar a [camada de serviços](sql-database-service-tiers.md) da base de dados único ou aumentar eDTUs de um conjunto de base de dados flexível em qualquer altura para melhorar o desempenho, mas pretende identificar oportunidades para melhorar e otimizar o desempenho da consulta pela primeira vez. Índices em falta e consultas mal otimizadas são razões mais comuns para desempenho fraca bases de dados. Este artigo fornece orientações para optimização na base de dados SQL do desempenho.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Passos para avaliar e ajustar o desempenho de base de dados
1.  No [Portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**, selecione a base de dados e, em seguida, utilize o gráfico de monitorização para procurar as sua máximo a aproximação de recursos. Consumo de DTU é apresentado por predefinição. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
2.  Utilize [Conhecimentos aprofundados de desempenho de consulta](sql-database-query-performance.md) para avaliar as consultas utilizando DTUs e, em seguida, utilize a [Classificação de base de dados do SQL](sql-database-advisor.md) para ver as recomendações para criar e largando índices, parametrizar consultas e corrigir problemas de esquema.
3.  Pode utilizar vistas de gestão dinâmica (DMVs), eventos expandido (Xevents) e o arquivo de consulta SSMS para obter os parâmetros de desempenho em tempo real. Consulte o [tópico de orientação de desempenho](sql-database-performance-guidance.md) de monitorização e sugestões de sintonização detalhadas.


    > [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Passos para melhorar o desempenho de base de dados com mais recursos
1.  Para bases de dados simples, pode [Alterar camadas de serviços](sql-database-scale-up.md) a pedido para melhorar o desempenho da base de dados.
2.  Para várias bases de dados, considere utilizar [agrupamentos de base de dados flexível](sql-database-elastic-pool-guidance.md) para dimensionar automaticamente recursos.

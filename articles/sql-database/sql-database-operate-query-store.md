<properties
   pageTitle="Funcionamento da loja de consulta numa base de dados do Azure SQL"
   description="Saiba como trabalhar com o arquivo de consulta numa base de dados do Azure SQL"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Funcionamento da loja de consulta numa base de dados do Azure SQL 

Arquivo de consulta no Azure é uma funcionalidade de base de dados totalmente geridas que continuamente recolhe e apresenta informações detalhadas de históricas sobre todas as consultas. Poderá pensar sobre arquivo de consulta como sendo semelhantes a gravador de dados de um avião voo que significativamente simplifica o desempenho da consulta de resolução de problemas ambos para a nuvem e no local clientes. Este artigo explica aspetos específicos da loja de consulta no Azure a funcionar. Utilizar estes dados de consulta previamente recolhidos, pode rapidamente diagnosticar e resolver problemas de desempenho e assim perder tempo mais focar o seu negócio. 

Arquivo de consulta foi [globalmente disponíveis](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) na base de dados do SQL Azure desde Novembro de 2015. Arquivo de consulta é foundation para análise de desempenho e funcionalidades, como a [classificação de base de dados do SQL e Dashboard de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)de sintonização. No momento da publicação neste artigo, arquivo de consulta está em execução mais do que 200.000 bases de dados de utilizador no Azure, recolher as informações relacionadas com a consulta para vários meses, sem interrupções.

> [AZURE.IMPORTANT] A Microsoft está processo de ativação do arquivo de consulta para todas as bases de dados do Azure SQL (existentes e novos). 

## <a name="optimal-query-store-configuration"></a>Configuração do arquivo de consulta ideal

Esta secção descreve as predefinições da configuração ideal que foram concebidas para assegurar o funcionamento fiável da loja de consulta e dependentes funcionalidades, como [classificação de base de dados do SQL e Dashboard de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configuração predefinida está optimizada para recolha de dados contínuo, que é mínimo tempo gasto nos Estados OFF/READ_ONLY.

| Configuração | Descrição | Predefinido | Comentário |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Especifica o limite para o espaço de dados que pode tomar arquivo de consulta no interior da base de dados de cliente de z | 100 | Impostas para novas bases de dados |
| INTERVAL_LENGTH_MINUTES | Define o tamanho da janela de tempo durante o qual as estatísticas de runtime recolhidos para planos de consulta são agregadas e persistentes. Cada plano de consulta ativa tem pelo mais de uma linha para um período de tempo definido com esta configuração | 60   | Impostas para novas bases de dados |
| STALE_QUERY_THRESHOLD_DAYS | Política de limpeza baseados no tempo que controla o período de retenção de runtime persistentes estatísticas e consultas Inativas | 30 | Impostas para novas bases de dados e bases de dados com predefinição anterior (367) |
| SIZE_BASED_CLEANUP_MODE | Especifica se a limpeza de dados automática ocorre quando o tamanho do arquivo de consulta de dados abordagens o limite | AUTOMÁTICA | Impostas para todas as bases de dados |
| QUERY_CAPTURE_MODE | Especifica se são registadas todas as consultas ou apenas um subconjunto de consultas | AUTOMÁTICA | Impostas para todas as bases de dados |
| FLUSH_INTERVAL_SECONDS | Especifica o prazo máximo durante o qual capturado runtime estatísticas são mantidas na memória, antes de esvaziar no disco | 900 | Impostas para novas bases de dados |
||||||

> [AZURE.IMPORTANT] Estas predefinições são aplicadas automaticamente na fase final da ativação de arquivo de consulta em todas as bases de dados do Azure SQL (consulte anterior nota importante). Após esta luz para cima, base de dados do SQL Azure não alterar valores de configuração definidos pelo clientes, a menos que eles um impacto negativo carga de trabalho principal ou fiáveis operações do arquivo de consulta.

Se quiser manter-se com as definições personalizadas, utilize [ALTER DATABASE com opções de arquivo de consulta](https://msdn.microsoft.com/library/bb522682.aspx) para reverter configuração para o estado anterior. Dar saída [Melhores práticas com o arquivo de consulta](https://msdn.microsoft.com/library/mt604821.aspx) para saber como início escolheu parâmetros de configuração ideal.

## <a name="next-steps"></a>Próximos passos

[Conhecimentos aprofundados de desempenho de base de dados SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações consulte os artigos seguintes:

- [Um gravador de dados de voo para a base de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Monitorizar o desempenho ao utilizar o arquivo de consulta](https://msdn.microsoft.com/library/dn817826.aspx)

- [Cenários de utilização da loja de consulta](https://msdn.microsoft.com/library/mt614796.aspx)

- [Monitorizar o desempenho ao utilizar o arquivo de consulta](https://msdn.microsoft.com/library/dn817826.aspx) 

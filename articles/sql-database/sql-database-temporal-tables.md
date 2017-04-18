<properties
   pageTitle="Introdução ao Temporal tabelas na base de dados Azure SQL | Microsoft Azure"
   description="Saiba como começar a utilizar tabelas Temporal na base de dados do SQL Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introdução ao Temporal tabelas na base de dados Azure SQL

As tabelas temporais são uma nova funcionalidade de programabilidade da base de dados do SQL Azure que permite-lhe controlar e analisar o histórico completo das alterações nos seus dados, sem necessidade de codificação personalizada. Tabelas temporais manter dados estreitamente relacionados com contexto de tempo para que podem ser interpretados factos armazenados como válido apenas dentro do período específico. Esta propriedade de tabelas Temporal permite para análise de baseados no tempo eficiente e obter informações de evolução de dados.

##<a name="temporal-scenario"></a>Cenário temporal

Este artigo explica os passos para utilizar tabelas Temporal num cenário de aplicação. Imaginemos que pretende controlar a actividade utilizadores num Web site novo que está a ser desenvolvido do zero ou um Web site existente que pretende expandir com a análise de atividade do utilizador. Neste exemplo simplificado, vamos assumir que o número de páginas web visitadas durante um período de tempo é um indicador de que precisa de ser capturado e monitorizadas da base de dados de Web site está alojado numa base de dados do SQL Azure. O objetivo da análise histórico da atividade de utilizador é obter as entradas de reestruturar as Web site e fornecer a melhor experiência para os visitantes.

O modelo de base de dados para este cenário é muito simple – métrica de atividade de utilizador é representada com um campo de número inteiro única, **PageVisited**e é capturada juntamente com a informação básica sobre o perfil de utilizador. Para além disso, para uma análise de tempo com base que pretende manter uma série de linhas para cada utilizador em que cada linha representa o número de páginas de um determinado utilizador visitado dentro de um determinado período de tempo.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, não tem de colocar qualquer esforço na sua aplicação para manter estas informações de atividade. Com tabelas Temporal, este processo é automatizado - que lhe dá flexibilidade completa durante o design do Web site e mais tempo para focar a análise de dados própria. A única coisa que tem de fazer é para se certificar de que a tabela **WebSiteInfo** está configurada como [temporal com versões sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Os passos exatos para utilizar tabelas Temporal neste cenário são descritos abaixo.

##<a name="step-1-configure-tables-as-temporal"></a>Passo 1: Configurar tabelas como temporal

Dependendo de se são começando desenvolvimento novo ou atualizar a aplicação existente, irá criar tabelas temporais ou modificar os existentes adicionando atributos temporais. Em geral caso, seu cenário pode ser uma mistura destas duas opções. Execute estes ação utilizando o [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [As ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento Transact-SQL.


> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Criar nova tabela

Utilize o item de menu de contexto "Nova tabela com versões sistema" no Explorador do objeto SSMS para abrir o editor de consultas com um script do modelo de tabela temporal e, em seguida, utilize "Especificar valores de parâmetros de modelo" (Ctrl + Shift + M) para preencher o modelo:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

No SSDT, escolher o modelo de "Temporal tabela (sistema-com versões)" ao adicionar novos itens para o projeto de base de dados. Que irá abrir o estruturador de tabelas e permite-lhe especificar facilmente o esquema de tabela:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Também pode ver uma tabela temporal criar ao especificar as declarações de Transact-SQL diretamente, conforme mostrado no exemplo abaixo. Note que os elementos de cada tabela temporal obrigatórios são a definição do período e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de utilizador que irá guardar versões históricas linha:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Quando criar tabela temporal versões do sistema, a tabela histórico que acompanha com a configuração predefinida é criada automaticamente. A tabela de histórico predefinida contém um índice de árvore B agrupado nas colunas períodos (final, iniciar) com a compressão de página activada. Esta configuração é ideal para a maioria dos cenários na qual são utilizadas temporais tabelas, especialmente para [auditoria de dados](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Neste caso em particular, podemos destinam-se para efetuar análise de tendências com base no tempo através de um histórico de dados mais longo e com maiores conjuntos de dados, pelo que a opção de armazenamento para a tabela histórico é um índice columnstore agrupadas. Fornece um columnstore agrupada muito bom compressão e o desempenho analytical consultas. Tabelas temporais dar-lhe a flexibilidade de poder configurar índices nas tabelas atuais e temporais completamente de forma independente. 

**Nota**: Columnstore índices só estão disponíveis na camada de serviço premium.

O script seguinte mostra como índice predefinido na tabela histórico pode ser alterado para a columnstore agrupada:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tabelas temporais são representadas no Explorador do objeto com o ícone específico para a identificação mais fácil, enquanto a sua tabela histórico é apresentada como um nó subordinado.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Alterar a tabela existente para temporal

Vamos abordar o cenário alternativo na qual a tabela WebsiteUserInfo já existe, mas não foi concebida para manter um registo de alterações. Neste caso, pode simplesmente expandir a tabela existente para se tornar temporal, conforme mostrado no exemplo seguinte:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Passo 2: Execute regularmente sua carga de trabalho

A vantagem principal de tabelas Temporal é que não é necessário alterar ou ajustar o seu Web site de qualquer forma para executar o registo de alterações. Depois de criada, tabelas Temporal persistirem transparente versões anteriores de linha, sempre que executar modificações nos seus dados. 

Para poder aproveitar automática monitorização de alterações este cenário em particular, vamos apenas atualizar coluna **PagesVisited** sempre quando o utilizador termina a suas sessão no Web site:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

É importante para Repare que a consulta de atualização não precisa de saber a hora exata quando ocorreu a operação real nem como dados histórico serão preservados para análises futuras. Ambos os aspetos automaticamente são processados pela base de dados do SQL Azure. O diagrama seguinte ilustra como os dados do histórico está a ser gerados no cada atualização.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Passo 3: Efetuar uma análise de dados de histórico

Agora quando temporal sistema-controlo de versões está ativado, a análise de dados históricas é apenas uma consulta afastando-o de. Neste artigo, irá fornecemos alguns exemplos desse endereço análise cenários comuns - para obter informações sobre todos os detalhes, explorar várias opções introduzidas com a cláusula [Para SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Para ver os principais utilizadores de 10 ordenados pelo número de páginas web visitadas partir uma hora de há, execute esta consulta:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Pode facilmente modificar esta consulta para analisar visitas de site a partir de um dia atrás, há um mês ou em qualquer ponto no passado que deseja.

Para executar análises estatísticas básicas para o dia anterior, utilize o exemplo seguinte:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Para procurar as atividades de um utilizador específico, dentro de um período de tempo, utilize a contidas cláusula in:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualização de gráfica é especialmente conveniente para consultas temporais como pode mostrar tendências e padrões de utilização numa intuitiva forma muito facilmente:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Esquema de tabela de evolução

Normalmente, terá de alterar o esquema da tabela temporal enquanto estão a fazer programação da aplicações. Para que, basta executar normal ALTER TABLE demonstrações e base de dados do SQL Azure serão corretamente propagados as alterações na tabela histórico. O script seguinte mostra como pode adicionar atributo adicional para controlo:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Da mesma forma, pode alterar a definição da coluna enquanto está ativa sua carga de trabalho:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Por fim, pode remover uma coluna que não são precisas.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
Em alternativa, utilize o mais recente [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para alterar o esquema de tabela temporal enquanto está ligado à base de dados (modo online) ou como parte do projeto da base de dados (modo offline).

##<a name="controlling-retention-of-historical-data"></a>Controlar retenção de dados de históricos

Com tabelas temporais versões do sistema, a tabela histórico pode aumentar o tamanho da base de dados mais do que tabelas normais. Uma tabela do histórico de grandes dimensões e crescente pode tornar-se um problema ambos devido a custos de armazenamento puro, bem como impor um desempenho de imposto de consultar temporal. Por conseguinte, a programação de uma política de retenção de dados para gerir os dados na tabela histórico é um aspecto importante de planeamento e gestão do ciclo de vida de cada tabela temporal. Base de dados SQL Azure, tem as seguintes abordagens para gerir os dados históricos na tabela temporal:

- [Tabela de partição](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Script de limpeza personalizada](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Próximos passos

Para obter informações detalhadas sobre tabelas Temporal, consulte o artigo [documentação MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite 9 de canal para ouvir uma [história de sucesso do cliente real temporal implementação](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e ver uma [live demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

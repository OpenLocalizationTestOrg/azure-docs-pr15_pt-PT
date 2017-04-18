<properties
   pageTitle="Visualizar dados armazém de dados SQL do Microsoft Azure do Power BI"
   description="Visualizar dados armazém de dados SQL com o Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Formação de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Este tutorial mostra-lhe como utilizar o Power BI para ligar ao armazenamento de dados SQL e criar algumas visualizações básicas.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Pré-requisitos

Para visualizar passo a passo neste tutorial, é necessário:

- Pré-carregado um armazém de dados SQL com a base de dados AdventureWorksDW. Para aprovisionar isto, consulte o artigo [criar um armazém de dados SQL][] e escolha carregar os dados de exemplo. Se já tiver um armazém de dados mas não possui dados de exemplo, pode [carregar os dados de exemplo manualmente][].


## <a name="1-connect-to-your-database"></a>1. a ligar à sua base de dados

Para abrir o Power BI e ligar a sua base de dados AdventureWorksDW:

1. Inicie sessão no [portal do Azure][].
2. Clique em **bases de dados SQL** e escolha a sua base de dados do armazém de dados do SQL AdventureWorks.

    ![Localizar a base de dados][1]

3. Clique no botão Abrir no Power BI.

    ![Botão do Power BI][2]

4. Agora deverá ver a página de ligação de SQL armazém de dados apresentando o seu endereço de web de base de dados. Clique em seguinte.

    ![Ligação do Power BI][3]

6. Introduza o seu nome de utilizador do Azure SQL server e palavra-passe e irá estar totalmente ligada à sua base de dados do armazém de dados SQL.

    ![Início de sessão do Power BI no][4]

7. Assim que tiver iniciado sessão no Power BI, clique no conjunto de dados AdventureWorksDW na pá para a esquerda. Isto vai abrir a base de dados.

    ![AdventureWorksDW abrir do Power BI][5]



## <a name="2-create-a-report"></a>2. a criar um relatório

Agora está pronto para utilizar o Power BI para analisar os seus dados de exemplo AdventureWorksDW. Para executar a análise, AdventureWorksDW tem uma vista denominada AggregateSales. Esta vista contém algumas as métricas chave para analisar as vendas da empresa.

1. Para criar um mapa do montante de vendas de acordo com código postal, no painel direito de campos, clique na vista de AggregateSales para expandi-lo. Clique nas colunas CódigoPostal e Montantedevendas para os selecionar.

    ![Power BI selecione AggregateSales][6]

    Power BI reconhece automaticamente este é dados geográficos e colocá-la num mapa para si.

    ![Mapa do Power BI][7]

2. Este passo cria um gráfico de barras que mostra a quantidade de vendas por rendimento de cliente. Para criar este Ir para a vista de AggregateSales expandida. Clique no campo SalesAmount. Arraste o campo Receita do cliente para a esquerda e largue-a numa eixo.

    ![Eixo select do Power BI][8]

    Vamos passa o gráfico de barras sobre à esquerda.

    ![Power BI barra][9]

3. Este passo cria um gráfico de linhas que mostra o montante de vendas por data da encomenda. Para criar este Ir para a vista de AggregateSales expandida. Clique em SalesAmount e DataDaEncomenda. Nas visualizações coluna clique no ícone de gráfico de linhas; Este é o primeiro ícone na segunda linha em visualizações.

    ![Gráfico de linha select do Power BI][10]

    Tem agora um relatório que mostra três diferentes visualizações de dados.

    ![Linha do Power BI][11]

Pode guardar o seu progresso em qualquer altura ao clicar em **ficheiro** e selecionar **Guardar**.

## <a name="next-steps"></a>Próximos passos
Agora que tenha a dada algum tempo a interessado com os dados de exemplo, consulte o artigo como [desenvolver][], [carregar][]ou [Migrar][]. Ou veja o [Web site do Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar manualmente dados de exemplo]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Criar um armazém de dados SQL]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com/
[Web site do Power BI]: http://www.powerbi.com/

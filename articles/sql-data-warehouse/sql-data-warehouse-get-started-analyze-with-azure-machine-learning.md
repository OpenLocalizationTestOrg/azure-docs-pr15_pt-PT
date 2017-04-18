<properties
   pageTitle="Analisar dados com o Azure máquina aprendizagem | Microsoft Azure"
   description="Utilize o Azure máquina de formação para criar uma modelo com base em dados armazenados no armazém de dados do SQL Azure de aprendizagem automática aspeto do Office."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure máquina aprendizagem

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Formação de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Neste tutorial utiliza Azure máquina de formação para criar uma modelo com base em dados armazenados no armazém de dados do SQL Azure de aprendizagem automática aspeto do Office. Especificamente, isto baseia-se uma campanha de marketing alvo para Adventure Works, Centro de cópias bicicleta, às previsões se um cliente é provável comprar uma bicicleta ou não.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Pré-requisitos
Para visualizar passo a passo neste tutorial, é necessário:

- Um armazém de dados SQL carregados previamente com dados de exemplo AdventureWorksDW. Para aprovisionar isto, consulte o artigo [criar um armazém de dados SQL][] e escolha carregar os dados de exemplo. Se já tiver um armazém de dados mas não possui dados de exemplo, pode [carregar os dados de exemplo manualmente][].

## <a name="1-get-data"></a>1. a obter dados
Os dados são na vista na base de dados AdventureWorksDW dbo.vTargetMail. Para ler estes dados:

1. Inicie sessão no [Azure máquina aprendizagem studio][] e clique no meu experiências.
2. Clique em **+ Novo** e selecione **Experiência em branco**.
3. Introduza um nome para a sua experiência: direccionado Marketing.
4. Arraste o módulo do **leitor** a partir do painel módulos para a tela.
5. Especifique os detalhes da sua base de dados do armazém de dados do SQL no painel de propriedades.
6. Especifique a **consulta** de base de dados para ler os dados de interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Execute a experiência clicando em **Executar** da tela de experiência.
![Executar a experiência][1]


Depois da experiência execução é concluída com êxito, clique na porta de saída na parte inferior do módulo leitor e selecione **visualizar** para ver os dados importados.
![Ver dados importados][3]


## <a name="2-clean-the-data"></a>2. limpar os dados
Para limpar os dados, largue algumas colunas que não são relevantes para o modelo. Para fazer isto:

1. Arraste o módulo de **Colunas de projeto** para a tela.
2. No painel de propriedades para especificar quais as colunas que pretende largar, clique em **Seletor de coluna de iniciação** .
![Colunas de projeto][4]

3. Excluir duas colunas: CustomerAlternateKey e GeographyKey.
![Remover colunas desnecessárias][5]


## <a name="3-build-the-model"></a>3. construir o modelo
Vamos vai dividir os dados 80-20: 80% para formar uma modelo e de 20% para testar o modelo de aprendizagem automática. Oferecemos irá utilizar dos algoritmos "Classe duas" para este problema de classificação binário.

1. Arraste o módulo de **divisão** para a tela.
2. Introduza 0,8 para a fração de linhas no primeiro conjunto de dados de saída no painel de propriedades.
![Dividir dados em conjunto de teste de verificação e formação][6]
3. Arraste o módulo de **Classe duas árvore de decisões aumentado** para a tela.
4. Arraste o módulo de **Modelo de comboio** para da tela e especificar as entradas. Em seguida, clique em **Iniciação selector de coluna** no painel de propriedades.
      - Em primeiro lugar de entrada: algoritmo de ML.
      - Segundo de entrada: dados para formar o algoritmo no.
![Ligar o módulo de modelo de comboio][7]
5. Selecione a coluna **BikeBuyer** como a coluna de prever.
![Selecione a coluna para prever][8]


## <a name="4-score-the-model"></a>4. o modelo de pontuação
Agora, vamos irá testar como executa o modelo de dados de teste. Vamos vai comparar o algoritmo de nossa escolha com um algoritmo de diferentes para ver que executa o melhor.

1. Arraste o módulo de **Modelo de pontuação** para a tela.
    Entrada pela primeira vez: formação entrada segunda modelo: testar dados ![o modelo de pontuação][9]
2. Arraste a **Classe de duas Bayes ponto máquina** para a tela de experiência. Vamos vai comparar como este algoritmo executa em comparação com duas classe aumentado árvore de decisão.
3. Copie e cole os módulos comboio modelo e modelo de pontuação na tela.
4. Arraste o módulo de **Modelo de avaliar** para a tela para comparar duas dos algoritmos.
5. **Executar** a experiência.
![Executar a experiência][10]
6. Clique na porta de saída na parte inferior do módulo avaliar modelo e clique em visualizar.
![Visualizar os resultados de avaliação][11]

Métricas fornecidas são a curva ROC, curva de diagrama e elevação de precisão resgate. Consultar estes métricas, podemos ver que o primeiro modelo executado melhor do que a segunda opção. Para ver o que o primeiro modelo previsto, clique na porta de saída do modelo de pontuação e clique em visualizar.
![Visualizar os resultados de pontuação][12]

Verá duas colunas mais adicionadas ao seu conjunto de dados de teste.

- Visita probabilidades: a probabilidade que um cliente é uma compradores de bicicleta.
- Etiquetas visita: a classificação concluída pelo modelo de – compradores de bicicleta (1) ou não (0). Este limiar de probabilidade para rotular está definido para 50% e pode ser ajustado.

Comparar a coluna BikeBuyer (real) com os rótulos classificados (predição), pode ver como bem executou o modelo. Como os passos seguintes, pode utilizar este modelo para efetuar previsões para novos clientes e publicar este modelo como um serviço web ou escrever resultados armazém de dados SQL.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a criação de modelos de aprendizagem de automática aspeto do Office, consulte [Introdução à máquina de formação no Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure studio de aprendizagem automática]:https://studio.azureml.net/
[Introdução a um computador aprendizagem no Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[carregar manualmente dados de exemplo]: sql-data-warehouse-load-sample-databases.md
[Criar um armazém de dados SQL]: sql-data-warehouse-get-started-provision.md

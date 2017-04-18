<properties
   pageTitle="Utilizar a aprendizagem máquina Azure com armazém de dados SQL | Microsoft Azure"
   description="Tutorial para utilizar o Azure máquina aprendizagem com armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Utilizar a aprendizagem máquina Azure com armazém de dados SQL

Azure máquina formação é um serviço de totalmente geridas analytics aspeto do Office que pode utilizar para criar modelos de aspeto do Office contra os seus dados no armazém de dados do SQL e, em seguida, publicar como serviços web pronto consumir. Pode saber as noções básicas ao ler o artigo [Introdução ao máquina de formação no Azure][]de aprendizagem automática e de análise de aspeto do Office.  Pode, em seguida, saiba como criar, formar, pontuação e testar a um modelo de aprendizagem máquina utilizando a [Criar experimentar tutorial][].

Neste artigo, vai aprender a efetue o seguinte procedimento utilizando o [Azure máquina aprendizagem Studio][]:

- Ler os dados da base de dados para criar, formar e pontuação um modelo de aspeto do Office
- Escrever dados para a base de dados


## <a name="read-data-from-sql-data-warehouse"></a>Ler os dados a partir do armazém de dados SQL

Vamos vai ler os dados da tabela de produto na base de dados AdventureWorksDW.

### <a name="step-1"></a>Passo 1

Iniciar uma nova experiência ao clicar em + novo na parte inferior da janela de máquina aprendizagem Studio, selecione experiência e, em seguida, selecione experimentar em branco. Selecione o nome de experiência predefinido na parte superior da tela e mude o nome para algo significado, por exemplo, previsão de preço de bicicleta.

### <a name="step-2"></a>Passo 2

Procure o módulo do leitor na paleta de conjuntos de dados e módulos à esquerda da tela experiência. Arraste o módulo à tela de experiência.
![][drag_reader]

### <a name="step-3"></a>Passo 3

Selecione o módulo do leitor e preencha o painel de propriedades.

1. Selecione a base de dados Azure SQL como a origem de dados.
2. Nome do servidor de base de dados: escreva o nome do servidor. Pode utilizar o [Azure portal][] para localizar isto.

![][server_name]

3. Nome da base de dados: escreva o nome de uma base de dados no servidor apenas especificado.
4. Nome de conta de utilizador do servidor: escreva o nome de utilizador de uma conta que possua permissões de acesso para a base de dados.
5. Palavra-passe de conta de utilizador Server: fornecer a palavra-passe para a conta de utilizador especificado.
6. Aceitar qualquer certificado do servidor: Utilize esta opção (menos segura) se pretende ignorar a revisão do certificado de site antes de ler os seus dados.
7. Consulta de base de dados: introduza uma instrução SQL que descreva os dados que pretende ler. Neste caso, podemos vai ler os dados da tabela Produto utilizando a seguinte consulta.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Passo 4

1. Execute a experiência, clicando em execução da tela de experiência.
2. Quando termina a experiência, o módulo do leitor terá uma marca de verificação verde para indicar que foi concluída com êxito. Tenha também em atenção terminado a executar o estado no canto superior direito.

![][run]

3. Para ver os dados importados, clique na porta de saída na parte inferior do dataset automóvel e selecione visualizar.


## <a name="create-train-and-score-a-model"></a>Criar, formar e um modelo de pontuação

Agora pode utilizar este conjunto de dados para:

- Criar um modelo: processar dados e definir funcionalidades
- Preparar o modelo: escolher e aplicar um algoritmo de aprendizagem
- Pontuação e testar o modelo: prever novo preço de bicicleta


![][model]

Para saber mais sobre como criar, formar, pontuação e testar uma máquina de utilização de modelo de formação [Criar experimentar tutorial][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Escrever dados armazém de dados do SQL Azure

Vamos gravará o conjunto de resultados para ProductPriceForecast tabela na base de dados AdventureWorksDW.

### <a name="step-1"></a>Passo 1

Procure o módulo sénior na paleta de conjuntos de dados e módulos à esquerda da tela experiência. Arraste o módulo à tela de experiência.

![][drag_writer]

### <a name="step-2"></a>Passo 2

Selecione o módulo sénior e preencha o painel de propriedades.

1. Selecione a base de dados Azure SQL como o destino de dados.
2. Nome do servidor de base de dados: escreva o nome do servidor. Pode utilizar o [Azure portal][] para localizar isto.
3. Nome da base de dados: escreva o nome de uma base de dados no servidor apenas especificado.
4. Nome de conta de utilizador do servidor: escreva o nome de utilizador de uma conta que possua permissões de escrita para a base de dados.
5. Palavra-passe de conta de utilizador Server: fornecer a palavra-passe para a conta de utilizador especificado.
6. Aceitar qualquer certificado de servidor (inseguro): selecione esta opção se não quiser ver o certificado.
7. Lista separados por vírgulas de colunas para serem guardados: forneça uma lista das colunas de conjunto de dados ou o resultado que pretende de saída.
8. Nome da tabela de dados: Especifique o nome da tabela de dados.
9. Lista separados por vírgulas de colunas de datatable: Especifique os nomes das colunas para utilizar na nova tabela. Os nomes das colunas podem ser diferentes daqueles no conjunto de dados de origem, mas deve listar o mesmo número de colunas aqui que o utilizador definiu para a tabela de resultados.
10. Número de linhas escritos por operação de SQL Azure: pode configurar o número de linhas que são escritas para uma base de dados do SQL numa operação.

![][writer_properties]

### <a name="step-3"></a>Passo 3

1. Execute a experiência, clicando em execução da tela de experiência.
2. Quando termina a experiência, todos os módulos terá uma marca de verificação verde para indicar que eles foi concluída com êxito.

## <a name="next-steps"></a>Próximos passos

Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Descrição geral do desenvolvimento armazém de dados SQL]: ./sql-data-warehouse-overview-develop.md
[Criar a experiência tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introdução a um computador aprendizagem no Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure máquina aprendizagem Studio]: https://studio.azureml.net/Home
[Portal do Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

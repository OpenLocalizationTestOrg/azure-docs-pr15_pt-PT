<properties
    pageTitle="Implementar os serviços web do Azure ML utilizam módulos de importação de dados e de exportação de dados | Microsoft Azure"
    description="Saiba como utilizar os dados de importação e exportação de dados módulos para enviar e receber dados de um serviço web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Implementar o Azure ML web services que utilizam a importação de dados e módulos de exportação de dados 

Quando cria uma experiência de aspeto do Office, normalmente, adicione uma entrada de serviço de web e de saída. Quando implementar a experiência, consumidores podem enviar e receber dados a partir do serviço web através de entradas e saídas. Para algumas aplicações, dados de um consumidor poderão estar disponível a partir de um feed de dados ou já residem na origem de dados externa, como o armazenamento de Blobs do Azure. Nestes casos, não precisam de ler e escrever dados através de entradas de serviço web e saídas. Podem, em vez disso, utilize o serviço de execução do lote (BES) para ler dados de origem de dados utilizando um módulo de importar dados e escrever os resultados de pontuação para uma localização de dados diferentes utilizar um módulo de exportar dados.

Os dados de importação e exportação módulos de dados, pode ler a partir do e escrever para um número de dados locais como um URL da Web através de HTTP, uma consulta Hive, uma base de dados Azure SQL, armazenamento de tabela do Azure, armazenamento de Blobs do Azure, um Feed de dados fornecem ou uma base de dados do SQL no local.

Este tópico utiliza o "exemplo 5: avaliar comboio, teste, para a classificação binária: Dataset adultos" de exemplo e assume que já foi carregado o conjunto de dados para uma tabela do Azure SQL com o nome censusdata.

## <a name="create-the-training-experiment"></a>Criar a experiência de formação 
 
Quando abre o "exemplo 5: avaliar comboio, teste, para a classificação binária: conjunto de dados para adultos" exemplo que utiliza o conjunto de dados de classificação de binário adultos contagem rendimento de exemplo. E a experiência da tela terá uma aspeto semelhante a imagem seguinte.

![Configuração inicial da experiência.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

Para ler os dados a partir da tabela do Azure SQL:

1.  Elimine o módulo do conjunto de dados.
2.  Na caixa de componentes de pesquisa, escreva importar.
3.  A partir da lista de resultados, adicione um módulo de *Importar dados* a tela de experiência.
4.  Ligar a saída do módulo *Importar dados* a entrada do módulo *LIMPARB dados em falta* .
5.  No painel Propriedades, selecione a **Base de dados do SQL Azure** na lista pendente **Origem de dados** .
6.  No **nome do servidor de base de dados**, o **nome da base de dados**, **nome de utilizador**e **palavra-passe de** campos, introduza as informações adequadas para a base de dados.
7.  No campo de consulta de base de dados, introduza a seguinte consulta.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  Na parte inferior da experiência da tela, clique em **Executar**.

## <a name="create-the-predictive-experiment"></a>Criar a experiência de aspeto do Office

Seguinte que configurar a experiência de aspeto do Office a partir do qual irá implementar o serviço web.

1.  Na parte inferior da tela experiência, clique em **Configurar o serviço Web** e selecione o **Aspeto do Office serviço Web [Recomendado]**.
2.  Remova *Web serviço entrada* e *saída de serviço Web módulos* da experiência de aspeto do Office. 
3.  Na caixa de componentes de pesquisa, escreva exportar.
4.  A partir da lista de resultados, adicione um módulo de *Exportar dados* da tela de experiência.
5.  Ligar a saída do módulo de *Modelo de classificação* a entrada do módulo *Exportar dados* . 
6.  No painel Propriedades, selecione a **Base de dados do SQL Azure** na lista pendente de destino de dados.
7.  No **nome do servidor de base de dados**, o **nome da base de dados**, **nome de conta de utilizador do servidor**e campos de **palavra-passe do conta de utilizador do servidor** , introduza as informações adequadas para a base de dados.
8.  No campo de **lista de colunas para serem guardados separados por vírgulas** , escreva classificados etiquetas.
9.  No **campo nome da tabela de dados**, escreva dbo. ScoredLabels. Se a tabela que se não existir, é criada quando a experiência é executada ou chama-se o serviço web.
10. No campo de **lista de colunas de datatable separados por vírgulas** , escreva ScoredLabels.

Quando escreve uma aplicação que liga para o serviço final web, pretende especificar uma consulta de entrada diferentes ou tabela de destino em tempo de execução. Para configurar estas entradas e saídas, pode utilizar a funcionalidade de parâmetros de serviço Web para definir a propriedade de *origem de dados* do módulo *Importar dados* e a propriedade de destino de dados de modo de *Exportar dados* .  Para obter mais informações sobre os parâmetros de serviço Web, consulte o artigo a [entrada de parâmetros de serviço Web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) a Cortana Intelligence e máquina aprendizagem blogue.

Para configurar os parâmetros do serviço Web para a consulta de importação e a tabela de destino:

1.  No painel de propriedades para o módulo *Importar dados* , clique no ícone na parte superior direita do campo de **consulta de base de dados** e selecione **Definir como parâmetro do serviço web**.
2.  No painel de propriedades para o módulo de *Exportar dados* , clique no ícone na parte superior direita do campo **nome da tabela de dados** e selecione **Definir como parâmetro do serviço web**.
3.  Na parte inferior do painel de propriedades de módulo de *Exportar dados* , na secção de **Parâmetros de serviço Web** , clique em consulta de base de dados e mude o nome para consulta.
4.  Clique em **nome da tabela de dados** e mudar o nome de- **tabela**.

Quando tiver terminado, a experiência deverá ter um aspeto semelhante ao seguinte imagem.
 
![Aspeto final de experiência.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora já pode implementar a experiência como um serviço web.

## <a name="deploy-the-web-service"></a>Implementar o serviço web 
Pode implementar a um clássico ou o novo serviço web.

### <a name="deploy-a-classic-web-service"></a>Implementar um serviço Web clássico

Para implementar o como um serviço Web clássica e criar uma aplicação consumi-lo:

1.  Na parte inferior da tela experiência, clique em executar.
2.  Quando tiver concluído a executar, clique em **Implementar o serviço Web** e selecione **Implementar o serviço Web [clássica]**.
3.  No dashboard de serviço web, localize a chave de API. Copiar e guardá-lo para utilizar mais tarde.
4.  Na tabela **Ponto final predefinido** , clique na ligação de **Execução de lote** para abrir a página de ajuda de API.
5.  No Visual Studio, crie uma aplicação de consola do c#.
6.  Na página de ajuda API, localize a secção de **Código de exemplo** na parte inferior da página.
7.  Copie e cole o código de exemplo c# no seu ficheiro Program.cs e remova todas as referências para o armazenamento de Blobs.
8.  Actualize o valor da variável *apiKey* com a chave de API guardada anteriormente.
9.  Localize a declaração de pedido e atualizar os valores de parâmetros do serviço Web que são transmitidos para os módulos de *Dados de importação* e *Exportação de dados* . Neste caso, irá utilizar a consulta original, mas defina um novo nome de tabela.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Execute a aplicação. 

Após a conclusão da executar, uma nova tabela é adicionada à base de dados que contém os resultados de pontuação.

### <a name="deploy-a-new-web-service"></a>Implementar um novo serviço Web

Para implementar o como um novo serviço Web e criar uma aplicação consumi-lo:

1.  Na parte inferior da experiência da tela, clique em **Executar**.
2.  Quando tiver concluído a executar, clique em **Implementar o serviço Web** e selecione **Implementar [novo] serviço Web**.
3.  Na página implementar experiência, introduza um nome para o serviço web e selecione um plano para comparar, em seguida, clique em **Implementar**.
4.  Na página do **Guia de introdução** , clique em **consumir**.
5.  Na secção de **Exemplos de código** , clique em **lotes**.
6.  No Visual Studio, crie uma aplicação de consola do c#.
7.  Copie e cole o código de exemplo c# no seu ficheiro Program.cs.
8.  Actualize o valor da variável *apiKey* com a **Chave primária** localizada na secção **informações básicas de consumo** .
9.  Localize a declaração de *scoreRequest* e atualizar os valores de parâmetros do serviço Web que são transmitidos para os módulos de *Dados de importação* e *Exportação de dados* . Neste caso, irá utilizar a consulta original, mas defina um novo nome de tabela.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Execute a aplicação. 
 


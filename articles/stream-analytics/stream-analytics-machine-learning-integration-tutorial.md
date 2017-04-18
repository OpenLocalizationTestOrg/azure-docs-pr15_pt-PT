<properties
    pageTitle="Análise de sentimento utilizando a análise da cadeia de Azure e Azure máquina aprendizagem | Microsoft Azure"
    description="Como utilizar uma função definida pelo utilizador e formação de máquina numa tarefa da cadeia Analytics"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>


<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Análise de sentimento utilizando o Azure máquina aprendizagem e de análise de sequência do Azure #

Este artigo destina-se para o ajudar a configurar rapidamente uma tarefa de análise da cadeia de Azure simple, com a integração do Azure máquina aprendizagem. Iremos utilizar um modelo de formação de máquina de análise de sentimento a partir da Galeria de informações da empresa Cortana para analisar dados em tempo real de texto e, determinar a pontuação sentimento em tempo real. As informações neste artigo podem ajudá-lo a compreender cenários, tais como a análise de sentimento em tempo real no Twitter dados de fluxo, analisar registos de cliente chats com a equipa de suporte e avaliar comentários em fóruns, blogues e vídeos, para além de muitas outras em tempo real e aspeto do Office pontuação cenários.

Este artigo disponibiliza um ficheiro CSV de exemplo com texto como entrada no armazenamento de Blobs do Azure, mostrado na seguinte imagem. A tarefa se aplica ao modelo de análise sentimento como uma função definida pelo utilizador (UDF) nos dados de texto de exemplo a partir da loja de Blobs. O resultado final é colocado na loja blob mesmo noutro ficheiro CSV. 

![Formação de máquina de análise da cadeia](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A seguinte imagem demonstra esta configuração. Para um cenário mais real, pode substituir o armazenamento de Blobs com transmissão Twitter dados a partir de uma entrada de Azure evento concentradores. Para além disso, pode criar uma visualização em tempo real do [Microsoft Power BI](https://powerbi.microsoft.com/) da sentimento a agregação.    

![Formação de máquina de análise da cadeia](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para concluir as tarefas que são demonstradas neste artigo são os seguintes:

-   Uma subscrição ativa do Azure.
-   Um ficheiro CSV com alguns dados no mesmo. Pode transferir o ficheiro apresentado na figura 1 a partir do [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv)ou pode criar o seu ficheiro. Para este artigo, iremos partem do princípio de que utiliza a fornecida de transferência no GitHub.

De alto nível para concluir as tarefas demonstradas neste artigo, faça o seguinte procedimento:

1.  Carregue o ficheiro de entrada de CSV ao armazenamento de Blobs do Azure.
2.  Adicione um modelo de análise sentimento a partir da Galeria de informações da empresa Cortana para a área de trabalho do Azure máquina aprendizagem.
3.  Implemente este modelo como um serviço web na área de trabalho de máquina aprendizagem.
4.  Crie uma tarefa de análise da cadeia chamadas este serviço web como uma função, para determinar sentimento para a entrada do texto.
5.  Iniciar a tarefa de análise da cadeia e observe o resultado.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Carregar o ficheiro de entrada de CSV ao armazenamento Blob

Para este passo, pode utilizar qualquer ficheiro CSV, tal como um já especificada como disponíveis para transferência no GitHub. Pode utilizar [O Explorador de armazenamento do Azure](http://storageexplorer.com/) ou Visual Studio para carregar o ficheiro ou pode utilizar o código personalizado. Utilizamos exemplos com base no Visual Studio.

1.  No Visual Studio, clique em **Azure** > **armazenamento** > **Anexar armazenamento externo**. Introduza um **nome de conta** e **chave da conta**.  

    ![Formação, Explorer de servidor de máquina de análise da cadeia](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Expandir a capacidade de armazenamento anexado no passo 1, clique em **Criar contentor Blob**e, em seguida, introduza um nome lógico. Depois de criar o contentor, abri-lo para ver os seus conteúdos. (-Estará vazia neste momento).  

    ![Transmitir em fluxo a análise de máquina formação, criar blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Para carregar o ficheiro CSV, clique em **Carregar Blob**e, em seguida, clique em **ficheiro a partir do disco local**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicionar o modelo de análise sentimento a partir da Galeria de informações da empresa Cortana

1.  Transferir o [modelo de análise de sentimento aspeto do Office](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) a partir da Galeria de informações da empresa Cortana.  
2.  No computador aprendizagem Studio, clique em **Abrir no Studio**.  

    ![Transmitir em fluxo a análise de máquina formação, abrir máquina aprendizagem Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Inicie sessão para ir para a área de trabalho. Selecione a localização que melhor se adequa às sua próprias localização.
4.  Clique em **Executar** na parte inferior da página.  
5.  Depois do processo for executado com êxito, clique em **Implementar o serviço Web**.
6.  Está pronto a utilizar o modelo de análise sentimento. Para validar, clique no botão de **teste** e forneça a introdução de texto, tais como "Posso adora Microsoft". O teste deve devolver um resultado semelhante ao seguinte:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Formação de máquina de análise da cadeia, dados de análise](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Na coluna **aplicações** , clique na ligação para o **Excel 2010 ou livro anterior** obter a sua chave de API e o URL que terá de mais tarde para configurar a tarefa de análise da cadeia. (Este passo só é necessária para utilizar um modelo de aprendizagem automática a partir de outra conta Azure da área de trabalho. Este artigo assume for o caso, para resolver esse cenário.)  

Tenha em atenção o URL e acesso chave do serviço web de ficheiro do Excel transferido, conforme apresentado abaixo:  

![Formação de máquina de análise da cadeia, vista rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar uma tarefa de análise da cadeia que utiliza o modelo de aprendizagem automática

1.  Aceda ao [Azure portal](https://manage.windowsazure.com).  
2.  Clique em **Novo** > **Serviços de dados** > **a análise da cadeia** > **criação rápida**. Introduza um nome para a tarefa em **Nome da tarefa**, introduza na região adequada para a tarefa na **região**e, em seguida, selecione a conta na **Conta de armazenamento de monitorização regionais**.    
3.  Quando a tarefa estiver criada, no separador **entradas do tipo** , clique em **Adicionar uma entrada**.  

    ![Fluxo Analytics máquina formação, adicionar a entrada de aprendizagem automática](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  Na primeira página do assistente **Adicionar entrada** , clique em **fluxo de dados**e, em seguida, clique em **seguinte**. Na página seguinte, clique em **Armazenamento de BLOBs** como a entrada e, em seguida, clique em **seguinte**.  
5.  Na página **Definições do armazenamento de BLOBs** do assistente, fornece o nome de BLOBs conta do armazenamento contentor que definiu anteriormente quando carregou os dados. Clique em **seguinte**. Para o **Formato de serialização de evento**, clique em **CSV**. Aceite os valores predefinidos para o resto da página **definições de serialização** . Clique em **OK**.  
6.  No separador **resultados** , clique em **Adicionar um resultado**.  

    ![Formação de máquina de análise de fluxo, adicionar saída](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Clique em **Armazenamento de BLOBs**e, em seguida, introduza os mesmos parâmetros, exceto o contentor. O valor de **entrada** foi configurado para ler a partir do contentor com o nome "test" onde o ficheiro **CSV** foi carregado. Para **saída**, introduza "testoutput". Nomes do contentor de têm de ser diferentes. Certifique-se de que existe neste contentor.     
8.  Clique em **seguinte** para configurar **Definições serialização a saída**. Tal como acontece com a **entrada**, clique em **CSV**e, em seguida, clique no botão **OK** .
9.  No separador **funções** , clique em **Adicionar uma função de aprendizagem automática**.  

    ![Fluxo Analytics máquina formação, adicionar máquina aprendizagem função](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. Na página **Definições do serviço de Web de aprendizagem de máquina** , localize a área de trabalho formação de máquina, serviço web e ponto final predefinido. Este artigo, aplica as definições manualmente para obter familiaridade com a configuração de um serviço web para qualquer área de trabalho, desde que conhece o URL e tem a chave de API. Introduza o **URL** do ponto final e a **chave de API**. Clique em **OK**.    

    ![Formação de máquina de análise da cadeia, serviço web de aprendizagem automática](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. No separador da **consulta** , modificar a consulta da seguinte forma:    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Clique em **Guardar** para guardar a consulta.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Iniciar a tarefa de análise da cadeia e observe a saída

1.  Clique em **Iniciar** na parte inferior da página de tarefa.
2.  No **Iniciar a caixa de diálogo de consulta**, clique em **Hora personalizada**e, em seguida, selecione um tempo anteriores ao quando carregou de CSV ao armazenamento Blob do. Clique em **OK**.  

    ![Formação de máquina de análise da cadeia, hora personalizado](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Aceda ao armazenamento de BLOBs utilizando a ferramenta que utilizou para carregar o ficheiro CSV, por exemplo, Visual Studio.
4.  Alguns minutos depois da tarefa é iniciada, o contentor de saída é criado e um ficheiro CSV é carregado para o mesmo.  
5.  Abra o ficheiro no editor de CSV predefinido. Deverá ser apresentado algo semelhante ao seguinte:  

    ![Ver formação de máquina de análise da cadeia, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusão

Este artigo demonstra como criar uma tarefa de análise da cadeia que lê transmissão de dados de texto e aplica-se a análise de sentimento aos dados em tempo real. Pode realizar tudo isto sem necessitar de se preocupar as especificidades de criação de um modelo de análise sentimento. Esta é uma das vantagens do conjunto de informações da empresa Cortana.

Também pode ver métricas relacionados com a função do Azure máquina aprendizagem. Para fazer isto, clique no separador **Monitor** . Três métricas relacionados com a função são apresentadas.  

- **Função pedidos** indica o número de pedidos enviados para um serviço web de aprendizagem automática.  
- **Função eventos** indica o número de eventos no pedido de. Por predefinição, cada pedido para um serviço web de aprendizagem máquina contém até 1.000 eventos.  
- **Ocorreu uma falha com pedidos de função** indica o número de pedidos de falhados do serviço web de aprendizagem máquina.  

    ![Formação de máquina de análise da cadeia, vista do monitor de computador aprendizagem](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  

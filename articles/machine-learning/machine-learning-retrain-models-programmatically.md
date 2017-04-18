<properties
    pageTitle="Modelos de formação de máquina de retransmissão através de programação | Microsoft Azure"
    description="Saiba como através de programação reciclagem de um modelo e atualizar o serviço web para utilizar o modelo recentemente qualificado no Azure máquina aprendizagem."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Modelos de formação de máquina de retransmissão através de programação  

Neste tutorial, irá aprender a Reciclagem através de programação de um serviço Web do Azure máquina aprendizagem utilizando c# e o serviço de máquina aprendizagem lote execução.

Assim que tiver retrained o modelo, os seguintes tutoriais mostram como atualizar o modelo no seu serviço web aspeto do Office:

- Se implementado um serviço web de clássica no portal do máquina aprendizagem Web Services, consulte o artigo [Reciclagem de um serviço web de clássica](machine-learning-retrain-a-classic-web-service.md). 
- Se implementado um novo serviço web, consulte o artigo [Reciclagem um novo serviço web utilizando os cmdlets de gestão de aprendizagem do computador](machine-learning-retrain-new-web-service-using-powershell.md).

Para obter uma descrição geral do processo de readaptação, consulte o artigo [Reciclagem de um modelo de aprendizagem automática](machine-learning-retrain-machine-learning-model.md).

Se pretender começar com o serviço de web novo Azure o Gestor de recursos com base existente, consulte o artigo [Reciclagem de um serviço web aspeto do Office existente](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Criar uma experiência de formação
 
Neste exemplo, que irá utilizar "exemplo 5: avaliar comboio, teste, para a classificação binária: conjunto de dados para adultos" a partir de amostras de formação do Microsoft Azure máquina. 
    
Para criar a experiência:

1.  Inicie sessão ao Microsoft Azure de máquina Studio de formação. 
2.  No canto inferior direito do dashboard, clique em **Novo**.
3.  Samples Microsoft, selecione 5 de exemplo.
4.  Para mudar o nome a experiência, na parte superior da tela experiência, selecione o nome de experiência "exemplo 5: avaliar comboio, teste, para a classificação binária: conjunto de dados para adultos".
5.  Modelo de contagem de tipo.
6.  Na parte inferior da experiência da tela, clique em **Executar**.
7.  Clique em **configurar o serviço de web** e selecione o **serviço web de Retraining**. 

    ![Experiência inicial.][2]

Diagrama 2: Inicial experiência.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Criar uma experiência de aspeto do Office e publicar como um serviço web  

Em seguida, criar uma experiência de Predicative.

1.  Na parte inferior da tela experiência, clique em **Configurar o serviço Web** e selecione o **Serviço Web aspeto do Office**. Isto guarda o modelo como um modelo de formação e adiciona módulos de entrada e saída da serviço web. 
2.  Clique em **Executar**. 
3.  Depois da experiência já terminou em execução, clique em **Implementar o serviço Web [clássica]** ou **Implementar [novo] serviço Web**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Implementar a experiência de formação como um serviço web de formação

Para o modelo de formação de retransmissão, tem de implementar a experiência de formação que criou como um serviço web de Retraining. Este serviço web necessita de um módulo de *Saída de serviço Web* ligado a * [Comboio modelo] [ train-model] * módulo, possam produzir novos modelos de formação.

1. Para voltar para a experiência de formação, clique no ícone de experiências no painel esquerdo, em seguida, clique na experiência com o nome modelo contagem.  
2. Na caixa de pesquisa de itens de experiência de pesquisa, escreva o serviço web. 
3. Arrastar um módulo de *Entrada de serviço Web* para a tela de experiência e ligue respectiva saída do módulo *LIMPARB dados em falta* .  Este procedimento assegura que os seus dados readaptação são processados da mesma forma como os dados originais de formação.
4. Arraste dois *serviço web saída* módulos para a tela de experiência. Ligar a saída do módulo *Comboio modelo* a um e o resultado do módulo *Avaliar modelo* para o outro. O resultado de serviço web para **Modelo comboio** dá-no novo modelo de formação. A saída anexada ao **Avaliar modelo** devolve saída esse módulo, que é os resultados de desempenho.
5. Clique em **Executar**. 

Seguinte tem de implementar a experiência de formação como um serviço web que gera um modelo de formação e resultados de avaliação do modelo. Para realizar esta tarefa, o próximo conjunto de ações dependem de se estiver a trabalhar com um serviço web de Classic ou um novo serviço web.  
  
**Serviço web clássico**

Na parte inferior da tela experiência, clique em **Configurar o serviço Web** e selecione **Implementar o serviço Web [clássica]**. O serviço Web **Dashboard** é apresentada com a chave de API e a página de ajuda API para lote execução. Apenas o método de execução do lote pode ser utilizado para criar modelos de formação.

**Novo serviço web**

Na parte inferior da tela experiência, clique em **Configurar o serviço Web** e selecione **Implementar [novo] serviço Web**. O portal de serviços Web de aprendizagem máquina Web serviço Azure abre-se para a página de serviço web implementar. Escreva um nome para o serviço web e selecione um plano de pagamento e, em seguida, clique em **Implementar**. Apenas o método de execução do lote pode ser utilizado para criar modelos de formação

Em ambos os casos, depois de experiência concluída em execução, o fluxo de trabalho resultante deverá ter o aspeto da seguinte forma:

![Resultante fluxo de trabalho depois de ter executado.][4]

Diagrama 3: Resultante fluxo de trabalho depois de ter executado.

## <a name="retrain-the-model-with-new-data-using-bes"></a>O modelo de retransmissão por novos dados utilizando BES

Neste exemplo, está a utilizar c# para criar a aplicação readaptação. Também pode utilizar o código de exemplo Python ou R para realizar esta tarefa.

Para ligar as APIs da Reciclagem:

1. Criar uma aplicação de consola do c# no Visual Studio (-novo > projeto -> Windows ambiente de trabalho -> a aplicação de consola).
2.  Inicie sessão no portal do serviço de Web de aprendizagem máquina.
3.  Se estiver a trabalhar com um serviço web de clássico, clique em **Serviços Web clássica**.
    1.  Clique no serviço web que estiver a trabalhar com.
    2.  Clique no ponto final predefinido.
    3.  Clique em **consumir**.
    4.  Na parte inferior da página **consumir** , na secção de **Exemplos de código** , clique em **lotes**.
    5.  Continue para o passo 5 deste procedimento.
4.  Se estiver a trabalhar com um novo serviço web, clique em **Serviços Web**.
    1.  Clique no serviço web que estiver a trabalhar com.
    2.  Clique em **consumir**.
    3.  Na parte inferior da página consumir, na secção de **Exemplos de código** , clique em **lotes**.
5.  Copiar o exemplo c# código para execução lote e colá-la ficheiro Program.cs, certificar-se de que o espaço de nomes permanece intacto.

Adicione o pacote de Nuget Microsoft.AspNet.WebApi.Client conforme especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, poderá tem primeiro de instalar a biblioteca do cliente para serviços de armazenamento do Microsoft Azure. Para mais informações, consulte o artigo [Serviços de armazenamento do Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey

Localize a declaração de **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na secção **informações básicas de consumo** da página **consumir** , localize a chave primária e copiá-lo para a declaração de **apikey** .

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Windows Azure

O código de exemplo BES carregamentos de um ficheiro a partir de uma unidade local (por exemplo "C:\temp\CensusIpnput.csv") para o armazenamento do Windows Azure, processa-lo e escreve os resultados armazenamento do Windows Azure.  

Para realizar esta tarefa, tem de obter as informações de nome, chave e contentor da conta de armazenamento da sua conta de armazenamento a partir do portal do Azure clássico e os valores correspondentes de atualizar o código. 

1. Inicie sessão no portal do Azure clássico.
1. Na coluna da navegação do lado esquerdo, clique em **armazenamento**.
1. A partir da lista de contas de armazenamento, selecione um para armazenar o modelo de retrained.
1. Na parte inferior da página, clique em **Gerir as teclas de acesso**.
1. Copiar e guarde a **Chave primária de acesso** e feche a caixa de diálogo. 
1. Na parte superior da página, clique em **contentores**.
1. Selecione um contentor existente ou crie um novo e guardar o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* e atualizar os valores que guardou a partir do portal Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Também tem de garantir que o ficheiro de entrada está disponível na localização que especificou no código. 

### <a name="specify-the-output-location"></a>Especificar a localização de saída

Quando especificar a localização de saída na carga de pedir útil, a extensão do ficheiro especificado no *RelativeLocation* tem de ser especificada como ilearner. 

Consulte o exemplo seguinte:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Os nomes das suas localizações de saída poderão ser diferentes neste tutorial baseada na ordem em que adicionou os módulos de saída de serviço web. Uma vez que esta experiência de formação com duas saídas configurar, os resultados incluem informações de localização de armazenamento para ambos-los.  

![Reciclagem saída][6]

Diagrama de 4: Reciclagem saída.

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados readaptação
 
Quando executar a aplicação, o resultado inclui o URL e SA token necessário para aceder os resultados de avaliação.

Pode ver os resultados de desempenho do modelo retrained combinando os *BaseLocation*, *RelativeLocation*e *SasBlobToken* partir dos resultados de saída para *output2* (como mostrado na imagem saída readaptação anterior) e colar o URL completo na barra de endereço do browser.  

Examine os resultados para determinar se o modelo recentemente qualificado executa bem suficiente para substituir a existente.

Copiar o *BaseLocation*, *RelativeLocation*e *SasBlobToken* a partir dos resultados de saída, que irá utilizar durante o processo de readaptação.

## <a name="next-steps"></a>Próximos passos

Se implementado o serviço web aspeto do Office ao clicar em **Implementar o serviço Web [clássica]**, consulte o artigo [Reciclagem de um serviço web de clássica](machine-learning-retrain-a-classic-web-service.md).

Se implementado o serviço web aspeto do Office ao clicar em **Implementar [novo] serviço Web**, consulte o artigo [Reciclagem um novo serviço web utilizando os cmdlets de gestão de aprendizagem do computador](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
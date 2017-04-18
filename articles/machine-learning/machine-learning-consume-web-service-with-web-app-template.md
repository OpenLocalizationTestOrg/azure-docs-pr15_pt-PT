<properties
    pageTitle="Consumir um serviço web de aprendizagem máquina com um modelo de aplicação web | Microsoft Azure"
    description="Utilize um modelo de aplicação web no Azure Marketplace para consumir um serviço web aspeto do Office no Azure máquina aprendizagem."
    keywords="formação de máquina de serviço Web, operationalization, de REST API,"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Consumir um serviço web de aprendizagem do Azure máquina com um modelo de aplicação web

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço web clássica. 

Uma vez que tenha desenvolvido o seu modelo de aspeto do Office e implementado-lo como um serviço Azure web utilizando máquina aprendizagem Studio ou utilizar ferramentas como R ou Python, pode aceder ao modelo de operationalized utilizando um REST API.

Existem várias formas de consumir REST API e aceder ao serviço web. Por exemplo, pode escrever uma aplicação no c#, R ou Python utilizando o código de exemplo gerado automaticamente quando implementou o serviço web (disponível na página de ajuda API no dashboard de serviço web na máquina aprendizagem Studio). Ou pode utilizar o livro do Microsoft Excel de exemplo criado para si (também está disponível no dashboard de serviço web no Studio).

Mas a forma mais rápida e fácil de aceder ao serviço web é entre os modelos de aplicação Web disponíveis no [Mercado de aplicações Web Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>A máquina Azure aprendizagem modelos da aplicação Web

Modelos de aplicação web disponíveis no Azure Marketplace, podem criar uma aplicação web personalizada que sabe o serviço web dados de entrada e os resultados esperados. Tudo o que precisa de fazer é dar o acesso da aplicação web aos seus dados e o serviço web e o modelo é que os restantes.

Dois modelos estão disponíveis:

- [Modelo de aplicação do Azure ML pedido-resposta serviço Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modelo de aplicação do Azure ML lote execução serviço Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada modelo cria uma aplicação de ASP.NET exemplo, utilizando o API URI e a chave para o serviço web e implementa-lo como um web site para Azure. O modelo de resposta do pedido de serviço (RRS) cria uma aplicação web que permite-lhe enviar uma única linha de dados para o serviço web para obter um único resultado. O modelo de serviço de execução de lote (BES) cria uma aplicação web que permite-lhe enviar o número de linhas de dados para obter resultados de várias.

Sem codificação é necessária para utilizar estes modelos. Basta fornecer o API URI e a chave e o modelo constrói a aplicação para si.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Como utilizar o modelo de resposta do pedido de serviço (RRS)

Depois de ter implementado o serviço web, pode seguir os passos abaixo para utilizar o modelo de aplicação web do RRS, conforme o diagrama seguinte.

![Processo para utilizar RRS web modelo][image1]

1. No computador aprendizagem Studio, abra o separador **Serviços Web** e, em seguida, abra o serviço web que pretende aceder. Copiar a chave listada em **chave API** e guardá-lo.

    ![API chave][image3]

2. Abra a página de ajuda do **Pedido/resposta** API. Na parte superior da página de ajuda, em **Pedir**, copie o valor de **URI pedir** e guardá-lo. Este valor terá o seguinte aspeto:

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![URI pedido][image4]

3. Aceda ao [Azure portal](https://portal.azure.com), **início de sessão**, clique em **Novo**, procure e selecione **Azure ML resposta do pedido de serviço Web App**e, em seguida, clique em **Criar**. 

    - Dê um nome exclusivo à aplicação web. O URL da aplicação web será este nome seguido `.azurewebsites.net.` por exemplo,`http://carprediction.azurewebsites.net.`

    - Selecione a subscrição do Azure e serviços sob o qual está a executar o seu serviço web.

    - Clique em **Criar**.

    ![Criar a aplicação web][image5]

4. Quando o Azure já terminou implementar a aplicação web, clique no **URL** na página Definições do web app no Azure ou introduza o URL num browser. Por exemplo,`http://carprediction.azurewebsites.net.`

5. Quando a aplicação web é executado-lo irá pedir-lhe o **URL da mensagem API** e a **Chave de API**.
Introduza os valores que guardou anteriormente:
    - **URI do pedido** a partir da página de ajuda de API para **URL da mensagem API**
    - **Tecla de API** do dashboard de serviço web para a **Chave de API**.

    Clique em **Submeter**.

    ![Introduza a mensagem URI e chave de API][image6]

6. A aplicação web apresenta a sua página de **Configuração da aplicação Web** com as definições do serviço web atual. Aqui pode efetuar alterações às definições utilizadas pelo web app.

    > [AZURE.NOTE] Alterar as definições de aqui apenas altera-los para esta aplicação web. Tal não alterar as predefinições do seu serviço web. Por exemplo, se alterar a **Descrição** aqui não altera a descrição da apresentado no dashboard de serviço web na máquina aprendizagem Studio.

    Quando terminar, clique em **Guardar alterações**e, em seguida, clique em **Ir para a Home Page**.

7. Na home page, pode introduzir valores para enviar para o serviço web, clique em **Submeter**e o resultado será devolvido.

Se pretender regressar à página de **configuração** , aceda à `setting.aspx` page da aplicação web. Por exemplo: `http://carprediction.azurewebsites.net/setting.aspx.` ser-lhe-á pedido para introduzir a chave de API novamente - precisa que para aceder à página e atualizar as definições.

Pode parar, reinicie ou eliminar a aplicação web no portal do Azure como qualquer outra aplicação web. Desde que está a ser executado pode navegar para o endereço web principal e introduza novos valores.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Como utilizar o modelo de serviço de execução de lote (BES)

Pode utilizar o modelo de aplicação web BES da mesma forma que o modelo RRS, exceto que a aplicação web que é criada permitirá submeter várias linhas de dados e receber vários resultados.

Os resultados de um serviço de web de execução do lote são armazenados no contentor de armazenamento Azure; os valores de entrada podem originar de armazenamento Azure ou um ficheiro local.
Por isso, terá de um contentor de armazenamento Azure mantenha os resultados devolvidos pelo web app e, terá de preparar os dados de entrada.

![Processo para utilizar BES web modelo][image2]

1. Siga o mesmo procedimento para criar o BES web app para o modelo RRS, exceto:
    - Obter o **URI pedir** a partir da página de ajuda do **Lote execução** API para o serviço web.
    - Ir para o [Modelo de aplicação ao Web do serviço de execução Azure ML lote](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir o modelo de BES no Azure Marketplace e clique em **Criar Web App**.

2. Para especificar onde pretende que os resultados armazenados, introduza as informações de contentor de destino na home page da aplicação web. Também pode especificar onde a aplicação web pode obter os valores de entrada, tanto num contentor de armazenamento Azure de um ficheiro local.
Clique em **Submeter**.

    ![Informações de armazenamento][image7]

A aplicação web será apresentada uma página com o estado da tarefa.
Quando a tarefa estiver concluída ser-lhe-á dada a localização dos resultados no armazenamento de Blobs do Azure. Também tem a opção de transferir os resultados para um ficheiro local.

## <a name="for-more-information"></a>Para obter mais informações

Para saber mais sobre...

- criar uma experiência de aprendizagem máquina com máquina de formação Studio, consulte o artigo [criar a sua primeira experiência no Azure máquina de formação Studio](machine-learning-create-experiment.md)

- como implementar a sua aprendizagem máquina experimentar como um serviço web, consulte o artigo [Implementar um serviço de web Azure máquina de formação](machine-learning-publish-a-machine-learning-web-service.md)

- outras formas de aceder ao seu serviço web, consulte o artigo [como consumir um serviço web de aprendizagem de máquina do Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

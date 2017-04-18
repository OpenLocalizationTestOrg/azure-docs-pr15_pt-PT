<properties
    pageTitle="Saiba como gerir serviços de web AzureML utilizando a gestão de API | Microsoft Azure"
    description="Um guia que mostra como gerir os serviços de web AzureML utilizando a gestão de API."
    keywords="aprendizagem, gestão de api automática"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="roalexan" />


# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Saiba como gerir serviços de web AzureML utilizando a gestão de API

##<a name="overview"></a>Descrição geral

Este guia mostra-lhe como rapidamente começar a utilizar a gestão de API para gerir os seus serviços de web AzureML.

##<a name="what-is-azure-api-management"></a>O que é a gestão de API do Azure?

Gestão de API Azure é um serviço Azure que permite-lhe gerir os pontos finais REST API através da definição de acesso do utilizador, limitação de utilização e monitorização do dashboard. Clique [aqui](https://azure.microsoft.com/services/api-management/) para obter detalhes sobre a gestão de API do Azure. Clique [aqui](../api-management/api-management-get-started.md) para um guia sobre como começar com a gestão de API do Azure. Este outras guia qual este guia é baseado no, abrange mais tópicos, incluindo configurações de notificação, preços de camadas, processamento de resposta, autenticação de utilizador, criar produtos, subscrições de programador e dashboarding de utilização.

##<a name="what-is-azureml"></a>O que é AzureML?

AzureML é um serviço Azure para de aprendizagem automática que permite-lhe construir facilmente, implementar e partilhar soluções de análise avançadas. Clique [aqui](https://azure.microsoft.com/services/machine-learning/) para obter detalhes sobre AzureML.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este guia, é necessário:

* Uma conta Azure. Se não tiver uma conta Azure, clique [aqui](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes sobre como criar uma conta de avaliação gratuita.
* Uma conta de AzureML. Se não tiver uma conta de AzureML, clique [aqui](https://studio.azureml.net/) para obter detalhes sobre como criar uma conta de avaliação gratuita.
* A área de trabalho, o serviço e api_key para uma experiência de AzureML implementada como um serviço web. Clique [aqui](machine-learning-create-experiment.md) para obter detalhes sobre como criar uma experiência de AzureML. Clique [aqui](machine-learning-publish-a-machine-learning-web-service.md) para obter detalhes sobre como implementar uma experiência de AzureML como um serviço web. Em alternativa, o anexo A tem instruções sobre como criar e testar uma experiência de AzureML simple e implementá-lo como um serviço web.

##<a name="create-an-api-management-instance"></a>Criar uma instância de gestão de API

Seguem-se os passos para utilizar a gestão de API para gerir o seu serviço de web AzureML. Primeiro, crie uma instância do serviço. Inicie a sessão [Portal clássica](https://manage.windowsazure.com/) e clique em **Novo** > **Serviços de aplicação** > **API gestão** > **Criar**.

![criar a instância](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Especificar um **URL**de exclusivo. Este guia utiliza **demoazureml** – é necessário escolher algo diferente. Selecione a **subscrição** e **região** pretendida para a instância do serviço. Depois de efetuar as suas seleções, clique no botão seguinte.

![criar-serviço-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Especificar um valor para o **Nome da organização**. Este guia utiliza **demoazureml** – é necessário escolher algo diferente. Introduza o seu endereço de e-mail no campo de **correio electrónico do administrador** . Este endereço de correio eletrónico é utilizado para notificações a partir do sistema de gestão de API.

![criar-serviço-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Clique na caixa de verificação para criar a instância do serviço. *Demora até trinta minutos para um novo serviço seja criada*.

##<a name="create-the-api"></a>Criar a API

Quando estiver criada a instância do serviço, o próximo passo é criar API. Uma API é composta por um conjunto de operações que pode ser chamado a partir de uma aplicação de cliente. Operações de API são através do proxy para os serviços web existentes. Este guia cria APIs esse proxy para as AzureML RRS e BES os serviços web existentes.

APIs são criados e configuradas a partir do portal do publisher API, que pode é acedido através do Portal clássica do Azure. Para chegar ao portal do publisher, selecione a instância do serviço.

![Selecione a instância do serviço](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure.

![serviço gerir](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Clique **APIs** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Adicionar API**.

![menu de gestão de API](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Escreva **AzureML demonstração API** como o **nome da Web API**. Escreva **https://ussouthcentral.services.azureml.net** como o **URL do serviço Web**. Escreva **azureml demonstração** como o **URL da Web API sufixo**. Selecione **HTTPS** como o **URL da Web API** esquema. Selecione **Starter** como **produtos**. Quando tiver terminado, clique em **Guardar** para criar a API.

![Adicionar-novo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##<a name="add-the-operations"></a>Adicionar as operações

Clique em **operação de adição** para adicionar operações a esta API.

![operação adicionar](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Será apresentada a janela de **nova operação** e no separador **assinatura** será seleccionado por predefinição.

##<a name="add-rrs-operation"></a>Adicione RRS operação

Primeiro, crie uma operação para o serviço de AzureML RRS. Selecione **Publicar** como o **verbo HTTP**. Tipo de **/services/ /workspaces/ {área de trabalho} {serviço} / execute?api versão = {apiversion} & Detalhes = {Detalhes}** como o **modelo de URL**. Escreva **Executar RRS** como o **nome a apresentar**.

![Adicionar-rrs-operação de assinatura](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Clique em **respostas** > **Adicionar** no lado esquerdo e selecione **200 OK**. Clique em **Guardar** para guardar esta operação.

![Adicionar-rrs-operação de resposta](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##<a name="add-bes-operations"></a>Adicionar BES operações

Capturas de ecrã não são incluídas para as operações de BES à medida que são muito semelhantes para adicionar a operação de RRS.

###<a name="submit-but-not-start-a-batch-execution-job"></a>Submeter (mas não iniciar) uma tarefa de execução de Batch

Clique em **operação de adição** para adicionar a operação de AzureML BES à API. Selecione **Publicar** para o **verbo HTTP**. Tipo de **/services/ /workspaces/ {área de trabalho} {serviço} / jobs?api versão = {apiversion}** para o **modelo de URL**. Escreva **BES submeter** para o **nome a apresentar**. Clique em **respostas** > **Adicionar** no lado esquerdo e selecione **200 OK**. Clique em **Guardar** para guardar esta operação.

###<a name="start-a-batch-execution-job"></a>Iniciar uma tarefa de execução de Batch

Clique em **operação de adição** para adicionar a operação de AzureML BES à API. Selecione **Publicar** para o **verbo HTTP**. Tipo de **/workspaces/ {área de trabalho} /services/ {serviço} /jobs/ {jobid} / start?api versão = {apiversion}** para o **modelo de URL**. Escreva **BES iniciar** para o **nome a apresentar**. Clique em **respostas** > **Adicionar** no lado esquerdo e selecione **200 OK**. Clique em **Guardar** para guardar esta operação.

###<a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obter o resultado de uma tarefa de execução de lote ou de estado

Clique em **operação de adição** para adicionar a operação de AzureML BES à API. Selecione **obter** para o **verbo HTTP**. Tipo de **/workspaces/ {área de trabalho} /services/ {serviço} /jobs/ {jobid} ?api versão = {apiversion}** para o **modelo de URL**. Escreva **BES Estado** para o **nome a apresentar**. Clique em **respostas** > **Adicionar** no lado esquerdo e selecione **200 OK**. Clique em **Guardar** para guardar esta operação.

###<a name="delete-a-batch-execution-job"></a>Eliminar uma tarefa de execução de Batch

Clique em **operação de adição** para adicionar a operação de AzureML BES à API. Selecione **Eliminar** para o **verbo HTTP**. Tipo de **/workspaces/ {área de trabalho} /services/ {serviço} /jobs/ {jobid} ?api versão = {apiversion}** para o **modelo de URL**. Escreva **BES eliminar** para o **nome a apresentar**. Clique em **respostas** > **Adicionar** no lado esquerdo e selecione **200 OK**. Clique em **Guardar** para guardar esta operação.

##<a name="call-an-operation-from-the-developer-portal"></a>Uma operação de chamadas a partir do Portal de programador

Operações podem ser chamadas diretamente a partir do portal do programador, que fornece uma forma conveniente para ver e testar a operações de uma API. Neste passo guia irá efetuar a chamada **O RRS executar** método que foi adicionado à **API de demonstração AzureML**. Clique em **Developer portal para** a partir do menu na parte superior direita do Portal clássica.

![portal de programador](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Clique **APIs** a partir do menu superior e, em seguida, clique em **AzureML demonstração API** para ver as operações disponíveis.

![demoazureml api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Selecione **RRS executar** a operação de. Clique em **experimentar**.

![Experimente-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Para os parâmetros de pedido, escreva a sua **área de trabalho**, **serviço**, **2.0** para o **apiversion**e **Verdadeiro** para que os **Detalhes**. Pode encontrar a sua **área de trabalho** e o **serviço** no dashboard de serviço web de AzureML (consulte **teste o serviço web** do anexo A).

Para os cabeçalhos de pedido, clique em **Adicionar cabeçalho** e escreva o **Tipo de conteúdo** e **aplicação/json**, em seguida, clique em **Adicionar cabeçalho** e escreva **autorização** e **portadores <YOUR AZUREML SERVICE API-KEY> **. Pode encontrar a sua **chave de api** no dashboard de serviço web de AzureML (consulte **teste o serviço web** do anexo A).

Tipo de **{"Entradas": {"input1": {"ColumnNames": ["Col2"], "valores": [["é um bom dia"]]}}, "GlobalParameters": {}}** para corpo do pedido.

![api de demonstração azureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Clique em **Enviar**.

![enviar](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Depois de uma operação é chamada, o portal do programador apresenta o **URL pedida** a partir do serviço de back-end, o **Estado da resposta**, os **cabeçalhos de resposta**e qualquer tipo de **conteúdo de resposta**.

![Estado da resposta](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##<a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Anexo A - criar e testar uma simple AzureML serviço web

###<a name="creating-the-experiment"></a>Criar a experiência

Seguem-se os passos para criar uma experiência de AzureML simple e implementá-lo como um serviço web. Demora de serviço web como uma coluna de texto arbitrário de entrada e devolve um conjunto de funcionalidades representado como números inteiros. Por exemplo:

Texto | Texto hash
--- | ---
Este é um bom dia | 1 1 2 2 0 2 0 1

Em primeiro lugar, utilizando um browser da sua escolha, navegue até à: [https://studio.azureml.net/](https://studio.azureml.net/) e introduza as suas credenciais para iniciar sessão. Em seguida, crie uma nova experiência em branco.

![modelos de experiência de pesquisa](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Mude o nome para **SimpleFeatureHashingExperiment**. Expanda **Guardado conjuntos de dados** e arraste **Revisões de livro de endereços da Amazon** para a sua experiência.

![simples-funcionalidade-o hashing-experiência](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda **manipulação** e de **Transformação de dados** e arraste **Selecionar colunas no conjunto de dados** para a sua experiência. Ligar **revisões de livro de endereços da Amazon** para **Selecionar colunas no conjunto de dados**.

![Seleccionar colunas](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Clique em **Selecionar colunas no conjunto de dados** e, em seguida, clique em **Iniciação selector de coluna** e selecione **Col2**. Clique em marca de verificação para aplicar estas alterações.

![Seleccionar colunas](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **A análise de texto** e arraste **O Hashing funcionalidade** experiência. Ligar a **Seleccionar colunas no conjunto de dados** a **funcionalidade Hashing**.

![ligar-projeto-colunas](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Escreva **3** para o **Hashing bitsize**. Esta opção criará 8 (23) colunas.

![hashing bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Neste momento, poderá pretende clique em **Executar** para testar a experiência.

![executar](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###<a name="create-a-web-service"></a>Criar um serviço web

Agora, crie um serviço web. Expanda o **Serviço Web** e arraste **entrada** para a sua experiência. Ligar a **entrada** a **funcionalidade Hashing**. Arraste também **saída** para sua experiência. Ligar a **saída** a **funcionalidade Hashing**.

![saída-para-funcionalidade-o hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Clique em **Publicar web serviço**.

![publicar--serviço web](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Clique em **Sim** para publicar a experiência.

![Sim para publicar](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###<a name="test-the-web-service"></a>Testar o serviço web

Um serviço web de AzureML consiste em RSS (serviço pedido/resposta) e os pontos finais (serviço de execução do lote) de BES. RSS destina-se a execução síncrona. BES destina-se a execução de tarefas assíncrona. Para testar o seu serviço web com a origem de Python de exemplo abaixo, poderá ter de transferir e instalar o SDK do Azure para Python (consulte o artigo: [como instalar o Python](../python-how-to-install.md)).

Também terá da **área de trabalho**, **o serviço**e **api_key** da sua experiência para a origem de exemplo abaixo. Pode encontrar a área de trabalho e o serviço ao clicar em **Pedido/resposta** ou **Lote execução** para a sua experiência no dashboard de serviço web.

![localizar-área de trabalho-e-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Pode encontrar o **api_key** ao clicar em sua experiência no dashboard de serviço web.

![tecla de api localizar](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####<a name="test-rrs-endpoint"></a>Ponto final RRS de teste

#####<a name="test-button"></a>Botão Testar

Uma forma fácil de testar o ponto final RRS é clicar em **Testar** no dashboard de serviço web.

![teste](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Escreva **Este é um bom dia** para **col2**. Clique na marca de verificação.

![dados introduza](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Irá ver algo parecido com

![exemplo de resultado](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####<a name="sample-code"></a>Código de exemplo

Outra forma de testar a sua RRS é a partir do código do cliente. Se clicar em **pedido/resposta** o dashboard e desloque-se para a parte inferior, irá ver exemplos de código para c#, Python e R. Também verá a sintaxe de pedido de RRS, incluindo o pedido URI, cabeçalhos e corpo.

Este guia mostra um exemplo de Python de trabalho. Terá de modificá-la com a **área de trabalho**, **o serviço**e **api_key** da sua experiência.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####<a name="test-bes-endpoint"></a>Ponto final de BES de teste
Clique em **execução lote** o dashboard e desloque-se para a parte inferior. Irá ver exemplos de código para c#, Python e R. Também verá a sintaxe da BES pedidos de para submeter uma tarefa, iniciar uma tarefa, obter o estado ou os resultados de uma tarefa e eliminar uma tarefa.

Este guia mostra um exemplo de Python de trabalho. Tem de modificá-la com a **área de trabalho**, **o serviço**e **api_key** da sua experiência. Para além disso, tem de modificar o **nome da conta de armazenamento**, a **chave da conta de armazenamento**e o **nome do contentor de armazenamento**. Por fim, terá de modificar a localização do **ficheiro de entrada** e a localização do **ficheiro de exportação**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()

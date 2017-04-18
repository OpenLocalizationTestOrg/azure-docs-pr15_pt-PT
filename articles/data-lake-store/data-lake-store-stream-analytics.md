<properties
   pageTitle="Fluxo de dados de análise da cadeia para o arquivo de dados de Lake | Azure"
   description="Utilizar a análise da cadeia de Azure para dados em sequência sessão Azure dados Lake loja"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Dados da cadeia a partir do Azure armazenamento Blob para o arquivo de dados de Lake utilizando a análise da cadeia de Azure

Este artigo vai aprender a utilizar o Azure dados Lake arquivo como uma saída para uma tarefa de análise da cadeia de Azure. Este artigo demonstra cenário simple que lê dados a partir de um blob de armazenamento do Windows Azure (entrada) e escreve os dados ao arquivo de dados de Lake (resultado).

>[AZURE.NOTE] Neste momento, a criação e de configuração de arquivo de Lake dados saídas para análise da cadeia é suportado apenas no [Portal clássica do Azure](https://manage.windowsazure.com). Por conseguinte, algumas partes deste tutorial irão utilizar o Portal do Azure clássica.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Ativar a sua subscrição do Azure** para dados Lake arquivo público pré-visualização. Consulte as [instruções](data-lake-store-get-started-portal.md#signup).

- **Conta de armazenamento do Windows azure**. Irá utilizar um contentor blob a partir desta conta para a entrada de dados para uma tarefa de análise da cadeia. Para este tutorial, partem do pressuposto de que criar uma conta de armazenamento denominado **datalakestoreasa** e um contentor dentro da conta denominado **datalakestoreasacontainer**. Depois de ter criado o contentor, carregar um ficheiro de dados de exemplo-lo. Pode obter um ficheiro de dados de exemplo a partir do [Azure dados Lake Git repositório](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Pode utilizar vários clientes, tal como [O Explorador de armazenamento do Azure](http://storageexplorer.com/), para carregar os dados para um contentor blob.

    >[AZURE.NOTE] Se criar a conta a partir do Portal do Azure, certifique-se de que cria com o modelo **clássico** de implementação. Este procedimento garante que a conta de armazenamento pode ser acedida a partir do Portal clássica do Azure, uma vez que é o que utilizamos para criar uma tarefa de análise da cadeia. Para obter instruções sobre como criar uma conta de armazenamento a partir do Portal do Azure utilizando a implementação de clássico, consulte o artigo [criar uma conta de armazenamento do Windows Azure](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > Em alternativa, pode criar uma conta de armazenamento a partir do Portal de clássica Azure.

- **Conta azure dados Lake loja**. Siga as instruções no [artigo Introdução ao arquivo de Lake Azure dados utilizando o Portal do Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa de análise da cadeia

Comece por criar uma tarefa de análise da cadeia que inclui uma origem de entrada e de destino de saída. Para este tutorial, a origem for um contentor de Blobs do Azure e o destino for arquivo de dados de Lake.

1. Ao iniciar sessão no [Portal clássica Azure](https://manage.windowsazure.com).

2. A partir do canto inferior esquerdo do ecrã, clique em **Novo**, **Serviços de dados**, **A análise da cadeia**, **Criação rápida**. Forneça os valores, conforme apresentado abaixo e, em seguida, clique em **Criar tarefa do fluxo de análise**.

    ![Criar uma tarefa de análise da cadeia] (./media/data-lake-store-stream-analytics/create.job.png "Criar uma tarefa de análise da cadeia")

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada de Blobs do projecto

1. Abra a página para a tarefa de análise da cadeia, clique no separador de **entradas do tipo** e, em seguida, clique em **Adicionar uma entrada** para iniciar o assistente.

2. Na página **Adicionar uma entrada para o seu trabalho** , selecione a **sequência de dados**e, em seguida, clique na seta avançar.

    ![Adicionar uma entrada para o seu trabalho] (./media/data-lake-store-stream-analytics/create.input.1.png "Adicionar uma entrada para o seu trabalho")

3. Na página **Adicionar um fluxo de dados para o seu trabalho** , selecione **armazenamento de BLOBs**e, em seguida, clique na seta avançar.

    ![Adicionar um fluxo de dados para o projecto] (./media/data-lake-store-stream-analytics/create.input.2.png "Adicionar um fluxo de dados para o projecto")

4. Na página **definições do armazenamento de BLOBs** , fornece detalhes para o armazenamento de BLOBs que irá utilizar como origem de dados de entrada.

    ![Forneça o blob definições de armazenamento] (./media/data-lake-store-stream-analytics/create.input.3.png "Forneça o blob definições de armazenamento")

    * **Enter um alias de entrada**. Este é um nome exclusivo fornecidos para a tarefa de entrada.
    * **Selecione uma conta de armazenamento**. Certifique-se a conta de armazenamento é na região mesmo que a tarefa de análise da cadeia ou será implicam custo adicional de mover dados entre regiões.
    * **Fornecer um contentor de armazenamento**. Pode optar por criar um novo contentor ou selecione um contentor existente.

    Clique na seta reencaminhar.

5. Na página **definições de serialização** , defina o formato de serialização como **CSV**, delimitador como **separador**, codificação como **UTF8**e, em seguida, clique na marca de verificação.

    ![Fornecer as definições de serialização] (./media/data-lake-store-stream-analytics/create.input.4.png "Fornecer as definições de serialização")

6. Depois de ter com o assistente, a entrada de BLOBs será adicionada no separador **introduções** e a coluna de **diagnóstico** deve mostrar **OK**. Pode também explicitamente testar a ligação para a entrada, utilizando o botão de **Testar ligação** na parte inferior.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Criar uma saída de arquivo de Lake de dados para o projecto

1. Abra a página para a tarefa de análise da cadeia, clique no separador de **resultados** e, em seguida, clique em **Adicionar um resultado** para iniciar o assistente.

2. Na página **Adicionar um resultado para o seu trabalho** , selecione **Arquivo de dados de Lake**e, em seguida, clique na seta avançar.

    ![Adicionar um resultado para o seu trabalho] (./media/data-lake-store-stream-analytics/create.output.1.png "Adicionar um resultado para o seu trabalho")

3. Na página da **ligação de autorizar** , se já tiver criado uma conta do arquivo de Lake de dados, clique em **Autorizar agora**. Caso contrário, clique em **Inscrever-se agora** para criar uma nova conta. Para este tutorial, diga-nos partem do princípio de que já tem uma conta de arquivo de Lake de dados criada (tal como mencionado no pré-requisito). Será automaticamente autorizado utilizando as credenciais com o qual tiver iniciado sessão no Portal clássica do Azure.

    ![Autorize arquivo de dados de Lake] (./media/data-lake-store-stream-analytics/create.output.2.png "Autorize arquivo de dados de Lake")

4. Na página **Definições de arquivo de Lake de dados** , introduza as informações, conforme apresentado na captura de ecrã abaixo.

    ![Definições de especificar dados Lake arquivo] (./media/data-lake-store-stream-analytics/create.output.3.png "Definições de especificar dados Lake arquivo")

    * **Enter um alias de saída**. Este é um nome exclusivo que fornecer no resultado da tarefa.
    * **Especificar uma conta do arquivo de dados de Lake**. Deve já tiver criado esta situação, tal como mencionado no pré-requisito.
    * **Especificar um padrão de prefixo do caminho**. Isto é necessário para identificar os ficheiros de resultados que são escritos ao arquivo de dados de Lake pela tarefa de análise da cadeia. Porque os títulos dos resultados de escritos pela tarefa são num formato de GUID, incluindo um prefixo irá ajudar a identificar a saída escrita. Se pretende incluir um carimbo de data e hora como parte do prefixo Certifique-se de incluir `{date}/{time}` no padrão prefixo. Se incluir este, os campos de **Data **e **Hora formato** estão activados e pode selecionar o formato de escolha.

    Clique na seta reencaminhar.

5. Na página **definições de serialização** , defina o formato de serialização como **CSV**, delimitador como **separador**, codificação como **UTF8**e, em seguida, clique na marca de verificação.

    ![Especifique o formato de saída] (./media/data-lake-store-stream-analytics/create.output.4.png "Especifique o formato de saída")

6. Depois de ter com o assistente, o resultado de arquivo de Lake dados será adicionado no separador **saídas** e a coluna de **diagnóstico** deve mostrar **OK**. Pode também explicitamente testar a ligação para o resultado ao utilizar o botão **Testar ligação** na parte inferior.

## <a name="run-the-stream-analytics-job"></a>Executar o processo de análise da cadeia

Para executar uma tarefa de análise da cadeia, tem de executar uma consulta a partir do separador da consulta. Para este tutorial, pode executar a consulta de exemplo, substituindo os marcadores de posição com o trabalho de entrada e saída aliases, conforme apresentado na captura de ecrã abaixo.

![Executar consulta] (./media/data-lake-store-stream-analytics/run.query.png "Executar consulta")

Clique em **Guardar** a partir da parte inferior do ecrã e, em seguida, clique em **Iniciar**. Na caixa de diálogo, selecione **Hora personalizada**e, em seguida, selecione uma data no passado, tal como **1/1/2016**. Clique na marca de verificação para iniciar a tarefa. Pode demorar até a alguns minutos para iniciar a tarefa.

![Definir tempo de trabalho] (./media/data-lake-store-stream-analytics/run.query.2.png "Definir tempo de trabalho")

Depois de inicia a tarefa, clique no separador **Monitor** para ver como os dados foram processados.

![Trabalho do monitor] (./media/data-lake-store-stream-analytics/run.query.3.png "Trabalho do monitor")

Por fim, pode utilizar o [Portal do Azure](https://portal.azure.com) abre a sua conta do arquivo de Lake de dados e verificar se os dados com êxito foram escritos para a conta.

![Verificar saída] (./media/data-lake-store-stream-analytics/run.query.4.png "Verificar saída")

No painel do Explorador de dados, repare que o resultado é escrito para uma pasta conforme especificado no arquivo de Lake a dados de saída definições (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Consulte também

* [Criar um cluster de HDInsight para utilizar o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

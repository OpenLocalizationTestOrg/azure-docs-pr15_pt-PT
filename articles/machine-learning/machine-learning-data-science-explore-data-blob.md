<properties 
    pageTitle="Explorar dados em armazenamento de Blobs do Azure com Pandas | Microsoft Azure" 
    description="Como explorar dados armazenados no contentor de Blobs do Azure utilizando Pandas." 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar dados em armazenamento de Blobs do Azure com Pandas

Este documento aborda como explorar dados armazenados no contentor de Blobs do Azure utilizando [Pandas](http://pandas.pydata.org/) Python pacote.

As seguintes **menu** ligações para tópicos que descrevem como utilizar ferramentas para explorar dados a partir de vários ambientes de armazenamento. Esta tarefa é um passo no [Processo de ciências de dados]().

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criar uma conta de armazenamento Azure. Se precisar de instruções, consulte o artigo [criar uma conta de armazenamento do Windows Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Os dados armazenados numa conta de armazenamento de Blobs do Azure. Se precisar de instruções, consulte o artigo [mover dados para e de armazenamento do Windows Azure](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregar os dados para um DataFrame Pandas
Para explorar e manipular um conjunto de dados,-tem primeiro de ser transferido a partir da origem de BLOBs para um ficheiro local, que, em seguida, pode ser carregado num DataFrame Pandas. Eis os passos a seguir para este procedimento:

1. Transferir os dados a partir do Azure blob com o seguinte Python código de exemplo com o serviço de Blobs. Substitua a variável o código seguinte seus valores específicos: 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Ler os dados numa moldura de dados Pandas a partir do ficheiro transferido.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora está pronto para explorar os dados e gerar funcionalidades neste conjunto de dados.

##<a name="blob-dataexploration"></a>Exemplos de exploração de dados utilizando Pandas

Eis alguns exemplos de formas de explorar dados utilizando Pandas:

1. Verificar se o **número de linhas e colunas** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Inspecionar** o primeiro ou último primeiras **linhas** no conjunto de dados seguinte:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Verificar o **tipo de dados** de que cada coluna tenha sido importada como utilizando o seguinte código de exemplo
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Selecione a **Estatística básica** para as colunas no conjunto de dados da seguinte forma
 
        dataframe_blobdata.describe()
    
5. Veja o número de entradas para cada valor da coluna da seguinte forma

        dataframe_blobdata['<column_name>'].value_counts()

6. **Contar valores em falta** versus o número real de entradas em cada coluna utilizando o seguinte código de exemplo

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Se tiver **em falta valores** de uma coluna específica dos dados, pode largá-los da seguinte forma:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Outra forma de substituir valores em falta é com a função de modo:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Criar um gráfico de **histograma** utilizando o número de variável de agrupamentos para desenhar a distribuição de uma variável 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Observe **correlação** entre variáveis utilizando um scatterplot ou utilizando a função de correlação incorporados

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

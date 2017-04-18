<properties
    pageTitle="Criar funcionalidades para dados de armazenamento de Blobs do Azure utilizando Panda | Microsoft Azure"
    description="Como criar funcionalidades para dados armazenados no contentor de Blobs do Azure com o pacote de Panda Python."
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
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />

#<a name="create-features-for-azure-blob-storage-data-using-panda"></a>Criar funcionalidades para dados de armazenamento de Blobs do Azure utilizando Panda

Este documento mostra como criar funcionalidades para dados armazenados no contentor de Blobs do Azure utilizando o pacote de [Pandas](http://pandas.pydata.org/) Python. Depois de destaques como carregar os dados numa moldura dados Panda,-mostra como gerar categorias funcionalidades de utilização de scripts de Python com valores de indicadores e binning funcionalidades.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Este **menu** contém ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tiver criado uma conta de armazenamento de Blobs do Azure e armazenou os seus dados aí. Se precisar de instruções para configurar uma conta, consulte o artigo [criar uma conta de armazenamento do Windows Azure](../storage/storage-create-storage-account.md#create-a-storage-account)


## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados numa moldura Pandas dados
Para explorar e manipular um conjunto de dados,-lo têm de ser transferida a partir da origem de BLOBs para um ficheiro local que, em seguida, pode ser carregado numa moldura Pandas dados. Eis os passos a seguir para este procedimento:

1. Transferir os dados a partir do Azure blob com o seguinte código de Python de exemplo com o serviço de Blobs. Substitua a variável o código abaixo seus valores específicos:

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

##<a name="blob-featuregen"></a>Geração de funcionalidade

As duas secções mostram como gerar funcionalidades categorias com valores de indicadores e binning utilização de Python scripts.

###<a name="blob-countfeature"></a>Valor do indicador com base funcionalidade geração

Funcionalidades de categorias podem ser criadas da seguinte forma:

1. Inspecione a distribuição da coluna categorias:

        dataframe_blobdata['<categorical_column>'].value_counts()

2. Gerar valores de indicador para cada um dos valores de coluna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Aderir a coluna de indicador com a moldura de dados original

            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Remova a variável original propriamente dito:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

###<a name="blob-binningfeature"></a>Geração de funcionalidade binning

Para gerar funcionalidades binned, podemos proceder da seguinte forma:

1. Adicionar uma sequência de colunas a uma coluna numérica do agrupamento

        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)

2. Converter binning para uma sequência de variáveis booleanas

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')

3. Por fim, participar as variáveis fictícias novamente para a moldura de dados original

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

##<a name="sql-featuregen"></a>Escrever dados BLOBs do Azure e consumir no Azure máquina aprendizagem

Depois de ter a explorar os dados e criado as funcionalidades necessárias, pode carregar os dados (amostra ou featurized) para um Azure blob e consuma-lo no Azure máquina aprendizagem através dos seguintes passos: tenha em atenção que podem ser criadas funcionalidades adicionais na Studio de formação de máquina de Azure também.
1. Escrever a moldura de dados para o ficheiro local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar os dados para BLOBs do Azure da seguinte forma:

        from azure.storage.blob import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

        try:

        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Agora, podem ser lidos os dados a partir do blob utilizando o módulo Azure máquina aprendizagem [Importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , conforme mostrado no ecrã abaixo:

![blob leitor](./media/machine-learning-data-science-process-data-blob/reader_blob.png)

<properties 
    pageTitle="Armazenamento de dados de exemplo no Azure blob | Microsoft Azure" 
    description="Dados de exemplo na armazenamento de Blobs do Azure" 
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
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Armazenamento de dados de exemplo no Azure blob


Este documento abrange dados amostragem armazenados no armazenamento de Blobs do Azure ao transferi-lo através de programação e, em seguida, amostragem-lo utilizando procedimentos escritos Python.

**Porque é que os seus dados de exemplo?**
Se o conjunto de dados que pretende analisar for grande, normalmente é uma boa ideia para baixo amostras os dados a reduzi-lo para um tamanho mais pequeno, mas representativo e mais viável. Isto facilita a compreensão de dados, informações detalhadas e engenharia de funcionalidade. Seu papel no processo de análise de Cortana é protótipos rápido das funções de processamento de dados e modelos de aprendizagem automática.

No **menu** abaixo ligações para tópicos que descrevem como dados a partir de vários ambientes de armazenamento de exemplo. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarefa amostragem é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="download-and-down-sample-data"></a>Transfira e dados de exemplo para baixo
1. Transfira os dados a partir do armazenamento de Blobs do Azure utilizando o serviço de Blobs do código de Python de exemplo seguinte: 

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

2. Ler os dados numa moldura de dados Pandas do ficheiro transferido acima.

        import pandas as pd

        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Seta para baixo amostras de dados utilizando o `numpy`do `random.choice` da seguinte forma:

        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Agora pode trabalhar com a moldura de dados acima com a amostra de percentagem de 1 para informações mais detalhadas e criação de funcionalidade.

##<a name="heading"></a>Carregar dados e lê-la para o Azure máquina aprendizagem

Pode utilizar o seguinte código de exemplo para baixo amostras os dados e utilizá-lo diretamente no Azure ML:

1. Escrever a moldura de dados para um ficheiro local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar o ficheiro local para uma BLOBs do Azure utilizando o código de exemplo seguinte:

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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Ler os dados a partir de Blobs do Azure utilizando Azure ML [Importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , conforme mostrado na imagem abaixo:
 
![blob leitor](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

 

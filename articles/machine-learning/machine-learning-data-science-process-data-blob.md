<properties 
    pageTitle="Processar os dados Azure blob com analytics avançados | Microsoft Azure" 
    description="Processar dados no armazenamento Azure Blob." 
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

#<a name="heading"></a>Processar dados do Azure blob com analytics avançado

Este documento abrange dados explorar e funcionalidades do gerador de dados armazenados no armazenamento Azure Blob. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados para um pacote de dados de Pandas
Para explorar e manipular um dataset, este tem de ser transferido da origem de blob num ficheiro local que, em seguida, pode ser carregado numa moldura de dados de Pandas. Eis os passos a seguir para efectuar este procedimento:

1. Transferir os dados da Azure blob com o seguinte código de Python de exemplo utilizando o serviço de blob. Substitua a variável no código abaixo seus valores específicos: 

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


2. Ler os dados numa moldura de dados Pandas do ficheiro transferido.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora está pronto para explorar os dados e gerar funcionalidades este dataset.


##<a name="blob-dataexploration"></a>Exploração de dados

Eis alguns exemplos de formas de explorar dados utilizando Pandas:

1. Inspeccionar o número de linhas e colunas 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Inspeccione as primeiros ou últimos algumas linhas no conjunto de dados conforme a seguir indicado:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Verifique o tipo de dados de que cada coluna tenha sido importada como utilizando o seguinte código de exemplo
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Verificar as estatísticas de base para as colunas no conjunto de dados do seguinte modo
 
        dataframe_blobdata.describe()
    
5. Observe o número de entradas para cada valor de coluna da seguinte forma

        dataframe_blobdata['<column_name>'].value_counts()

6. Contar valores em falta versus o número real de entradas em cada coluna utilizando o seguinte código de exemplo

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Se tiver valores em falta para uma determinada coluna nos dados, pode largá-los do seguinte modo:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Outra forma para substituir valores em falta é com a função de modo:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Criar um gráfico de histograma utilizando a variável número de posições para desenhar a distribuição de uma variável 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Observe correlações entre variáveis utilizando um scatterplot ou utilizando a função de correlação incorporada

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>Geração de funcionalidade
    
A Microsoft pode gerar funcionalidades utilizando o Python do seguinte modo:

###<a name="blob-countfeature"></a>Valor do indicador baseado geração de funcionalidade

Funcionalidades categorias podem ser criadas da seguinte forma:

1. Inspeccione a distribuição da coluna categorias:
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. Gerar valores de indicadores para cada um dos valores de coluna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Aderir a coluna de indicador com o pacote de dados original 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Remova a variável original próprio:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Binning de funcionalidade de geração

Para gerar as funcionalidades de binned, vamos proceder do seguinte modo:

1. Adicionar uma sequência de colunas a uma coluna numérica de posição
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. Converter o binning para uma sequência de variáveis booleanos

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. Finalmente, associar as variáveis fictícias novamente para o pacote de dados original

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>Escrever dados no Azure blob e consumidores na aprendizagem de máquinas Azure

Depois de ter explorado os dados e criado as funcionalidades necessárias, pode carregar os dados (amostra ou featurized) para um Azure blob e consomem-lo na aprendizagem de máquinas Azure utilizando os seguintes passos: tenha em atenção que as funcionalidades adicionais podem ser criadas no Studio de aprendizagem de máquinas de Azure bem. 
1. Escrever o pacote de dados no ficheiro local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar os dados para um Azure blob do seguinte modo:

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

3. Agora os dados podem ser lidos do blob utilizando os [Dados de importação] de aprendizagem de máquinas Azure[ import-data] módulo como é mostrado no ecrã abaixo:
 
![blob de leitor][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

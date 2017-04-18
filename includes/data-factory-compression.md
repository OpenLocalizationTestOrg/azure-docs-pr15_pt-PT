### <a name="compression-support"></a>Suporte de compressão  
Processar grandes conjuntos de dados pode causar congestionamentos e/s e de rede. Por conseguinte, dados comprimidos no armazena podem não só acelerar a transferência de dados através da rede e poupar espaço em disco, mas também trazer melhoramentos significativos ao desempenho no processamento de dados grandes. Atualmente, compressão é suportada para arquivos de dados com base no ficheiro como BLOBs do Azure ou sistema de ficheiros no local.  

> [AZURE.NOTE] Definições de compressão não são suportadas para os dados na **AvroFormat**, **OrcFormat**ou **ParquetFormat**. 

Para especificar a compressão de um conjunto de dados, utilize a propriedade de **compressão** no conjunto de dados JSON tal como no exemplo seguinte:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
A secção de **compressão** tem duas propriedades:  
  
- **Tipo:** o codec de compressão, que pode ser **GZIP**, **Deflate** ou **BZIP2**.  
- **Nível:** a razão de compressão, que pode ser **Optimal** ou **mais rápida**. 
    - **Mais rápida:** A operação de compressão deve concluir mais rapidamente possível, mesmo se o ficheiro resultante não é optimizada comprimido de origem. 
    - **Optimal**: A operação de compressão deve ser optimizada comprimida, mesmo que a operação demora mais tempo para concluir. 
    
    Para mais informações, consulte o tópico de [Nível de compressão](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Suponha que o conjunto de dados de exemplo acima é utilizado como a saída de uma atividade de cópia, a atividade de cópia comprime os dados de saída com codec GZIP utilizando o rácio ideal e, em seguida, escrever os dados comprimidos para um ficheiro com o nome pagecounts.csv.gz no armazenamento de Blobs do Azure.   

Quando especificar propriedade compressão um conjunto de dados entrado JSON, pipeline de pode ler dados comprimidos da origem e se indicar a propriedade de um conjunto de dados de saída JSON, a atividade de cópia pode escrever dados comprimidos para o destino. Eis alguns cenários de exemplo: 

- Dados de leitura GZIP comprimidos a partir de um BLOBs do Azure, descomprimi-lo e escrever dados de resultado para uma base de dados do Azure SQL. Definir o conjunto de dados de Blobs do Azure teclado com a compressão propriedade JSON neste caso. 
- Ler os dados a partir de um ficheiro de texto simples a partir do sistema de ficheiros no local, comprimi-las utilizando o formato de GZip e escrever os dados comprimidos uma BLOBs do Azure. Definir um conjunto de dados de Blobs do Azure de saída com a compressão propriedade JSON neste caso.  
- Ler um dados comprimidos GZIP a partir de um BLOBs do Azure, descomprimi-lo, comprimi-lo utilizando BZIP2 e escrever dados de resultado para um BLOBs do Azure. Definir o conjunto de dados de Blobs do Azure entrado com o tipo de compressão definido para GZIP e o conjunto de dados de saída com o tipo de compressão definido para BZIP2 neste caso.   

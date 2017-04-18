<properties
   pageTitle="Copiar dados a partir do Azure armazenamento de Blobs para o arquivo de dados de Lake | Microsoft Azure"
   description="Utilize a ferramenta de AdlCopy para copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake"
   services="data-lake-store"
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
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake

> [AZURE.SELECTOR]
- [Utilizar DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Utilizar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Azure dados Lake arquivo fornece uma ferramenta de linha de comandos, [AdlCopy](http://aka.ms/downloadadlcopy), para copiar dados a partir das seguintes origens:

* A partir do Azure armazenamento blob para o arquivo de dados de Lake. Não pode utilizar AdlCopy para copiar dados a partir do arquivo de dados de Lake para blobs de armazenamento do Windows Azure.

* Entre duas contas do Azure dados Lake loja. 

Além disso, pode utilizar a ferramenta de AdlCopy em dois modos diferentes:

* **Autónomo**, onde a ferramenta utiliza recursos de arquivo de Lake de dados para efetuar a tarefa.

* **Utilizar uma conta de Lake a análise de dados**, onde as unidades atribuídas à sua conta de dados Lake Analytics são utilizadas para executar a operação de cópia. Poderá pretender utilizar esta opção quando está à procura de executar as tarefas de cópia de uma forma previsível.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Contentor de **Blobs do Azure armazenamento** com alguns dados.

- **Conta azure dados Lake Analytics (opcional)** - consulte o artigo [Introdução ao Azure dados Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta do arquivo de Lake de dados.

- **Ferramenta de AdlCopy**. Instale a ferramenta de AdlCopy a partir do [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy

Utilizar a seguinte sintaxe para trabalhar com a ferramenta de AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Os parâmetros na sintaxe são descritos abaixo:

| Opção    | Descrição                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Origem    | Especifica a localização da origem de dados de Blobs do Azure armazenamento. A origem pode ser um contentor blob, um blob ou outra conta de arquivo de Lake de dados.                                                                                                                                                                                                                                                                                                 |
| Destino      | Especifica o destino de arquivo de Lake de dados para copiar para.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Especifica a tecla de acesso de armazenamento para a origem de Blobs do Azure armazenamento. O que é necessário apenas se a origem for um contentor blob ou de um blob.                                                                                                                                                                                                                                                                                                                                                 |
| Conta   | **Opcional**. Utilize esta opção se pretender utilizar conta Azure dados Lake Analytics para executar a tarefa de cópia. Se utilizar a opção de /Account na sintaxe mas não especificar uma conta de dados Lake Analytics, AdlCopy utiliza uma conta predefinida para executar a tarefa. Além disso, se utilizar esta opção, tem de adicionar origem (Azure armazenamento Blob) e de destino (Azure dados Lake Store) como origens de dados para a sua conta de análise de Lake de dados.  |
| Unidades     |  Especifica o número de unidades de dados Lake Analytics que será utilizada para a tarefa de cópia. Esta opção é obrigatória se utilizar a opção **/Account** para especificar a conta de análise de Lake de dados.
| Padrão   | Especifica um padrão de regex indica quais blobs ou ficheiros para copiar. AdlCopy utiliza a correspondência entre maiúsculas e minúsculas. É o padrão de predefinido utilizado quando não é especificado nenhum padrão copiar todos os itens. Especificar várias padrões de ficheiro não é suportada.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utilizar AdlCopy (como programa autónomo) para copiar dados a partir de um blob de armazenamento do Windows Azure

1. Abra uma linha de comandos e navegue para o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico do contentor de origem para um arquivo de Lake dados:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] A sintaxe da acima indica o ficheiro a ser copiado para uma pasta na conta de arquivo de Lake de dados. Ferramenta de AdlCopy cria uma pasta, se o nome da pasta especificada não existir.

    Vai ser-lhe para introduzir as credenciais para a subscrição Azure em qual tiver a sua conta do arquivo de Lake de dados. Irá ver um resultado semelhante ao seguinte:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Também pode copiar todos os os blobs a partir de um contentor para a conta de arquivo de Lake dados utilizando o seguinte comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por exemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Utilizar AdlCopy (como programa autónomo) para copiar dados de outra conta de arquivo de Lake de dados

Também pode utilizar AdlCopy para mover dados entre duas contas do arquivo de Lake de dados.

1. Abra uma linha de comandos e navegue para o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um ficheiro específico de uma conta de dados Lake arquivo para outra.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Por exemplo:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] A sintaxe da acima indica o ficheiro a ser copiado para uma pasta no destino da conta do arquivo de Lake de dados. Ferramenta de AdlCopy cria uma pasta, se o nome da pasta especificada não existir.

    Vai ser-lhe para introduzir as credenciais para a subscrição Azure em qual tiver a sua conta do arquivo de Lake de dados. Irá ver um resultado semelhante ao seguinte:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. O seguinte comando copia todos os ficheiros a partir de uma pasta específica na origem de conta de arquivo de Lake de dados para uma pasta no destino da conta do arquivo de Lake de dados.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilizar AdlCopy (com a conta de dados Lake Analytics) para copiar dados

Também pode utilizar a sua conta de dados Lake Analytics para executar a tarefa de AdlCopy para copiar dados de blobs do Azure armazenamento ao arquivo de dados de Lake. Normalmente utilizaria esta opção quando os dados para ser movida estão no intervalo de gigabytes e terabytes e pretende débito previsíveis e melhor desempenho.

Para utilizar a sua conta de dados Lake Analytics com AdlCopy para copiar a partir de um Blob de armazenamento do Azure, a origem (Azure armazenamento Blob) deve ser adicionada como uma origem de dados para a sua conta de análise de Lake de dados. Para obter instruções sobre como adicionar origens de dados adicionais à sua conta de análise de Lake de dados, consulte o artigo [Gerir a análise de dados Lake conta origens de dados](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Se está a copiar a partir de uma conta Azure dados Lake loja como a origem de utilizar uma conta de análise de Lake de dados, não terá associar a conta de arquivo de Lake de dados com a conta de análise de Lake de dados. O requisito para associar o arquivo de origem com a conta de dados Lake Analytics é apenas quando a origem for uma conta de armazenamento do Windows Azure.

Execute o seguinte comando para copiar a partir de um blob de armazenamento do Windows Azure a uma conta de arquivo de Lake dados utilizando a análise de Lake dados conta:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


Da mesma forma, execute o seguinte comando para copiar a partir de um blob de armazenamento do Windows Azure a uma conta de arquivo de Lake dados utilizando a análise de Lake dados conta:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Utilizar AdlCopy para copiar dados utilizando a correspondência de padrão

Nesta secção, pode aprende a utilizar AdlCopy para copiar dados de uma origem (no nosso exemplo abaixo utilizamos Azure armazenamento Blob) a uma conta do arquivo de Lake a dados de destino utilizando a correspondência de padrão. Por exemplo, pode utilizar os passos abaixo para copiar todos os ficheiros com a extensão de ficheiro. csv do blob de origem para o destino.

1. Abra uma linha de comandos e navegue para o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar todos os ficheiros com extensão *. csv de um blob específico do contentor de origem para um arquivo de Lake dados:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Faturação

* Se utilizar a ferramenta de AdlCopy como programa autónomo será efetuada para os custos de saída para mover dados, se a conta de armazenamento do Windows Azure de origem não está na mesma região como o arquivo de Lake de dados.

* Se utilizar a ferramenta de AdlCopy com a sua conta de dados Lake Analytics, será aplicada padrão de [taxas de faturaçãohttps Lake a análise de dados](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Considerações para utilizar AdlCopy

* AdlCopy (para obter a versão 1.0.5 do anexo II), suporta copiar dados a partir de fontes que constituem tem mais de milhares de ficheiros e pastas. No entanto, se encontrar problemas de copiar um conjunto de dados grande, pode distribuir ficheiros/pastas em sub-pastas diferentes e utilizar o caminho para esses subpastas como a origem em vez disso.

## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

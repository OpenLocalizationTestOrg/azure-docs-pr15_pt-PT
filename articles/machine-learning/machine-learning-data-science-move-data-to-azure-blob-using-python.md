<properties
    pageTitle="Mover os dados de armazenamento de Blobs do Azure através do Python e para | Microsoft Azure"
    description="Mover os dados de armazenamento de Blobs do Azure através do Python e para"
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
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Mover os dados de armazenamento de Blobs do Azure através do Python e para

Este tópico descreve como da lista, carregar e transferir blobs utilizar a API Python. Com a API Python fornecido no Azure SDK, pode:

- Criar um contentor
- Carregar um blob para um contentor
- Transferir blobs
- Blobs num contentor de lista
- Eliminar um blob

Para mais informações sobre como utilizar a API Python, consulte o artigo [como utilizar o serviço de armazenamento de Blobs do Python](../storage/storage-python-how-to-use-blob-storage.md).

Orientações sobre tecnologias utilizadas para mover os dados para e/ou a partir do armazenamento de Blobs do Azure estão ligados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se estiver a utilizar VM tiver sido configurada com os scripts fornecidos pela [dados ciência Virtual máquinas no Azure](machine-learning-data-science-virtual-machines.md), AzCopy já está instalado na VM.

> [AZURE.NOTE] Para obter uma introdução concluída ao armazenamento de Blobs do Azure, referir [Noções básicas de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e ao [Serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Pré-requisitos

Este documento assume que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferir dados, tem de saber a sua chave de nome e a conta da conta de armazenamento Azure.

- Para configurar uma subscrição do Azure, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).

- Para obter instruções sobre como criar uma conta de armazenamento e para obter conta e informações da chave, consulte o artigo [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Carregar dados para Blob

Adicione o fragmento que se segue na parte superior de qualquer código Python na qual pretende aceder programaticamente armazenamento do Windows Azure:

    from azure.storage.blob import BlobService

O objeto **BlobService** permite-lhe trabalhar com contentores e blobs. O código seguinte cria um objeto de BlobService utilizando a chave de nome e a conta da conta de armazenamento. Substitua o nome da conta e chave da conta com a sua chave e conta real.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Utilize os seguintes métodos para carregar os dados para um blob:

1. colocar\_bloco\_blob\_a partir do\_caminho (os carregamentos pendentes os conteúdos de um ficheiro a partir de path especificado)
2. colocar\_block_blob\_a partir do\_ficheiro (carrega o conteúdo a partir de um ficheiro já aberta/da cadeia)
3. colocar\_bloco\_blob\_a partir do\_bytes (carregamentos uma matriz de bytes)
4. colocar\_bloco\_blob\_a partir do\_texto (carrega o valor de texto especificado utilizando a codificação especificada)

O seguinte código de exemplo carrega um ficheiro local de um contentor:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O seguinte código de exemplo os carregamentos pendentes todos os ficheiros (excluindo directórios) num diretório local ao armazenamento blob:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Transferir dados do Blob

Utilize os seguintes métodos para transferir dados a partir de um blob:
1. obter\_blob\_para\_caminho
2. obter\_blob\_para\_ficheiro
3. obter\_blob\_para\_bytes
4. obter\_blob\_para\_texto

Estes métodos que executam o necessárias das secções quando o tamanho dos dados excede 64 MB.

O seguinte código de exemplo transfere o conteúdo de um blob num contentor para um ficheiro local:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O seguinte código de exemplo transfere todos os blobs a partir de um contentor. Utiliza lista\_blobs para obter a lista de blobs disponíveis no contentor e as transfere para uma pasta local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name

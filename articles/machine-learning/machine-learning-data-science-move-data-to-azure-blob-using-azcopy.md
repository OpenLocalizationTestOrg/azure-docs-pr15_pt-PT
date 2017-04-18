<properties
    pageTitle="Mover os dados de armazenamento de Blobs do Azure através do AzCopy e para | Microsoft Azure"
    description="Mover os dados de armazenamento de Blobs do Azure através do AzCopy e para"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Mover os dados de armazenamento de Blobs do Azure através do AzCopy e para

AzCopy é um utilitário da linha de comandos concebido para carregar, transferir e copiar dados para e de Blobs do Microsoft Azure, ficheiros e armazenamento de tabela.

Para obter instruções sobre como instalar AzCopy e informações adicionais sobre a utilização-lo com a plataforma Azure, consulte o artigo [Introdução ao utilitário de linha de comandos do AzCopy](../storage/storage-use-azcopy.md).

Orientações sobre tecnologias utilizadas para mover os dados para e/ou a partir do armazenamento de Blobs do Azure estão ligados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se estiver a utilizar VM tiver sido configurada com os scripts fornecidos pela [dados ciência Virtual máquinas no Azure](machine-learning-data-science-virtual-machines.md), AzCopy já está instalado na VM.

> [AZURE.NOTE] Para obter uma introdução concluída ao armazenamento de Blobs do Azure, referir [Noções básicas de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e ao [Serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Pré-requisitos

Este documento assume que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferir dados, tem de saber a sua chave de nome e a conta da conta de armazenamento Azure.

- Para configurar uma subscrição do Azure, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).

- Para obter instruções sobre como criar uma conta de armazenamento e para obter conta e informações da chave, consulte o artigo [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Executar AzCopy comandos

Para executar AzCopy comandos, abra uma janela de comando e navegue para o diretório de instalação AzCopy no seu computador, onde se encontra o AzCopy.exe executável. 

A sintaxe básica para comandos AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] Pode adicionar a localização da instalação AzCopy para o caminho do sistema e, em seguida, execute os comandos a partir de qualquer directório. Por predefinição, AzCopy está instalado para *% ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Carregar ficheiros para uma BLOBs do Azure

Para carregar um ficheiro, utilize o seguinte comando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Transferir ficheiros a partir de um BLOBs do Azure

Para transferir um ficheiro a partir de um BLOBs do Azure, utilize o seguinte comando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transferir blobs entre o Azure contentores

Para transferir blobs entre o Azure contentores, utilize o seguinte comando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Sugestões para utilizar AzCopy

> [AZURE.TIP]   
> 1. Quando **carregar** ficheiros, */S* carregamentos de ficheiros de forma recursiva. Sem este parâmetro, ficheiros nos seus subdiretórios não são carregados.  
> 2. Quando a **transferência de** ficheiro, */S* procura a contentor de forma recursiva até que todos os ficheiros no directório especificado e os seus subdiretórios ou todos os ficheiros que correspondem ao padrão especificado no directório indicado e os seus subdiretórios, são transferidos.  
> 3.  Não é possível especificar um **ficheiro específico blob** para transferir a utilizar o parâmetro */Source* . Para transferir um ficheiro específico, especifique o nome do ficheiro blob para transferir a utilizar o parâmetro */Pattern* . Parâmetro **/S** pode ser utilizado para ter AzCopy procure uma nome de ficheiro padrão forma recursiva. Sem o parâmetro padrão, AzCopy transfere todos os ficheiros nesse directório.

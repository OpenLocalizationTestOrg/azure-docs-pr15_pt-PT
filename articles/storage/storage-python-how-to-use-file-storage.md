<properties
    pageTitle="Como utilizar o armazenamento de ficheiros do Azure a partir do Python | Microsoft Azure"
    description="Saiba como utilizar o armazenamento de ficheiros do Azure a partir do Python para carregar, lista, transferir e eliminar ficheiros."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>Como utilizar o armazenamento de ficheiros do Azure a partir do Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Descrição geral

Este artigo mostrar-lhe como efetuar cenários comuns utilizando o armazenamento de ficheiros. Os exemplos são gravados no Python e utilizam o [Microsoft Azure armazenamento SDK para Python]. Os cenários abrangidos incluem a carregar, lista, transferir e eliminar ficheiros.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Criar uma partilha

O objeto **FileService** permite-lhe trabalhar com partilhas, diretórios e ficheiros. O código seguinte cria um objeto de **FileService** . Adicione o seguinte na parte superior de qualquer ficheiro Python na qual pretende aceder programaticamente armazenamento do Windows Azure.

    from azure.storage.file import FileService

O código seguinte cria um objeto de **FileService** utilizando a chave de nome e a conta da conta de armazenamento.  Substitua 'minha conta' e 'mykey' com o seu nome de conta e chave.

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

No exemplo seguinte de código, pode utilizar um objeto de **FileService** para criar a partilha, se não existe.

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>Carregar um ficheiro para uma partilha

Uma partilha de armazenamento de ficheiros do Azure contém pelo menos, um diretório de raiz onde residem os ficheiros. Nesta secção, vai aprender carregar um ficheiro a partir do armazenamento local para o diretório de raiz de uma partilha.

Para criar um ficheiro e carregar dados, utilize o **criar\_ficheiro\_a partir do\_caminho**, **criar\_ficheiro\_a partir do\_sequência**, **criar\_ficheiro\_a partir do\_bytes** ou **criar\_ficheiro\_a partir do\_texto** métodos. Estes são os métodos de alto nível que executam o necessárias das secções quando o tamanho dos dados excede 64 MB.

**criar\_ficheiro\_a partir do\_caminho** carrega o conteúdo de um ficheiro a partir do caminho especificado, e **criar\_ficheiro\_a partir do\_sequência** carrega o conteúdo a partir de um ficheiro/sequência já aberto. **criar\_ficheiro\_a partir do\_bytes** carrega uma matriz de bytes, e **criar\_ficheiro\_a partir do\_texto** carrega o valor de texto especificado utilizando a codificação especificada (a predefinição é UTF-8).

O exemplo seguinte carrega o conteúdo do ficheiro **sunset.png** no ficheiro de **Meuficheiro** .

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>Como: criar um diretório

Para organizar armazenamento colocação de ficheiros no interior do sub-directórios em vez de ser todos eles no diretório de raiz. O serviço de armazenamento de ficheiros Azure permite-lhe criar tantas directórios como permitirá que a sua conta. O código abaixo irá criar um diretório subpastas com o nome **sampledir** no diretório de raiz.

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como: lista de ficheiros e directórios de uma partilha

Para listar os ficheiros e directórios de uma partilha, utilize o **lista\_directórios\_e\_ficheiros** método. Este método devolve um gerador. O código seguinte exporta o **nome** de cada ficheiro e diretório de uma partilha de consola do.

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>Transferir ficheiros

Para transferir dados de um ficheiro, utilize **obter\_ficheiro\_para\_caminho**, **obter\_ficheiro\_para\_sequência**, **obter\_ficheiro\_para\_bytes**, ou **obter\_ficheiro\_para\_texto**. Estes são os métodos de alto nível que executam o necessárias das secções quando o tamanho dos dados excede 64 MB.

O exemplo seguinte demonstra a utilização **obter\_ficheiro\_para\_caminho** para transferir o conteúdo do ficheiro **Meuficheiro** e armazená-lo para o ficheiro de **saída sunset.png** .

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>Eliminar um ficheiro

Por fim, para eliminar um ficheiro, contacte o **delete_file**.

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de ficheiros, siga estas ligações para mais informações.

- [Centro de programadores do Python](/develop/python/)
- [Serviços de armazenamento Azure REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Blogue da equipa do armazenamento Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python

<properties
    pageTitle="Como utilizar o armazenamento de BLOBs (armazenamento de objeto) a partir do Rubi | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com armazenamento de Blobs do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Como utilizar o armazenamento de Blobs do Rubi

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. Armazenamento de BLOBs pode armazenar qualquer tipo de texto ou dados binários, como um documento, ficheiro de multimédia ou instalador de aplicações. Armazenamento de BLOBs é também conhecido como o armazenamento do objeto.

Este guia mostrar-lhe como efetuar cenários comuns a utilizar o armazenamento de Blobs. Os exemplos estão escritos com a API rubi. Os cenários abrangidos incluem blobs **carregar, lista, transferir** e **Eliminar** .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Rubi

Crie uma aplicação rubi. Para obter instruções, consulte o artigo [Rubi na aplicação Web do carris numa VM Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurar a aplicação para aceder ao armazenamento

Para utilizar o armazenamento do Windows Azure, é necessário transferir e utilizar o pacote de azure Rubi, que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de resto de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote

1. Utilize uma interface da linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix).

2. Escreva "azure de instalação gem" na janela de comandos para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote

Utilizar o editor de texto Favoritos, adicione o seguinte para a parte superior do ficheiro Rubi onde tenciona utilizar o armazenamento:

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento Azure

O módulo azure vai ler as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_ACCESS_KEY** para as informações necessárias para ligar à sua conta de armazenamento Azure. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações da conta antes de utilizar **Azure::Blob::BlobService** com o seguinte código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Para obter estes valores a partir de um classic ou conta de armazenamento do Gestor de recursos no portal do Azure:

1. Inicie a sessão [portal do Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. Na pá definições no lado direito, clique em **Teclas de acesso**.
4. O pá de teclas de acesso que é apresentada, verá a tecla de acesso 1 e 2 da chave de acesso. Pode utilizar qualquer um destes. 
5. Clique no ícone de copiar para copiar a chave para a área de transferência. 

Para obter estes valores a partir de uma conta de armazenamento clássica no portal do Azure clássico:

1. Inicie a sessão [portal do Azure clássica](https://manage.windowsazure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. Clique em **GERIR as teclas de acesso** na parte inferior do painel de navegação.
4. De pop-up a caixa de diálogo, verá o nome da conta de armazenamento, a tecla de acesso principal e a chave de acesso secundário. Chave do access, pode utilizar principal ou no gráfico secundário. 
5. Clique no ícone de copiar para copiar a chave para a área de transferência.

## <a name="create-a-container"></a>Criar um contentor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

O objeto **Azure::Blob::BlobService** permite-lhe trabalhar com contentores e blobs. Para criar um contentor, utilize o **criar\_container()** método.

Exemplo de código seguinte cria um contentor ou imprimir saída o erro se existir um.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Se pretender tornar os ficheiros no contentor público, pode definir permissões no contentor.

Apenas pode modificar a <strong>criar\_container()</strong> chamada para transmitir a **: público\_acesso\_nível** opção:

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Os valores válidos para a **: público\_acesso\_nível** opção são:

* **blob:** Especifica completa público acesso de leitura para contentor e blob de dados. Os clientes podem enumerar blobs dentro do contentor através do pedido anónimo, mas não é possível enumerar contentores dentro da conta de armazenamento.

* **contentor:** Especifica o público acesso de leitura para blobs. Dados de BLOBs dentro deste contentor podem ser lidos através do pedido anónimo, mas os dados de contentor não estão disponíveis. Os clientes não é possível enumerar blobs dentro do contentor através do pedido anónimo.

Em alternativa, pode modificar o nível de acesso do público de um contentor utilizando **definir\_contentor\_acl()** método para especificar o nível de acesso do público.

O exemplo seguinte de código altera o nível de acesso do público ao **contentor**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Para carregar o conteúdo para um blob, utilize o **criar\_bloco\_blob()** método para criar o blob, utilizar um ficheiro ou uma cadeia de como o conteúdo do blob.

O código seguinte carrega o ficheiro **test.png** como um novo blob denominado "imagem-blob" no contentor.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista

Para listar os contentores, utilize o método de **list_containers()** .
Para listar blobs dentro de um contentor, utilize **lista\_blobs()** método.

Isto exporta os urls das todos os blobs em todos os contentores da conta.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Transferir blobs

Para transferir blobs, utilize o **obter\_blob()** método para obter o conteúdo.

Exemplo de código seguinte demonstra a utilização **obter\_blob()** para transferir o conteúdo de "imagem blob" e escrevê-lo para um ficheiro local.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Eliminar um Blob
Por fim, para eliminar um blob, utilize o **Eliminar\_blob()** método. Exemplo de código seguinte demonstra como eliminar um blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as tarefas mais complexas do armazenamento, siga estas ligações:

- [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositório de [Azure SDK para Rubi](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub
- [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)

<properties
    pageTitle="Utilizar o clip Azure com armazenamento Azure | Microsoft Azure"
    description="Saiba como utilizar a Interface de comandos do Azure (Azure CLI) com o armazenamento do Windows Azure para criar e gerir contas de armazenamento e trabalhar com blobs do Azure e ficheiros. O clip do Azure é uma ferramenta em diferentes plataformas "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>Utilizar o clip Azure com armazenamento Azure

## <a name="overview"></a>Descrição geral

O clip do Azure fornece um conjunto de abrir origem, em diferentes plataformas comandos para trabalhar com a plataforma Azure. Fornece muitas das mesmas funcionalidades que se encontram no [portal do Azure](https://portal.azure.com) , bem como dados avançada funcionalidade do access.

Neste guia, vamos explorar como utilizar a [Interface de comandos do Azure (Azure CLI)](../xplat-cli-install.md) para executar uma variedade de tarefas de administração e desenvolvimento com armazenamento do Windows Azure. Recomendamos que transferir e instalar ou atualizar para o clip mais recente do Azure antes de utilizar este guia.

Este guia assume que compreender os conceitos básicos de armazenamento do Windows Azure. O guia fornece um número de scripts para demonstrar a utilização do clip do Azure com armazenamento do Windows Azure. Não se esqueça de atualizar as variáveis de script com base na sua configuração antes de executar cada script.

> [AZURE.NOTE] O guia fornece exemplos de script de comandos e o clip do Azure para contas de armazenamento clássica. Consulte o artigo [utilizar o clip do Azure para Mac, Linux e o Windows com o Azure a gestão de recursos](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para Azure clip comandos para contas de armazenamento do Gestor de recursos.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Introdução ao armazenamento do Windows Azure e o clip do Azure em 5 minutos

Este guia utiliza Ubuntu para obter exemplos, mas outras plataformas SO devem executar de forma semelhante.

**Familiarizado com o Azure:** Obter uma subscrição do Microsoft Azure e uma conta Microsoft associada a essa subscrição. Para obter informações sobre as opções de compra Azure, consulte o artigo [Versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e [Membro oferece](https://azure.microsoft.com/pricing/member-offers/) (para os membros do MSDN, Microsoft Partner Network e BizSpark e outros programas da Microsoft).

Consulte [atribuir funções de administrador no Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) para obter mais informações sobre as subscrições Azure.

**Depois de criar uma subscrição do Microsoft Azure e conta:**

1. Transfira e instale o clip de Azure seguir as instruções indicadas no [instalar o clip do Azure](../xplat-cli-install.md).
2. Depois de ter sido instalado o clip do Azure, será capaz de utilizar o comando azure a partir do seu interface de comandos (festa, terminais, linha de comandos) para aceder aos comandos da Clip Azure. Tipo de `azure` comando e deverá ver o resultado seguinte.

    ![Resultado do comando Azure][Image1]

3. Na interface de linha de comandos, escreva `azure storage` lista de todos os comandos de armazenamento azure e obter uma impressão primeira dos recursos funcionais o clip do Azure fornece. Pode escrever o nome do comando com parâmetro **-h** (por exemplo, `azure storage share create -h`) para ver os detalhes da sintaxe de comando.
4. Agora, vamos dar-lhe um script simples que mostra os comandos de clip Azure básicos para aceder ao armazenamento do Windows Azure. O script primeiro irá pedir-lhe para definir duas variáveis para a sua conta de armazenamento e chave. Em seguida, o script irá criar um novo contentor na nova conta de armazenamento e carregar um ficheiro de imagem existente (BLOBs) para desse contentor. Depois do script listas de todos os blobs no contentor, este irá transferir o ficheiro de imagem ao directório de destino que existe no computador local.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. No seu computador local, abra o editor de texto preferido (vim por exemplo). Escreva o script acima para o seu editor de texto.

6. Agora, tem de atualizar as variáveis de script com base nas suas definições de configuração.

    - **< storage_account_name >** Utilize o nome indicado no script ou introduza um novo nome para a sua conta de armazenamento. **Importantes:** O nome da conta de armazenamento tem de ser exclusivo no Azure. Tem de ser minúscula, demasiado!

    - **< storage_account_key >** A tecla de acesso da sua conta de armazenamento.

    - **< container_name >** Utilize o nome indicado no script ou introduza um novo nome para o contentor.

    - **< image_to_upload >** Introduza um caminho para uma imagem no seu computador local, tais como: "~ / images/HelloWorld.png".

    - **< destination_folder >** Introduza um caminho para um diretório local para armazenar ficheiros transferidos a partir do armazenamento do Windows Azure, tais como: "~/downloadImages".

7. Depois de atualizar as variáveis necessárias no vim, prima combinações de teclas "Esc,:, wq!" Para guardar o script.

8. Para executar este script, basta escreva o nome do ficheiro de script na consola do festa. Depois de executa este script, deve ter uma pasta de destino local que inclui o ficheiro de imagem transferido. A captura de ecrã seguinte mostra um resultado de exemplo:

Depois de executa o script, deve ter uma pasta de destino local que inclui o ficheiro de imagem transferido.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gerir contas de armazenamento com o clip do Azure

### <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Enquanto a maioria dos comandos de armazenamento irão trabalhar sem uma subscrição do Azure, recomendamos que para ligar à sua subscrição a partir do clip Azure. Para configurar o clip do Azure para trabalhar com a sua subscrição, siga os passos em [ligar a uma subscrição do Azure a partir do clip o Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

Para utilizar o armazenamento Azure, terá uma conta de armazenamento. Pode criar uma nova conta de armazenamento Azure depois de ter configurado o seu computador para ligar à sua subscrição.

        azure storage account create <account_name>

O nome da sua conta de armazenamento tem de estar entre 3 e 24 carateres de comprimento e utilizar números e letras minúsculas apenas.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Configurar uma conta de armazenamento Azure predefinida no variáveis de ambiente

Pode ter várias contas de armazenamento na sua subscrição. Pode escolher um deles e configurá-la as variáveis de ambiente para todos os comandos de armazenamento na mesma sessão. Permite-lhe executar os comandos de armazenamento do Azure clip sem especificar a conta de armazenamento e chave explicitamente.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Outra forma para configurar uma conta de armazenamento predefinido está a utilizar cadeia de ligação. Em primeiro lugar obter a cadeia de ligação ao comando:

        azure storage account connectionstring show <account_name>

Em seguida, copie a cadeia de ligação de saída e configurá-lo a variável de ambiente:

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Criar e gerir blobs

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Esta secção assume que já está familiarizado com os conceitos de armazenamento de Blobs do Azure. Para obter informações detalhadas, consulte o artigo [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md) e [Blob conceitos de serviço](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Criar um contentor

Cada blob no Azure armazenamento tem de ser um contentor. Pode criar um contentor privada utilizando o `azure storage container create` comando:

        azure storage container create mycontainer

> [AZURE.NOTE] Existem três níveis de acesso de leitura anónimo: **desativar**, **Blob**e **contentor**. Para impedir o acesso anónimo blobs, defina o parâmetro de permissão para **desativar**. Por predefinição, o novo contentor for privado e pode ser acedido apenas pelo proprietário da conta. Para permitir que o público leitura acesso anónimo para recursos de BLOBs, mas não contentor metadados ou para a lista de blobs no contentor, defina o parâmetro de permissão para um **Blob**. Para permitir que o acesso de leitura completa público blob recursos, contentor metadados e a lista de blobs no contentor, defina o parâmetro de permissão ao **contentor**. Para mais informações, consulte o artigo [Gerir anónimo acesso de leitura contentores e blobs](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Armazenamento de Blobs do Azure suporta bloco blobs e blobs de página. Para mais informações, consulte o artigo [Noções sobre bloco Blobs, acrescentar Blobs e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs na um contentor, pode utilizar o `azure storage blob upload`. Por predefinição, este comando carrega os ficheiros locais de um blob bloco. Para especificar o tipo de para o blob, pode utilizar o `--blobtype` parâmetro.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Transfira blobs a partir de um contentor

O exemplo seguinte demonstra como transferir blobs a partir de um contentor.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Copiar blobs

Pode copiar modo assíncrono blobs num ou em vários contas de armazenamento e regiões.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outro. Neste exemplo estamos criar um contentor onde blobs publicamente, estão acessíveis de forma anónima.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Este exemplo executa uma cópia assíncrona. Pode monitorizar o estado de cada operação de cópia ao executar o `azure storage blob copy show` operação.

Tenha em atenção que o URL de origem fornecido para a operação de cópia tem de ser acessível publicamente, ou incluir um token de SA (assinatura acesso partilhado).

### <a name="delete-a-blob"></a>Eliminar um blob

Para eliminar um blob, utilize a abaixo comando:

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Criar e gerir partilhas de ficheiros

Armazenamento de ficheiros Azure oferece armazenamento partilhado para aplicações utilizando o protocolo SMB padrão. Máquinas virtuais do Microsoft Azure e serviços em nuvem, bem como as aplicações no local, podem partilhar dados do ficheiro através de partilhas montadas. Pode gerir partilhas de ficheiros e dados de ficheiros através do clip o Azure. Para mais informações sobre o armazenamento de ficheiros do Azure, consulte o artigo [Introdução ao armazenamento de ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md) ou [como utilizar o armazenamento de ficheiros do Azure com Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Uma partilha de ficheiros do Azure é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e directórios têm de ser criados numa partilha de ficheiros. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até dos limites de capacidade da conta de armazenamento. O exemplo seguinte cria uma partilha de ficheiros chamada **minhapartilha**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Criar um diretório

Um diretório fornece uma estrutura hierárquica opcional para uma partilha de ficheiros Azure. O exemplo seguinte cria num diretório chamado **O_meu_dir** na partilha de ficheiro.

        azure storage directory create myshare myDir

Tenha em atenção que caminho do directório pode incluir vários níveis, *por exemplo*, **um / b**. No entanto, tem de garantir que todos os directórios principais existe. Por exemplo, para caminho **um / b**, tem de criar diretório **um** primeiro, em seguida, criar diretório **b**.

### <a name="upload-a-local-file-to-directory"></a>Carregar um ficheiro local para o diretório

O exemplo seguinte carrega um ficheiro a partir de **~/temp/samplefile.txt** ao diretório **O_meu_dir** . Edite o caminho do ficheiro para que aponte para um ficheiro válido no seu computador local:

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Note que um ficheiro na partilha pode ser até 1 TB de tamanho.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Listar os ficheiros em raiz de partilhar ou do diretório

Pode listar os ficheiros e os seus subdiretórios numa raiz de partilhar ou num directório utilizando o seguinte comando:

        azure storage file list myshare myDir

Note que o nome do directório é opcional para a operação de listagem. Se for omitido, o comando lista os conteúdos do diretório de raiz da partilha.

### <a name="copy-files"></a>Copiar ficheiros

Começando com a versão 0.9.8 do Azure clip, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Abaixo estamos demonstram como efetuar estas operações de copiar utilizando os comandos do clip. Para copiar um ficheiro para o novo directório:

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Para copiar um blob para um diretório de ficheiro:

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Próximos passos

Eis alguns artigos relacionados e recursos para saber mais sobre o armazenamento do Windows Azure.

- [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Referência da API REST armazenamento Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png

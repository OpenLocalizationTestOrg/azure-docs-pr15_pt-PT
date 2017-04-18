<properties
    pageTitle="Gerir o acesso anónimo de leitura para contentores e blobs | Microsoft Azure"
    description="Saiba como disponibilizar contentores e blobs para acesso anónimo e como aceder às mesmas através de programação."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerir o acesso anónimo de leitura para contentores e blobs

## <a name="overview"></a>Descrição geral

Por predefinição, apenas o proprietário da conta de armazenamento pode aceder a recursos de armazenamento dentro dessa conta. Para o armazenamento de BLOBs, pode definir permissões de um contentor para permitir o acesso anónimo de leitura para o contentor e respetivos blobs, para que pode conceder acesso aos recursos sem partilhar a sua chave da conta.

Acesso anónimo é melhor para os cenários onde pretende que determinadas blobs para estar sempre disponível para acesso anónimo de leitura. Para melhor com controlo, pode criar uma assinatura de acesso partilhado, que permite-lhe para acesso de delegado restringido utilizar permissões diferentes e através de um intervalo de tempo especificado. Para mais informações sobre como criar assinaturas acesso partilhado, consulte o artigo [Utilizar partilhada acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de utilizadores anónimos contentores e blobs

Por predefinição, um contentor e qualquer blobs dentro da mesma podem ser acedidas apenas pelo proprietário da conta de armazenamento. Para dar aos utilizadores anónimos permissões de leitura para um contentor e respetivos blobs, pode definir as permissões de contentor para permitir o acesso de público. Os utilizadores anónimos podem ler blobs dentro de um contentor acessível publicamente sem autenticar o pedido.

Contentores fornecem as seguintes opções para gerir o acesso de contentor:

- **Público acesso de leitura total:** Podem ser lidos contentor e blob dados através do pedido anónimo. Os clientes podem enumerar blobs dentro do contentor através do pedido anónimo, mas não é possível enumerar contentores dentro da conta de armazenamento.

- **Acesso para blobs só de leitura do público:** Dados BLOBs dentro deste contentor podem ser lidos através do pedido anónimo, mas os dados de contentor não estão disponíveis. Os clientes não é possível enumerar blobs dentro do contentor através do pedido anónimo.

- **Nenhum público acesso de leitura:** Dados de contentor e blob podem ser lidos por apenas o proprietário da conta.

Pode definir permissões de contentor das seguintes formas:

- A partir do [Azure Portal](https://portal.azure.com).
- Através de programação, utilizando a biblioteca de cliente do armazenamento ou REST API.
- Ao utilizar o PowerShell. Para obter informações sobre como definir permissões de contentor a partir do Azure PowerShell, consulte o artigo [Utilizar o PowerShell Azure com armazenamento do Windows Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Definir permissões de contentor a partir do Portal do Azure

Para definir permissões de contentor a partir do [Portal do Azure](https://portal.azure.com), siga estes passos:

1. Navegue para o dashboard para a sua conta de armazenamento.
2. Selecione o nome do contentor a partir da lista. Clicar no nome expõe blobs no contentor de que selecionou
3. Selecione a **política de acesso** da barra de ferramentas.
4. No campo **tipo de acesso** , selecione o nível de permissões pretendido, conforme mostrado na captura de ecrã abaixo.

    ![Metadados do contentor caixa de diálogo Editar](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Definir permissões de contentor através de programação utilizando o .NET

Para definir permissões para um contentor utilizando a biblioteca de cliente .NET, obtenha permissões existentes no contentor ao contactar o método de **GetPermissions** . Em seguida, defina a propriedade de **PublicAccess** para o objeto **BlobContainerPermissions** que é devolvido pelo método **GetPermissions** . Por fim, chame o método de **definir permissões** com as permissões atualizados.

O exemplo seguinte define as permissões o contentor para completa público acesso de leitura. Para definir permissões o público acesso de leitura para blobs apenas, defina a propriedade de **PublicAccess** para **BlobContainerPublicAccessType.Blob**. Para remover todas as permissões para utilizadores anónimos, defina a propriedade para **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Aceder a contentores e blobs de forma anónima

Um cliente que acede contentores e blobs de forma anónima, pode utilizar construtores que não necessitam de credenciais. Os exemplos seguintes mostram algumas maneiras diferentes de forma anónima de recursos de Blobs do serviço de referência.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anónimo

Pode criar um novo objeto de cliente do serviço para o acesso anónimo, fornecendo o ponto final de serviço de Blob da conta. No entanto, também tem souber o nome de um contentor nessa conta que está disponível para acesso anónimo.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Referência de um contentor de forma anónima

Se tiver o URL para um contentor que está disponível de forma anónima, pode utilizá-lo a referenciar o contentor diretamente.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Referência de um blob de forma anónima

Se tiver o URL para um blob que está disponível para acesso anónimo, pode referenciar o blob diretamente utilizar esse URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Funcionalidades disponíveis para utilizadores anónimos

A tabela seguinte mostra quais as operações que podem ser chamadas por utilizadores anónimos quando ACL um contentor está definido para permitir o acesso do público.

| Operação do resto                                         | Permissão com completa público acesso de leitura | Permissão com público acesso de leitura para blobs apenas |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Lista contentores                                        | Apenas o proprietário                              | Apenas o proprietário                                        |
| Criar o contentor                                       | Apenas o proprietário                              | Apenas o proprietário                                        |
| Obter as propriedades de contentor                               | Todos os                                     | Apenas o proprietário                                        |
| Obter o contentor metadados                                 | Todos os                                     | Apenas o proprietário                                        |
| Definir contentor metadados                                 | Apenas o proprietário                              | Apenas o proprietário                                        |
| Obtenha o contentor ACL                                      | Apenas o proprietário                              | Apenas o proprietário                                        |
| Definir o contentor ACL                                      | Apenas o proprietário                              | Apenas o proprietário                                        |
| Eliminar contentor                                       | Apenas o proprietário                              | Apenas o proprietário                                        |
| Lista Blobs                                             | Todos os                                     | Apenas o proprietário                                        |
| Colocar BLOBs                                               | Apenas o proprietário                              | Apenas o proprietário                                        |
| Obter Blob                                               | Todos os                                     | Todos os                                               |
| Obter as propriedades de BLOBs                                    | Todos os                                     | Todos os                                               |
| Definir as propriedades de BLOBs                                    | Apenas o proprietário                              | Apenas o proprietário                                        |
| Obter Blob metadados                                      | Todos os                                     | Todos os                                               |
| Definir Blob metadados                                      | Apenas o proprietário                              | Apenas o proprietário                                        |
| Colocar o bloco                                              | Apenas o proprietário                              | Apenas o proprietário                                        |
| Obter a lista de bloqueios (apenas bloqueia consolidada)                 | Todos os                                     | Todos os                                               |
| Obter a lista de bloqueios (apenas blocos de repetição ou todos os blocos) | Apenas o proprietário                              | Apenas o proprietário                                        |
| Colocar a lista de blocos                                         | Apenas o proprietário                              | Apenas o proprietário                                        |
| Eliminar Blob                                            | Apenas o proprietário                              | Apenas o proprietário                                        |
| Copiar Blob                                              | Apenas o proprietário                              | Apenas o proprietário                                        |
| Instantâneo Blob                                          | Apenas o proprietário                              | Apenas o proprietário                                        |
| Locação financeira Blob                                             | Apenas o proprietário                              | Apenas o proprietário                                        |
| Colocar a página                                               | Apenas o proprietário                              | Apenas o proprietário                                        |
| Obter intervalos de página                                        | Todos os                                     | Todos os                                                  |
| Acrescentar Blob                                            | Apenas o proprietário                              | Apenas o proprietário                                                  |


## <a name="see-also"></a>Consulte também

- [Autenticação para os serviços de armazenamento Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Utilizar assinaturas de acesso partilhado (SA)](storage-dotnet-shared-access-signature-part-1.md)
- [Delegação de acesso com uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)

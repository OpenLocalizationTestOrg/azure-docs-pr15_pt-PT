<properties
    pageTitle="Criar um instantâneo só de leitura de um blob | Microsoft Azure"
    description="Saiba como criar um instantâneo de um blob para agregar dados blob num dado momento altura. Compreenda como são faturados instantâneos e como utilizá-las para minimizar os custos de capacidade."
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

# <a name="create-a-blob-snapshot"></a>Criar um instantâneo de BLOBs

## <a name="overview"></a>Descrição geral

Um instantâneo é a versão só de leitura de um blob que é disponibilizado num ponto no tempo. Instantâneos são úteis para cópias de segurança blobs. Depois de criar um instantâneo, pode ler, copiar ou eliminá-la, mas não é possível modificar.

Um instantâneo de um blob é idêntico aos seu blob base, exceto a que o blob URI tem um valor **DateTime** acrescentado ao blob URI para indicar a hora em que o instantâneo foi tirado. Por exemplo, se uma página blob URI é `http://storagesample.core.blob.windows.net/mydrives/myvhd`, instantâneo URI é semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Todos os instantâneos partilham o blob base URI. A única distinção entre o blob base e o instantâneo é o valor de **data/hora** anexado.

Um blob pode ter qualquer número de instantâneos. Instantâneos mantenham-la até explicitamente são eliminadas. Um instantâneo não é possível outlive respetivo blob base. Pode enumerar instantâneos associados com o blob base para controlar o seu instantâneos atuais.

Quando cria um instantâneo de um blob, propriedades do sistema do blob são copiadas para o instantâneo com os mesmos valores. Metadados do blob base também são copiado instantâneo, a menos que especifique os metadados em separado do limite da quando o criar.

Qualquer concessões associadas com o blob base que afetam o instantâneo. Não é possível adquirir uma locação num instantâneo.

## <a name="create-a-snapshot"></a>Criar um instantâneo

Exemplo de código que se segue mostra como criar um instantâneo no .NET. Este exemplo Especifica metadados em separado do limite da quando é criado.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Copiar instantâneos

Operações de copiar que envolvam blobs e instantâneos siga estas regras:

- Pode copiar um instantâneo ao longo do seu blob base. Através da promoção um instantâneo para a posição do blob base, pode restaurar uma versão anterior de um blob. O permanece instantâneo, mas a base blob é substituída por uma cópia gravável do instantâneo.

- Pode copiar um instantâneo para um blob destino com um nome diferente. O blob destino resultante é um blob gravável e não um instantâneo.

- Quando um blob origem é copiado, qualquer instantâneos do blob de origem não são copiados para o destino. Quando um blob de destino é substituído por uma cópia, qualquer instantâneos associados com o original blob de destino permanecem intactos.

- Quando cria um instantâneo de um blob bloco, lista de bloqueios consolidada do blob também é copiada para o instantâneo. Quaisquer blocos de repetição não são copiados.

## <a name="specify-an-access-condition"></a>Especifique uma condição de acesso

Pode especificar uma condição de acesso para que o instantâneo é criado apenas se uma condição é cumprida. Para especificar uma condição de acesso, utilize a propriedade **AccessCondition** . Se a condição especificada não for cumprida, o instantâneo não é criado e o serviço de BLOBs devolve o código de estado HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Eliminar instantâneos

Não pode eliminar um blob com instantâneos, a menos que os instantâneos são também eliminados. Pode eliminar um instantâneo individualmente ou especificar que todas as instantâneos ser eliminada quando é eliminado o blob de origem. Se tentar eliminar um blob que ainda tem instantâneos, resulta um erro.

O exemplo de código seguinte mostra como eliminar um blob e respetivos instantâneos no .NET, onde `blockBlob` é uma variável do tipo de **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Instantâneos com armazenamento Azure Premium

Utilização de instantâneos com seguir Premium armazenamento estas regras:

- O número máximo de instantâneos por blob de página numa conta premium armazenamento é de 100. Se for excedido esse limite, a operação de Blobs do instantâneo devolve o código de erro 409 (**SnapshotCountExceeded**).

- Pode tirar um instantâneo de um blob de página numa conta de armazenamento premium em 10 minutos. Se taxa for excedida, a operação de Blobs do instantâneo devolve o código de erro 409 (**SnaphotOperationRateExceeded**).

- Não é possível chamar obter Blob para ler um instantâneo de um blob de página numa conta de armazenamento premium. Chamar Blob obter um instantâneo numa conta de armazenamento premium devolve o código de erro 400 (**InvalidOperation**). No entanto, pode ligar a obter propriedades de BLOBs e obter metadados Blob contra um instantâneo numa conta de armazenamento premium.

- Para ler um instantâneo, pode utilizar a operação de BLOBs copiar para copiar um instantâneo para outra blob de página na conta. O blob de destino para a operação de cópia não deve ter qualquer instantâneos existentes. Se o blob de destino tem instantâneos, em seguida, a operação de cópia Blob devolve o código de erro 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Voltar o URI absoluto para um instantâneo

Este exemplo de código c# cria um instantâneo e escreve o URI absoluto para a localização principal.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Compreender a forma de instantâneos imputação dos encargos

Criação de um instantâneo, que é uma cópia só de leitura de um blob, pode resultar em custos de armazenamento de dados adicionais à sua conta. Quando a estruturar a sua aplicação, é importante ter em mente forma destes encargos poderão de imputação para que pode minimizar custos desnecessários.

### <a name="important-billing-considerations"></a>Considerações de faturaçãohttps importantes

A lista seguinte inclui pontos-chave a ter em consideração quando criar um instantâneo.

- Conta de armazenamento tarifas para blocos exclusivos ou páginas, quer estejam no blob ou no instantâneo. A conta não assumir taxas adicionais para instantâneos associados a um blob até que a Atualize o blob no qual se baseiam. Depois de atualizar o blob base, diverge a partir do seus instantâneos. Quando isto acontece, que lhe ser cobrada da blocos exclusivos ou páginas em cada blob ou instantâneo.

- Quando substitui um bloco dentro de um blob bloco, esse bloco subsequentemente é cobrado como um único bloco. Este é verdadeiro, mesmo se o bloco de tenha o mesmo ID de bloco e os mesmos dados-lo no instantâneo. Depois do bloco de é consolidado novamente,-diverge a partir do homólogo para qualquer instantâneo e será cobrado para os respetivos dados. O mesmo se aplica-se para uma página num blob de página que é atualizado com dados idênticos.

- Substituir um blob bloco ao contactar o método **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** substitui todos os blocos no blob. Se tiver um instantâneo associado com esse blob, todos os blocos na base blob e instantâneo agora divergem e será cobrado para todos os blocos de ambas as blobs. Este é verdadeiro, mesmo se os dados do blob base e o instantâneo permanecem idênticos.

- O serviço de Blobs do Azure não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e consolidado é tratado como exclusivos, mesmo se tiver os mesmos dados e o mesmo ID bloco. Uma vez que as taxas de imputação dos blocos de exclusivos de, é importante ter em consideração que atualizar um blob que tem um resultados instantâneo no adicionais blocos exclusivos e taxas adicionais.

> [AZURE.NOTE] Práticas recomendadas ditam faz a gestão de instantâneos cuidadosamente para evitar taxas adicionais. Recomendamos que faz a gestão de instantâneos da seguinte maneira:

> - Eliminar e criar novamente instantâneos associados com um blob sempre que atualizar o blob, mesmo se estiver a atualizar com dados idênticos, a menos que a estrutura da aplicação requer que manter instantâneos. Ao eliminar e voltar a criar instantâneos o blob, pode garantir que a blob e instantâneos coincidem.

> - Se manutenção de instantâneos para um blob, evite chamar **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** para atualizar o blob. Esses métodos substituem todos os blocos de BLOBs, para que o seu base blob e instantâneos divergem significativamente. Em vez disso, atualize o menor número possível de blocos através dos métodos **PutBlock** e **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Instantâneo de cenários de faturação


Os cenários seguintes demonstram a forma de taxas de imputação para um blob bloco e respetivos instantâneos.

Cenário 1, o blob base não tenha sido atualizado depois do instantâneo foi tirado, para que os encargos são suportados apenas pelo blocos exclusivos 1, 2 e 3.

![Azure recursos de armazenamento](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Cenário 2, o blob base foi atualizado, mas não tiver o instantâneo. Foi atualizado bloco 3 e, apesar de que contém os mesmos dados e o ID do mesmo, não é o mesmo bloquear 3 no instantâneo. Como resultado, a conta é cobrada para blocos de quatro.

![Azure recursos de armazenamento](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Cenário 3, o blob base foi atualizado, mas não tiver o instantâneo. Bloco 3 foi substituído com bloco 4 no blob base, mas o instantâneo ainda reflete bloco 3. Como resultado, a conta é cobrada para blocos de quatro.

![Azure recursos de armazenamento](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Cenário 4, o blob base foi atualizado completamente e contém nenhum dos seus blocos de originais. Como resultado, a conta é cobrada para todos os blocos de exclusivos oito. Este cenário pode ocorrer se estiver a utilizar um método de atualização como **UploadFile**, **UploadText**, **UploadFromStream**ou **UploadByteArray**, porque estes métodos substituir todos os conteúdos de um blob.

![Azure recursos de armazenamento](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Próximos passos

Para obter exemplos adicionais a utilizar o armazenamento de BLOBs, consulte o artigo [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Pode transferir uma aplicação de exemplo e executá-la ou procure o código no GitHub. 

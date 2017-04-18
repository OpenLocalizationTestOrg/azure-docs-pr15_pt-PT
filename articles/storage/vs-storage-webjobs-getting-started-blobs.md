<properties
    pageTitle="Introdução ao blob armazenamento e Visual Studio ligado serviços (WebJob projetos) | Microsoft Azure"
    description="Como começar a utilizar o armazenamento de BLOBs num projeto WebJob após ligar a um armazenamento Azure utilizando o Visual Studio ligado serviços."
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao Azure Blob armazenamento e Visual Studio ligado serviços (WebJob projetos)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Este artigo fornece c# exemplos de código que mostram como acionar um processo quando um BLOBs do Azure são criada ou atualizada. Os exemplos de código de utilizar a versão de [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) 1. x. Quando adicionar uma conta de armazenamento a um projeto WebJob utilizando a caixa de diálogo do Visual Studio **Adicionar serviços ligados** , está instalado o pacote de Azure armazenamento NuGet adequado, as referências de .NET adequadas são adicionadas ao projeto, e são atualizadas as cadeias de ligação para a conta de armazenamento no ficheiro App.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como acionar uma função quando um blob é criado ou atualizado

Esta secção mostra como utilizar o atributo **BlobTrigger** .

 **Nota:** O SDK WebJobs analisa ficheiros de registo para ver para blobs novas ou alteradas. Este processo é implicitamente lento; uma função pode, não, ser acionada até alguns minutos ou já depois do blob é criado.  Se tem a aplicação de blobs do processo imediatamente, é o método recomendado criar uma mensagem de fila de espera quando criar o blob e utilizar o atributo [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) em vez do atributo **BlobTrigger** sobre a função que processa o blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Única marcador de posição do nome de Blobs com extensão  

O código seguinte de exemplo copia blobs de texto que aparecem no contentor de *teclado* para o contentor de *saída* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributo leva um parâmetro de cadeia que especifica o nome do contentor e um marcador de posição para o nome de Blobs. Neste exemplo, se for criado um blob denominado *Blob1.txt* no contentor de *entrada* , a função cria um blob denominado *Blob1.txt* no contentor de *saída* .

Pode especificar um padrão de nome com o marcador de posição do nome de BLOBs, conforme mostrado no seguinte exemplo de código:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Este código copia blobs apenas que têm nomes começados por "original-". Por exemplo, *original Blob1.txt* no contentor de *entrada* é copiado para *Copiar Blob1.txt* no contentor de *saída* .

Se precisar de especificar um padrão de nome para nomes de BLOBs que tenham as chavetas no nome de, faça duplo as chavetas. Por exemplo, se pretender localizar blobs no contentor de *imagens* que tenham nomes da seguinte forma:

        {20140101}-soundfile.mp3

Utilize esta opção para o seu padrão:

        images/{{20140101}}-{name}

No exemplo, o valor *nome* do marcador de posição seria *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Marcadores de posição de blob separado, nome e a extensão

O código seguinte de exemplo altera a extensão de ficheiro à medida que copie blobs que aparecem no contentor de *teclado* para o contentor de *saída* . O código inicia a extensão do blob *entrada* e define a extensão do blob de *saída* para *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos de que pode ligar a blobs

Pode utilizar o atributo **BlobTrigger** nos seguintes tipos de:

* **cadeia**
* **TextReader**
* **Sequência**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos de serialização anulados pelo [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se pretender trabalhe diretamente com a conta de armazenamento Azure, também pode adicionar um parâmetro **CloudStorageAccount** a assinatura de método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Conteúdo de Blobs do texto por enlace como uma cadeia de introdução

Se blobs do texto são esperados, **BlobTrigger** podem ser aplicadas a um parâmetro de **cadeia** . O código seguinte de exemplo liga-se um blob de texto para um parâmetro de **cadeia** com o nome **logMessage**. A função utiliza esse parâmetro para escrever o conteúdo do blob ao dashboard WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Introdução serializado blob conteúdo utilizando ICloudBlobStreamBinder

O código de exemplo seguinte utiliza uma classe que implementa **ICloudBlobStreamBinder** para ativar o atributo **BlobTrigger** vincular um blob com o tipo de **WebImage** .

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

O código de encadernação **WebImage** é fornecido uma aula **WebImageBinder** que deriva da **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Como lidar blobs corrompida foi

Quando uma função **BlobTrigger** falha, o SDK liga-lhe-lo novamente, no caso de falha foi causada por um erro de breves. Se a falha é causada pelo conteúdo do blob, a função falha sempre tentar o blob do processo. Por predefinição, o SDK liga-lhe uma função até 5 vezes para um determinado blob. Se o experimentar quinto falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-poison*.

O número máximo de tentativas é configurável. A mesma definição [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) é utilizada para processamento de BLOBs corrompida foi e processamento de mensagem corrompida foi fila de espera.

A mensagem de fila de espera para blobs corrompida foi é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{WebJob nome}*. Funções. *{Nome da função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

No seguinte exemplo de código, a função **CopyBlob** tem código que faz com que o-lo a falha sempre denomina-se. Depois do SDK chama-a para o número máximo de tentativas, é criada uma mensagem na fila de BLOBs corrompida foi e essa mensagem é processada pela função **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

O SDK deserializes automaticamente a mensagem JSON. Eis a classe de **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de inquéritos blob

O SDK WebJobs analisa todos os contentores especificados por atributos de **BlobTrigger** ao início da aplicação. Numa conta grande capacidade de armazenamento esta pesquisa pode demorar algum tempo, por isso, poderá ser tempo antes de blobs novos forem encontrados e **BlobTrigger** funções são executadas.

Para detetar blobs novas ou alteradas depois de início da aplicação, o SDK lê periodicamente a partir de registos do armazenamento de Blobs. Os registos de BLOBs são colocadas na memória intermédia e apenas física obtenham escritos em dez minutos ou por isso, por isso pode ser atraso significativo depois de um blob é criado ou atualizado antes do correspondente **BlobTrigger** função executa.

Existe uma exceção para blobs que cria utilizando o atributo **Blob** . Quando o SDK WebJobs cria um novo blob, transmite o novo blob imediatamente a qualquer funções **BlobTrigger** correspondentes. Por isso, se tiver uma cadeia de entradas de BLOBs e saídas, o SDK pode processá-las eficientemente. Mas se pretender executar o seu blob funções para blobs que são criados ou atualizados por outros meios de processamento de latência baixa, recomendamos que utilize **QueueTrigger** em vez de **BlobTrigger**.

### <a name="blob-receipts"></a>Recibos de BLOBs

O SDK WebJobs torna-se de que a função sem **BlobTrigger** obtém denominada mais de uma vez para o mesmo blob novo ou actualizado. Faz isto, mantendo *blob recibos* para determinar se uma versão de BLOBs determinado foi processada.

Recibos de BLOBs são armazenados num contentor com o nome *anfitriões de webjobs azure* na conta de armazenamento Azure especificada pela cadeia de ligação AzureWebJobsStorage. Um recibo de BLOBs tem as seguintes informações:

* A função que foi denominada para o blob ("*{WebJob nome}*. Funções. *{Nome da função}*"como, por exemplo:"WebJob1.Functions.CopyBlob")
* O nome do contentor
* O tipo de BLOBs ("BlockBlob" ou "PageBlob")
* O nome de BLOBs
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Se pretende forçar reprocessamento de um blob, pode eliminar manualmente a recibo blob para esse blob do contentor *anfitriões de webjobs azure* .

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados abrangidos pelo artigo filas

Para obter informações sobre como gerir o processamento de BLOBs acionado a uma mensagem de fila de espera, ou WebJobs SDK cenários específicos para blob processamento, consulte o artigo [como utilizar o armazenamento de fila Azure com o SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Tópicos relacionados abrangidos no artigo incluem o seguinte:

* Funções de assíncrona
* Várias instâncias
* Desligar sem problemas
* Utilize os atributos de WebJobs SDK no corpo de uma função
* Defina as cadeias de ligação SDK no código.
* Valores definidos para WebJobs SDK parâmetros construtor no código
* Configure **MaxDequeueCount** para processamento de BLOBs corrompida foi.
* Acionar uma função manualmente
* Escrever os registos

## <a name="next-steps"></a>Próximos passos

Este artigo forneceu exemplos de código que mostram como lidar cenários comuns de trabalhar com blobs do Azure. Para obter mais informações sobre como utilizar Azure WebJobs e o SDK WebJobs, consulte o artigo [recursos de documentação Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

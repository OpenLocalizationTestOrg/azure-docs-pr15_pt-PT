<properties 
    pageTitle="Como utilizar o armazenamento de Blobs do Azure com o SDK WebJobs" 
    description="Saiba como utilizar o armazenamento de Blobs do Azure com o SDK WebJobs. Acionar um processo quando for apresentado um novo blob num contentor e processar 'blobs corrompida foi'." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Como utilizar o armazenamento de Blobs do Azure com o SDK WebJobs

## <a name="overview"></a>Descrição geral

Este guia fornece c# exemplos de código que mostram como acionar um processo quando um BLOBs do Azure são criada ou atualizada. Os exemplos de código utilizam [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versão 1. x.

Para obter exemplos de código que mostram como criar blobs, veja [como utilizar o armazenamento de fila Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
O guia assume saber [como criar um projeto WebJob no Visual Studio com cadeias de ligação que apontam para a sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md) ou a [múltiplas contas de armazenamento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Como acionar uma função quando um blob é criado ou atualizado

Esta secção mostra como utilizar o `BlobTrigger` atributo. 

> [AZURE.NOTE] O SDK WebJobs analisa ficheiros de registo para ver para blobs novas ou alteradas. Este processo não está em tempo real; uma função pode, não, ser acionada até alguns minutos ou já depois do blob é criado. Além disso, base de [armazenamento de registos são criados num "os melhores esforços"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Não há garantias ser capturados de todos os eventos. Em algumas condições, poderão ser perdidos registos. Se as limitações fiabilidade e velocidade de accionadores blob não são permitidas para a sua aplicação, o método recomendado é criar uma mensagem de fila de espera quando criar o blob e utilizar o atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) em vez do `BlobTrigger` atributo sobre a função que processa o blob.

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

## <a id="types"></a>Tipos de que pode ligar a blobs

Pode utilizar o `BlobTrigger` atributo nos seguintes tipos de:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Outros tipos de serialização anulados pelo [ICloudBlobStreamBinder](#icbsb) 

Se pretende que trabalhe diretamente com a conta de armazenamento Azure, também pode adicionar um `CloudStorageAccount` parâmetro para a assinatura de método.

Para obter exemplos, consulte o artigo o [código de encadernação blob no repositório azure-webjobs-sdk no GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Conteúdo de Blobs do texto por enlace como uma cadeia de introdução

Se blobs do texto são esperados, `BlobTrigger` podem ser aplicadas a uma `string` parâmetro. O código seguinte de exemplo liga-se um blob de texto para um `string` parâmetro com o nome `logMessage`. A função utiliza esse parâmetro para escrever o conteúdo do blob ao dashboard WebJobs SDK. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Introdução serializado blob conteúdo utilizando ICloudBlobStreamBinder

O código de exemplo seguinte utiliza uma classe que implementa `ICloudBlobStreamBinder` para ativar a `BlobTrigger` atributo para vincular um blob para o `WebImage` tipo.

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

O `WebImage` encadernação código é fornecido numa `WebImageBinder` classe que deriva da `ICloudBlobStreamBinder`.

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

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Obter o caminho de BLOBs para o blob acionador

Para obter o nome do contentor e o nome de Blobs do blob que aciona a função, inclua um `blobTrigger` um parâmetro na assinatura função de cadeia.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>Como lidar blobs corrompida foi

Quando um `BlobTrigger` falha de função, o SDK chama-lo novamente, no caso de falha foi causada por um erro de breves. Se a falha é causada pelo conteúdo do blob, a função falha sempre tentar o blob do processo. Por predefinição, o SDK liga-lhe uma função até 5 vezes para um determinado blob. Se o experimentar quinto falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-poison*.

O número máximo de tentativas é configurável. A mesma definição [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) é utilizada para processamento de BLOBs corrompida foi e processamento de mensagem corrompida foi fila de espera. 

A mensagem de fila de espera para blobs corrompida foi é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{WebJob nome}*. Funções. *{Nome da função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

No seguinte exemplo de código, o `CopyBlob` código que faz com que falhar sempre denomina-se que tem. Depois do SDK a chamadas para o número máximo de tentativas, é criada uma mensagem na fila de BLOBs corrompida foi e essa mensagem é processada pela `LogPoisonBlob` função. 

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

O SDK deserializes automaticamente a mensagem JSON. Eis o `PoisonBlobMessage` classe: 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>Algoritmo de inquéritos blob

O SDK WebJobs analise todos os contentores especificados pelo `BlobTrigger` atributos ao início da aplicação. Numa conta grande capacidade de armazenamento esta pesquisa pode demorar algum tempo, por isso, poderá ser tempo antes de blobs novos forem encontrados e `BlobTrigger` funções são executadas.

Para detetar blobs novas ou alteradas depois de início da aplicação, o SDK lê periodicamente a partir de registos do armazenamento de Blobs. Os registos de BLOBs são colocadas na memória intermédia e apenas física obtenham escritos em dez minutos ou por isso, por isso, poderão existir atraso significativo depois de um blob é criado ou atualizado antes do correspondente `BlobTrigger` executa a função. 

Existe uma exceção para blobs que cria utilizando o `Blob` atributo. Quando o SDK WebJobs cria um novo blob, transmite o novo blob imediatamente a qualquer correspondentes `BlobTrigger` funções. Por isso, se tiver uma cadeia de entradas de BLOBs e saídas, o SDK pode processá-las eficientemente. Mas se pretender executar o blob funções de processamento para blobs que são criados ou atualizados por outros meios de latência baixa, recomendamos que utilize `QueueTrigger` em vez de `BlobTrigger`.

### <a id="receipts"></a>Recibos de BLOBs

O SDK WebJobs assegura que não `BlobTrigger` função obtém denominada mais de uma vez para o mesmo blob novo ou actualizado. Faz isto, mantendo *blob recibos* para determinar se uma versão de BLOBs determinado foi processada.

Recibos de BLOBs são armazenados num contentor com o nome *anfitriões de webjobs azure* na conta de armazenamento Azure especificada pela cadeia de ligação AzureWebJobsStorage. Um recibo de BLOBs tem as seguintes informações:

* A função que foi denominada para o blob ("*{WebJob nome}*. Funções. *{Nome da função}*"como, por exemplo:"WebJob1.Functions.CopyBlob")
* O nome do contentor
* O tipo de BLOBs ("BlockBlob" ou "PageBlob")
* O nome de BLOBs
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Se pretende forçar reprocessamento de um blob, pode eliminar manualmente a recibo blob para esse blob do contentor *anfitriões de webjobs azure* .

## <a id="queues"></a>Tópicos relacionados abrangidos pelo artigo filas

Para obter informações sobre como gerir o processamento de BLOBs acionado a uma mensagem de fila de espera, ou WebJobs SDK cenários específicos para blob processamento, consulte o artigo [como utilizar o armazenamento de fila Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Tópicos relacionados abrangidos no artigo incluem o seguinte:

* Funções de assíncrona
* Várias instâncias
* Desligar sem problemas
* Utilize os atributos de WebJobs SDK no corpo de uma função
* Defina as cadeias de ligação SDK no código.
* Valores definidos para WebJobs SDK parâmetros construtor no código
* Configurar `MaxDequeueCount` para processamento de BLOBs corrompida foi.
* Acionar uma função manualmente
* Escrever os registos

## <a id="nextsteps"></a>Próximos passos

Este guia forneceu exemplos de código que mostram como lidar cenários comuns de trabalhar com blobs do Azure. Para obter mais informações sobre como utilizar Azure WebJobs e o SDK WebJobs, consulte o artigo [Azure WebJobs recomendado recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 

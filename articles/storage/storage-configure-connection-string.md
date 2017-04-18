<properties 
    pageTitle="Configurar uma cadeia de ligação ao armazenamento Azure | Microsoft Azure"
    description="Configure uma cadeia de ligação a uma conta de armazenamento Azure. Uma cadeia de ligação inclui as informações necessárias para autenticar o acesso a uma conta de armazenamento da aplicação de tempo de execução."
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

# <a name="configure-azure-storage-connection-strings"></a>Configurar armazenamento Azure cadeias de ligação

## <a name="overview"></a>Descrição geral

Uma cadeia de ligação inclui as informações de autenticação necessárias para aceder a dados numa conta de armazenamento Azure a partir da sua aplicação o tempo de execução. Pode configurar uma cadeia de ligação para:

- Ligar ao emulador de armazenamento Azure.
- Aceder a uma conta de armazenamento no Azure.
- Aceder a recursos especificados no Azure através de uma assinatura de acesso partilhado (SA).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Armazenar a cadeia de ligação

A aplicação terá de aceder a cadeia de ligação o tempo de execução para autenticar pedidos feitos ao armazenamento do Windows Azure. Tem algumas opções diferentes para armazenar a cadeia de ligação:

- Para uma aplicação em execução no ambiente de trabalho ou num dispositivo, pode armazenar a cadeia de ligação num `app.config `ficheiro ou uma `web.config` ficheiro. Adicione a cadeia de ligação para a secção **AppSettings** .
- Para uma aplicação a ser executada num serviço em nuvem Azure, pode armazenar a cadeia de ligação no [ficheiro de esquema (.cscfg) de configuração do serviço Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicione a cadeia de ligação para a secção **ConfigurationSettings** o ficheiro de configuração do serviço.
- Também pode utilizar a cadeia de ligação diretamente no seu código. Para a maioria dos cenários, no entanto, recomendamos que que armazene sua cadeia de configuração num ficheiro de configuração.

Armazenar a cadeia de ligação dentro de um ficheiro de configuração torna mais fácil atualizar a cadeia de ligação para alternar entre o emulador de armazenamento e uma conta de armazenamento Azure na nuvem. Só precisa de editar a cadeia de ligação para apontarem para o seu ambiente de destino.

Pode utilizar a classe de [Gestor de configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para aceder à sua cadeia de ligação o tempo de execução, independentemente de onde a sua aplicação está em execução.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Criar uma cadeia de ligação ao emulador de armazenamento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte o artigo [utilizar o Azure armazenamento emulador desenvolvimento e testar](storage-use-emulator.md) para mais informações sobre o emulador de armazenamento.

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Criar uma cadeia de ligação a uma conta de armazenamento Azure

Para criar uma cadeia de ligação à sua conta de armazenamento Azure, utilize o formato da cadeia de ligação abaixo. Indicar se pretende ligar-se para a conta de armazenamento através de HTTPS (recomendado) ou HTTP, substitua `myAccountName` com o nome da sua conta de armazenamento e substituir `myAccountKey` com a sua chave de acesso de conta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por exemplo, a cadeia de ligação será semelhante a cadeia de ligação de exemplo seguinte:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Armazenamento Azure suporta HTTP e HTTPS numa cadeia de ligação; No entanto, a utilização de HTTPS se vivamente.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de ligação utilizando uma assinatura de acesso partilhado

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Criar uma cadeia de ligação para um ponto final de armazenamento explícita

Pode especificar explicitamente os pontos finais de serviço na sua cadeia de ligação em vez de utilizar os pontos finais predefinido. Para criar uma cadeia de ligação que especifica um ponto final explícito, especifique o ponto final de serviço completa para cada serviço, incluindo a especificação do protocolo (HTTPS (recomendado) ou HTTP), no seguinte formato:

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Um cenário onde pode optar por especificar um ponto final explícito é se tiver mapeado o ponto final de armazenamento de BLOBs para um domínio personalizado. Nesse caso, pode especificar o ponto final personalizado para o armazenamento de BLOBs na sua cadeia de ligação e, opcionalmente, especifique os pontos finais predefinido para outro serviço se a aplicação utiliza-os.

Eis alguns exemplos de cadeias de ligação válida que especificar um ponto final explícito para o serviço de BLOBs:

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

É utilizado o valor de ponto final que se encontra listado na cadeia de ligação para construir o pedido URIs para o serviço de BLOBs e dita-lo a forma de qualquer URIs que são devolvidos ao seu código.

Tenha em atenção que se optar por omitir um ponto final de serviço da cadeia de ligação, em seguida, não será possível utilizar essa cadeia de ligação para aceder aos dados desse serviço a partir do código.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de ligação com um sufixo de ponto final

Para criar uma cadeia de ligação para o serviço de armazenamento na regiões ou instâncias com sufixos endpoint diferente, tal como para Azure China ou Azure governação, utilize o seguinte formato de cadeia de ligação. Indique se pretende ligar à conta de armazenamento através de HTTP ou HTTPS, substituir `myAccountName` com o nome da sua conta de armazenamento, substituir `myAccountKey` com a sua chave de acesso da conta e substituir `mySuffix` com o sufixo URI:


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


Por exemplo, a cadeia de ligação deve ser semelhante a cadeia de ligação seguinte:

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>Uma cadeia de ligação de análise

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Próximos passos

- [Utilizar o armazenamento Azure emulador para desenvolvimento e teste](storage-use-emulator.md)
- [Explorador de armazenamento Azure](storage-explorers.md)
- [Utilizar assinaturas de acesso partilhado (SA)](storage-dotnet-shared-access-signature-part-1.md)

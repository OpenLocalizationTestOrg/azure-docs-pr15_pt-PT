<properties
    pageTitle="Encriptação do lado do cliente com o .NET para armazenamento do Microsoft Azure | Microsoft Azure"
    description="A biblioteca de cliente de armazenamento do Azure para .NET suporta a encriptação do lado do cliente e integração com o Azure chave cofre para máxima segurança para as suas aplicações de armazenamento do Windows Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Encriptação do lado do cliente e Azure cofre chave para o armazenamento do Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral

A [Biblioteca de cliente do Azure armazenamento para o pacote de .NET Nuget](https://www.nuget.org/packages/WindowsAzure.Storage) suporta a encriptação de dados em aplicações cliente do antes de a carregar para o armazenamento do Windows Azure e desencriptar dados durante a transferência para o cliente. A biblioteca também suporta a integração com o [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) para gestão de chave de conta de armazenamento.

Para obter um tutorial passo a passo que o orienta-pelo processo de encriptação blobs utilizar encriptação do lado do cliente e o Azure chave cofre, consulte o artigo [blobs encriptar e desencriptar no armazenamento do Windows Azure utilizando Azure chave cofre](storage-encrypt-decrypt-blobs-key-vault.md).

Para encriptação do lado do cliente com Java, consulte o artigo [Encriptação do lado do cliente com Java para armazenamento do Windows Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através de técnica de envelope

Os processos de encriptação de siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através de técnica de envelope

Encriptação através de técnica de envelope funciona da seguinte forma:

1. A biblioteca do cliente armazenamento Azure gera uma chave de encriptação conteúdo (CEK), que é uma chave simétrica um tempo utilização.
2. Dados de utilizador são encriptados utilizando este CEK.
3. O CEK, em seguida, é moldado (encriptados) utilizando a chave de encriptação de chave (KEK). O KEK está identificado por um identificador de chave e pode ser um par de chaves assimétrico ou uma chave simétrica e pode ser gerido localmente ou armazenado no Azure chave cofres.

    A biblioteca do cliente armazenamento própria nunca tem acesso ao KEK. A biblioteca invoca o algoritmo de moldagem chave que é fornecido pela chave cofre. Os utilizadores podem optar por utilizar fornecedores personalizados para moldagem/revelação chave se pretender.

4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Windows Azure. A tecla moldada juntamente com alguns metadados de encriptação adicionais está armazenada como metadados (num blob) ou interpolada com os dados encriptados (mensagens em fila e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através de técnica de envelope

Desencriptação através de técnica de envelope funciona da seguinte forma:

1. A biblioteca do cliente assume que o utilizador está a gerir a chave de encriptação chave (KEK) localmente ou no Azure chave cofres. O utilizador não é necessário saber a chave específica que foi utilizado para encriptação. Em vez disso, um solucionador chave que é resolvido identificadores da chave diferentes para teclas pode configurar o e utilizado.
2. A biblioteca do cliente transfere os dados encriptados juntamente com qualquer material de encriptação que está armazenado no serviço.
3. A chave de encriptação conteúdo moldado (CEK) é moldado (desencriptado) utilizando a chave de encriptação de chave (KEK). Aqui novamente, a biblioteca de cliente não tem acesso ao KEK. Basta invocar a personalizadas ou algoritmo identificar do fornecedor de chave cofre.
4. A chave de encriptação conteúdo (CEK), em seguida, é utilizada para desencriptar os dados de utilizador encriptada.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação

A biblioteca do cliente armazenamento utiliza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar dados de utilizador. [Interligação de bloco de codificação (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) especificamente, modo com AES. Cada serviço funciona de forma um pouco diferente, para discutimos cada um deles aqui.

### <a name="blobs"></a>BLOBs

A biblioteca do cliente atualmente suporta a encriptação dos apenas blobs todos. Especificamente, a encriptação é suportada quando os utilizadores utilizam o **UploadFrom** * métodos ou o * *OpenWrite** método. Para transferências, ambos concluída e transferências do intervalo são suportadas.

Durante a encriptação, a biblioteca do cliente irá gerar um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação conteúdo aleatória (CEK) de bytes 32 e executar encriptação de envelope dos dados blob com esta informação. O CEK moldado e alguns metadados de encriptação adicionais, em seguida, são armazenados como blob metadados juntamente com o blob encriptado o serviço.

> [AZURE.WARNING] Se estiver a editar ou carregar a sua própria metadados para o blob, é necessário para se certificar de que este metadados são preservado. Se carregar novos metadados sem este metadados, o CEK moldado, IV e outros metadados serão perdidos e o conteúdo de BLOBs nunca serão recuperável novamente.

Transferir um blob encriptado envolve a obter o conteúdo da BLOBs inteira utilizando o **DownloadTo***/**BlobReadStream** métodos conveniência. O CEK moldado é continente e utilizada juntamente com o IV (armazenado como metadados blob neste caso) para devolver os dados desencriptados aos utilizadores.

Transferir um intervalo arbitrário (**DownloadRange*** métodos) o blob encriptado envolve ajustar o intervalo fornecido pelos utilizadores para obter uma pequena quantidade de dados adicionais que podem ser utilizados para desencriptar com êxito o intervalo solicitado.

Todos os tipos de blob (bloquear blobs, página blobs e acrescentar blobs) pode ser encriptar/desencriptar utilizando este esquema.

### <a name="queues"></a>Filas

Uma vez que as mensagens da fila podem ser de qualquer formato, a biblioteca do cliente define um formato personalizado que inclui a inicialização Vector (IV) e a chave de encriptação conteúdo encriptada (CEK) no texto da mensagem.

Durante a encriptação, a biblioteca do cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa encriptação de envelope a fila de texto da mensagem com esta informação. O CEK moldado e alguns metadados de encriptação adicionais, em seguida, são adicionadas à mensagem encriptada fila de espera. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Ao desencriptar, a tecla moldada é extraída a partir da mensagem de fila de espera e continente. O IV também é extraído a partir da mensagem de fila de espera e utilizado juntamente com a tecla moldada para desencriptar os dados da mensagem fila de espera. Note que os metadados de encriptação são pequenos (em 500 bytes), por isso enquanto contar a para o limite de 64KB de uma mensagem de fila de espera, o impacto deve ser fácil.

### <a name="tables"></a>Tabelas

A biblioteca do cliente suporta a encriptação dos propriedades entidade para inserir e substituir operações.

>[AZURE.NOTE] Intercalar não é suportado. Uma vez que um subconjunto de propriedades possa ter sido encriptado anteriormente a utilizar uma chave diferente, basta intercalar as novas propriedades e atualizar os metadados irão resultar na perda de dados. Intercalar um requer a efetuar chamadas de serviço extra para ler a entidade previamente existente a partir do serviço, ou utilizar uma nova chave por propriedade, os quais não são adequados por motivos de desempenho.

Encriptação de dados de tabela funciona da seguinte forma:  

1. Os utilizadores especificar as propriedades para ser encriptados.
2. A biblioteca do cliente gera um Vetor de inicialização aleatório (IV) de 16 bytes juntamente com uma chave de encriptação conteúdo aleatória (CEK) de 32 bytes para cada entidade e executa encriptação do envelope nas propriedades da individuais para ser encriptado por decorrentes um novo IV por propriedade. A propriedade encriptada está armazenada como dados binários.
3. O CEK moldado e alguns metadados de encriptação adicionais, em seguida, são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia que contém as informações sobre IV, versão e chave moldado. A propriedade segundo reservada (_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações acerca das propriedades que estão encriptados. As informações nesta propriedade segunda (_ClientEncryptionMetadata2) própria estão encriptada.
4. Devido a estas propriedades reservadas adicionais necessárias para encriptação, os utilizadores, poderão agora ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade tem de ser menor que 1 MB.

Tenha em atenção que apenas as propriedades da cadeia pode ser encriptada. Se outros tipos de propriedades para ser encriptados, tem de ser convertidos em cadeias. As cadeias encriptadas são armazenadas no serviço como binárias propriedades e, estes são convertidos em novamente cadeias após desencriptação.

Para tabelas, para além da política de encriptação, os utilizadores tem de especificar as propriedades para ser encriptados. Isto pode ser feito especificando quer um atributo [EncryptProperty] (para entidades POCO que deriva TableEntity) ou uma resolução de encriptação nas opções do pedido. A resolução de uma encriptação é um delegado, que assume uma partição chave, tecla de linha e o nome da propriedade e devolve um valor booleano que indica se deve ser encriptada dessa propriedade. Durante a encriptação, a biblioteca do cliente irá utilizar estas informações para decidir se deve ser encriptada uma propriedade ao escrever o fio. O delegado também fornece a possibilidade de lógica à volta da forma como as propriedades são encriptadas. (Por exemplo, se X, em seguida encriptar propriedade A; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não é necessário fornecer esta informação enquanto lê ou consultar entidades.

### <a name="batch-operations"></a>Operações batch

Em operações lote, a mesma KEK será utilizada em todas as linhas nessa operação lote porque a biblioteca do cliente só permite um objeto de opções (e, consequentemente, uma política de/KEK) por operação batch. No entanto, a biblioteca do cliente irá internamente gerar um novo IV aleatório e CEK aleatório por linha no lote de. Os utilizadores também podem optar por encriptar propriedades diferentes para cada operação no lote de definindo este comportamento na resolução de encriptação.

### <a name="queries"></a>Consultas

Para executar operações de consulta, tem de especificar um chave solucionador que consegue resolver todas as teclas o conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um fornecedor, a biblioteca do cliente irá gerar um erro. Para qualquer consulta que efetua projecções do lado do servidor, a biblioteca do cliente irá adicionar as propriedades de metadados de encriptação especiais (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por predefinição para as colunas selecionadas.

## <a name="azure-key-vault"></a>Azure cofre chave

Azure chave cofre ajuda chaves de criptografia salvaguarda e segredos utilizados pelo nuvem aplicações e serviços. Ao utilizar o Azure chave cofre, os utilizadores podem encriptar teclas e segredos (tal como chaves de autenticação, as teclas de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estejam protegidas por módulos de segurança do hardware (Os HSMs). Para obter mais informações, consulte o artigo [o que é o Azure chave cofre?](../key-vault/key-vault-whatis.md).

A biblioteca do cliente armazenamento utiliza a biblioteca de core de chave cofre para fornecer um quadro comum ao longo do Azure para gestão de teclas. Os utilizadores também aceder a adicional vantagem de utilizar a biblioteca de extensões de chave cofre. A biblioteca de extensões disponibiliza funcionalidades úteis à volta simple e de forma totalmente integrada Symmetric/RSA local e a fornecedores de chave na nuvem e com agregação e em cache.

### <a name="interface-and-dependencies"></a>Interface e dependências

Existem três pacotes de Cofre de chave:

- Microsoft.Azure.KeyVault.Core contém a IKey e IKeyResolver. É um pacote pequenas sem dependências com. A biblioteca de cliente de armazenamento para .NET define-la como uma dependência.
- Microsoft.Azure.KeyVault contém o cliente de chave cofre restantes.
- Microsoft.Azure.KeyVault.Extensions contém o código de extensão que inclui implementações dos algoritmos e um RSAKey e um SymmetricKey. -Depende os espaços de nomes principais e KeyVault e fornece a funcionalidade para definir uma agregação solucionador (quando os utilizadores pretender utilizar vários fornecedores de chaves) e um solucionador chave colocação em cache. Apesar da biblioteca do cliente de armazenamento não diretamente depende este pacote, se pretenderem que os utilizadores utilizar Azure chave cofre para armazenar as respetivas chaves ou utilizar as extensões de cofre chave para consumir local e na nuvem fornecedores de criptografia, elas terão este pacote.

Chave cofre foi concebido para o valor máximo de modelo global de teclas e limites aceleração por chave cofre destinam-se com este deve ter em conta. Quando executa a encriptação do lado do cliente com chave cofre, o modelo preferencial é utilizar teclas de modelo global de simétricas armazenados como segredos num cofre chave e em cache localmente. Os utilizadores têm de fazer o seguinte:

1. Criar uma palavra-passe offline e carregue-o para chave cofre.
2. Utilize identificador base o segredo como um parâmetro para resolver a versão atual do segredo para encriptação e localmente estas informações em cache. Utilizar CachingKeyResolver para colocação em cache; os utilizadores não estão esperados para implementar os seus próprios colocação em cache lógica.
3. Utilize a resolução da cache como uma entrada ao criar a política de encriptação.

Podem encontrar mais informações sobre a utilização de chave cofre nas [exemplos de código de encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Melhores práticas

Suporte de encriptação está disponível apenas na biblioteca de cliente de armazenamento para .NET. Windows Phone e Runtime do Windows não suportam atualmente encriptação.

>[AZURE.IMPORTANT] Tenha em atenção estes pontos importantes ao utilizar a encriptação do lado do cliente:
>
>- Quando a partir de leitura ou ao escrever um blob encriptado, utilize comandos de carregamento de blob toda e comandos de transferência de Blobs do intervalo/inteiro. Evitar escrita para um blob encriptado utilizando operações de protocolo, tais como colocar bloco, colocar a lista de bloqueios, escrever páginas, limpar páginas ou acrescentar bloco; caso contrário, poderá danificada o blob encriptado e torná-lo legível.
>- Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.
>- Se definir metadados sobre o blob encriptado, pode substituir os metadados relacionadas com a encriptação necessários para o desencriptação, uma vez que a definição de metadados não adicionada. Também é verdadeiro para instantâneos; Evite especificando metadados durante a criação de um instantâneo de um blob encriptado. Se tem de estar definido metadados, certifique-se de que chamar o método de **FetchAttributes** pela primeira vez, para obter os metadados de encriptação actual e evitar escritas em simultâneo enquanto está a ser definido metadados.
>- Ative a propriedade **RequireEncryption** nas opções de pedido de predefinido para os utilizadores que devem funcionam apenas com dados encriptados. Consulte abaixo para obter mais informações.


## <a name="client-api--interface"></a>API de cliente / Interface

Ao criar um objeto de EncryptionPolicy, os utilizadores podem fornecer apenas uma chave (execução IKey), apenas um solucionador (execução IKeyResolver) ou ambas. IKey é o tipo de chave básico que é identificada utilizando um identificador de chave e que fornece a lógica de moldagem/revelação. IKeyResolver é utilizada para resolver uma chave de durante o processo de desencriptação. Define um método de ResolveKey que devolve uma IKey atribuído um identificador de chave. Fornece aos utilizadores a capacidade para escolher entre múltiplas teclas que são geridas em várias localizações.

- Para encriptação, a tecla é utilizada sempre e ausência de uma chave de irá resultar num erro.
- Para desencriptação:
    - A resolução de chave é chamada se especificada para obter a chave. Se a resolução for especificada, mas não tem um mapeamento para o identificador chave, é devolvido um erro.
    - Se não for especificado solucionador mas uma chave de for especificada, a tecla é utilizada se respectivo identificador corresponder o identificador de chave necessário. Se o identificador não corresponder, é devolvido um erro.

As [amostras de encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) demonstram um cenário de ponto a ponto mais detalhado para blobs, filas e tabelas, juntamente com a integração de chave cofre.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption

Os utilizadores, opcionalmente, podem ativar a um modo de funcionamento onde devem estar encriptadas todos os carregamentos e transferências. Neste modo, irão falhar tentativas de dados sem uma política de encriptação de carregar ou transferir dados que não estão encriptados no serviço no cliente. A propriedade **RequireEncryption** do objeto opções pedido controla este comportamento. Se a sua aplicação irá encriptar todos os objetos armazenados em armazenamento do Windows Azure, pode definir a propriedade **RequireEncryption** no menu Opções de pedido de predefinida para o objeto de cliente do serviço. Por exemplo, defina **CloudBlobClient.DefaultRequestOptions.RequireEncryption** como **Verdadeiro** para exigir encriptação para todas as operações de blob transpor esse objeto de cliente.

### <a name="blob-service-encryption"></a>Encriptação de Blobs do serviço

Criar um objecto **BlobEncryptionPolicy** e defini-lo nas opções do pedido (por API ou de um nível de cliente utilizando **DefaultRequestOptions**). Tudo o resto será efetuado pela biblioteca do cliente internamente.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Upload the encrypted contents to the blob.
    blob.UploadFromStream(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    MemoryStream outputStream = new MemoryStream();
    blob.DownloadToStream(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Encriptação do serviço de fila de espera

Criar um objecto **QueueEncryptionPolicy** e defini-lo nas opções do pedido (por API ou de um nível de cliente utilizando **DefaultRequestOptions**). Tudo o resto será efetuado pela biblioteca do cliente internamente.


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
    queue.AddMessage(message, null, null, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### <a name="table-service-encryption"></a>Encriptação do serviço de tabela

Para além de criar uma política de encriptação e defini-la das opções de pedido, tem de especificar uma **EncryptionResolver** no **TableRequestOptions**ou configurar o atributo [EncryptProperty] na entidade.

#### <a name="using-the-resolver"></a>Utilizar a resolução


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    {
        EncryptionResolver = (pk, rk, propName) =>
        {
            if (propName == "foo")
            {
                return true;
            }
            return false;
        },
        EncryptionPolicy = policy
    };

    // Insert Entity
    currentTable.Execute(TableOperation.Insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    {
        EncryptionPolicy = policy
    };

    TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
    TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### <a name="using-attributes"></a>Utilização de atributos

Tal como mencionado acima, se a entidade implementa TableEntity, as propriedades podem decoradas com o atributo [EncryptProperty] em vez de especificando o **EncryptionResolver**.

    [EncryptProperty]
    public string EncryptedProperty1 { get; set; }

## <a name="encryption-and-performance"></a>Encriptação e desempenho

Note que os resultados de dados de armazenamento no sobrecarga adicional no desempenho de encriptação. A chave de conteúdo e IV tem de ser gerado, o próprio conteúdo deve ser encriptado e dados meta adicionais tem de ser formatados e carregados. Este sobrecarga variam consoante a quantidade de dados a ser encriptados. Recomendamos que os clientes sempre testam suas aplicações para obter um desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximos passos

- [Tutorial: Encriptar e desencriptar blobs no armazenamento do Windows Azure utilizando Cofre de chave do Azure](storage-encrypt-decrypt-blobs-key-vault.md)
- Transferir a [Biblioteca de cliente do Azure armazenamento para o pacote de .NET NuGet](https://www.nuget.org/packages/WindowsAzure.Storage)
- Transferir os pacotes Azure chave cofre NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [cliente](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/)e [extensões de](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
- Visite a [documentação do Cofre de palavras chave do Azure](../key-vault/key-vault-whatis.md)


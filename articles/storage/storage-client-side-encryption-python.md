<properties
    pageTitle="Encriptação do lado do cliente com Python para armazenamento do Microsoft Azure | Microsoft Azure"
    description="A biblioteca de cliente de armazenamento do Azure para Python suporta a encriptação do lado do cliente para máxima segurança para as suas aplicações de armazenamento do Windows Azure."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Encriptação do lado do cliente com Python para armazenamento do Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral

A [Biblioteca de cliente do Azure armazenamento para Python](https://pypi.python.org/pypi/azure-storage) suporta a encriptação de dados em aplicações cliente do antes de a carregar para o armazenamento do Windows Azure e desencriptar dados durante a transferência para o cliente.

>[AZURE.NOTE] A biblioteca do Azure armazenamento Python está na pré-visualização.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através de técnica de envelope
Os processos de encriptação de siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através de técnica de envelope
Encriptação através de técnica de envelope funciona da seguinte forma:

1.  A biblioteca do cliente armazenamento Azure gera uma chave de encriptação conteúdo (CEK), que é uma chave simétrica um tempo utilização.

2.  Dados de utilizador são encriptados utilizando este CEK.

3.  O CEK, em seguida, é moldado (encriptados) utilizando a chave de encriptação de chave (KEK). O KEK está identificado por um identificador de chave e pode ser um par de chaves assimétrico ou uma chave simétrica, que é gerida localmente.
A biblioteca do cliente armazenamento propriamente dito nunca tem acesso ao KEK. A biblioteca invoca o algoritmo de moldagem chave que é fornecido pela KEK. Os utilizadores podem optar por utilizar fornecedores personalizados para moldagem/revelação chave se pretender.

4.  Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Windows Azure. A tecla moldada juntamente com alguns metadados de encriptação adicionais está armazenada como metadados (num blob) ou interpolada com os dados encriptados (mensagens em fila e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através de técnica de envelope
Desencriptação através de técnica de envelope funciona da seguinte forma:

1.  A biblioteca do cliente assume que o utilizador está a gerir a chave de encriptação chave (KEK) localmente. O utilizador não é necessário saber a chave específica que foi utilizado para encriptação. Em vez disso, um chave solucionador, que é resolvido identificadores da chave diferentes para teclas, pode configurar o e utilizado.

2.  A biblioteca do cliente transfere os dados encriptados juntamente com qualquer material de encriptação que está armazenado no serviço.

3.  A chave de encriptação conteúdo moldado (CEK) é moldado (desencriptado) utilizando a chave de encriptação de chave (KEK). Aqui novamente, a biblioteca de cliente não tem acesso ao KEK. -Simplesmente invoca algoritmo identificar o fornecedor personalizado.

4.  A chave de encriptação conteúdo (CEK), em seguida, é utilizada para desencriptar os dados de utilizador encriptada.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação  
A biblioteca do cliente armazenamento utiliza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar dados de utilizador. [Interligação de bloco de codificação (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) especificamente, modo com AES. Cada serviço funciona de forma um pouco diferente, para discutimos cada um deles aqui.

### <a name="blobs"></a>BLOBs
A biblioteca do cliente atualmente suporta a encriptação dos apenas blobs todos. Especificamente, a encriptação é suportada quando os utilizadores utilizam o **Criar*** métodos. Para transferências, ambos concluída e transferências do intervalo são suportadas e parallelization de ambos carregar e transferir está disponível.

Durante a encriptação, a biblioteca do cliente irá gerar um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação conteúdo aleatória (CEK) de bytes 32 e executar encriptação de envelope dos dados blob com esta informação. O CEK moldado e alguns metadados de encriptação adicionais, em seguida, são armazenados como blob metadados juntamente com o blob encriptado o serviço.

>[AZURE.WARNING] Se estiver a editar ou carregar a sua própria metadados para o blob, é necessário para se certificar de que este metadados são preservado. Se carregar novos metadados sem este metadados, o CEK moldado, IV e outros metadados serão perdidas e o conteúdo de BLOBs nunca serão recuperável novamente.

Transferir um blob encriptado envolve a obter o conteúdo do blob todo a **começar**a utilizar * métodos conveniência. O CEK moldado é continente e utilizada juntamente com o IV (armazenado como metadados blob neste caso) para devolver os dados desencriptados aos utilizadores.

Transferir um intervalo arbitrário (**obter*** métodos com parâmetros de intervalo transmitido) o blob encriptado envolve ajustar o intervalo fornecido pelos utilizadores para obter uma pequena quantidade de dados adicionais que podem ser utilizados para desencriptar com êxito o intervalo solicitado.

Bloquear blobs e blobs página apenas podem ser encriptar/desencriptar utilizando este esquema. Não existe neste momento não existe suporte para encriptar acrescentar blobs.

### <a name="queues"></a>Filas
Uma vez que as mensagens da fila podem ser de qualquer formato, a biblioteca do cliente define um formato personalizado que inclui a inicialização Vector (IV) e a chave de encriptação conteúdo encriptada (CEK) no texto da mensagem.

Durante a encriptação, a biblioteca do cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa encriptação de envelope a fila de texto da mensagem com esta informação. O CEK moldado e alguns metadados de encriptação adicionais, em seguida, são adicionadas à mensagem encriptada fila de espera. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Ao desencriptar, a tecla moldada é extraída a partir da mensagem de fila de espera e continente. O IV também é extraído a partir da mensagem de fila de espera e utilizado juntamente com a tecla moldada para desencriptar os dados da mensagem fila de espera. Note que os metadados de encriptação são pequenos (em 500 bytes), por isso enquanto contar a para o limite de 64KB de uma mensagem de fila de espera, o impacto deve ser fácil.

### <a name="tables"></a>Tabelas
A biblioteca do cliente suporta a encriptação dos propriedades entidade para inserir e substituir operações.

>[AZURE.NOTE] Intercalar não é suportado. Uma vez que um subconjunto de propriedades possa ter sido encriptado anteriormente a utilizar uma chave diferente, basta intercalar as novas propriedades e atualizar os metadados irão resultar na perda de dados. Intercalar um requer a efetuar chamadas de serviço extra para ler a entidade previamente existente a partir do serviço, ou utilizar uma nova chave por propriedade, os quais não são adequados por motivos de desempenho.

Encriptação de dados de tabela funciona da seguinte forma:

1.  Os utilizadores especificar as propriedades para ser encriptados.

2.  A biblioteca do cliente gera um Vetor de inicialização aleatório (IV) de 16 bytes juntamente com uma chave de encriptação conteúdo aleatória (CEK) de 32 bytes para cada entidade e executa encriptação do envelope nas propriedades da individuais para ser encriptado por decorrentes um novo IV por propriedade. A propriedade encriptada está armazenada como dados binários.

3.  O CEK moldado e alguns metadados de encriptação adicionais, em seguida, são armazenados como duas propriedades reservadas adicionais. O primeiro reservada propriedade (\_ClientEncryptionMetadata1) é uma propriedade de cadeia que contém informações sobre IV, versão e chave moldado. O segundo reservada propriedade (\_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações acerca das propriedades que estão encriptados. As informações nesta propriedade segunda (\_ClientEncryptionMetadata2) própria está encriptada.

4.  Devido a estas propriedades reservadas adicionais necessárias para encriptação, os utilizadores, poderão agora ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade tem de ser menor que 1MB.

    Tenha em atenção que apenas as propriedades da cadeia pode ser encriptada. Se outros tipos de propriedades para ser encriptados, tem de ser convertidos em cadeias. As cadeias encriptadas são armazenadas no serviço como propriedades binárias e são convertidas voltar a cadeias (não processadas cadeias, não EntityProperties com o tipo de EdmType.STRING) após desencriptação.

    Para tabelas, para além da política de encriptação, os utilizadores tem de especificar as propriedades para ser encriptados. Isto pode ser efetuado por um destas propriedades armazenar TableEntity objetos com o conjunto de tipo para EdmType.STRING e encriptar definida como VERDADEIRO ou configurar a encryption_resolver_function no objeto tableservice. Uma resolução de encriptação é uma função que assume uma partição chave, tecla de linha e o nome da propriedade e devolve um valor booleano que indica se deve ser encriptada dessa propriedade. Durante a encriptação, a biblioteca do cliente irá utilizar estas informações para decidir se deve ser encriptada uma propriedade ao escrever o fio. O delegado também fornece a possibilidade de lógica à volta da forma como as propriedades são encriptadas. (Por exemplo, se X, em seguida encriptar propriedade A; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não é necessário fornecer esta informação enquanto lê ou consultar entidades.

### <a name="batch-operations"></a>Operações batch
Uma política de encriptação aplica-se para todas as linhas no lote de. A biblioteca do cliente internamente irá gerar um novo IV aleatório e CEK aleatório por linha no lote de. Os utilizadores também podem optar por encriptar propriedades diferentes para cada operação no lote de definindo este comportamento na resolução de encriptação.
Se um lote é criado como Gestor de contexto através do método de batch() tableservice, política de encriptação o tableservice será automaticamente será aplicada o lote. Se é criada uma secção explicitamente ao contactar o suporte do construtor, a política de encriptação tem de ser passaram como um parâmetro e inalterada para a esquerda para o tempo de vida do lote de.
Tenha em atenção que entidades são encriptadas como são inseridos no lote utilizando a política de encriptação o lote (entidades não são encriptadas no momento da consolidar o lote utilizando a política de encriptação o tableservice).

### <a name="queries"></a>Consultas
Para executar operações de consulta, tem de especificar um chave solucionador que consegue resolver todas as teclas o conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um fornecedor, a biblioteca do cliente irá gerar um erro. Para qualquer consulta que efetua projecções de lado do servidor, a biblioteca do cliente irá adicionar as propriedades de metadados de encriptação especiais (\_ClientEncryptionMetadata1 e \_ClientEncryptionMetadata2) por predefinição para as colunas selecionadas.

>[AZURE.IMPORTANT] Tenha em atenção estes pontos importantes ao utilizar a encriptação do lado do cliente:
>
>- Quando a partir de leitura ou ao escrever um blob encriptado, utilize comandos de carregamento de blob toda e comandos de transferência de Blobs do intervalo/inteiro. Evitar escrita para um blob encriptado utilizando operações de protocolo, tais como colocar bloco, colocar a lista de bloqueios, escrever páginas ou páginas limpar; caso contrário, poderá danificada o blob encriptado e torná-lo legível.
>
>- Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.
>
>- Se definir metadados sobre o blob encriptado, pode substituir os metadados relacionadas com a encriptação necessários para o desencriptação, uma vez que a definição de metadados não adicionada. Também é verdadeiro para instantâneos; Evite especificando metadados durante a criação de um instantâneo de um blob encriptado. Se tem de estar definido metadados, certifique-se de que chamar o método de **get_blob_metadata** pela primeira vez, para obter os metadados de encriptação atual e evitar escritas em simultâneo enquanto está a ser definido metadados.
>
>- Active o sinalizador de **require_encryption** no objeto do serviço para utilizadores que deve só funciona com dados encriptados. Consulte abaixo para obter mais informações.

A biblioteca do cliente armazenamento espera a KEK fornecido e solucionador chave para implementar a interface seguinte. Suporte de [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) para a gestão de Python KEK está pendente e será integrado nesta biblioteca quando concluído.


## <a name="client-api--interface"></a>API de cliente / Interface
Após a criação de um objeto de serviço de armazenamento (ou seja, blockblobservice), o utilizador pode atribuir valores para os campos que constituem uma política de encriptação: key_encryption_key, key_resolver_function e require_encryption. Os utilizadores podem fornecer apenas KEK, apenas um solucionador, ou ambas. key_encryption_key é o tipo de chave básico que é identificada utilizando um identificador de chave e que fornece a lógica de moldagem/revelação. key_resolver_function é utilizada para resolver uma chave durante o processo de desencriptação. Devolve um KEK válido atribuído um identificador de chave. Fornece aos utilizadores a capacidade para escolher entre múltiplas teclas que são geridas em várias localizações.

O KEK tem de implementar os seguintes métodos para encriptar com êxito dados:
- wrap_key(cek): é moldado CEK especificada (bytes) utilizando um algoritmo de escolha do utilizador. Devolve a tecla moldada.
- get_key_wrap_algorithm(): devolve o algoritmo utilizado para moldar teclas.
- get_kid(): devolve a identificação de chave de cadeia para este KEK.
O KEK tem de implementar os seguintes métodos para com êxito desencriptar dados:
- unwrap_key (cek, algoritmo): devolve o formulário moldado do CEK especificado utilizando o algoritmo de cadeia especificada.
- get_kid(): devolve um id de chave de cadeia para este KEK.

A resolução de chave tem, pelo menos, implementar um método que, atribuído um id da chave, devolve o KEK correspondente implemente o interface acima. Apenas este método é a serem atribuídas à propriedade do key_resolver_function o objeto de serviço.

- Para encriptação, a tecla é utilizada sempre e ausência de uma chave de irá resultar num erro.
- Para desencriptação:
    - A resolução de chave é chamada se especificada para obter a chave. Se a resolução for especificada, mas não tem um mapeamento para o identificador chave, é devolvido um erro.
    - Se não for especificado solucionador mas uma chave for especificada, a tecla é utilizada se respectivo identificador corresponder o identificador de chave necessário. Se o identificador não corresponder, é devolvido um erro.

      Os exemplos de encriptação no azure.storage.samples <fix URL>demonstrar um cenário de ponto a ponto mais detalhado para blobs, filas e tabelas.
        Exemplos de implementações do KEK e resolução de chave são fornecidos nos ficheiros de exemplo como KeyWrapper e KeyResolver respetivamente.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Os utilizadores, opcionalmente, podem ativar a um modo de funcionamento onde devem estar encriptadas todos os carregamentos e transferências. Neste modo, irão falhar tentativas de dados sem uma política de encriptação de carregar ou transferir dados que não estão encriptados no serviço no cliente. O sinalizador **require_encryption** no objeto serviço controla este comportamento.

### <a name="blob-service-encryption"></a>Encriptação de Blobs do serviço
Defina a encriptação política campos no objeto blockblobservice. Tudo o resto será efetuado pela biblioteca do cliente internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Encriptação do serviço de fila de espera
Defina a encriptação política campos no objeto queueservice. Tudo o resto será efetuado pela biblioteca do cliente internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Encriptação do serviço de tabela
Para além de criar uma política de encriptação e defini-la das opções de pedido, tem de especificar uma **encryption_resolver_function** na **tableservice**ou definir o atributo encriptar na EntityProperty.

### <a name="using-the-resolver"></a>Utilizar a resolução

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>Utilização de atributos
Tal como mencionado acima, uma propriedade pode ser marcada para encriptação armazená-lo de um objeto de EntityProperty e definindo o campo encriptar.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Encriptação e desempenho
Note que os resultados de dados de armazenamento no sobrecarga adicional no desempenho de encriptação. A chave de conteúdo e IV tem de ser gerado, o próprio conteúdo deve ser encriptado e metadados adicionais tem de ser formatado e carregados. Este sobrecarga variam consoante a quantidade de dados a ser encriptados. Recomendamos que os clientes sempre testam suas aplicações para obter um desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximos passos

- Transferir a [Biblioteca de cliente do Azure armazenamento para o pacote Java PyPi](https://pypi.python.org/pypi/azure-storage)
- Transferir o [código de GitHub da origem de armazenamento Azure biblioteca do cliente para Python](https://github.com/Azure/azure-storage-python)

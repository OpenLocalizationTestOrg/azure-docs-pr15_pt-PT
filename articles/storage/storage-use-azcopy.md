<properties
    pageTitle="Copiar ou mover dados ao armazenamento com AzCopy | Microsoft Azure"
    description="Utilize o utilitário AzCopy para mover ou copiar dados de ou blob, tabela e o conteúdo do ficheiro. Copiar dados para o armazenamento do Windows Azure a partir de ficheiros locais ou copiar dados dentro ou entre contas de armazenamento. Migrar facilmente os seus dados para o armazenamento do Windows Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Transferir dados com o utilitário AzCopy da linha de comandos

## <a name="overview"></a>Descrição geral

AzCopy é um utilitário da linha de comandos do Windows concebido para copiar dados para e a partir do armazenamento de Blobs do Microsoft Azure, ficheiro e tabela utilizando comandos simples com um desempenho ideal. Pode copiar dados de um objecto para outro dentro da sua conta de armazenamento ou entre contas de armazenamento.

> [AZURE.NOTE] Este guia assume que já está familiarizado com o [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/). Caso contrário, a documentação de [Introdução ao Azure armazenamento](storage-introduction.md) de leitura será útil. Mais nenhumas, terá de criar [uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para poder começar a utilizar o AzCopy.

## <a name="download-and-install-azcopy"></a>Transferir e instalar o AzCopy

### <a name="windows"></a>Windows

Transferir a [versão mais recente do AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac/Linux

AzCopy não está disponível para Mac/Linux OSs. No entanto, o clip Azure é uma alternativa adequada para copiar os dados de armazenamento do Windows Azure e para. Leia a [utilizar o clip do Azure com armazenamento do Windows Azure](storage-azure-cli.md) para obter mais informações.

## <a name="writing-your-first-azcopy-command"></a>Escrever o seu primeiro comando AzCopy

A sintaxe básica para comandos AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

Abra uma janela de comando e navegue para o diretório de instalação AzCopy no seu computador - onde o `AzCopy.exe` executável está localizado. Se pretender, pode adicionar a localização da instalação AzCopy para o caminho do sistema. Por predefinição, é instalado AzCopy `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Os exemplos seguintes demonstram uma variedade de cenários para copiar dados para e do Microsoft Azure Blobs, ficheiros e tabelas. Consulte a secção [AzCopy parâmetros](#azcopy-parameters) para obter uma explicação detalhada dos parâmetros utilizados em cada amostra.

## <a name="blob-download"></a>Blob: Transferir

### <a name="download-single-blob"></a>Transferir blob único

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Tenha em atenção que, se a pasta `C:\myfolder` não existir, AzCopy irá criá-lo e transferir `abc.txt ` para a nova pasta.

### <a name="download-single-blob-from-secondary-region"></a>Transfira blob único da região secundário

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Tenha em atenção que tem de ter acesso de leitura de armazenamento geo redundantes ativado.

### <a name="download-all-blobs"></a>Transferir todos os blobs

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Vamos assuma que as blobs seguintes residem no contentor especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de transferência, diretório `C:\myfolder` irá incluir os seguintes ficheiros:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se não especificar opção `/S`, sem blobs serão transferidos.

### <a name="download-blobs-with-specified-prefix"></a>Transferir blobs com prefixo especificado

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Partem do pressuposto que de blobs seguintes residem no contentor especificado. Todos os blobs comecem com o prefixo `a` serão transferidos:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de transferência, a pasta `C:\myfolder` irá incluir os seguintes ficheiros:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

O prefixo aplica-se ao directório virtual, a primeira parte do nome do blob de formulários. No exemplo mostrado acima, diretório virtual não corresponder o prefixo especificado, para que não seja transferido. Além disso, se a opção `\S` não for especificado, AzCopy não irá transferir qualquer blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Definir a hora da última modificação de ficheiros exportados para ser o mesmo que o blobs de origem

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

Também pode excluir blobs da operação de transferência com base na sua hora da última modificação. Por exemplo, se pretende excluir blobs cuja última modificação tempo é o mesmo ou mais recente do que o ficheiro de destino, adicione o `/XN` opção:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Ou se pretende excluir blobs cuja última modificação tempo é o mesmo ou mais antigas do que o ficheiro de destino, adicione o `/XO` opção:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>Blob: carregar

### <a name="upload-single-file"></a>Carregar ficheiro único

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Se o contentor de destino especificado não existir, o AzCopy criá-lo e carregue o ficheiro para o mesmo.

### <a name="upload-single-file-to-virtual-directory"></a>Carregar ficheiro único ao directório virtual

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Se o directório virtual especificado não existir, AzCopy irá carregar o ficheiro para incluir o directório virtual em seu nome (*por exemplo*, `vd/abc.txt` no exemplo acima).

### <a name="upload-all-files"></a>Carregar todos os ficheiros

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Especificar opção `/S` carrega o conteúdo do directório especificado de forma recursiva armazenamento de BLOBs, que significa que todas as suas subpastas e os respetivos ficheiros serão carregados também. Por exemplo, partem do pressuposto de residem os seguintes ficheiros na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contentor irá incluir os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se não especificar opção `/S`, AzCopy não serão carregados forma recursiva. Após a operação de carregamento, o contentor irá incluir os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Carregar ficheiros correspondência de padrão especificado

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Partem do pressuposto de residem os seguintes ficheiros na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contentor irá incluir os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se não especificar opção `/S`, AzCopy só serão carregados blobs que não se encontram num diretório virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especificar o tipo de conteúdo MIME de um blob de destino

Por predefinição, AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. Começando com a versão 3.1.0, pode especificar explicitamente o tipo de conteúdo através da opção `/SetContentType:[content-type]`. Esta sintaxe define o tipo de conteúdo para todos os blobs numa operação de carregamento.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Se especificar `/SetContentType` sem um valor, em seguida, AzCopy irá definir cada blob ou tipo de conteúdo do ficheiro de acordo com a respectiva extensão de ficheiro.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>Blob: copiar

### <a name="copy-single-blob-within-storage-account"></a>Copiar blob única conta de armazenamento

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Ao copiar um blob dentro de uma conta de armazenamento, é efetuada uma operação de [Copiar do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-single-blob-across-storage-accounts"></a>Copiar blob única em contas de armazenamento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Ao copiar um blob em contas de armazenamento, é efetuada uma operação de [Copiar do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copie blob único da região secundário à região principal

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Tenha em atenção que tem de ter acesso de leitura de armazenamento geo redundantes ativado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiar blob único e o respetivos instantâneos em contas de armazenamento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Após a operação de copiar o contentor de destino irá incluir o blob e respetivos instantâneos. Assumindo que o blob no exemplo acima tem dois instantâneos, o contentor irá incluir o blob e instantâneos seguintes:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Modo síncrono copiar blobs em contas de armazenamento

AzCopy por predefinição modo assíncrono copia os dados entre dois pontos finais de armazenamento. Por conseguinte, a operação de cópia será executada em segundo plano com capacidade de largura de banda reserva que tem a mesma rapidez com sem SLA em termos como um blob onde será copiado e AzCopy irá verificar periodicamente o estado de cópia até que a cópia seja concluída ou falha.

O `/SyncCopy` opção garante que a operação de cópia receberão velocidade consistente. AzCopy executa a cópia síncrona ao transferir blobs para copiar a partir da origem especificada para memória local e, em seguida, carregá-los para o destino de armazenamento de Blobs.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`poderá gerar o custo de saída adicionais em comparação com cópia assíncrona, é a abordagem recomendada utilizar esta opção numa VM Azure que está na mesma região como a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="file-download"></a>Ficheiro: Transferir

### <a name="download-single-file"></a>Transferir o ficheiro único

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se a origem especificada for uma partilha de ficheiros Azure, em seguida, quer tem de especificar o nome do ficheiro exata, (*por exemplo,* `abc.txt`) para transferir um único ficheiro ou especificar a opção `/S` para transferir todos os ficheiros no modo de recursivo partilhar. Tentar especificar um padrão de ficheiro e a opção `/S` em conjunto, irá resultar num erro.

### <a name="download-all-files"></a>Transferir todos os ficheiros

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Tenha em atenção que quaisquer esvaziar pastas não serão transferidas.

## <a name="file-upload"></a>Ficheiro: carregar

### <a name="upload-single-file"></a>Carregar ficheiro único

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Carregar todos os ficheiros

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Tenha em atenção que não serão carregadas quaisquer pastas vazias.

### <a name="upload-files-matching-specified-pattern"></a>Carregar ficheiros correspondência de padrão especificado

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>Ficheiro: copiar

### <a name="copy-across-file-shares"></a>Copiar em partilhas de ficheiros

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Copie da partilha de ficheiros para blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Tenha em atenção que copiar assíncrona de armazenamento de ficheiros para página Blob não é suportada.

### <a name="copy-from-blob-to-file-share"></a>Copiar de BLOBs para partilha de ficheiros

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Modo síncrono copiar ficheiros

Pode especificar o `/SyncCopy` opção para copiar dados a partir do armazenamento de ficheiros ao armazenamento de ficheiros, a partir do armazenamento de ficheiros ao armazenamento Blob e a partir do armazenamento de Blobs do armazenamento de ficheiros para o modo síncrono, AzCopy faz isto ao transferi a origem de dados para o local memória e carregue-o novamente para o destino.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Ao copiar a partir do armazenamento de ficheiros ao armazenamento Blob, o tipo de BLOBs predefinido é BLOBs do bloco, utilizadores podem especificar opção `/BlobType:page` para alterar o tipo de Blobs do destino.

Tenha em atenção que `/SyncCopy` podem gerar saída adicional de custos comparar assíncrona cópia, é a abordagem recomendada utilizar esta opção na VM Azure que está na mesma região como a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="table-export"></a>Tabela: Exportar

### <a name="export-table"></a>Exportar tabela

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy escreve um ficheiro de manifesto para a pasta de destino especificado. O ficheiro de manifesto é utilizado no processo de importação para localizar os ficheiros de dados necessários e executar a validação de dados. Por predefinição, o ficheiro de manifesto utiliza a seguinte convenção de nomenclatura:

    <account name>_<table name>_<timestamp>.manifest

Utilizador também pode especificar a opção `/Manifest:<manifest file name>` para definir o nome de ficheiro de manifesto.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Dividir a exportação para vários ficheiros

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utiliza um *índice de volume* em nomes de ficheiro de dados dividida para distinguir vários ficheiros. O índice de volume consiste em duas partes, um *índice de intervalo chave partição* e *dividir o índice de ficheiro*. Ambos os índices são baseado em zero.

O índice de intervalo chave partição será 0 se o utilizador não especificar opção `/PKRS`.

Por exemplo, suponhamos AzCopy gera dois ficheiros de dados, depois do utilizador Especifica opção `/SplitSize`. Os nomes de ficheiro de dados resultante poderão ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenha em atenção que o valor mínimo possíveis para a opção `/SplitSize` é de 32MB. Se o destino especificado for armazenamento de BLOBs, AzCopy vai dividir o ficheiro de dados quando os tamanhos atinge a limitação de tamanho de BLOBs (200GB), independentemente de se a opção `/SplitSize` especificada pelo utilizador.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Exportar tabela para o formato de ficheiro de dados JSON ou CSV

AzCopy por predefinição exporta tabelas aos ficheiros de dados JSON. Pode especificar a opção `/PayloadFormat:JSON|CSV` para exportar as tabelas como JSON ou CSV.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Quando especificar o formato de carga útil CSV, AzCopy também irá gerar um ficheiro de esquema com extensão de ficheiro `.schema.csv` para cada ficheiro de dados.

### <a name="export-table-entities-concurrently"></a>Exporte entidades de tabela em simultâneo

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy será iniciado operações em simultâneo para exportar entidades quando o utilizador Especifica opção `/PKRS`. Cada operação exporta um intervalo de chave partição.

Tenha em atenção que o número de operações em simultâneo também é controlado pelo opção `/NC`. AzCopy utiliza o número de processadores core como o valor predefinido de `/NC` ao copiar entidades de tabela, mesmo se `/NC` não foi especificado. Quando o utilizador Especifica a opção `/PKRS`, AzCopy utiliza o valor mais pequeno dos dois valores - partição chave intervalos versus implicitamente ou explicitamente especificados operações em simultâneo - para determinar o número de operações em simultâneo para começar. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

### <a name="export-table-to-blob"></a>Exportar tabela para blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy irá gerar um ficheiro de dados JSON para o contentor blob com convenção de nomenclatura a seguir:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O ficheiro de dados JSON gerado segue o formato de carga útil para metadados mínimos. Para obter detalhes sobre este formato de carga útil, consulte o artigo [Formatar de carga útil para operações de serviço de tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Note que ao exportar tabelas para blobs, AzCopy irá transferir as entidades de tabela para ficheiros de dados temporário locais e, em seguida, carregar essas entidades para o blob. Estes ficheiros de dados temporários são colocados na pasta diário ficheiro com o caminho predefinido "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", pode especificar opção/z: [pasta de ficheiros de diário] para alterar o diário localização de pasta de ficheiros e, por conseguinte, alterar a localização de ficheiros de dados temporários. Tamanho dos ficheiros de dados temporários for decidido ao tamanho dos entidades de tabela e o tamanho que especificou com a opção /SplitSize, apesar do ficheiro de dados temporário no disco local será eliminado instantaneamente assim que tiver sido carregá-lo para o blob, certifique-se de que tem espaço suficiente no disco local para armazenar estes ficheiros temporários dados antes de serem eliminadas.

## <a name="table-import"></a>Tabela: importar

### <a name="import-table"></a>Tabela de importação

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

A opção `/EntityOperation` indica como inserir entidades na tabela. Valores possíveis são:

- `InsertOrSkip`: Ignora uma entidade existente ou insere uma nova entidade se não existir na tabela.
- `InsertOrMerge`: Intercala uma entidade existente ou insere uma nova entidade se não existir na tabela.
- `InsertOrReplace`: Substitui uma entidade existente ou insere uma nova entidade se não existir na tabela.

Nota que não é possível especificar opção `/PKRS` num cenário de importação. Ao contrário do cenário de exportação, na qual tem de especificar opção `/PKRS` para começar a operações em simultâneo, AzCopy por predefinição iniciará operações em simultâneo ao importar uma tabela. O número predefinido de operações em simultâneo ao for igual ao número de processadores core; No entanto, pode especificar um número diferente de em simultâneo com a opção `/NC`. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

Tenha em atenção que AzCopy apenas suporta a importação para JSON, não CSV. AzCopy não suporta a importação de tabela a partir do utilizador criado JSON e manifesto ficheiros. Ambos os ficheiros tem de vir de uma exportação de tabela AzCopy. Para evitar erros, consulte não modificar o JSON exportado ou ficheiro de manifesto.

### <a name="import-entities-to-table-using-blobs"></a>Importar entidades para a tabela utilizando blobs

Partem do princípio um contentor Blob contém o seguinte: ficheiro A JSON que representa uma tabela do Azure e o seu ficheiro de manifesto acompanhamento.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Pode executar o seguinte comando para importar entidades para uma tabela utilizando o ficheiro de manifesto no contentor blob:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Outras funcionalidades de AzCopy

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiar apenas os dados que não existem no destino

O `/XO` e `/XN` parâmetros permitem-lhe excluir os recursos de origem do mais antigo ou mais recente da ser copiada, respetivamente. Se apenas pretender copiar os recursos de origem que não existem no destino, pode especificar ambos os parâmetros no comando AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Nota: Este não é suportado quando a origem ou o destino é uma tabela.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Utilizar um ficheiro de resposta para especificar parâmetros da linha de comandos

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

Pode incluir qualquer parâmetros de linha de comandos AzCopy num ficheiro de resposta. AzCopy processa os parâmetros no ficheiro como se tivesse sido especificados na linha de comandos, efetuar uma substituição direta com o conteúdo do ficheiro.

Partem do pressuposto de um ficheiro de resposta denominado `copyoperation.txt`, que contém as linhas seguintes. Cada parâmetro AzCopy pode ser especificado numa única linha

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou linhas separadas por:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy irá falhar se dividir o parâmetro em duas linhas, conforme mostrado aqui para o `/sourcekey` parâmetro:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utilizar vários ficheiros de resposta para especificar parâmetros da linha de comandos

Partem do pressuposto de um ficheiro de resposta denominado `source.txt` que especifica um contentor de origem:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

E um ficheiro de resposta denominado `dest.txt` que especifica uma pasta de destino no sistema de ficheiros:

    /Dest:C:\myfolder

E um ficheiro de resposta denominado `options.txt` que especifica as opções para AzCopy:

    /S /Y

Para ligar a AzCopy com estes ficheiros de resposta, as quais residem num diretório `C:\responsefiles`, utilize este comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy processa este comando apenas ao que teria se tiver incluído todos os parâmetros individuais na linha de comandos:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Especificar uma assinatura de acesso partilhado (SA)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

Também pode especificar uma SA no contentor URI:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Pasta de ficheiros do diário

Sempre que emitir um comando para AzCopy, verifica se existe um ficheiro de diário na pasta predefinida ou, se existir numa pasta que especificou através desta opção. Se o ficheiro de diário não existir em qualquer local, AzCopy trata a operação como nova e gera um novo ficheiro de diário.

Se o ficheiro de diário existir, AzCopy irá verificar se a linha de comandos de entrada corresponde da linha de comandos no ficheiro de diário. Se as duas linhas de comando corresponder, AzCopy continua a operação incompleta. Se não corresponder, vai ser-lhe para quer substituir o ficheiro de jornal para iniciar uma nova operação, ou para cancelar a operação actual.

Se pretender utilizar a localização predefinida para o ficheiro de diário:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Se omitir opção `/Z`, ou especificar opção `/Z` sem o caminho da pasta, conforme mostrado acima, AzCopy cria o ficheiro de diário na localização predefinida, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o ficheiro de diário já existir, AzCopy continua a operação baseada no ficheiro de diário.

Se pretender especificar uma localização para o ficheiro de diário personalizada:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Este exemplo cria o ficheiro de diário, se ainda não existir. Se existir, AzCopy continua a operação baseada no ficheiro de diário.

Se pretender retomar uma operação de AzCopy:

    AzCopy /Z:C:\journalfolder\

Este exemplo continua a última operação, poderá ter falhado para concluir.

### <a name="generate-a-log-file"></a>Gerar um ficheiro de registo

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se especificar opção `/V` sem facultar o caminho do ficheiro para o registo verboso, em seguida, AzCopy cria o ficheiro de registo na localização predefinida, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Caso contrário, pode criar um ficheiro de registo numa localização personalizada:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Tenha em atenção que, se especificar um caminho relativo seguir opção `/V`, tais como `/V:test/azcopy1.log`, em seguida, o registo verboso é criado no directório de trabalho atual dentro de uma subpasta denominada `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificar o número de operações em simultâneo para iniciar

Opção `/NC` Especifica o número de operações de cópia em simultâneo. Por predefinição, o AzCopy inicia um determinado número de operações em simultâneo para aumentar o débito de transferência de dados. Para operações de tabela, é igual ao número de processadores que tem o número de operações em simultâneo. Para Blob e operações de ficheiros, o número de operações em simultâneo é igual a 8 vezes o número de processadores que tem. Se estiver a executar AzCopy através de uma rede de largura de banda reduzida, pode especificar um número menor para /NC evitar falha causada pela concorrência do recurso.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Executar AzCopy contra emulador armazenamento Azure

Pode executar AzCopy contra o [Azure emulador de armazenamento](storage-use-emulator.md) de Blobs:

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

Tabelas e:

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>Parâmetros de AzCopy

Parâmetros para AzCopy são descritos abaixo. Também pode escrever um dos seguintes comandos na linha de comandos para obter ajuda na utilização de AzCopy:

- Para obter ajuda detalhada da linha de comandos para AzCopy:`AzCopy /?`
- Para obter ajuda detalhada com qualquer parâmetro AzCopy:`AzCopy /?:SourceKey`
- Para obter exemplos da linha de comandos:`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Da origem de: "origem"

Especifica a origem de dados a partir do qual pretende copiar. Pode ser a origem de um diretório de sistema do ficheiro, um contentor blob, um diretório virtual blob, uma partilha de ficheiros de armazenamento, um diretório de ficheiro do armazenamento ou uma tabela do Azure.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="destdestination"></a>/ Destino: "destino"

Especifica o destino para copiar para. O destino pode ser um diretório de sistema do ficheiro, um contentor blob, um diretório virtual blob, uma partilha de ficheiros de armazenamento, um diretório de ficheiro do armazenamento ou uma tabela do Azure.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="patternfile-pattern"></a>/ Padrão de: "padrão de ficheiro"

Especifica um padrão de ficheiro indica quais os ficheiros para copiar. O comportamento do parâmetro /Pattern é determinado pela localização de origem de dados e a presença da opção de modo recursiva. Modo recursivo for especificado através da opção /S.

Se a origem especificada for um diretório no sistema de ficheiros, em seguida, caracteres universais padrão estão em efeito e, no padrão de ficheiro fornecido é correspondido contra ficheiros no diretório. Se a opção que /s for especificado, em seguida, AzCopy corresponde também o padrão especificado relativamente a todos os ficheiros em qualquer subpastas sob o diretório.

Se a origem especificada for um contentor de BLOBs ou directório virtual, caracteres universais não são aplicadas. Se a opção que /s for especificado, em seguida, AzCopy interpreta o padrão de ficheiro especificado como um prefixo blob. Se a opção que /s não for especificado, em seguida, AzCopy corresponde o padrão de ficheiro contra nomes blob exata.

Se a origem especificada for uma partilha de ficheiros Azure, em seguida, que tem de especificar o nome do ficheiro exata, (por exemplo, abc.txt) para copiar um único ficheiro, ou especificar a opção /S para copiar todos os ficheiros de forma a partilhar recursiva. /S tentar especificar um padrão de ficheiro e a opção irá resultar em conjunto num erro.

AzCopy utiliza a correspondência entre maiúsculas e minúsculas quando o /Source é um contentor de BLOBs ou directório virtual blob e utiliza a correspondência de maiúsculas e minúsculas em todos os outros casos.

O padrão de ficheiro predefinido utilizado quando não é especificado nenhum padrão do ficheiro é *.* para uma localização de sistema de ficheiros ou um prefixo vazio para uma localização de armazenamento do Windows Azure. Especificar várias padrões de ficheiro não é suportada.

**Aplicáveis:** BLOBs, ficheiros

### <a name="destkeystorage-key"></a>/ DestKey: "chave de armazenamento"

Especifica a chave de conta de armazenamento para o recurso de destino.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="destsassas-token"></a>/ DestSAS: "token de SA"

Especifica uma assinatura de acesso de partilhados (SA) com permissões de leitura e escrita para o destino (se aplicável). Escritos entre as associações de segurança com aspas, tal como poderá contém carateres da linha de comandos especiais.

Se o recurso de destino estiver num contentor de BLOBs, partilha de ficheiros ou tabela, pode especificar esta opção, seguida do token de SA ou pode especificar as associações de segurança como parte do contentor de Blobs do destino, partilha de ficheiros ou URI da tabela, sem esta opção.

Se a origem e destino são ambos os blobs, o blob de destino tem de estar dentro da mesma conta de armazenamento como o blob de origem.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="sourcekeystorage-key"></a>/ SourceKey: "chave de armazenamento"

Especifica a chave de conta de armazenamento para o recurso de origem.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="sourcesassas-token"></a>/ SourceSAS: "token de SA"

Especifica uma assinatura de acesso partilhado com permissões de leitura e de lista para a origem (se aplicável). Escritos entre as associações de segurança com aspas, tal como poderá contém carateres da linha de comandos especiais.

Se o recurso de origem for um contentor blob e for fornecida nem uma chave de nem uma SA, o contentor blob ser lidos através do acesso anónimo.

Se a origem for uma tabela ou partilha de ficheiros, uma tecla ou uma SA devem ser fornecida.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="s"></a>/S

Especifica o modo de recursiva para operações de cópia. No modo de recursiva, AzCopy irá copiar todos os blobs ou ficheiros que correspondem ao ficheiro especificado padrão, incluindo as subpastas.

**Aplicáveis:** BLOBs, ficheiros

### <a name="blobtypeblock--page--append"></a>/ BlobType: "Bloco" | "página" | "acrescentar"

Especifica se o blob de destino é um blob bloco, um blob de página ou um blob acrescentar. Esta opção é aplicável apenas quando o carregamento de um blob. Caso contrário, é gerado um erro. Se o destino é um blob e esta opção não for especificada, por predefinição, o AzCopy cria um blob bloco.

**Aplicáveis:** BLOBs

### <a name="checkmd5"></a>/ CheckMD5

Calcula um hash MD5 para dados transferidos e verifica que MD5 hash armazenados no blob ou corresponde à propriedade de MD5 conteúdo do ficheiro da hash calculado. A verificação de MD5 está desativada por predefinição, para que tem de especificar esta opção para executar a verificação de MD5 quando transferir dados.

Note que o armazenamento do Windows Azure não garante que hash MD5 armazenado para o ficheiro ou blob está atualizado. Cabe do cliente para actualizar MD5 sempre que o ficheiro ou blob modificado.

AzCopy sempre define a propriedade de conteúdo-MD5 para um ficheiro ou BLOBs do Azure após carregá-lo para o serviço.  

**Aplicáveis:** BLOBs, ficheiros

### <a name="snapshot"></a>/ Instantâneo

Indica se deve transferir instantâneos. Esta opção só é válida quando a origem for um blob.

Os instantâneos blob transferidos são mudados neste formato: .extension blob de nome (instantâneo-time)

Por predefinição, não são copiados instantâneos.

**Aplicáveis:** BLOBs

### <a name="vverbose-log-file"></a>/ V: [--ficheiro de registo verboso]

Saídas verboso as mensagens de estado para um ficheiro de registo.

Por predefinição, o ficheiro de registo verboso é denominado AzCopyVerbose.log no `%LocalAppData%\Microsoft\Azure\AzCopy`. Se especificar uma localização de ficheiro existente para esta opção, o registo verboso será anexado a esse ficheiro.  

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="zjournal-file-folder"></a>/ Z: [pasta de ficheiros de diário]

Especifica uma pasta de ficheiros de diário para a retomar uma operação.

AzCopy suporta sempre retomar se foi interrompida uma operação.

Se esta opção não for especificada ou especificado sem um caminho da pasta, em seguida, AzCopy irá criar o ficheiro de diário na localização predefinida, que é % LocalAppData%\Microsoft\Azure\AzCopy.

Sempre que emitir um comando para AzCopy, verifica se existe um ficheiro de diário na pasta predefinida ou, se existir numa pasta que especificou através desta opção. Se o ficheiro de diário não existir em qualquer local, AzCopy trata a operação como nova e gera um novo ficheiro de diário.

Se o ficheiro de diário existir, AzCopy irá verificar se a linha de comandos de entrada corresponde da linha de comandos no ficheiro de diário. Se as duas linhas de comando corresponder, AzCopy continua a operação incompleta. Se não corresponder, vai ser-lhe para quer substituir o ficheiro de jornal para iniciar uma nova operação, ou para cancelar a operação actual.

O ficheiro de diário é eliminado após a conclusão com êxito da operação de.

Tenha em atenção que retomar uma operação a partir de um ficheiro de diário criado por uma versão anterior do AzCopy não é suportada.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="parameter-file"></a>/@:"parameter-file"

Especifica um ficheiro que contém os parâmetros. AzCopy processa os parâmetros no ficheiro, tal como se tivesse sido especificados na linha de comandos.

Num ficheiro de resposta, pode especificar várias parâmetros numa única linha ou especificar cada parâmetro na sua própria linha. Tenha em atenção que um parâmetro individual não é possível abranger várias linhas.

Ficheiros de resposta podem incluir linhas de comentários que começam com o símbolo #.

Pode especificar vários ficheiros de resposta. No entanto, tenha em atenção que não suporta o AzCopy aninhadas ficheiros de resposta.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="y"></a>/Y

Suprime todas as mensagens de confirmação de AzCopy.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="l"></a>/L

Especifica uma operação de listagem apenas; sem dados são copiados.

AzCopy irão interpretar o utilizando desta opção como uma simulação para executar a linha de comandos sem esta opção /L e contar o número de objetos quantos onde serão copiados, pode especificar opção onde serão copiados /V ao mesmo tempo para verificar quais os objectos no registo verboso.

O comportamento desta opção também é determinado pela localização de origem de dados e a presença da recursiva opção /S e ficheiro padrão opção modo /Pattern.

AzCopy necessita de permissão de leitura e lista desta localização de origem ao utilizar esta opção.

**Aplicáveis:** BLOBs, ficheiros

### <a name="mt"></a>/MT

Define última modificação um tempo o ficheiro transferido a ser a mesma como o blob de origem ou do ficheiro.

**Aplicáveis:** BLOBs, ficheiros

### <a name="xn"></a>/XN

Exclui um recurso mais recente de origem. O recurso não será copiado se a hora da última modificação da origem de é o mesmo ou mais recente do que destino.

**Aplicáveis:** BLOBs, ficheiros

### <a name="xo"></a>/XO

Exclui um recurso de origem mais antigo. O recurso não será copiado se a hora da última modificação da origem de é o mesmo ou mais de destino.

**Aplicáveis:** BLOBs, ficheiros

### <a name="a"></a>/A

Os carregamentos pendentes apenas os ficheiros que tenham o atributo arquivo definido.

**Aplicáveis:** BLOBs, ficheiros

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Os carregamentos pendentes apenas os ficheiros que tem alguma do conjunto de atributos especificados.

Atributos disponíveis incluem:

- R = ficheiros só de leitura
- A = ficheiros prontos para o arquivo
- S = ficheiros do sistema
- H = ficheiros ocultos
- C = comprimido ficheiros
- N = ficheiros Normal
- E = encriptado ficheiros
- T = ficheiros temporários
- The = ficheiros Offline
- Posso = ficheiros indexados não

**Aplicáveis:** BLOBs, ficheiros

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Exclui os ficheiros que tem alguma do conjunto de atributos especificados.

Atributos disponíveis incluem:

- R = ficheiros só de leitura
- A = ficheiros preparados para arquivo
- S = ficheiros do sistema
- H = ficheiros ocultos
- C = comprimido ficheiros
- N = ficheiros Normal
- E = encriptado ficheiros
- T = ficheiros temporários
- The = ficheiros Offline
- Posso = ficheiros indexados não

**Aplicáveis:** BLOBs, ficheiros

### <a name="delimiterdelimiter"></a>/ Delimitador: "delimitador"

Indica o carácter delimitador utilizado para delimitar directórios virtuais no nome de uma blob.

Por predefinição, AzCopy utilizações / como o caráter delimitador. No entanto, o AzCopy suporta a qualquer caráter comuns a utilizar (tais como @, # ou %) como delimitador. Se precisar de incluir um destes carateres especiais na linha de comandos, escreva o nome de ficheiro com aspas.

Esta opção só é aplicável para transferir blobs.

**Aplicáveis:** BLOBs

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "número-de-em simultâneo-operações"

Especifica o número de operações em simultâneo.

AzCopy por predefinição é iniciado um determinado número de operações em simultâneo para aumentar o débito de transferência de dados. Note que grande número de operações em simultâneo num ambiente de largura de banda baixa poderá sobrecarregar a ligação de rede e impedir que as operações de totalmente concluir. Optimizar operações em simultâneo com base em largura de banda de rede disponíveis real.

O limite superior para operações em simultâneo é 512.

**Aplicáveis:** Tabelas de BLOBs, ficheiros,

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Tabela"

Especifica que o `source` recurso é um blob disponível no ambiente de desenvolvimento local, a ser executada em emulador de armazenamento.

**Aplicáveis:** BLOBs, tabelas

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Tabela"

Especifica que o `destination` recurso é um blob disponível no ambiente de desenvolvimento local, a ser executada em emulador de armazenamento.

**Aplicáveis:** BLOBs, tabelas

### <a name="pkrskey1key2key3"></a>/ PKRS: "chave1 chave2 # #key3 #..."

Divide o intervalo de chave partição para ativar a exportação de dados de tabela em paralelo, o que aumenta a velocidade da operação de exportação.

Se esta opção não for especificada, AzCopy utiliza um única tópico para exportar entidades de tabela. Por exemplo, se o utilizador Especifica /PKRS: "aa #bb", em seguida, AzCopy inicia três operações em simultâneo.

Cada operação exporta um dos três intervalos chave partição, conforme apresentado abaixo:

  [primeiro-partição-chave, aa)

  [bb aa)

  [bb, tecla de partição última]

**Aplicáveis:** Tabelas

### <a name="splitsizefile-size"></a>/ SplitSize: "tamanho do ficheiro"

Especifica o ficheiro exportado dividir tamanho em MB, o valor mínimo permitido é 32.

Se esta opção não for especificada, o AzCopy exportar dados da tabela para único ficheiro.

Se os dados da tabela são exportados para um blob e o tamanho de ficheiro exportado atingir o limite de 200 GB para tamanho blob, AzCopy vai dividir o ficheiro exportado, mesmo se esta opção não for especificada.

**Aplicáveis:** Tabelas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica o comportamento de importação de dados de tabela.

- InsertOrSkip - ignora uma entidade existente ou insere uma nova entidade se não existir na tabela.

- InsertOrMerge - intercala uma entidade existente ou insere uma nova entidade se não existir na tabela.

- InsertOrReplace - substitui uma entidade existente ou insere uma nova entidade se não existir na tabela.

**Aplicáveis:** Tabelas

### <a name="manifestmanifest-file"></a>/ Manifesto: "ficheiro de manifesto"

Especifica o ficheiro de manifesto para a tabela Exportar e importar operação.

Esta opção é opcional durante a operação de exportação, AzCopy irá gerar um ficheiro de manifesto com o nome predefinido se esta opção não for especificada.

Esta opção é necessária durante a operação de importação para localizar os ficheiros de dados.

**Aplicáveis:** Tabelas

### <a name="synccopy"></a>/ SyncCopy

Indica se pretende copiar modo síncrono blobs ou ficheiros entre dois pontos finais de armazenamento do Windows Azure.

AzCopy por predefinição utiliza cópia assíncrona do lado do servidor. Especifique esta opção para efetuar uma cópia síncrona, que transfere blobs ou ficheiros para memória local e, em seguida, carrega-los de armazenamento do Windows Azure.

Pode utilizar esta opção quando copiar ficheiros dentro do armazenamento de Blobs do armazenamento de ficheiros, ou a partir do armazenamento de Blobs do armazenamento de ficheiros ou vice versa.

**Aplicáveis:** BLOBs, ficheiros

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "tipo de conteúdo"

Especifica o tipo de conteúdo MIME para ficheiros ou blobs de destino.

AzCopy define o tipo de conteúdo para um ficheiro ou blob à aplicação/octeto-sequência por predefinição. Pode definir o tipo de conteúdo para todos os ficheiros ou blobs ao explicitamente especificar um valor para esta opção.

Se esta opção sem um valor que especificar, em seguida, AzCopy irá definir cada blob ou tipo de conteúdo do ficheiro de acordo com a respectiva extensão de ficheiro.

**Aplicáveis:** BLOBs, ficheiros

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Especifica o formato do ficheiro de dados exportados tabela.

Se esta opção não for especificada, por predefinição, AzCopy exporta ficheiro de dados de tabela no formato JSON.

**Aplicáveis:** Tabelas

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e as melhores práticas

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitar escritas em simultâneo ao copiar dados

Quando copia blobs ou ficheiros com AzCopy, tenha em atenção que outra aplicação poderá ser modificar os dados enquanto está a copiar. Se possível, certifique-se de que os dados que está a copiar não estão a ser modificados durante a operação de cópia. Por exemplo, ao copiar um VHD associado com uma máquina virtual Azure, certifique-se de que não existem outras aplicações estão atualmente a escrever no VHD. Uma boa forma para fazer isto é locação financeira o recurso sejam copiados. Em alternativa, pode criar um instantâneo do VHD pela primeira vez e, em seguida, copie o instantâneo.

Se não consegue a impedir que outras aplicações a partir de escrever blobs ou ficheiros, enquanto que estão a ser copiadas, em seguida, tenha em atenção que no momento em que a tarefa é concluída, os recursos copiados já não podem ter paridade completa com os recursos de origem.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Execute uma instância de AzCopy num computador de um.
AzCopy foi concebido para maximizar a utilização do seu recurso de máquina para acelerar a transferência de dados, recomendamos que execute apenas uma instância de AzCopy num computador de um e, especifique a opção `/NC` se precisar de operações mais em simultâneo. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Ativar algoritmos de MD5 compatível com FIPS para AzCopy quando que "utilizar FIPS compatível com algoritmos para encriptação, o hashing e iniciar sessão".
AzCopy por predefinição utiliza .NET MD5 implementação para calcular o MD5 ao copiar objetos, mas existem alguns requisitos de segurança que necessitam de AzCopy para activar FIPS compatível com MD5 definição.

Pode criar um ficheiro de App `AzCopy.exe.config` com propriedade `AzureStorageUseV1MD5` e colocá-la anulação com AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Propriedade "AzureStorageUseV1MD5" • verdadeiro - o valor predefinido, AzCopy utilizará .NET MD5 implementação.
• FALSO – AzCopy irá utilizar algoritmo de MD5 compatível com FIPS.

Tenha em atenção que algoritmos compatível com FIPS está desativado por predefinição no seu computador Windows, pode escrever secpol na sua janela executar e verifique este parâmetro na definição de segurança -> Política Local -> segurança Opções -> criptografia do sistema: algoritmos em conformidade do FIPS de utilização para encriptação, hashing e iniciar sessão.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o armazenamento do Azure e AzCopy, consulte os seguintes recursos.

### <a name="azure-storage-documentation"></a>Azure documentação de armazenamento:

- [Introdução ao Azure armazenamento](storage-introduction.md)
- [Como utilizar o armazenamento de Blobs do .NET](storage-dotnet-how-to-use-blobs.md)
- [Como utilizar o armazenamento de ficheiros a partir do .NET](storage-dotnet-how-to-use-files.md)
- [Como utilizar o armazenamento de tabela a partir do .NET](storage-dotnet-how-to-use-tables.md)
- [Como criar, gerir ou eliminar uma conta de armazenamento](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Azure armazenamento mensagens no blogue:
- [Introdução ao Azure armazenamento dados movimento biblioteca pré-visualização](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Introdução ao copiar síncrono e tipo de conteúdo personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Anunciar o disponibilidade geral de 3.0 AzCopy plus versão de pré-visualização do AzCopy 4.0 com suporte de tabela e ficheiro](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Otimizada para copiar em grande escala cenários](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: Suporte para armazenamento geo redundantes acesso de leitura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Transferir dados com o modo de voltar arrancar e token de SA](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Utilizar a conta da publicação em cópia Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: A transferir e carregar ficheiros para Blobs do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

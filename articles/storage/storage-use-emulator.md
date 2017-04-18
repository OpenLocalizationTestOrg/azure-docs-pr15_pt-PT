<properties 
    pageTitle="Utilizar o armazenamento Azure emulador para desenvolvimento e teste | Microsoft Azure" 
    description="O emulador armazenamento Azure fornece um ambiente de desenvolvimento local gratuita para desenvolver e testar contra armazenamento do Windows Azure. Saiba mais sobre o emulador de armazenamento, incluindo como pedidos são autenticados, como ligar para o emulador a partir da sua aplicação e como utilizar a ferramenta de linha de comandos." 
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
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utilizar o armazenamento Azure emulador para desenvolvimento e teste

## <a name="overview"></a>Descrição geral

O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços de Blobs do Azure, tabela de fila e para fins de programação. Utilizar o emulador de armazenamento, pode testar a aplicação contra localmente, os serviços de armazenamento sem criar uma subscrição do Azure nem sempre quaisquer custos. Quando estiver satisfeito com a forma como a aplicação está a trabalhar no emulador, pode alternar para utilizar uma conta do Azure armazenamento na nuvem.

> [AZURE.NOTE] O emulador armazenamento está disponível como parte do [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Também pode instalar o emulador de armazenamento utilizando o [instalador autónomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Para configurar o emulador de armazenamento, tem de ter privilégios administrativos no computador.
> 
> O emulador armazenamento atualmente apenas é executada no Windows.
>  
> Tenha em atenção que criou numa versão do emulador de armazenamento de dados não se garante para serem acessíveis ao utilizar uma versão diferente. Se precisar de manter os seus dados para a longo prazo, é recomendável que armazene os dados numa conta de armazenamento Azure, em vez do emulador de armazenamento.

## <a name="how-the-storage-emulator-works"></a>Como funciona o emulador de armazenamento
 
O emulador armazenamento utiliza uma instância do Microsoft SQL Server local e o sistema de ficheiros local para emular os serviços de armazenamento Azure. Por predefinição, o emulador armazenamento utiliza uma base de dados no Microsoft SQL Server 2012 Express LocalDB.  Pode escolher configurar o emulador de armazenamento para aceder a uma local instância do SQL Server em vez da instância LocalDB. Para mais informações, consulte [início e de inicialização emulador de armazenamento](#start-and-initialize-the-storage-emulator) abaixo.

Pode instalar o SQL Server Management Studio Express para gerir a sua instalação LocalDB. O emulador armazenamento se liga ao SQL Server ou LocalDB utilizando a autenticação do Windows. 

Existem algumas diferenças de funcionalidade entre os serviços de armazenamento Azure e emulador de armazenamento. Para mais informações sobre estas diferenças, consulte o artigo [diferenças entre o emulador de armazenamento e o armazenamento do Windows Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>Pedidos de autenticação contra emulador de armazenamento

Tal como faria com Azure armazenamento na nuvem, cada pedido que faça contra emulador de armazenamento têm de ser autenticado, a menos que seja um pedido anónimo. É possível autenticar pedidos contra emulador de armazenamento utilizando a autenticação de chave partilhada ou com uma assinatura de acesso partilhado (SA).

### <a name="authentication-with-shared-key-credentials"></a>Autenticação com credenciais de chave partilhada

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Para obter mais detalhes sobre as cadeias de ligação, consulte o artigo [Configurar cadeias de ligação do Azure armazenamento](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Autenticação com uma assinatura de acesso partilhado 

Algumas bibliotecas de cliente do armazenamento Azure, como a biblioteca de Xamarin suportam apenas a autenticação com um token de assinatura (SA) acesso partilhado. Terá de criar este token de SA utilizando uma ferramenta ou a aplicação que suporte a autenticação de chave partilhada. Uma forma fácil para gerar o token de SA é através do Azure PowerShell:

1. Se ainda não o fez, instale Azure PowerShell. Utilizar a versão mais recente dos cmdlets do Azure PowerShell é recomendado. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md#Install) para obter instruções de instalação.

2. Abra o PowerShell do Azure e execute os seguintes comandos. Lembre-se substituir *nome_conta* e *ACCOUNT_KEY = =* com as suas credenciais. Substitua *CONTAINER_NAME* com um nome à sua escolha.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

A assinatura de acesso partilhado resultante URI para o novo contentor deverá ser semelhante ao seguinte:

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

A assinatura de acesso partilhado criada com este exemplo é válida para um dia. A assinatura concede acesso completo (ou seja, ler, escrever, eliminar, lista) para blobs dentro do contentor.

Para mais informações sobre assinaturas de acesso partilhado, consulte o artigo [Utilizar partilhada acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e iniciar o emulador de armazenamento

Para iniciar o emulador armazenamento Azure, selecione o botão de início ou prima a tecla do Windows. Comece a escrever **Emulador de armazenamento do Azure**e selecione o emulador a partir da lista de aplicações. 

Quando o emulador está em execução, verá um ícone na área de notificação da barra de tarefas do Windows.

Quando inicia o emulador de armazenamento, é apresentada uma janela da linha de comandos. Pode utilizar esta janela da linha de comandos para iniciar e parar o emulador de armazenamento, bem como limpar dados, obter o estado atual e iniciar o emulador. Para mais informações, consulte o artigo [Referência de ferramenta de linha de comandos do armazenamento emulador](#storage-emulator-command-line-tool-reference).

Quando a janela de linha de comandos estiver fechada, o emulador armazenamento continua a ser executado. Para ativar novamente a linha de comandos, siga os passos acima, como se iniciar o emulador de armazenamento.

A primeira vez que executa o emulador de armazenamento, o ambiente do armazenamento local está inicializado por si. O processo de inicialização cria uma base de dados no LocalDB e reservas portas HTTP para cada serviço de armazenamento local. 

Está instalado o emulador de armazenamento por predefinição ao diretório c:\Programas\Microsoft ficheiros (x86) \Microsoft SDKs\Azure\Storage emulador. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Iniciar o emulador de armazenamento para utilizar outra base de dados SQL

Pode utilizar a ferramenta de linha de comandos do armazenamento emulador ao iniciar o emulador de armazenamento para apontar para uma instância de base de dados SQL que não seja a instância de LocalDB predefinida. Tenha em atenção que deve estar a executar a ferramenta de linha de comandos com privilégios administrativos para iniciar a base de dados back-end para o emulador de armazenamento:

1. Clique no botão **Iniciar** ou prima a tecla do **Windows** . Comece a escrever `Azure Storage Emulator` e selecione-o quando este aparecer ativar a ferramenta de linha de comandos do armazenamento emulador.
2. Na janela da linha de comandos, escreva o seguinte comando, onde `<SQLServerInstance>` é o nome da instância do SQL Server. Para utilizar LocalDb, especifique `(localdb)\v11.0` como a instância do SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    Também pode utilizar o comando seguinte, o que indica o emulador para utilizar a instância do SQL Server predefinido:

        AzureStorageEmulator init /server .\\ 

    Ou, pode utilizar o seguinte comando, inicia novamente a base de dados para a instância de LocalDB predefinida:

        AzureStorageEmulator init /forceCreate 

Para mais informações sobre estes comandos, consulte o artigo [Referência de ferramenta de linha de comandos do armazenamento emulador](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Endereçar recursos no emulador de armazenamento

Os pontos finais de serviço para o emulador armazenamento são diferentes de uma conta de armazenamento Azure. A diferença é que o computador local não efetua resolução do nome de domínio, para que os pontos finais emulador de armazenamento necessitam de um endereço local em vez de um nome de domínio.

Quando endereço um recurso numa conta de armazenamento Azure, utiliza o esquema, onde o nome da conta faz parte do nome do anfitrião URI e o recurso a ser endereçado faz parte do caminho URI seguinte:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Por exemplo, o URI seguinte é um endereço válido para um blob numa conta de armazenamento Azure:

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

O emulador de armazenamento, uma vez que o computador local não efetua resolução do nome de domínio, o nome da conta é parte do caminho URI em vez do nome do anfitrião. Utilize o seguinte esquema de um recurso a ser executada em emulador de armazenamento:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Por exemplo, o seguinte endereço poderá ser utilizado para aceder a um blob no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Os pontos finais de serviço para o emulador armazenamento são:

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Endereçar a conta secundária com RT GRS

Começando com a versão 3.1, a conta de emulador armazenamento suporta replicação geo redundantes acesso de leitura (GRS RT). Para obter recursos de armazenamento na nuvem e no local emulador, pode aceder a localização secundária por acrescentá - secundário ao nome da conta. Por exemplo, o seguinte endereço poderá ser utilizado para aceder a um blob utilizar secundária só de leitura no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Acesso de programação ao secundária com o emulador de armazenamento, utilize a biblioteca de cliente do armazenamento de .NET versão 3.2 ou posterior. Consulte a [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obter detalhes.

## <a name="storage-emulator-command-line-tool-reference"></a>Referência do armazenamento emulador ferramenta linha de comandos

Introduzido na versão 3.0, ao iniciar o emulador de armazenamento, verá uma linha de comandos janela pop-up. Utilize a janela da linha de comandos para iniciar e parar o emulador, bem como para o estado de consulta e executar outras operações.

> [AZURE.NOTE] Se tiver o Microsoft Azure calcular emulador instalado, um ícone de tabuleiro do sistema irão aparecer quando iniciar o emulador de armazenamento. Botão direito do rato sobre o ícone para mostrar um menu, que fornece uma forma gráfica para iniciar e parar o emulador de armazenamento.

### <a name="command-line-syntax"></a>Sintaxe de linha de comandos

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Opções

Para ver a lista de opções, escreva `/help` na linha de comandos.

| Opção | Descrição                                                    | Comando                                                                                                 | Argumentos                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Iniciar**  | É iniciado o emulador de armazenamento.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-inprocess*: iniciar o emulador no processo de atual em vez de criar um novo processo.                          |
| **Parar**   | Deixa o emulador de armazenamento.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **Estado** | Imprime o estado do emulador armazenamento.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Limpar**  | Limpa os dados em todos os serviços especificados na linha de comandos. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*: limpa blob dados. <br/>*fila de espera*: limpa dados fila de espera. <br/>*tabela*: limpa dados da tabela. <br/>*todos os*: limpa todos os dados em todos os serviços. |
| **Inicialização**   | Executa inicialização única para configurar o emulador.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-servidor serverName\instanceName*: Especifica o servidor que aloja a instância do SQL. <br/>*-sqlinstance instanceName*: Especifica o nome da instância do SQL para ser utilizado na instância de servidor predefinido. <br/>*-forcecreate*: força criação da base de dados SQL, mesmo se já existe. <br/>*-inprocess*: executa inicialização no processo de atual em vez de gerar um processo de novo. Tem de iniciar o processo com permissões elevadas para realizar inicialização atual.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento do Windows Azure

Uma vez que o emulador de armazenamento é um ambiente emulada a ser executada numa instância do SQL local, existem algumas diferenças de funcionalidade entre o emulador e uma conta de armazenamento Azure na nuvem:

- O emulador armazenamento suporta apenas uma única conta fixa e uma chave de autenticação conhecidos.

- O emulador de armazenamento não é um serviço de armazenamento dimensionáveis e não vai suportar um grande número de clientes em simultâneo.

- Conforme descrito em [endereçamento de recursos no emulador de armazenamento](#addressing-resources-in-the-storage-emulator), recursos estão endereçados forma diferente no emulador de armazenamento comparada com uma conta de armazenamento Azure. Esta diferença costuma que resolução do nome de domínio está disponível na nuvem, mas não no computador local.

- Começando com a versão 3.1, a conta de emulador armazenamento suporta replicação geo redundantes acesso de leitura (GRS RT). Na emulador, todas as contas ter ativado com RT-GRS e nunca existe qualquer tempo de desfasamento entre as réplicas principais e secundárias. As operações de estatística de serviço de BLOBs obter, obter estatística de serviço de fila de espera e obter estatísticas dos serviço tabela são suportadas pela conta secundária e sempre irá devolver o valor da `LastSyncTime` elemento de resposta como a hora atual, de acordo com a base de dados do SQL subjacente.

    Acesso de programação ao secundária com o emulador de armazenamento, utilize a biblioteca de cliente do armazenamento de .NET versão 3.2 ou posterior. Consulte a [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obter detalhes.

- O serviço de ficheiro e os pontos finais de serviço do protocolo SMB não são suportados atualmente no emulador de armazenamento.

- O emulador armazenamento devolve um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - pedido incorrecto) se utilizar uma versão dos serviços de armazenamento que ainda não é suportada pela versão do emulador que estiver a utilizar.

### <a name="differences-for-blob-storage"></a>Diferenças de armazenamento de BLOBs 

As diferenças seguintes aplicam-se ao armazenamento Blob do emulador:

- O armazenamento emulador apenas suporta blob tamanhos até 2 GB.

- Uma operação de colocar Blob poderá ter êxito contra um blob de que existe no emulador de armazenamento e possui uma locação ativa, mesmo se o ID de locação financeira não for especificado como parte do pedido. 

- Acrescente Blob operações não são suportadas pelo emulador. Tentar uma operação um blob acrescentar devolve um erro de FeatureNotSupportedByEmulator (código de estado HTTP 400 - pedido incorrecto).

### <a name="differences-for-table-storage"></a>Diferenças de armazenamento de tabela 

As diferenças seguintes aplicam-se ao armazenamento de tabelas no emulador:

- Propriedades de data no serviço de tabela no emulador de armazenamento suportam apenas o intervalo suportado pelo SQL Server 2005 (*por exemplo*, que são necessários para ser posterior a 1 de Janeiro de 1753). Todas as datas anteriores a 1 de Janeiro de 1753 são alteradas para este valor. A precisão das datas está limitada à precisão do SQL Server 2005, que significa que datas são precisas a 1/300th de segundo.

- O emulador armazenamento suporta valores de propriedade da chave de chave e linha partição de menos de 512 bytes cada. Para além disso, o tamanho total do nome da conta, nome da tabela e nomes de propriedade chave em conjunto não pode exceder 900 bytes.

- O tamanho total de uma linha numa tabela no emulador de armazenamento está limitado à menor que 1 MB.

- Na emulador armazenamento, escreva propriedades dos dados `Edm.Guid` ou `Edm.Binary` suporta apenas a `Equal (eq)` e `NotEqual (ne)` operadores de comparação consulta filtrar cadeias.

### <a name="differences-for-queue-storage"></a>Diferenças de armazenamento de fila de espera

Não existem diferenças específico ao armazenamento de fila de espera no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de lançamento do armazenamento emulador

### <a name="version-45"></a>Versão 4,5

- Fixo um erro que causado inicialização e a instalação do emulador de armazenamento para falhar quando a base de dados de segurança foi mudado o nome.

### <a name="version-44"></a>Versão 4.4

- O emulador armazenamento suporta agora a versão 2015-12-11 dos serviços de armazenamento no Blob, fila de espera e tabela pontos finais de serviço.

- Recolha de lixo o emulador de armazenamento de dados de Blobs está agora mais eficiente recorrentes relacionadas com grandes quantidades de blobs.

- Fixo um erro que causado contentor ACL XML a ser validadas de forma ligeiramente diferente a partir de como o serviço de armazenamento é que o mesmo.

- Fixo um erro que, por vezes, causado máximo e mínimo valores de data/hora para ser comunicado no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3

- O emulador armazenamento suporta agora a versão 2015-07-08 dos serviços de armazenamento no Blob, fila de espera e tabela pontos finais de serviço.

### <a name="version-42"></a>Versão 4.2

- O emulador armazenamento suporta agora a versão 2015-04-05 dos serviços de armazenamento no Blob, fila de espera e tabela pontos finais de serviço.

### <a name="version-41"></a>Versão 4.1

- O emulador armazenamento suporta agora a versão 2015-02-21 dos serviços de armazenamento em Blob, fila de espera e tabela pontos finais de serviço, excetuando as novas funcionalidades de BLOBs acrescentar. 

- O emulador armazenamento devolve agora uma mensagem de erro significativo se utilizar uma versão dos serviços de armazenamento que ainda não é suportada por essa versão do emulador. Recomendamos que utilize a versão mais recente do emulador. Se se deparar com um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - pedido incorrecto), transfira a versão mais recente do emulador armazenamento.

- Fixada um erro onde os dados de entidade de tabela numa corrida condição causado estar incorreto durante as operações de impressão em série em simultâneo.

### <a name="version-40"></a>Versão 4.0

- Foi mudado o emulador armazenamento executável para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3,2

- O emulador armazenamento suporta agora a versão 2014-02-14 dos serviços de armazenamento no Blob, fila de espera e tabela pontos finais de serviço. Tenha em atenção que pontos finais de serviço de ficheiro não são atualmente suportados no emulador de armazenamento. Consulte o [controlo de versões para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) para obter detalhes sobre a versão de 2014-02-14.

### <a name="version-31"></a>Versão 3.1

- Acesso de leitura armazenamento geo redundantes (GRS RT) agora é suportado no emulador de armazenamento. A estatística de serviço de BLOBs obter, obter estatística de serviço de fila de espera e obter APIs de estatística de serviço de tabela são suportados para a conta secundária e sempre devolverá o valor do elemento de resposta LastSyncTime como a hora atual, de acordo com a base de dados do SQL subjacente. Acesso de programação ao secundária com o emulador de armazenamento, utilize a biblioteca de cliente do armazenamento de .NET versão 3.2 ou posterior. Consulte a biblioteca de cliente do Microsoft Azure armazenamento para .NET referência para obter detalhes.

### <a name="version-30"></a>Versão 3.0

- O emulador armazenamento Azure já não é fornecido no pacote do mesmo como emulador cluster.

- A interface de utilizador estruturador do armazenamento emulador é preterida favor uma interface de linha de comandos script. Para obter detalhes sobre a interface de linha de comandos, consulte Referência de ferramenta do armazenamento emulador da linha de comandos. A interface gráfica irão continuar a estar presentes na versão 3.0, mas só pode ser acedido quando está instalado o emulador calcular ao clicar no ícone do tabuleiro do sistema e selecionar mostrar IU do armazenamento emulador.

- Versão 2013-08-15 dos serviços de armazenamento Azure agora é totalmente suportada. (Anteriormente esta versão só foi suportada pelo armazenamento emulador versão 2.2.1 pré-visualização.)

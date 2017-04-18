<properties
    pageTitle="Utilizar Importar/exportar para o qual transferir dados ao armazenamento Blob | Microsoft Azure"
    description="Saiba como criar importar e exportar tarefas no portal do Azure clássico para transferir dados para blob armazenamento."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Utilizar o serviço do Microsoft Azure importar/exportar para transferir dados ao armazenamento Blob

## <a name="overview"></a>Descrição geral

Serviço de importação/exportação Azure permite-lhe transferir em segurança grandes quantidades de dados ao armazenamento de Blobs do Azure ao endereço de envio, unidades de disco rígido para um centro de dados Azure. Também pode utilizar este serviço para transferir dados a partir do armazenamento de Blobs do Azure para unidades de disco rígido e enviar para o seu site no local. Este serviço é adequado em situações onde pretende transferir várias TBs de dados para ou a partir do Azure, mas o carregamento ou transferência através da rede não é viável devido a largura de banda limitada ou custos de rede de alta.

O serviço requer que unidades de disco rígido deve ser armário bit encriptado para a segurança dos seus dados. O serviço suporta contas de armazenamento clássica apresentadas em todas as regiões do Azure público. Tem de fornecer as unidades de disco rígido para uma das localizações suportadas especificadas neste artigo.

Neste artigo, irá Saiba mais sobre o serviço de importação/exportação do Azure e como para o envio unidades para copiar os dados para e a partir do armazenamento de Blobs do Azure.

> [AZURE.IMPORTANT] Pode criar e gerir a importação e exportação trabalhos de armazenamento clássico através do portal do clássico ou o [serviço de importação/exportação REST APIs](http://go.microsoft.com/fwlink/?LinkID=329099). Contas de armazenamento do Gestor de recursos não são suportadas neste momento.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quando devo utilizar o serviço de importação/exportação de Azure?

Poderá considerar a utilização do Azure importar/exportar serviço quando carregar ou transferir dados através da rede é muito lenta ou introdução a largura de banda de rede adicionais é proibição de custo.

Pode utilizar este serviço em cenários tais como:

- Migrar dados para a nuvem: mover grandes quantidades de dados para Azure rapidamente e custo de forma eficaz.
- Distribuição de conteúdo: rapidamente enviar dados aos seus sites de cliente.
- Cópia de segurança: Tirar cópias de segurança dos seus dados no local para armazenar no armazenamento de Blobs do Azure.
- Recuperação de dados: recuperar grande quantidade de dados armazenados num armazenamento de BLOBs e mantê-la entregue a sua localização no local.

## <a name="pre-requisites"></a>Pré-requisitos

Nesta secção, podemos ter listado pré-requisitos necessários para utilizar este serviço. Reveja-los com atenção antes de enviar as unidades.

### <a name="storage-account"></a>Conta de armazenamento

Tem de ter uma subscrição existente do Azure e um ou mais **clássico** armazenamento contas para utilizar o serviço de importar/exportar. Cada tarefa pode ser utilizada para transferir dados para ou a partir de apenas uma conta de armazenamento clássico. Por outras palavras, não é possível expandir uma tarefa de importar/exportar única em várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte o artigo [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Tipos de BLOBs

Pode utilizar o serviço do Azure importar/exportar para copiar dados para **Bloquear** blobs ou blobs de **página** . Por outro lado, só pode exportar blobs **Bloco** , **página** blobs ou blobs **Acrescentar** a partir do armazenamento Azure com este serviço.

### <a name="job"></a>Tarefa

Para começar o processo de importação para ou exportar a partir do armazenamento de BLOBs, crie primeiro uma tarefa. Uma tarefa pode ser uma tarefa de importação ou de uma tarefa de exportação:

- Crie uma tarefa de importação ao qual pretende transferir dados que tem no local para blobs na sua conta de armazenamento Azure.
- Crie uma tarefa de exportação ao qual pretende transferir dados armazenados atualmente como blobs na sua conta de armazenamento para as unidades de disco rígido que são enviados para si.

Quando cria uma tarefa, notifique o serviço de importação/exportação que lhe vai envio uma ou mais unidades de disco rígido para um centro de dados Azure.

- Para uma tarefa de importação, irá de envio unidades de disco rígido que contêm os dados.
- Para uma tarefa de exportação, será de envio unidades de disco rígido vazias.
- Pode enviar até 10 unidades de disco rígido por tarefa.

Pode criar um importar ou exportar tarefa utilizando o [portal de Classic](https://manage.windowsazure.com/) ou a [API REST do Azure armazenamento importar/exportar](http://go.microsoft.com/fwlink/?LinkID=329099).

### <a name="client-tool"></a>Ferramenta de cliente

O primeiro passo na criação de uma tarefa de **Importar** é preparar a sua unidade que será enviada para serem importados. Preparar a sua unidade, tem de ligá-lo para um servidor local e executar a ferramenta de cliente do Azure importar/exportar no servidor local. Esta ferramenta de cliente facilita a copiar os dados para a unidade, encriptar os dados na unidade com BitLocker e os ficheiros de diário unidade a gerar.

Os ficheiros de diário armazenam informações básicas sobre as suas tarefas e a unidade como o número de série de unidade e o nome da conta de armazenamento. Este ficheiro diário não está armazenado na unidade de. É utilizado durante a criação de tarefa de importar. Passo a passo detalhes sobre a criação de emprego são fornecidos neste artigo.

A ferramenta de cliente apenas é compatível com o sistema de operativo Windows de 64 bits. Consulte a secção de [sistema operativo](#operating-system) para versões específicas do SO suportados.

Transfira a versão mais recente da [ferramenta de cliente do Azure importar/exportar](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Para obter mais detalhes sobre como utilizar a ferramenta de importar/exportar Azure, consulte a [Referência de ferramenta do Azure importar/exportar](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Unidades de disco rígido

Apenas 3,5 polegada unidades de disco rígido interno SATA II/III são suportadas para utilização com o serviço de importar/exportar. Pode utilizar unidades de disco rígido até 10TB.
Para importar tarefas, apenas o primeiro volume de dados na unidade de será processado. O volume de dados tem de ser formatado com NTFS.
Ao copiar dados no seu disco rígido, pode anexá-lo diretamente utilizando uma conexão SATA ou pode anexá-lo externamente utilizando um adaptador SATA II/III USB externo. Recomendamos que utilize um dos seguintes adaptadores SATA II/III USB externos:

- 68UPSATAA de Anker - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Se tiver um conversor de que não estiver listado acima, pode tentar executar a ferramenta de importar/exportar Azure utilizando o conversor para preparar a unidade e ver se funciona antes de adquirir um conversor suportado.

> [AZURE.IMPORTANT] Unidades de disco rígido externas que estão incluídos um adaptador USB incorporado não são suportadas por este serviço. Além disso, o disco dentro do invólucro de um disco externo não pode ser utilizado; Não envie HDDs externos.

### <a name="encryption"></a>Encriptação

Os dados na unidade de tem de ser encriptados utilizando encriptação de unidade BitLocker. Este procedimento protege dados enquanto estão em trânsito.

Para importar tarefas, existem duas formas de executar a encriptação. A primeira forma é utilizar a / encriptar parâmetro quando estiver a executar a ferramenta de cliente durante a preparação da unidade. A forma como o segundo é activar a encriptação BitLocker manualmente na unidade de e especificar a chave de encriptação na linha de comandos ferramenta cliente durante a preparação da unidade.

Para trabalhos de exportação, depois dos dados são copiados para as unidades de, o serviço encriptar a unidade de utilização do BitLocker antes de enviar-lhe. A chave de encriptação vai ser fornecida através do portal do clássico.  

### <a name="operating-system"></a>Sistema operativo

Pode utilizar um dos seguintes sistemas operativos de 64 bits preparar a unidade de disco rígido utilizando a ferramenta de importar/exportar do Azure antes de enviar a unidade de Azure:

Windows 7 Enterprise, Ultimate do Windows 7, Windows 8 Pro, empresa do Windows 8, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Todos estes sistemas operativos suportam encriptação de unidade BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup> Se estiver a utilizar um computador Windows 10 para preparar o seu disco rígido, transfira a versão mais recente da ferramenta Azure importar/exportar.

### <a name="locations"></a>Localizações

O serviço de Azure importar/exportar suporta copiar dados de e para a todas as contas de armazenamento do Azure público. Pode enviar unidades de disco rígido para uma das seguintes localizações. Se a sua conta de armazenamento for numa localização Azure pública que não é especificada aqui, uma localização de envio alternativo será fornecida quando estiver a criar a tarefa, utilizando o portal de Classic ou API REST importar/exportar.

Suportadas envio localizações:

- Leste dos EUA

- Ocidental dos e.u.a.

- Leste dos EUA 2

- Central (EUA)

- América do Norte Central (EUA)

- Sul Central (EUA)

- Europa Norte

- Europa Ocidental

- Este asiático

- Sudeste asiático

- Leste Austrália

- Sudeste Austrália

- Japão oeste

- Japão leste

- Índia central


### <a name="shipping"></a>Endereço de envio

**Unidades de envio para o Centro de dados:**

Ao criar uma tarefa de importação ou de exportação, vai ser fornecido um endereço de envio de uma das localizações suportadas para o envio as unidades. O endereço de envio fornecido irá dependem a localização da sua conta de armazenamento, mas não podem ser iguais a sua localização de conta de armazenamento.

Pode utilizar as transportadoras como FedEx, DHL, UPS ou do serviço de Postal-na enviar unidades para o endereço de envio.

**Unidades de envio a partir do Centro de dados:**

Ao criar uma tarefa de importação ou de exportação, tem de fornecer um endereço do remetente para a Microsoft para utilizar quando as unidades de envio após a sua tarefa está concluída. Certifique-se de que fornece uma morada do remetente válida para evitar atrasos na processamento.

Também tem de fornecer um operador de FedEx ou DHL válido número a ser utilizado pela Microsoft para envio novamente as unidades de conta. Um número de conta FedEx é necessário para envio unidades novamente a partir das localizações Europa e dos EUA. Um número de conta DHL é necessário para envio unidades novamente a partir de localizações da Ásia e Austrália. Pode criar uma conta de carrier [DHL](http://www.dhl.com/) (países da Ásia e Austrália) de [FedEx](http://www.fedex.com/us/oadr/) (para Europa e dos e.u.a.) ou se não tiver um. Se já tiver um número de conta carrier, verifique que é válida.

No seu pacotes de envio, tem de seguir os termos em [Termos de serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Tenha em atenção que os elementos de multimédia físicos que estiver a enviar poderão ter de cruz internacionais limites. É responsável para assegurar que os suporte físico e os dados são importados e/ou exportados em conformidade com a legislação aplicável. Antes envio suportes físicos, certifique-se com o seu conselheiros para verificar se os ficheiros de multimédia e dados por motivos legais podem ser enviados para o Centro de dados identificados. Isto irá ajudar a garantir que chega Microsoft forma atempada. Por exemplo, qualquer pacote que irá cross limites internacionais necessita de uma fatura comercial acompanhar com o pacote (exceto se passagem limites dentro da União Europeia). Pode imprimi uma cópia da fatura comercial a partir do website carrier preenchida. Exemplo de comercial da fatura são [fatura comercial DHL] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) ou [Fatura comercial FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Certifique-se de que Microsoft não tenha sido indicado como exportador.

## <a name="how-does-the-azure-importexport-service-work"></a>Como funciona o serviço de importação/exportação de Azure?

Pode transferir dados entre o seu site no local e armazenamento de Blobs do Azure através do serviço do Azure importar/exportar através da criação de tarefas e endereço de envio, unidades de disco rígido para um centro de dados Azure. Cada unidade de disco rígido que envia está associada uma única tarefa. Cada tarefa está associada uma conta de armazenamento única. Reveja a [secção de pré-requisitos](#pre-requisites) cuidadosamente para saber mais sobre os detalhes deste tipos de Blobs do serviço tais como suportado, tipos de disco, localizações e envio.

Nesta secção, irá descrevemos de alto nível os passos que envolvem importar e exportar tarefas. Mais tarde, na [secção guia](#quick-start), fornecemos instruções passo a passo para criar um importar e exportar tarefa.

### <a name="inside-an-import-job"></a>Dentro de uma tarefa de importação

De alto nível, uma tarefa de importação envolve os seguintes passos:

- Determine os dados para serem importados e o número de unidades, que terá de.
- Identifique as blobs de destino para os seus dados no armazenamento Blob do.
- Utilize a ferramenta de importar/exportar Azure para copiar os dados para um ou mais unidades de disco rígido e encriptá-las com BitLocker.  
- Crie uma tarefa de importação na sua conta de armazenamento clássico de destino utilizando o portal do clássico ou API REST importar/exportar. Se utilizar o portal de clássico, carregue os ficheiros de diário unidade.
- Forneça o endereço do remetente e o número de conta carrier a ser utilizado para as unidades de envio lhe.
- Envia as unidades de disco rígido para o endereço de envio fornecido durante a criação de tarefa.
- Atualize a entrega número os detalhes de tarefa de importação de rastreio e submeter a tarefa de importação.
- As unidades são recebidas e processadas no Centro de dados Azure.
- Unidades são enviadas com a sua conta carrier para o endereço do remetente fornecido na tarefa de importar.

    ![Figura 1:Import fluxo de trabalho](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>Dentro de uma tarefa de exportação

De alto nível, uma tarefa de exportação envolve os seguintes passos:

- Determine os dados a serem exportadas e o número de unidades, que terá de.
- Identifique os blobs de origem ou caminhos de contentor dos seus dados no armazenamento Blob do.
- Crie uma tarefa de exportação na sua conta de armazenamento de origem utilizando o portal de Classic ou API REST importar/exportar.
- Especifique o blobs de origem ou caminhos de contentor dos seus dados na tarefa de exportação.
- Forneça o endereço do remetente e o número de conta carrier para a ser utilizado para as unidades de envio lhe.
- Envia as unidades de disco rígido para o endereço de envio fornecido durante a criação de tarefa.
- Atualize a entrega número os detalhes de tarefa de exportação de rastreio e submeter a tarefa de exportação.
- As unidades são recebidas e processadas no Centro de dados Azure.
- As unidades são encriptadas com BitLocker; as teclas estão disponíveis através do portal de clássica.  
- As unidades são enviadas com a sua conta carrier para o endereço do remetente fornecido na tarefa de importar.

    ![Figura 2:Export fluxo de trabalho](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Ver o seu estado da tarefa

Pode controlar o estado da sua importação ou exportação tarefas a partir do portal de clássica. Navegue até à sua conta de armazenamento no portal do clássica e clique no separador **Importar/exportar** . Será apresentada uma lista das suas tarefas na página. Pode filtrar a lista de estado da tarefa, o nome da tarefa, o tipo de tarefa ou o número de rastreio.

Irá ver um dos seguintes Estados dependendo de onde a sua unidade está no processo de tarefa.

| Estado da tarefa   | Descrição                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criar     | A tarefa foi criada, mas ainda não forneceu as informações de envio.                                                                                                                                                                                                                                                                                                |
| Endereço de envio     | A tarefa foi criada e que forneça as informações de envio. **Nota**: quando a unidade é entregue para o Centro de dados Azure, o estado poderá ainda apresentam "Envio" há algum tempo. Assim que o serviço é iniciado copiar os dados, o estado será alterado para "Transferir". Se pretender ver o estado mais específico da unidade de CD, pode utilizar a API do resto importar/exportar. |
| Transferir | Os seus dados estão a ser transferidos a partir do seu disco rígido (para uma tarefa de importação) ou no seu disco rígido (para uma tarefa de exportação).                                                                                                                                                                                                                                                                 |
| Embalagem    | A transferência dos seus dados estiver concluída, e unidade de disco rígido está a ser preparada para envio lhe.                                                                                                                                                                                                                                                                             |
| Concluir     | Unidade de disco rígido foi enviada novamente para si.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Hora para a tarefa de processo

A quantidade de tempo que demora a uma tarefa de importar/exportar varia dependendo de fatores diferentes como hora de envio, de processo da tarefa tipo, tipo e tamanho dos dados a ser copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação não tem uma SLA. Pode utilizar a API REST para controlar o progresso da tarefa mais atentamente. Existe um parâmetro percentagem concluído na operação de lista de tarefas que lhe dá uma indicação do progresso de cópia. Comunique-nos se precisar de uma estimativa para concluir uma tarefa de importar/exportar crítico do tempo.

### <a name="pricing"></a>Preços

**Taxa de processamento de unidade**

Existe uma taxa de processamento de unidade para cada unidade processada como parte da sua importação ou exportação tarefa. Consulte os detalhes sobre [Preços do Azure importar/exportar](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Custos de envio**

Quando envia unidades para Azure, pagar o custo de envio à transportadora envio. Quando o Microsoft devolve as unidades para si, o custo de envio é cobrado à conta carrier que forneceu no momento da criação de emprego.

**Custos da transação**

Existem sem custos da transação ao importar dados para o armazenamento de Blobs. Taxas de saída padrão são aplicáveis quando os dados são exportados a partir do armazenamento de Blobs. Para obter mais detalhes sobre os custos da transação, consulte o artigo [preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Guia de introdução

Nesta secção, fornecemos instruções passo a passo para criar uma importação e uma tarefa de exportação. Certifique-se de que cumprir todos os [Pré-requisitos](#pre-requisites) antes de avançar.

## <a name="how-to-create-an-import-job"></a>Como criar uma tarefa de importação?

Crie uma tarefa de importação para copiar dados à sua conta de armazenamento Azure de unidades de disco rígido por uma ou mais unidades que contêm dados para o Centro de dados especificada de envio. A tarefa de importação concede detalhes sobre unidades de disco rígido, dados a ser copiada, conta de armazenamento e informações de envio para o serviço do Azure importar/exportar. Criar uma tarefa de importação é um processo três passos. Em primeiro lugar, prepare-se as unidades utilizando a ferramenta de cliente do Azure importar/exportar. Em segundo lugar, submeta uma tarefa de importação utilizando o portal de clássica. Em terceiro lugar, enviar as unidades para o endereço de envio fornecida durante a criação de emprego e atualizar as informações de envio em detalhes de tarefa.   

> [AZURE.IMPORTANT] Pode submeter apenas uma tarefa por conta de armazenamento. Cada unidade que envia pode ser importada para uma conta de armazenamento. Por exemplo, vamos supor que pretende importar dados para duas contas de armazenamento. Tem de utilizar unidades de disco rígido separadas para cada conta de armazenamento e criar tarefas separadas por conta de armazenamento.

### <a name="prepare-your-drives"></a>Preparar os seus discos

É o primeiro passo ao importar dados utilizando o serviço do Azure importar/exportar preparar o seu unidades utilizando a ferramenta de cliente do Azure importar/exportar. Siga os passos abaixo para preparar o seu unidades.

1.  Identifica os dados para serem importados. Isto pode ser directórios e autónomo ficheiros num servidor local ou numa partilha de rede.  

2.  Determine o número de unidades, que terá de dependendo do tamanho total dos dados. Adquirir o número necessário de polegada 3,5 SATA II/III unidades de disco rígido.

3.  Identificar a conta de armazenamento de destino, contentor, directórios virtuais e blobs.

4.  Determine a directórios e/ou ficheiros de autónomo que serão copiados para cada unidade de disco rígido.

5.  Utilize a [Ferramenta de importar/exportar Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para copiar os dados para um ou mais unidades de disco rígido.

    - A ferramenta de Azure importar/exportar cria sessões copiar para copiar os dados de origem para as unidades de disco rígido. Na sessão única cópia, a ferramenta pode copiar um único directório juntamente com os seus subdiretórios ou um único ficheiro.

    - Poderá ter várias cópia sessões se os dados de origem abrangem vários directórios.

    - Cada unidade de disco rígido que se preparar irão necessitar a sessão de pelo menos uma cópia.

6.  Pode especificar o / encriptar parâmetro para activar a encriptação Bitlocker na unidade de disco rígido. Em alternativa, pode também activar a encriptação Bitlocker manualmente na unidade de disco rígido e fornecer a tecla quando executar a ferramenta.

7.  A ferramenta de importar/exportar Azure gera um ficheiro de diário de unidade para cada unidade, tal como está preparado. O ficheiro de diário unidade está armazenado no seu computador local, não na própria unidade. Irá carregar o ficheiro de diário quando cria a tarefa de importação. Um ficheiro de diário unidade inclui o ID da unidade e a chave de BitLocker, bem como outras informações sobre a unidade.
**Importante**: cada unidade de disco rígido se preparar irá resultar num ficheiro de diário. Quando estiver a criar a tarefa de importação utilizando o portal de clássico, terá de carregar todos os ficheiros de diário das unidades que fazem parte de que a tarefa de importação. Unidades sem diário ficheiros não serão processados.

8.  Não modifique os dados em unidades de disco rígido ou o ficheiro de diário depois de concluir a preparação do disco.

Abaixo estão os comandos e exemplos para preparar a unidade de disco rígido utilizando a ferramenta de cliente do Azure importar/exportar.

Azure importar/exportar cliente ferramenta PrepImport comando para a primeira sessão copiar copiar um diretório:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Exemplo:**

No exemplo abaixo está a copiar todos os ficheiros e sub directórios de H:\Video a unidade de disco rígido instalados no x. Os dados serão importados para a conta de armazenamento de destino especificada pela chave de conta de armazenamento e para o contentor de armazenamento de chamada de vídeo. Se não apresentar o contentor de armazenamento, será criado. Este comando também irá formatar e encriptar a unidade de disco rígido destino.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure importar/exportar cliente PrepImport comando ferramenta para sessões de subsequentes copiar copiar um diretório de:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Para sessões de subsequentes copiar para a mesma unidade de disco rígido, especifique o mesmo nome de ficheiro de diário e forneça uma nova sessão de ID; Não é necessário para fornecer a unidade de chave e de destino da conta de armazenamento novamente, nem para formatar ou encriptar a unidade. Neste exemplo estamos está a copiar a pasta de H:\Photo e respectivos directórios sub para a mesma unidade de destino, para o contentor de armazenamento denominado fotografia.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

A ferramenta de cliente do Azure importar/exportar PrepImport comando para a primeira sessão copiar copiar um ficheiro:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

A ferramenta de cliente do Azure importar/exportar PrepImport comando para sessões de subsequentes copiar copiar um ficheiro:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Memorizar**: por predefinição, os dados serão importados como Blobs bloco. Pode utilizar o parâmetro /BlobType para importar dados como uma página Blobs. Por exemplo, se estiver a importar ficheiros VHD qual vai ser instalados como discos numa VM Azure, tem importá-los como Blobs de página. Se não tiver a certeza qual blob tipo deve para utilizar, pode especificar /blobType:auto e iremos ajudá-determinar o tipo de à direita. Neste caso, todos os ficheiros vhd e vhdx serão importados como Blobs de página e os restantes serão importados como Blobs bloco.

Ver mais detalhes sobre como utilizar a ferramenta de cliente do Azure importar/exportar no [Preparar unidades de disco rígido para importação](https://msdn.microsoft.com/library/dn529089.aspx).

Além disso, consulte o [Fluxo de trabalho de exemplo para preparar unidades de disco rígido para uma tarefa de importação](https://msdn.microsoft.com/library/dn529097.aspx) para obter instruções passo a passo mais detalhadas.  

### <a name="create-the-import-job"></a>Criar a tarefa de importação

1.  Após ter preparado sua unidade, navegue até à sua conta de armazenamento no [portal de clássica](https://manage.windowsazure.com) e visualizar o Dashboard. Em **Rapidamente rápida**, clique em **criar uma tarefa de importação**. Rever os passos e selecione a caixa de verificação para indicar que tenham preparados sua unidade e que tem o ficheiro de diário unidade disponível.

2.  No passo 1, fornece informações de contacto da pessoa responsável por esta tarefa de importar e uma morada do remetente válida. Se pretender guardar dados de registo verboso para a tarefa de importação, selecione a opção para **Guardar o registo verboso no meu contentor de BLOBs 'waimportexport'**.

3.  No passo 2, carregue os ficheiros de diário de unidade obtidos durante o passo de preparação da unidade. Irá precisar de carregar um ficheiro para cada unidade que tenha preparado.

    ![Criar a tarefa de importação - passo 3](./media/storage-import-export-service/import-job-03.png)

4.  No passo 3, introduza um nome descritivo para a tarefa de importação. Note que o nome que introduziu pode conter apenas letras em minúsculas, números, hífenes e sublinhado, tem de iniciar por uma letra e não pode conter espaços. Irá utilizar o nome que escolheu para controlar as tarefas enquanto estiverem em curso e depois de serem concluídas.

    Em seguida, selecione a região do Centro de dados a partir da lista. A região do Centro de dados irá indicar o Centro de dados e o endereço ao qual tem de fornecer o seu pacote. Consulte as FAQ abaixo para obter mais informações.

5.  No passo 4, selecione o remetente carrier a partir da lista e introduza o seu número de conta carrier. Microsoft utilizará esta conta para o envio as unidades lhe assim que a tarefa de importação estiver concluída.

    Se tiver o seu número de controlo, selecione o operador de entrega a partir da lista e introduza o seu número de controlo.

    Se não um número de rastreio ainda, selecione **fornecerei as minhas informações de envio para esta tarefa de importar uma vez que enviou a minha pacote**, em seguida, conclua o processo de importação.

6. Para introduzir o seu número de rastreio após ter enviada o pacote, para regressar à página **Importar/exportar** para a sua conta de armazenamento no portal do clássico, selecione a tarefa a partir da lista e, selecione **Informações de envio**. Navegue através do assistente e introduza o seu número de controlo no passo 2.

    Se o número de controlo não é atualizado dentro de 2 semanas de criação da tarefa, irá expirar a tarefa.

    Se a tarefa estiver no estado de criar, envio ou transferir, também pode atualizar o número de conta carrier no passo 2 do assistente. Assim que a tarefa estiver no estado de embalagem, não é possível atualizar o seu número de conta carrier para essa tarefa.

7. Pode controlar o progresso da tarefa no dashboard de portal. Consulte o artigo o que significa cada Estado da tarefa na secção anterior, [veja o estado da tarefa](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>Como criar uma tarefa de exportação?

Crie uma tarefa de exportação para notificar o serviço de importação/exportação que vai ser envio uma ou mais unidades vazias para o Centro de dados para que os dados podem ser exportados da sua conta de armazenamento para as unidades e as unidades de, em seguida, enviados para si.

### <a name="prepare-your-drives"></a>Preparar os seus discos

Verificações de pré-lançamento seguintes são recomendadas para preparar os seus discos para uma tarefa de exportação:

1. Selecione o número de discos necessários utilizando o comando de PreviewExport a ferramenta de Azure importar/exportar. Para mais informações, consulte o artigo [Pré-visualizar conduzem a utilização de uma tarefa de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). Ajuda-o a pré-visualizar a utilização da unidade para blobs que selecionou, com base no tamanho das unidades que irão utilizar.

2. Verifique se o pode leitura/escrita a unidade de disco rígido que será enviada para a tarefa de exportação.

### <a name="create-the-export-job"></a>Criar a tarefa de exportação

1.  Para criar uma tarefa de exportação, navegue até à sua conta de armazenamento no [portal de clássica](https://manage.windowsazure.com)e visualizar o Dashboard. Em **Rapidamente rápida**, clique em **criar uma tarefa de exportação** e avance através do assistente.

2.  No passo 2, fornece informações de contacto da pessoa responsável por esta tarefa de exportação. Se pretender guardar dados de registo verboso para a tarefa de exportação, selecione a opção para **Guardar o registo verboso no meu contentor de BLOBs 'waimportexport'**.

3.  No passo 3, especificar quais blob os dados que pretende exportar da sua conta de armazenamento à sua unidade em branco ou unidades. Pode optar por exportar todos os dados de BLOBs na conta de armazenamento ou pode especificar que blobs ou conjuntos de blobs para exportar.

    Para especificar um blob para exportar, utilize o selector **Igual a** e especifique o caminho relativo para o blob, começando com o nome do contentor. Utilize *$root* para especificar o contentor de raiz.

    Para especificar todos os blobs começando com um prefixo, utilize o selector **Começa com** e, especifique o prefixo, começando com uma barra '/'. O prefixo pode ser o prefixo do nome do contentor, o nome do contentor concluída ou o nome do contentor completo seguido o prefixo do nome do blob.

    A tabela seguinte mostra exemplos de caminhos de BLOBs válida:

    Selector de|Caminho de BLOBs|Descrição
    ---|---|---
    Começa com|/|Exporta todos os blobs na conta de armazenamento
    Começa com|/$root /|Exporta todos os blobs no contentor de raiz
    Começa com|/Book|Exporta todos os blobs em qualquer contentor que começa com o prefixo **livro de endereços**
    Começa com|/Music/|Exporta todos os blobs no contentor **música**
    Começa com|/ música/amor|Exporta todos os blobs contentor **música** que começam com prefixo **adora**
    Igual a|$root/logo.bmp|Exportações blob **logo. bmp** no contentor de raiz
    Igual a|videos/Story.mp4|Exportações blob **story.mp4** no contentor **vídeos**

    Tem de fornecer os caminhos de BLOBs em formatos válidos para evitar erros durante o processamento, conforme apresentado nesta captura de ecrã.

    ![Criar a tarefa de exportação - passo 3](./media/storage-import-export-service/export-job-03.png)


4.  No passo 4, introduza um nome descritivo para a tarefa de exportação. O nome que introduziu pode conter apenas letras em minúsculas, números, hífenes e sublinhado, tem de iniciar com uma letra e não pode conter espaços.

    A região do Centro de dados irá indicar o Centro de dados à qual tem de fornecer o seu pacote. Consulte as FAQ abaixo para obter mais informações.

5.  No passo 5, selecione o seu carrier remetente a partir da lista e, introduza o seu número de conta carrier. Microsoft utilizará esta conta para o envio unidades novamente para uma vez concluída a tarefa de exportação.

    Se tiver o seu número de controlo, selecione o operador de entrega a partir da lista e introduza o seu número de controlo.

    Se não um número de rastreio ainda, selecione **fornecerei as minhas informações de envio para esta tarefa de exportação, uma vez que enviou a minha pacote**, em seguida, conclua o processo de exportação.

6. Para introduzir o seu número de rastreio após ter enviada o pacote, para regressar à página **Importar/exportar** para a sua conta de armazenamento no portal do clássico, selecione a tarefa a partir da lista e, selecione **Informações de envio**. Navegue através do assistente e introduza o seu número de controlo no passo 2.

    Se o número de controlo não é atualizado dentro de 2 semanas de criação da tarefa, irá expirar a tarefa.

    Se a tarefa estiver no estado de criar, envio ou transferir, também pode atualizar o número de conta carrier no passo 2 do assistente. Assim que a tarefa estiver no estado de embalagem, não é possível atualizar o seu número de conta carrier para essa tarefa.

    > [AZURE.NOTE] Se o blob a serem exportadas está a ser utilizado no momento da copiar para a unidade de disco rígido, o serviço Azure importar/exportar tire um instantâneo do blob e copiar o instantâneo.

7.  Pode controlar o progresso da tarefa no dashboard de no portal do clássico. Consulte o artigo o que significa cada Estado da tarefa na secção anterior por "ver o estado da tarefa".

8.  Depois de receber as unidades com os seus dados exportados, pode ver e copiar as teclas de BitLocker geradas pelo serviço para a sua unidade. Navegue até à sua conta de armazenamento no portal do clássica e clique no separador importar/exportar. Selecione a sua tarefa de exportação a partir da lista e clique no botão de vista teclas. As teclas de BitLocker aparecem como é mostrado abaixo:

    ![Ver teclas BitLocker para tarefa de exportação](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Aceda à secção FAQ abaixo como cobrir as perguntas mais comuns clientes encontrar quando utilizar este serviço.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes ##


**Quanto tempo vai demorar para copiar os meus dados depois do meu mais unidades de atingir o Centro de dados?**

O tempo para copiar varia consoante fatores diferentes, como o tipo de tarefa, tipo e tamanho de dados a ser copiados, tamanho de discos fornecido e existentes carga de trabalho. Pode variar de algumas dias para de duas semanas, dependendo das seguintes fatores. Por isso, é difícil fornecer uma estimativa geral. O serviço tentará otimizar o seu trabalho ao copiar múltiplas unidades em paralelo sempre que possível. Se tiver uma tarefa de importar/exportar crítico tempo, comunique-numa estimativa.

**Quando deve utilizar o serviço de importação/exportação Azure?**
Um deverá tomar em consideração através do Azure importar exportar se o carregamento ou transferência através de rede leva-o até cerca estimar mais de 7 dias. Pode calcular quanto tempo vai demorar utilizando qualquer Calculadora online ou ao [Transferir](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) um localizada no nosso Azure importar exportar REST API amostra no repositório de amostras Azure na [Calculadora de velocidade de transferência de dados](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). Não é um cálculo exato, mas apenas uma indicação em bruto.

**Pode utilizar o serviço do Azure importar/exportar com uma conta de armazenamento do Gestor de recursos?**

Não, não é possível copiar dados para ou de uma conta de armazenamento do Gestor de recursos diretamente através do serviço do Azure importar/exportar. O serviço apenas suporta contas de armazenamento clássica. Suporte de conta de armazenamento do Gestor de recursos vai ser brevemente. Como alternativa, pode importar dados para uma conta de armazenamento clássica e migrá-lo à sua conta de armazenamento do Gestor de recursos com [AzCopy](storage-use-azcopy.md) ou CopyBlob.

**Pode copiar os ficheiros de Azure utilizando o serviço de importação/exportação de Azure?**

Não, o serviço do Azure importar/exportar apenas suporta bloco Blobs e Blobs de página. Todos os outros tipos de armazenamento incluindo Azure ficheiros, tabelas e filas não são suportados.

**É o serviço do Azure importar/exportar disponíveis para subscrições de CSP?**

Não, o serviço do Azure importar/exportar não suporta subscrições CSP. O suporte técnico será adicionado no futuro.

**Pode posso ignorar o passo de preparação de unidade para uma tarefa de importação ou pode preparar uma unidade sem a copiar?**

Qualquer unidade que pretende enviar para importar dados tem de ser preparada utilizando a ferramenta de cliente do Azure importar/exportar. Tem de utilizar a ferramenta de cliente para copiar dados para a unidade de.

**É necessário executar qualquer preparação do disco quando criar uma tarefa de exportação?**

Não, mas algumas verificações pré são recomendadas. Selecione o número de discos necessários utilizando o comando de PreviewExport a ferramenta de Azure importar/exportar. Para mais informações, consulte o artigo [Pré-visualizar conduzem a utilização de uma tarefa de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). Ajuda-o a pré-visualizar a utilização da unidade para blobs que selecionou, com base no tamanho das unidades que irão utilizar. Também pode verificar que possa ler a partir do e escrever na unidade de disco rígida que será enviada para a tarefa de exportação.

**O que acontece se acidentalmente enviar um disco que não está em conformidade com os requisitos suportados?**

O Centro de dados Azure irá regressar a unidade que não está em conformidade com os requisitos suportados para si. Se apenas algumas das unidades no pacote cumprem os requisitos de suporte, essas unidades serão processadas e as unidades que não cumprir os requisitos serão devolvidas para si.

**Pode cancelar minha tarefa?**

Pode cancelar uma tarefa ao seu estado é criar ou envio.

**Quanto tempo pode ver o estado das tarefas concluídas no portal de clássica?**

Pode ver o estado de tarefas concluídas para até cerca de 90 dias. Tarefas concluídas serão eliminadas após 90 dias.

**Se quiser importar ou exportar mais de 10 unidades, o que devo fazer?**

Uma importação ou exportação tarefa pode referenciar apenas 10 unidades numa única tarefa para o serviço de importar/exportar. Se pretender enviar unidades mais de 10, pode criar várias tarefas. Unidades que estão associadas a mesma tarefa devem ser enviadas em conjunto na mesma embalagem.

**Pode utilizar um adaptador USB SATA que não esteja na lista recomendada?**

Se tiver um conversor de que não estiver listado acima, pode tentar executar a ferramenta de importar/exportar Azure utilizando o conversor para preparar a unidade e ver se funciona antes de adquirir um conversor suportado.

**É que o serviço de formatar as unidades antes de devolvê-los?**

Não. Todas as unidades são encriptadas com BitLocker.

**Pode comprar unidades para importar/exportar tarefas a partir da Microsoft?**

Não. Terá de enviar o seus próprio unidades para ambas as importação e exportação tarefas.

**Após a conclusão da tarefa de importação, qual irá meus dados aspeto na conta de armazenamento? Vai ser preservada meu hierarquia directory?**

Ao preparar uma unidade de disco rígido para uma tarefa de importação, o destino é indicado pela /dstdir: parâmetro. Este é o contentor de destino na conta de armazenamento à qual os dados a partir de unidade de disco rígido são copiados. Dentro deste contentor de destino, directórios virtuais são criados para pastas de unidade de disco rígido e blobs são criados para os ficheiros.

**Se a unidade tiver ficheiros que já existam na minha conta de armazenamento, o serviço substituirá blobs existentes na minha conta de armazenamento?**

Quando preparar-se a unidade, pode especificar se os ficheiros de destino devem ser substituídos ou ignorados utilizando o parâmetro denominado /Disposition: < mudar o nome | substituir não | substituir >. Por predefinição, o serviço irá mudar o nome de novos ficheiros em vez de substituir blobs existentes.

**A ferramenta de cliente do Azure importar/exportar é compatível com sistemas operativos de 32 bits?**
Não. A ferramenta de cliente apenas é compatível com sistemas operativos de 64 bits. Se referem à secção sistemas operativos a [Pré-requisitos](#pre-requisites) para uma lista completa das versões do SO suportadas.

**Deve posso incluir nada que não seja a unidade de disco rígido no meu pacote?**

Fórum são fornecidos apenas unidades de disco rígido. Não inclua itens como cabos de alimentação power ou cabos USB.

**Tenho de ter para o envio meu unidades utilizando FedEx ou DHL?**

Pode enviar unidades ao centro de dados utilizando qualquer transportadora conhecida como FedEx, DHL, UPS ou serviço de Postal-nos. No entanto, para o endereço de envio, as unidades novamente para a partir do Centro de dados, tem de fornecer um número de conta FedEx nos EUA e da UE ou um número de conta DHL nas regiões Ásia e Austrália.

**Existem restrições com o meu unidade internacional de envio?**

Tenha em atenção que os elementos de multimédia físicos que estiver a enviar poderão ter de cruz internacionais limites. É responsável para assegurar que os suporte físico e os dados são importados e/ou exportados em conformidade com a legislação aplicável. Antes envio suportes físicos, certifique-se com o seu autorizados para verificar se os ficheiros de multimédia e dados por motivos legais podem ser enviados para o Centro de dados identificados. Isto irá ajudar a garantir que chega Microsoft forma atempada.

**Ao criar uma tarefa, o endereço de envio é numa localização que é a diferença entre a minha localização de armazenamento em conta. O que devo fazer?**

Algumas localizações de conta de armazenamento são mapeadas para localizações de envio alternativo. Anteriormente localizações disponíveis de envio também podem ser temporariamente mapeadas para localizações alternativas. Verificar sempre o endereço de envio fornecido durante a criação de tarefa antes de enviar as unidades.

**Por que motivo é que o meu estado da tarefa no clássica portal diga "de envio" quando o Web site Carrier mostra o meu pacote é entregue?**

O estado no portal do clássico altera de envio para transferir quando a unidade de processamento é iniciado. Se a unidade de chegou a funcionalidade, mas não tiver iniciado processamento, o estado da tarefa irá mostrar como envio.

**Quando a minha unidade de envio, a transportadora pede o nome de contacto do Centro de dados e o número de telefone. O que posso fornecer?**

O número de telefone é fornecido durante a criação de tarefa. Se precisar de nome de um contacto, contacte-na waimportexport@microsoft.com e irá fornecemos-lhe com essas informações.

**Pode utilizar o serviço do Azure importar/exportar para copiar caixas de correio do PST e dados do SharePoint para o Office 365?**

Consulte [os ficheiros PST de importação ou de dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Pode utilizar o serviço do Azure importar/exportar para copiar offline minhas cópias de segurança para o serviço de cópia de segurança do Azure?**

Consulte [fluxo de trabalho de cópia de segurança Offline no Azure cópia de segurança](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Consulte também:

- [Configurar a ferramenta de cliente do Azure importar/exportar](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transferir dados com o utilitário da linha de comandos AzCopy](storage-use-azcopy.md)

- [Importar Azure exportar REST API exemplo](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

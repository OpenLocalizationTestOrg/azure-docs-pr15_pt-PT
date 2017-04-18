<properties
    pageTitle="Introdução ao armazenamento | Microsoft Azure"
    description="Uma descrição geral de armazenamento do Windows Azure, armazenamento de dados online da Microsoft na nuvem. Saiba como utilizar a melhor solução de armazenamento na nuvem disponível nas aplicações."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introdução ao armazenamento do Microsoft Azure

## <a name="overview"></a>Descrição geral

Armazenamento Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem de durabilidade, disponibilidade e escalabilidade para satisfazer as necessidades dos seus clientes. Ao ler este artigo, os programadores, profissionais de TI e decisões empresariais criadores podem obter informações sobre:

- O que é o armazenamento do Windows Azure e como pode tirar partido do mesmo no seu nuvem, móvel, servidor e aplicações de ambiente de trabalho
- Que tipos de dados pode armazenar com os serviços de armazenamento do Windows Azure: partilhas de ficheiros e dados (objecto), os dados da tabela NoSQL, mensagens em fila de blob.
- Como é gerido acesso aos dados de armazenamento do Windows Azure
- Como os seus dados de armazenamento do Windows Azure são tornados resistentes através do redundância e replicação
- Que seguinte para criar a sua primeira aplicação de armazenamento do Windows Azure

Para começar a trabalhar rapidamente com o armazenamento do Windows Azure rapidamente, consulte o artigo [Introdução ao Azure armazenamento dentro de cinco minutos](storage-getting-started-guide.md).

Para obter detalhes sobre as ferramentas, bibliotecas e outros recursos para trabalhar com o armazenamento do Windows Azure, consulte o artigo [Próximos passos](#next-steps) abaixo.

## <a name="what-is-azure-storage"></a>O que é o armazenamento do Windows Azure?

Permite aos novos cenários para aplicações que exijam armazenamento dimensionável, resistente e altamente disponível para os seus dados – que é exatamente porque é que o Microsoft desenvolvido armazenamento do Windows Azure de informática em nuvem. Para além de tornar possíveis para os programadores criem aplicações em grande escala para suportar novos cenários, armazenamento do Windows Azure também fornece a Fundação de armazenamento para máquinas virtuais do Azure, um Testamento mais a sua robustez.

Armazenamento Azure é previstos excederem consideravelmente dimensionável, por isso pode armazenar e processo centenas de terabytes de dados para suportar os cenários de grande dados necessários ao análise científica, financeira e aplicações de multimédia. Ou pode armazenar pequenas quantidades de dados necessários para um site público pequenas empresas. Onde se situar às suas necessidades, pode pagar apenas para os dados que está a armazenar. Armazenamento Azure atualmente armazena dezenas de trillions de objetos de cliente exclusivo e alças de em média milhões de pedidos de por segundo.

O armazenamento do Azure está flexível, para que possa estruturar aplicações para uma audiência maior global e dimensionar nessas aplicações conforme necessário - tanto em termos a quantidade de dados armazenados e o número de pedidos de feitos contra-lo. Pode pagar apenas para os que utiliza e apenas quando a utilizá-lo.

Armazenamento Azure utiliza um sistema de criação automática de partições que automaticamente carga saldos os dados com base no tráfego. Isto significa que como pedidos de aumentar a aplicação, armazenamento do Windows Azure atribui automaticamente os recursos adequados para cumpri-los.

O armazenamento do Azure está acessível a partir de qualquer lugar no mundo, a partir de qualquer tipo de aplicação, se está a ser executado na nuvem, no ambiente de trabalho, num servidor no local ou um telemóvel ou tablet dispositivo. Pode utilizar o armazenamento do Windows Azure em cenários móveis onde a aplicação armazena um subconjunto de dados no dispositivo e sincroniza-lo com um conjunto completo de dados armazenados na nuvem.

Armazenamento Azure suporta clientes utilizando um conjunto diversificado de sistemas operativos (incluindo Windows e Linux) e uma variedade de linguagens de programação (incluindo .NET, Java, Node.js, Python, Rubi, PHP e C++ e linguagens de programação móveis) para o desenvolvimento conveniente. Armazenamento Azure também expõe recursos de dados através do simples REST APIs, que estão disponíveis para qualquer cliente capaz de envio e recepção de dados através de HTTP/HTTPS.

Armazenamento de Premium Azure fornece suporte do disco de alto desempenho, baixa latência para e/s intensivos das cargas de trabalho em execução no máquinas virtuais do Azure. Com o armazenamento do Azure Premium, pode anexar vários discos persistente dados a uma máquina virtual e configurá-los aos seus requisitos de desempenho. Cada disco de dados é cópias por um disco SSD no Azure Premium armazenamento para um desempenho e/s máximo. Consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md) para obter mais detalhes.

## <a name="introducing-the-azure-storage-services"></a>Introdução aos serviços de armazenamento Azure

Armazenamento Azure fornece os seguintes quatro serviços: Blob armazenamento, armazenamento de tabela, armazenamento de fila de espera e armazenamento de ficheiros.

- Armazenamento de BLOBs armazena dados de objeto não estruturados. Um BLOBs podem ser qualquer tipo de texto ou dados binários, como um documento, ficheiro de multimédia ou instalador de aplicações. Armazenamento de BLOBs é também conhecido como o armazenamento do objeto.
- Armazenamento de tabela armazena conjuntos de dados estruturados. Armazenamento de tabela é um arquivo de dados com o atributo chave NoSQL, que permite desenvolvimento rápido e de acesso rápido às grandes quantidades de dados.
- Armazenamento de filas fornece mensagens fiável para processamento de fluxo de trabalho e para a comunicação entre os componentes de serviços em nuvem.
- Armazenamento de ficheiros oferece partilhado armazenamento para as aplicações mais antigas utilizando o protocolo SMB padrão. Azure máquinas virtuais e serviços em nuvem que podem partilhar dados do ficheiro em componentes da aplicação através do partilhas montadas e aplicações de no local podem aceder a dados de uma partilha através do serviço de ficheiro REST API do ficheiro.

Uma conta de armazenamento Azure é uma conta segura que dá-lhe acesso a serviços de armazenamento do Windows Azure. A conta de armazenamento fornece o espaço de nomes exclusivo para os recursos de armazenamento. A imagem abaixo mostra as relações entre os recursos de armazenamento Azure numa conta de armazenamento:

![Recursos de armazenamento Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Armazenamento de BLOBs

Para utilizadores com grandes quantidades de dados do objeto não estruturados armazenar na nuvem, o armazenamento de BLOBs oferece uma solução rentável e dimensionável. Pode utilizar o armazenamento de BLOBs para armazenar conteúdo tal como:

- Documentos
- Dados de rede social como fotografias, vídeos, música e blogues
- Cópias de segurança dos ficheiros, computadores, bases de dados e dispositivos
- Imagens e texto para aplicações web
- Dados de configuração de aplicações na nuvem
- Dados grandes, como registos e outros conjuntos de dados grandes

Cada blob é organizada num contentor. Contentores também fornecem uma forma útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de membros em contentores e um contentor pode incluir qualquer número de blobs, por excesso para o limite de capacidade 500 TB da conta de armazenamento.  

Blob ofertas três os tipos de armazenamento de blobs, bloquear blobs, acrescentar blobs e blobs de página (discos).

- Bloquear blobs estão otimizados para streaming e armazenar objetos na nuvem e são uma ótima escolha para armazenar documentos, ficheiros de multimédia, etc as cópias de segurança.
- Acrescentar blobs são semelhantes às blobs bloco, mas estão otimizados para acrescentar operações. Um BLOBs acrescentar podem ser atualizados apenas ao adicionar um novo bloco para o fim. Acrescentar blobs são uma boa escolha para cenários como registo, onde os novos dados tem de ser escritos apenas para o fim do blob.
- Página blobs estão otimizados para que representa IaaS discos e suporte aleatório escreve e pode ser até 1 TB de tamanho. Uma rede de máquina virtual Azure anexados IaaS disco é um VHD armazenado como um blob de página.

Para muito grandes conjuntos de dados onde as restrições de rede tornar o carregamento ou transferência dados ao armazenamento Blob sobre o fio irrealistas pode enviar uma unidade de disco rígido para a Microsoft para importar ou exportar os dados diretamente a partir do Centro de dados. Consulte o artigo [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para Blob armazenamento](storage-import-export-service.md).

## <a name="table-storage"></a>Armazenamento de tabela

Aplicações modernas com frequência de procura arquivos de dados com maior escalabilidade e flexibilidade que gerações anterior do software necessário. Armazenamento de tabela oferece armazenamento altamente disponível, previstos excederem consideravelmente dimensionável, para que a sua aplicação pode dimensionar automaticamente para cumprir o pedido de utilizador. Armazenamento de tabela é arquivo de chave/atributo de NoSQL da Microsoft – que tem uma estrutura schemaless, tornando diferente a partir de bases de dados relacionais tradicionais. Com um arquivo de dados schemaless, é fácil a adaptar-se os dados como as necessidades da sua evolve de aplicação. Armazenamento de tabela é fácil de utilizar, para que os programadores podem criar aplicações rapidamente. Acesso aos dados é rápido e rentável para todos os tipos de aplicações.  Armazenamento de tabela é normalmente significativamente inferior na custo tradicional SQL para semelhantes volumes de dados.

Armazenamento de tabela é um arquivo de chave de atributo, que significa que está armazenado cada valor numa tabela com um nome de propriedade escrito. O nome da propriedade pode ser utilizado para filtrar e especificar critérios de seleção. Uma coleção de propriedades e os respetivos valores incluir uma entidade. Desde que esteja schemaless armazenamento de tabela, duas entidades na mesma tabela podem conter coleções de sites diferentes das propriedades e as propriedades que podem ser de diferentes tipos.

Pode utilizar o armazenamento de tabela para armazenar flexíveis conjuntos de dados, tais como os dados de utilizador para aplicações web, livros de endereços, informações sobre o dispositivo e qualquer outro tipo de metadados que requer o seu serviço.  Pode armazenar qualquer número de entidades numa tabela e uma conta de armazenamento pode conter qualquer número de tabelas, por excesso para o limite de capacidade da conta de armazenamento.

Como Blobs e filas, podem gerir e tabela do access protocolos de armazenamento através do resto padrão, no entanto, armazenamento de tabela também suporta um subconjunto do protocolo de OData, simplificar os avançadas consultar capacidades e ativação JSON e AtomPub (XML com base) os programadores de formatos.

Para as aplicações baseados na Internet hoje, NoSQL bases de dados como o armazenamento de tabelas fornecem uma alternativa mais popular para bases de dados relacionais tradicionais.

## <a name="queue-storage"></a>Armazenamento de fila de espera

Estruturar os pedidos de escala, componentes da aplicação com frequência são desacoplados, para que estes podem dimensionar de forma independente. Armazenamento de filas fornece uma solução de mensagens fiável para comunicação assíncrona entre os componentes de aplicação, se estiverem em execução na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. Armazenamento de filas também suporta a gerir tarefas assíncronas e de construção de fluxos de trabalho do processo.

Uma conta de armazenamento pode conter qualquer número de filas. Uma fila pode conter qualquer número de mensagens, por excesso para o limite de capacidade da conta de armazenamento. As mensagens individuais podem ser até 64 KB de tamanho.

## <a name="file-storage"></a>Armazenamento de ficheiros

Armazenamento de ficheiros Azure oferece partilhas de ficheiros SMB baseado na nuvem, para que pode migrar aplicações mais antigas que dependem de partilhas de ficheiros para Azure sem gravações e rapidamente. Com o armazenamento de ficheiros do Azure, aplicações em execução no Azure máquinas virtuais ou serviços em nuvem podem montagem uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho montagens uma partilha SMB típica. Qualquer número de componentes de aplicações, em seguida, pode montagem e aceder a partilha de armazenamento de ficheiros em simultâneo.

Uma vez que uma partilha de armazenamento de ficheiros é uma partilha de ficheiros SMB padrão, aplicações em execução no Azure podem aceder a dados na partilha através do sistema de ficheiros e/s APIs. Os programadores, por conseguinte, podem tirar partido as respetivas código existente e competências para migrar as aplicações existentes. Profissionais de TI podem utilizar os cmdlets do PowerShell para criar, montagem e gerir partilhas de armazenamento de ficheiros como parte da administração do Azure aplicações.

Como os outros serviços de armazenamento Azure, o armazenamento de ficheiros expõe um REST API para aceder aos dados de uma partilha. Aplicações no local, podem ligar o armazenamento de ficheiros REST API para aceder a dados de uma partilha de ficheiro. Desta forma, uma empresa pode optar por migrar algumas aplicações mais antigas para Azure e continuar a executar outras pessoas na sua própria organização. Tenha em atenção que só é possível aplicações que são executadas no Azure; montagem uma partilha de ficheiros uma aplicação no local só pode aceder a partilha de ficheiros através do REST API.

Aplicações distribuídas também podem utilizar o armazenamento de ficheiros para armazenar e partilhar dados da aplicação úteis e desenvolvimento e ferramentas de teste. Por exemplo, uma aplicação poderá armazenar ficheiros de configuração e copia os dados de diagnóstico como registos, métricas e falha num ficheiro armazenamento partilhar para que fiquem disponíveis para vários máquinas virtuais ou funções. Os programadores e para os administradores podem armazenar utilitários que precisam de criar ou gerir uma aplicação de uma partilha de armazenamento do ficheiro que está disponível para todos os componentes, em vez de instalá-las no cada máquina virtual ou instância de função.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Acesso ao Blob, tabela, fila de espera e recursos de ficheiro

Por predefinição, apenas o proprietário da conta de armazenamento pode aceder a recursos na conta de armazenamento. Para a segurança dos seus dados, têm de ser autenticado cada pedido relativamente a recursos na sua conta. Autenticação baseia-se num modelo de chave partilhada. BLOBs também podem ser configurados para suportar uma autenticação anónima.

A conta de armazenamento é atribuída duas teclas de acesso privado na criação que são usadas para autenticação. Está a ter duas chaves assegura que as aplicações do permanecem disponíveis quando que regularmente gerar novamente as teclas como recomendado de gestão de chaves de segurança comuns.

Se precisar de permitir que utilizadores controlado acesso aos seus recursos de armazenamento, em seguida, pode criar uma assinatura de acesso partilhado. Uma assinatura de acesso partilhado (SA) é um token que pode ser anexado a um URL que ativa o acesso delegado para um recurso de armazenamento. Qualquer pessoa que possua o token pode aceder ao recurso, aponte para com as permissões que especifica, para o período de tempo que a mesma é válida. Começando com a versão 2015-04-05, armazenamento do Windows Azure suporta dois tipos de assinaturas acesso partilhado: SA de serviço e SA de conta.

O serviço SA delegados acesso a um recurso de apenas uma dos serviços de armazenamento: o serviço BLOBs, fila de espera, tabela ou ficheiro.

Uma conta SA delegados acesso aos recursos num ou mais dos serviços de armazenamento. Pode delegar a acesso a operações de nível de serviço que não estão disponíveis com um serviço SA. Também pode delegar acesso a ler, escrever e eliminar operações em contentores de BLOBs, tabelas, filas e partilhas de ficheiros que não são permitidas com um serviço SA.

Por fim, pode especificar que um contentor e respetivos blobs ou um blob específico, estão disponíveis para o acesso do público. Quando indicar que um contentor ou blob for público, qualquer pessoa pode lê-la de forma anónima; Não é necessária nenhuma autenticação.  Públicos contentores e blobs são úteis para expor recursos, tais como multimédia e documentos que são alojados no Web site.  Para diminuir a latência da rede para uma audiência global, pode colocar na cache dados blob utilizados pelo Web sites com a CDN Azure.

Consulte o artigo [Utilizar partilhada acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações sobre assinaturas de acesso partilhado. Consulte o artigo [Gerir anónimo acesso de leitura contentores e blobs](storage-manage-access-to-resources.md) e [autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) para mais informações sobre acesso seguro a sua conta de armazenamento.

## <a name="replication-for-durability-and-high-availability"></a>Replicação para durabilidade e elevada disponibilidade

Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para garantir durabilidade e disponibilidade de alta. A replicação copia os seus dados, no Centro de dados do mesmo ou a um segundo Centro de dados, consoante a opção replicação que escolher. A replicação protege os seus dados e preserva o tempo de cima aplicação trabalho falhas de hardware breves. Se os seus dados são replicados para um segundo Centro de dados, que também protege os seus dados relativamente a uma falha grave na localização principal.

A replicação garante que a sua conta de armazenamento cumpre o [Contrato de nível de serviço (SLA) para o armazenamento do](https://azure.microsoft.com/support/legal/sla/storage/) mesmo no falhas. Consulte o artigo que SLA para obter informações sobre o armazenamento do Windows Azure garantias para durabilidade e disponibilidade. 

Quando cria uma conta de armazenamento, pode selecionar uma das seguintes opções de replicação:  

- **Armazenamento localmente redundante (LRS).** Armazenamento localmente redundante mantém três cópias dos seus dados. LRS é replicada três vezes dentro de um único centro de dados numa única região. LRS protege os dados de falhas de normal hardware, mas não a partir de falha de um único centro de dados.  

    LRS é fornecida com um desconto. Para durabilidade máxima, recomendamos que utilize armazenamento geo redundantes, descrito abaixo.


- **Armazenamento de zona redundantes (ZRS).** Armazenamento de zona redundantes mantém três cópias dos seus dados. ZRS é replicada três vezes através de dois ou três instalações, dentro de uma única região ou entre as duas regiões, fornecendo durabilidade mais elevada que LRS. ZRS assegura que os dados estiverem resistentes numa única região.  

    ZRS fornece um nível de durabilidade mais elevado LRS; No entanto, para durabilidade máxima, recomendamos que utilize armazenamento geo redundantes, descrito abaixo.  

    > [AZURE.NOTE] ZRS atualmente só está disponível para blobs bloco e só é suportada para versões de 2014-02-14 e versões posteriores.
    >
    > Assim que tiver criar a sua conta de armazenamento e selecionada ZRS, não é possível converter para utilizar para qualquer outro tipo de replicação, ou vice versa.

- **Armazenamento Geo redundantes (GRS)**. GRS mantém seis cópias dos seus dados. Com GRS, os seus dados são replicados três vezes no interior da região principal e também são replicados três vezes numa região secundária centenas de milhas afastando-o a região principal, fornecer o nível mais alto de durabilidade. Em caso de falha na região de principal, o armazenamento do Windows Azure irá activação pós-falha à região de secundário. GRS assegura que os dados estiverem resistentes nas duas áreas em separado.

    Para obter informações sobre principais e secundários pares por região, consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/).

- **Acesso de leitura armazenamento geo redundantes (GRS RT)**. Acesso de leitura geo redundantes armazenamento replica os seus dados para uma localização geográfica secundária e também fornece acesso de leitura para os seus dados na localização secundário. Acesso de leitura geo redundantes armazenamento permite-lhe aceder aos seus dados a partir da página principal ou a localização da secundária, no caso de uma localização torna-se indisponível. Armazenamento de geo redundantes de acesso de leitura é a opção de predefinida para a sua conta de armazenamento por predefinição, quando o criar. 

    > [AZURE.IMPORTANT] Pode alterar o modo como os seus dados são replicados depois de ter sido criada a sua conta de armazenamento, a menos que especificou ZRS quando criou a conta. No entanto, tenha em atenção que poderá implicam uma transferência de dados únicos adicionais se mudar do LRS para GRS ou RT GRS de custo.

Consulte o artigo [replicação de armazenamento do Windows Azure](storage-redundancy.md) para detalhes adicionais sobre as opções de replicação de armazenamento.

Para informações de preços de replicação de conta de armazenamento, consulte o artigo [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/#services) para obter mais informações sobre que serviços estão disponíveis em cada região.

Para obter detalhes arquitetura sobre durabilidade com armazenamento do Windows Azure, consulte o artigo [SOSP papel - armazenamento do Windows Azure: A altamente disponíveis armazenamento serviço em nuvem com forte consistência](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir dados de armazenamento Azure e para

Pode utilizar o utilitário da linha de comandos AzCopy para copiar blob, ficheiros e dados da tabela dentro da sua conta de armazenamento ou em contas de armazenamento. Para mais informações, consulte [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md) .

AzCopy é criado na parte superior da [Biblioteca de movimento de dados do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que está atualmente disponível na pré-visualização.

O serviço do Azure importar/exportar fornece uma maneira importar dados de BLOBs para ou exportar dados de BLOBs da sua conta de armazenamento através de um disco rígido tenha enviado para o Centro de dados Azure. Para mais informações sobre o serviço de importação/exportação, consulte o artigo [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados ao armazenamento Blob](storage-import-export-service.md).

## <a name="pricing"></a>Preços

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>APIs de armazenamento, bibliotecas e ferramentas

Recursos de armazenamento Azure podem ser acedidos por qualquer idioma que pode fazer pedidos HTTP/HTTPS. Para além disso, o armazenamento do Windows Azure oferece programação bibliotecas para vários idiomas populares. Estas bibliotecas simplificam muitos aspetos de trabalhar com o armazenamento do Windows Azure por processamento detalhes, como invocação síncrona e assíncrona, lotes de operações, gestão de exceção, número de tentativas automático, comportamento operacional e assim sucessivamente. Bibliotecas estão atualmente disponíveis para os idiomas e plataformas, com outras pessoas no pipeline de seguintes:

### <a name="azure-storage-data-services"></a>Serviços de dados de armazenamento Azure

- [Serviços de armazenamento REST API](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Biblioteca de armazenamento do cliente para .NET, Windows Phone e Runtime do Windows](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Biblioteca de armazenamento do cliente para C++](https://github.com/Azure/azure-storage-cpp)
- [Biblioteca de armazenamento do cliente para Java/Android](/develop/java/)
- [Biblioteca de armazenamento do cliente para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Biblioteca de armazenamento do cliente para PHP](/develop/php/)
- [Biblioteca de armazenamento do cliente para Rubi](/develop/ruby/)
- [Biblioteca de armazenamento do cliente para Python](/develop/python/)
- [Cmdlets de armazenamento para o PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Serviços de gestão de armazenamento Azure

- [Referência da API do resto do armazenamento recurso fornecedor](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Biblioteca de cliente do armazenamento recurso fornecedor para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Cmdlets do fornecedor de recursos de armazenamento para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [API do resto serviço Gestão de armazenamento (clássico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Serviços de movimento Azure armazenamento de dados

- [API do resto de serviço de importação/exportação de armazenamento](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Biblioteca de cliente do armazenamento dados movimento para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Explorador de armazenamento Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Ferramentas de cliente do armazenamento Azure](storage-explorers.md)
- [SDK Azure e ferramentas](https://azure.microsoft.com/tools/)
- [Armazenamento Azure emulador](http://www.microsoft.com/download/details.aspx?id=43709)
- [Azure PowerShell](../powershell-install-configure.md)
- [AzCopy utilitário de linha de comandos](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o armazenamento do Windows Azure, explore estes recursos:

### <a name="documentation"></a>Documentação

- [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Para administradores

- [Utilizar o Azure PowerShell com armazenamento Azure](storage-powershell-guide-full.md)
- [Utilizar o clip Azure com armazenamento Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Para programadores .NET

- [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md)
- [Introdução ao armazenamento de tabela do Azure utilizando o .NET](storage-dotnet-how-to-use-tables.md)
- [Introdução ao armazenamento de fila Azure utilizando o .NET](storage-dotnet-how-to-use-queues.md)
- [Introdução ao armazenamento de ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Para programadores Java/Android

- [Como utilizar o armazenamento de Blobs do Java](storage-java-how-to-use-blob-storage.md)
- [Como utilizar o armazenamento de tabela a partir do Java](storage-java-how-to-use-table-storage.md)
- [Como utilizar o armazenamento de fila de Java](storage-java-how-to-use-queue-storage.md)
- [Como utilizar o armazenamento de ficheiros a partir do Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Para programadores Node.js

- [Como utilizar o armazenamento de Blobs do Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Como utilizar o armazenamento de tabela a partir do Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Como utilizar o armazenamento de fila de Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para programadores PHP

- [Como utilizar o armazenamento de Blobs do PHP](storage-php-how-to-use-blobs.md)
- [Como utilizar o armazenamento de tabela a partir do PHP](storage-php-how-to-use-table-storage.md)
- [Como utilizar o armazenamento de fila de PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para programadores Rubi

- [Como utilizar o armazenamento de Blobs do Rubi](storage-ruby-how-to-use-blob-storage.md)
- [Como utilizar o armazenamento de tabela a partir do Rubi](storage-ruby-how-to-use-table-storage.md)
- [Como utilizar o armazenamento de fila de Rubi](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para programadores Python

- [Como utilizar o armazenamento de Blobs do Python](storage-python-how-to-use-blob-storage.md)
- [Como utilizar o armazenamento de tabela a partir do Python](storage-python-how-to-use-table-storage.md)
- [Como utilizar o armazenamento de fila de Python](storage-python-how-to-use-queue-storage.md)
- [Como utilizar o armazenamento de ficheiros a partir do Python](storage-python-how-to-use-file-storage.md)

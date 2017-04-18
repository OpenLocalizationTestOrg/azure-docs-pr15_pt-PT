<properties
    pageTitle="DocumentDB armazenamento e um desempenho | Microsoft Azure" 
    description="Saiba mais sobre o armazenamento de dados e armazenamento de documentos no DocumentDB e como pode dimensionar DocumentDB para satisfazer as necessidades de capacidade da sua aplicação." 
    keywords="armazenamento de documentos"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>Saiba mais sobre o armazenamento e desempenho previsível aprovisionamento no DocumentDB
Azure DocumentDB é um totalmente gerida, dimensionável orientados NoSQL da base de dados serviço para documentos JSON. Com DocumentDB, não tem de proceder ao aluguer máquinas virtuais, implementar o software ou monitorizar bases de dados. DocumentDB é operado e continuamente monitorizada por engenheiros da Microsoft para entregar a disponibilidade de classe do mundo, desempenho e proteção de dados.  

Pode começar a trabalhar com DocumentDB através da [criação de uma conta de base de dados](documentdb-create-account.md) e uma [DocumentDB base de dados](documentdb-create-database.md) através do [Azure portal](https://portal.azure.com/). DocumentDB bases de dados são oferecidas no armazenamento de unidades da unidade de estado sólido (SSD) cópias e débito. Configurações para estas unidades de armazenamento através da [criação de coleções de base de dados](documentdb-create-collection.md) existentes na sua conta de base de dados, cada uma das coleções com débito reservada que pode ser dimensionado para cima ou para baixo em qualquer momento para satisfazer as necessidades da sua aplicação. 

Se a aplicação exceder seu débito reservado para coleções de um ou vários, pedidos estão limitados numa base por coleção. Isto significa que alguns pedidos de aplicação poderão ter êxito enquanto outras pessoas podem ser limitadas.

Este artigo fornece uma descrição geral de recursos e métricas disponíveis para gerir capacidade e plano de armazenamento de dados. 

## <a name="database-account"></a>Conta de base de dados
Como um subscritor do Azure, pode aprovisionar um ou mais contas de base de dados de DocumentDB para gerir os seus recursos de base de dados. Cada subscrição está associada uma subscrição Azure única. 

Contas DocumentDB podem ser criadas através do [Azure portal](documentdb-create-account.md), ou ao utilizar [um modelo de processador ou clip de Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bases de dados
Uma base de dados DocumentDB única pode conter praticamente uma quantidade ilimitada de armazenamento de documentos agrupado em coleções de sites. Coleções de fornecem isolamento de desempenho - cada uma das coleções pode ser aprovisionada com débito que não é partilhado com outras coleções de sites na mesma base de dados ou conta. Uma base de dados DocumentDB é flexível em tamanho, que se situa entre GB para armazenamento de documentos TBs de SSD cópias e aprovisionado débito. Ao contrário de uma base de dados RDBMS tradicional, uma base de dados DocumentDB não está confinado a um único computador e pode abranger várias máquinas ou clusters.  

Com DocumentDB, que precisar para dimensionar as suas aplicações, pode criar coleções de mais ou bases de dados ou ambos. Bases de dados podem ser criados através de do [Azure portal](documentdb-create-database.md) ou através de qualquer uma do [SDK DocumentDB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Coleções de base de dados
Cada base de dados DocumentDB pode conter um ou mais coleções de sites. Coleções de funcionam como a partições dados altamente disponíveis para processamento de armazenamento de documentos e. Cada uma das coleções pode armazenar documentos com heterogéneo esquema. DocumentDB a indexação automática e as capacidades de consulta permitem-lhe filtrar e obter documentos facilmente. Uma coleção de fornece o âmbito de execução de armazenamento e consulta do documento. Uma coleção de também é um domínio da transação para todos os documentos nela contidas. Coleções de sites são atribuídas débito baseado no valor definido no portal do Azure ou através dos SDK. 

Coleções de sites são automaticamente divididas numa ou mais servidores físicos por DocumentDB. Quando cria uma coleção, pode especificar o débito aprovisionado em termos de unidades pedido por segundo e uma propriedade de chave partição. O valor desta propriedade é utilizado por DocumentDB para distribuir documentos entre a partições e encaminhar os pedidos de como consultas. O valor de chave partição também funciona como o limite da transação para procedimentos armazenados e accionadores. Cada coleção tem uma quantidade reservada de débito específica para essa coleção que não é partilhada com outras coleções de sites na mesma conta. Por isso, é possível dimensionar a aplicação tanto em termos de armazenamento e débito. 

Coleções de sites podem ser criadas através de do [Azure portal](documentdb-create-collection.md) ou através de qualquer uma do [SDK DocumentDB](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Pedido de unidades e operações de base de dados

Quando cria uma coleção de, é reservar débito em termos de [unidades pedido (RU)](documentdb-request-units.md) por segundo. Em vez de pensar sobre e gestão de recursos de hardware, poderá pensar uma **unidade de pedido** como uma medida única para os recursos necessários para realizar várias operações de base de dados e um pedido de aplicação de serviço. Uma operação de leitura de um documento de 1 KB consome o mesmo 1 RU independentemente do número de itens armazenados na coleção de ou o número de pedidos em simultâneo a executar ao mesmo. Todos os pedidos contra DocumentDB, incluindo as operações complexas como consultas SQL têm um valor de RU previsível que pode ser determinado altura de desenvolvimento. Se souber o tamanho dos seus documentos e a frequência de cada operação (lê, escritas e consultas) para suportar para a sua aplicação, pode aprovisionar o montante exacto de débito para satisfazer as necessidades da sua aplicação e dimensionar a base de dados para cima e para baixo, tal como o desempenho do seu suas necessidades. 

Cada uma das coleções pode estar reservada com débito em blocos de 100 unidades pedido por segundo, a partir do centenas até milhões de unidades pedido por segundo. O débito aprovisionado pode ser ajustado durante a vida de uma coleção de adaptar as necessidades de processamento de alteração e aceder a padrões da sua aplicação. Para mais informações, consulte o artigo [níveis de desempenho DocumentDB](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Coleções de sites estão entidades cobrar. O custo é determinado pelo débito aprovisionado da coleção de medida em unidades de pedido por segundo juntamente com o armazenamento total consumo em gigabytes. 

Quantas unidades pedido irá consumir uma operação em particular como inserir, eliminar, consulta ou execução do procedimento armazenado Uma unidade de pedido é uma medida normalizada de custo de processamento do pedido. Uma operação de leitura de um documento de 1 KB é 1 RU, mas como um pedido da inserir, substituir ou eliminar o mesmo documento irá consumir mais processamento de serviço e portanto mais unidades de pedido. Cada resposta do serviço inclui um cabeçalho personalizado (`x-ms-request-charge`) que os relatórios as unidades de pedido consumidas para o pedido. Este cabeçalho também está acessível através de [SDK](documentdb-sdk-dotnet.md). No .NET SDK, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) é uma propriedade do objeto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Se pretender estimar as suas necessidades débito antes de efetuar uma chamada única, pode utilizar o [Planeador de capacidade](documentdb-request-units.md#estimating-throughput-needs) para o ajudar com esta estimativa. 

>[AZURE.NOTE] O plano base 1 unidade pedido para um documento de 1 KB corresponde ao obter simple do documento com [Consistência sessão](documentdb-consistency-levels.md). 

Existem vários fatores que causam impacto nas unidades de pedido consumidas para uma operação de uma conta de base de dados DocumentDB. Incluem seguintes fatores:

- Tamanho do documento. À medida que os tamanhos de documento aumentam as unidades consumidas para ler ou escrever que os dados também irão aumentar.
- Contagem de propriedade. Pressupondo que a indexação predefinido de todas as propriedades, as unidades consumidas para escrever um documento aumentará como os aumentos de contagem de propriedade.
- Consistência dos dados. Ao utilizar níveis de consistência de dados de forte ou delimitada Staleness, serão consumidas unidades adicionais para ler documentos.
- Propriedades indexadas. Uma política de índice remissivo cada coleção determina quais são as propriedades estão indexadas por predefinição. Pode reduzir o consumo de unidade pedido ao limitar o número das propriedades indexadas. 
- Indexação do documento. Por predefinição que cada documento é indexado automaticamente, irá consumir menos unidades de pedido se optar por não indexar alguns dos seus documentos.

Para mais informações, consulte o artigo [unidades de pedido de DocumentDB](documentdb-request-units.md). 

Por exemplo, eis uma tabela que mostra quantas unidades pedido aprovisionar três tamanhos de documento diferente (1KB, 4KB e 64KB) e em dois níveis diferentes de desempenho (500 lê/segundo escritas/segundo de 100 e 500 lê/segundo + 500 escritas/segundo). A consistência de dados tiver sido configurada na sessão e, a política de indexação foi definida para nenhum.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamanho do documento</strong></p></td>
            <td valign="top"><p><strong>Lê/segundo</strong></p></td>
            <td valign="top"><p><strong>Escritas/segundo</strong></p></td>
            <td valign="top"><p><strong>Pedido de unidades</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

Consultas, procedimentos armazenados e accionadores consumam unidades pedido com base na complexidade das operações a ser executado. À medida que desenvolver a sua aplicação, inspecionar no cabeçalho da encargo pedido para compreender melhor como cada operação está a consumir a capacidade de unidade pedido.  


## <a name="choice-of-consistency-level-and-throughput"></a>Escolha de nível de consistência e débito
Escolha do nível de consistência predefinido tem um impacto na débito e latência. Pode definir o nível de consistência predefinido através de programação e através do portal do Azure. Também pode substituir o nível de consistência numa base por pedido. Por predefinição, o nível de consistência é definido como **sessão**, que fornece monótona leitura/escrita e leia as garantias de escrita. Consistência sessão é ótima para aplicações do utilizador centrada e fornece um total ideal da consistência e desempenho compromissos.    

Para obter instruções sobre como alterar o nível de consistência no portal do Azure, consulte o artigo [como gerir uma conta de DocumentDB](documentdb-manage-account.md#consistency). Em alternativa, para obter mais informações sobre os níveis de consistência, consulte o artigo [utilizar consistência níveis](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Documento aprovisionada gerais de armazenamento e índice remissivo
DocumentDB suporta a criação de uma única partição tanto com partições coleções de sites. Cada partição no DocumentDB suporta até 10 GB de armazenamento SSD cópia. 10GB de armazenamento de documento inclui os documentos mais armazenamento para o índice. Por predefinição, uma coleção de DocumentDB está configurada para indexar todos os documentos automaticamente sem necessidade de explicitamente qualquer índices secundários ou esquema. O overhead típica índice com base nas aplicações utilizando DocumentDB, é entre 2-20%. A tecnologia de indexação utilizada pelo DocumentDB assegura que os valores das propriedades, independentemente da sobrecarga de índice remissivo não ultrapasse superior a 80% do tamanho dos documentos com as predefinições. 

Por predefinição todos os documentos são indexados pelo DocumentDB automaticamente. No entanto, se pretender ajustar o overhead de índice remissivo, pode optar por remover determinados documentos de que está a ser indexadas no momento da inserção ou substituição de um documento, tal como descrito nas [DocumentDB políticas de indexação](documentdb-indexing-policies.md). Pode configurar uma colecção de DocumentDB para excluir todos os documentos na coleção da ser indexadas. Também pode configurar uma coleção de DocumentDB para indexar seletivamente apenas determinadas propriedades ou caminhos com carateres universais dos seus documentos JSON, conforme descrito na [configuração da política de uma coleção de indexação](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Também excluir propriedades ou documentos melhora o débito escrita – o que significa que irá consumir menos unidades de pedido.   

## <a name="next-steps"></a>Próximos passos

Para continuar a formação sobre como funciona o DocumentDB, consulte o artigo [dimensionamento no Azure DocumentDB e divisão](documentdb-partition-data.md).

Para obter instruções sobre como monitorizar níveis de desempenho no portal do Azure, consulte [Monitor de uma conta de DocumentDB](documentdb-monitor-accounts.md). Para mais informações sobre como escolher os níveis de desempenho para coleções de sites, consulte o artigo [níveis de desempenho no DocumentDB](documentdb-performance-levels.md).
 

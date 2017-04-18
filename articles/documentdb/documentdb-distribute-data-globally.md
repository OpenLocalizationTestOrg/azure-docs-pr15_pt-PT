<properties
   pageTitle="Distribuir dados globalmente com DocumentDB | Microsoft Azure"
   description="Saiba mais sobre recuperação de replicação geo, activação pós-falha e dados de escala planeta utilizando globais bases de dados a partir do Azure DocumentDB, um serviço de base de dados NoSQL totalmente gerido."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuir dados globalmente com DocumentDB

> [AZURE.NOTE] Distribuição global DocumentDB bases de dados é ativada automaticamente para todas as contas de DocumentDB recentemente criadas e ficará disponível. Estamos a trabalhar para ativar a distribuição global em todas as contas existentes, mas entretanto, se pretender que a distribuição globais ativada na sua conta, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e podemos irá ativá-lo para agora.

Azure DocumentDB foi concebido para satisfazer as necessidades das aplicações IoT que consiste milhões de dispositivos globalmente distribuídos e aplicações de escala de internet entregar experiências altamente responder aos utilizadores em todo o mundo. Estes sistemas de base de dados cara o desafio da realização acesso de latência baixa para dados da aplicação a partir de várias regiões geográficas com garantias de consistência e disponibilidade de dados bem definidos. Como um sistema de base de dados globalmente distribuída DocumentDB simplifica a distribuição global dos dados através da oferta de contas de base de dados totalmente geridas e com várias região que fornecem concessões limpar entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. Contas de base de dados DocumentDB são-lhe fornecidas com elevada disponibilidade, latências de ms dígito simples, vários [níveis de consistência bem definidos] [consistency], transparente activação regional com APIs multi-homing e a capacidade de elastically Dimensionar débito e armazenamento em todo o mundo. 

  
## <a name="configuring-multi-region-accounts"></a>Configurar região com várias contas

Configurar a sua conta de DocumentDB para dimensionar em todo o mundo pode ser realizada em menos de um minuto através do [Azure portal](documentdb-portal-global-replication.md). Tudo o que precisa de fazer é selecione o nível de consistência direita entre vários níveis de consistência bem definidos suportados e associar qualquer número de regiões Azure com a sua conta de base de dados. Os níveis de consistência DocumentDB fornecem concessões limpar entre garantia consistência específica e o desempenho. 

![Bem DocumentDB oferece vários, definidas consistência (média) modelos à escolha][1]

Bem DocumentDB oferece vários, definidas consistência (média) modelos à escolha.

Selecionar o nível de consistência direita depende dados consistência garante as necessidades da aplicação. DocumentDB automaticamente replica os seus dados através de todas as regiões especificadas e garante a consistência que selecionou para a sua conta de base de dados. 


## <a name="using-multi-region-failover"></a>Utilizar várias região activação pós-falha 

Azure DocumentDB for capaz de contas de base de dados de activação pós-falha transparente entre as regiões Azure múltiplos – a nova [API multi-homing] [ developingwithmultipleregions] garantia que a sua aplicação pode continuar a utilizar um ponto final de lógico e não é interrompida pela activação pós-falha. Activação pós-falha é controlado pelo utilizador, fornecendo flexibilidade para mudar a sua base de dados de conta no evento qualquer um dos intervalo de condições de falha possíveis ocorrem, incluindo a aplicação, infraestrutura, serviço ou falhas regionais (reais ou simuladas). Em caso de falha regional DocumentDB, o serviço transparente irá falhar sobre a sua conta de base de dados e continua a sua aplicação para aceder aos dados sem perder disponibilidade. Enquanto DocumentDB oferece [99,99% disponibilidade SLA][sla], pode testar o fim da sua aplicação para propriedades de disponibilidade de fim ao simulação de uma falha de regional ambas as, [através de programação] [ arm] , bem como através do Portal do Azure.


## <a name="scaling-across-the-planet"></a>Dimensionamento ao longo do planeta
DocumentDB permite-lhe independentemente aprovisionar débito e consuma armazenamento para cada coleção de DocumentDB em qualquer escala globalmente através de todas as regiões associadas à sua conta de base de dados. Uma coleção de DocumentDB automaticamente é distribuída globalmente e gerida através de todas as regiões associadas à sua conta de base de dados. Coleções de sites dentro da sua conta de base de dados podem ser distribuídas ao longo de qualquer uma das regiões Azure na qual o [serviço de DocumentDB está disponível][serviceregions]. 

O débito compradas e o armazenamento consumidas para cada coleção de DocumentDB está automaticamente aprovisionada através de todas as regiões uniforme. Esta opção permite-a sua aplicação para o dimensionar de forma totalmente integrada entre o globo [pagamentos apenas para o débito e armazenamento estiver a utilizar o dentro de cada hora][pricing]. Por exemplo, se tiver aprovisionado RUs de 2 milhões de uma coleção de DocumentDB, em seguida, cada uma das regiões associadas à sua conta de base de dados obtém RUs de 2 milhões para essa coleção. Isto é ilustrado abaixo.

![Dimensionamento débito para uma coleção de DocumentDB entre as quatro regiões][2]

DocumentDB garantias < 10 ms ler e < 15 ms escrever latências na P99. Os pedidos de leitura nunca do intervalo de limite de centro de dados para garantir os [requisitos de consistência tiver selecionado]a[consistency]. As gravações são sempre quórum consolidada localmente antes de estes são confirmadas para os clientes. Cada conta de base de dados está configurada com prioridade da região de escrita. Região designado com prioridade mais alta irá funcionar como a região atual de escrita da conta. SDK todos os transparente irá encaminhar escritas de conta da base de dados para a área de escrita atual. 

Por fim, uma vez que DocumentDB completamente é [desconhecido esquema] [ vldb] -que nunca tem de se preocupar gerir/atualização de esquemas ou índices secundários através de vários centros de dados. As suas [consultas SQL] [ sqlqueries] continuar a trabalhar enquanto os aplicação e modelos de dados continuam a evoluir. 


## <a name="enabling-global-distribution"></a>Ativar a distribuição globais 

Pode optar por tornar os seus dados localmente ou globalmente distribuídos associando um dos conta da base de dados de uma ou mais regiões Azure com um DocumentDB. Pode adicionar ou remover regiões à sua conta de base de dados em qualquer altura. Para ativar a distribuição global utilizando o portal, consulte o artigo [como efetuar replicação de bases de dados global DocumentDB através do portal Azure](documentdb-portal-global-replication.md). Para ativar a distribuição global forma programática, consulte o artigo [em desenvolvimento com contas de DocumentDB com várias região](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os dados da distribuição globalmente DocumentDB nos seguintes artigos:

* [Débito e armazenamento para uma coleção de aprovisionamento] [throughputandstorage]
* [APIs multi-homing através do resto. .NET, Java, Python e nó SDK] [developingwithmultipleregions]
* [Níveis de consistência no DocumentDB] [consistency]
* [Disponibilidade SLA] [sla]
* [Gerir a conta de base de dados] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md


<properties
   pageTitle="Aplicação web dimensionáveis | Arquitetura de referência Azure | Microsoft Azure"
   description="Melhorar a escalabilidade numa aplicação web em execução no Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Melhorar a escalabilidade numa aplicação web 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo mostra uma arquitetura de recomendado para melhorar a escalabilidade e o desempenho numa aplicação web em execução no Microsoft Azure. A arquitetura constrói [arquitetura de referência Azure: aplicação web básicas][basic-web-app]. As recomendações e considerações sobre a partir desse artigo aplica a esta arquitetura também.

>[AZURE.NOTE] Azure tem dois modelos de implementação diferente: Gestor de recursos e clássica. Este artigo utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

![[0]][0]

A arquitetura tem os seguintes componentes:

- **Grupo de recursos**. Um [grupo de recursos] [ resource-group] é um contentor lógico para recursos Azure. 

- ** [Web app] [ app-service-web-app] ** e ** [API aplicação][app-service-api-app]**. Uma aplicação moderna normal pode incluir um Web site e um ou mais web RESTful APIs. Uma web API poderá ser consumida por clientes de browser através de AJAX, aplicações de cliente nativo ou aplicações do lado do servidor. Considerações na web estruturar APIs, consulte [orientações de estrutura de API][api-guidance].    

- **WebJob**. Utilizar o [Azure WebJobs] [ webjobs] para executar tarefas de execução longa em segundo plano. Pode executar WebJobs numa agenda, continuamente, ou em resposta a um acionador, tal como colocar uma mensagem numa fila. É executado um WebJob como um processo de fundo no contexto de uma aplicação de serviço de aplicação. 

- **Fila de espera**. Na arquitectura mostrada aqui, filas de aplicação tarefas em segundo plano ao colocar uma mensagem para um [Armazenamento do Windows Azure fila] [ queue-storage] fila de espera. A mensagem de uma função a WebJob accionadores. 

    Em alternativa, pode utilizar filas Bus de serviço. Para uma comparação, consulte [Azure filas e filas de serviço Bus - comparados e em contraste com][queues-compared].

- **Cache**. Armazenar dados semiestruturados estáticos no [Azure Redis Cache][azure-redis].  

- **CDN**. Utilizar a [Rede de entrega de conteúdos do Azure] [ azure-cdn] (CDN) para o conteúdo disponível ao público da cache, para latência inferior e de entrega mais rápida de conteúdo.

- **Armazenamento de dados.** Utilizar a [Base de dados do SQL Azure] [ sql-db] para dados relacionais. Para os dados que não sejam relacionais, considere numa loja NoSQL, como o armazenamento de tabela do Azure ou [DocumentDB][documentdb].

- **Azure de pesquisa**. Utilizar a [Pesquisa do Azure] [ azure-search] para adicionar funcionalidades de pesquisa, incluindo sugestões de pesquisa, pesquisa nítida e pesquisa específicas do idioma. Pesquisa Azure é normalmente utilizada em conjunto com outro arquivo de dados, sobretudo se o arquivo de dados principal requer consistência estrita. Esta abordagem, teria os dados autoritativos o arquivo de dados e, colocar o índice de pesquisa para pesquisa Azure. Pesquisa Azure pode também ser utilizada para consolidar um índice de pesquisa simples a partir de vários arquivos de dados.  

- **Correio eletrónico/SMS**. Se a aplicação necessitar enviar correio eletrónico ou as mensagens SMS, utilize um serviço de terceiros como SendGrid ou Twilio, em vez de criar esta funcionalidade diretamente na aplicação.

## <a name="recommendations"></a>Recomendações

### <a name="app-service-apps"></a>Aplicações de serviço de aplicação 

Recomendamos que criar a aplicação web e web API como separadas aplicações de serviço de aplicação. Este modelo permite-lhe executá-los em separado planos de aplicação de serviço, que por sua vez permite-lhe dimensioná-las de forma independente. Caso não precise desse nível de escalabilidade a em primeiro lugar, pode implementar as aplicações para o mesmo plano e mova-os para planos separados mais tarde, se necessário. (Para os planos Basic, Standard e Premium, pode é faturada para as instâncias VM no plano de, não por aplicação. Consulte o artigo [aplicação de serviço de preços][app-service-pricing])

Se pretender utilizar as *Tabelas fácil* ou funcionalidades de *Fácil APIs* da aplicação de serviço móvel aplicações, crie uma aplicação de serviço de aplicação separada para esse efeito.  Estas funcionalidades dependem de um quadro de aplicação específica para ativá-las.

### <a name="webjobs"></a>WebJobs

Se o WebJob for recurso intenso, considere implementá-lo para uma aplicação de serviço de aplicação vazia dentro de um plano de serviço de aplicação separada, para fornecer instâncias dedicadas a WebJob. Consulte [orientações de tarefas do fundo][webjobs-guidance].  

### <a name="cache"></a>Cache

Pode melhorar o desempenho e escalabilidade utilizando [A Cache de Redis Azure] [ azure-redis] para alguns dados em cache. Considere utilizar Redis Cache para:

- Dados da transação semiestruturados estático.

- Estado da sessão.

- Resultado HTML. Isto pode ser útil em aplicações que compor complexa saída em HTML. 

Para obter orientações sobre como criar uma estratégia de cache mais detalhadas, consulte o artigo [Colocação em cache orientações][caching-guidance].

### <a name="cdn"></a>CDN 

Utilizar o [Azure CDN] [ azure-cdn] em conteúdo estático cache. A vantagem principal de uma CDN é reduzir a latência para os utilizadores, uma vez que o conteúdo é colocada em cache a um *servidor edge* que geograficamente se aproxime do utilizador. CDN também pode reduzir a carga da aplicação, uma vez que o tráfego não estiver a ser processado pela aplicação.

- Se a sua aplicação principalmente consiste em páginas estáticas, considere utilizar CDN para a aplicação completa em cache. Consulte o artigo [utilizar CDN Azure na aplicação de serviço de Azure][cdn-app-service].

- Caso contrário, colocar em conteúdo estático, tais como imagens, CSS e HTML ficheiros, para o armazenamento do Windows Azure e utilização CDN para os ficheiros em cache. Consulte o artigo [integrar uma conta de armazenamento com CDN][cdn-storage-account].

> [AZURE.NOTE] Azure CDN não é possível servir conteúdo que requer autenticação.

Para obter orientações mais detalhadas, consulte [orientações de rede de entrega de conteúdos (CDN)][cdn-guidance]. 

### <a name="storage"></a>Armazenamento

Aplicações modernas frequentemente processam grandes quantidades de dados. Para dimensionar para a nuvem, é importante selecionar o tipo de armazenamento à direita. Aqui estão algumas recomendações do plano base.  Para obter orientações mais detalhadas, consulte o artigo [Funcionalidades de arquivo de dados ao avaliar Polyglot persistente soluções de][polyglot-storage].

O que pretende armazenar | Exemplo | Armazenamento recomendado
--- | --- | ---
Ficheiros | Imagens, documentos, PDFs | Armazenamento de Blobs do Azure
Pares valor/chave | Dados de perfil de utilizador procurados por ID de utilizador | Armazenamento de tabela do Azure
Mensagens breves destinam a acionar processamento suplementar | Pedidos de ordem | Armazenamento de filas Azure, fila de Bus serviço ou serviço Bus tópico
Dados não relacionais, com um esquema flexível, que exige o consultar básicas | Catálogo de produtos | Base de dados do documento, como o Azure DocumentDB, MongoDB ou Apache CouchDB
Consulta de dados relacionais, exigir mais rica suporte, esquema restrita e/ou consistência forte | Inventário do produto | Base de dados Azure SQL

## <a name="scalability-considerations"></a>Considerações de escalabilidade

### <a name="app-service-app"></a>Aplicação de serviço de aplicação

Se a solução de incluir várias aplicações de serviço de aplicação, considere implementá-las para separar os planos do serviço de aplicação. Esta abordagem permite-lhe dimensioná-las de forma independente, uma vez que são executadas em instâncias separadas. Para mais informações sobre escalar para fora, consulte o artigo as [Considerações escalabilidade] [ basic-web-app-scalability] secção a [arquitetura de aplicação web básicas][basic-web-app].

Da mesma forma, considere a colocar uma WebJob nos suas próprias plano, para que não são executadas nas instâncias do mesmo processar pedidos de HTTP tarefas em segundo plano.  

### <a name="sql-database"></a>Base de dados SQL

Aumentar escalabilidade de uma base de dados do SQL por *sharding* a base de dados &mdash; ou seja, horizontalmente a partições a base de dados. Sharding permite-lhe dimensionar a base de dados na horizontal, utilizando [Ferramentas de base de dados flexível][sql-elastic]. Potenciais benefícios da sharding incluem:

- Débito da transação melhor.

- Consultas podem executar mais rápido sobre um subconjunto dos dados. 

### <a name="azure-search"></a>Pesquisa Azure

Pesquisa Azure remove o overhead da efetuar pesquisas de dados complexos a partir da loja de dados principal e -pode dimensionar para processar carga. Consulte o artigo [níveis de recursos de escala para consulta e indexação das cargas de trabalho na pesquisa Azure][azure-search-scaling].

## <a name="security-considerations"></a>Considerações de segurança

### <a name="cross-origin-resource-sharing-cors"></a>Recurso de origem cruz partilha (CORS)

Se criar um Web site e da web API como aplicações em separado, o Web site não pode efetuar chamadas de AJAX do lado do cliente para a API a menos que permita CORS. 

> [AZURE.NOTE] A segurança do browser impede que uma página web façam pedidos AJAX para outro domínio. Esta restrição chama-se a política de mesma origem e impede que um site malicioso a partir da leitura sensível a maiúsculas e dados de outro site. CORS é uma norma de W3C que permite a um servidor alargar a política de mesma origem e permitir que alguns pedidos de publicação em origem ao rejeitar outras pessoas.

Serviços de aplicação tem suporte incorporado para CORS, sem ser necessário especificá-las escrever qualquer código da aplicação. Consulte o artigo [consumir uma aplicação de API a partir de JavaScript utilizando CORS][cors]. Adicione o Web site para a lista de origens diferentes permitidos para a API. 

### <a name="sql-database-encryption"></a>Encriptação de base de dados SQL

Utilizar a [Encriptação de dados transparente] [ sql-encryption] se precisar de encriptar dados at rest na base de dados. Esta funcionalidade executa encriptação em tempo real e desencriptar de uma base de dados completa (incluindo cópias de segurança e ficheiros de registo da transação), sem necessidade de alterações à aplicação. Encriptação adicione alguns latência, por isso, é aconselhável para separar os dados que tem de ser seguro para sua própria base de dados e ativar encriptação apenas para essa base de dados.  

## <a name="next-steps"></a>Próximos passos

- Para maior disponibilidade, implementar a aplicação mais do que uma região e utilize o [Gestor de tráfego Azure] [ tm] para activação pós-falha. Para obter mais informações, consulte o artigo [arquitetura de referência Azure: aplicação Web com elevada disponibilidade][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Aplicação Web no Azure com escalabilidade melhorada"
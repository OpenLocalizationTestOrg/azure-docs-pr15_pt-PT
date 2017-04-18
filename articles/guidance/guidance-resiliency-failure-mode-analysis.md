<properties
   pageTitle="Análise de modo a falha | Microsoft Azure"
   description="Diretrizes para efetuar análises de modo de falha para as soluções de nuvem baseadas no Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Orientação do Azure RDP: análise de modo a falha

Análise de modo a falha (FMA) é um processo para criar RDP para um sistema, identificando pontos de falha possíveis no sistema. O FMA deve fazer parte das fases de arquitectura e estrutura, para que pode criar recuperação de falhas no sistema de desde o início.

Eis o processo geral para realizar uma FMA: 

1. Identifique todos os componentes no sistema. Inclua dependências externas, tais como como fornecedores de identidade, serviços de terceiros e assim sucessivamente.   

2. Para cada componente, identifique potenciais falhas que podem ocorrer. Um único componente poderá ter mais do que um modo de falha. Por exemplo, deve ter em consideração falhas de leitura e falhas de escrita em separado, porque os impacto e possível atenuações serão diferentes.

3. Taxa de cada modo de falha de acordo com o risco global. Considere seguintes fatores:  

    - O que há a probabilidade da falha. É relativamente comuns? Extrememly raro? Não precisa de exatos números; o objetivo é para o ajudar a classificar a prioridade.

    - O que é o impacto na aplicação, em termos de disponibilidade, perda de dados, custo monetário e interrupção empresas? 

4. Para cada modo falha, determine como a aplicação irá responder e recuperar. Considere concessões em complexidade de custo e da aplicação.   

Como ponto de partida para o processo de FMA, este artigo contém um catálogo potenciais modos de falha e os respetivos atenuações. O catálogo está organizado por tecnologia ou serviço Azure, assim como uma categoria geral para a estrutura de nível de aplicação. O catálogo não é exaustivo, mas abrange muitos do essencial Azure serviços. 


## <a name="app-service"></a>Aplicação de serviço

### <a name="app-service-app-shuts-down"></a>Aplicação de serviço de aplicação encerra.

**Deteção**. Possíveis causas:

- Encerramento esperado
    
    - Um operador encerra da aplicação; Por exemplo, utilizando o portal Azure.

    - A aplicação foi descarregada porque estava inactivo. (Apenas se a `Always On` definição está desactivada.)

- Encerramento inesperado

    - A aplicação falha.

    - Uma instância de aplicação serviço VM torna-se indisponível.


Registo Application_End vai chamar o encerramento do domínio de aplicação (falha de sistema do processo contornos) e é a única forma de captura o encerramento do domínio de aplicação.

**Recuperação**

- Se o encerramento era esperado, utilize o evento de encerramento a aplicação para encerrar correctamente. Por exemplo, no ASP.NET, utilize o `Application_End` método.

- Se a aplicação foi descarregada enquanto idle, é automaticamente reiniciado no próximo pedido. No entanto, irá assumir "Iniciar fria" custo. 

- Para impedir que a aplicação que está a ser descarregadas enquanto idle, ativar o `Always On` definição no web app. Consulte o artigo [Configurar web apps no serviço de aplicação do Azure][app-service-configure].

- Para impedir um operador de encerrar a aplicação, defina um cadeado de recurso com `ReadOnly` nível. Consulte o artigo [recursos de bloqueio com o Gestor de recursos do Azure][rm-locks].

- Se a aplicação falha ou um VM de serviço de aplicação torna-se indisponível, o serviço de aplicação reinicia automaticamente a aplicação. 


**Diagnósticos**. Registos de início de aplicações e os registos do servidor web. Consulte o artigo [Ativar diagnósticos do registo para as aplicações de web na aplicação de serviço de Azure][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Um utilizador específico repetidamente faz com que os pedidos de incorretas ou overloads o sistema. 

**Deteção**. Autenticar os utilizadores e incluir ID de utilizador nos registos de aplicação.

**Recuperação**

- Utilizar a [Gestão de API do Azure] [ api-management] para pedidos de limitação do utilizador. Consulte o artigo [pedido avançado limitação com a gestão de API do Azure][api-management-throttling]

- Bloquear o utilizador.

**Diagnósticos**. Registar todos os pedidos de autenticação.


### <a name="a-bad-update-was-deployed"></a>Uma atualização incorretas foi implementada.

**Deteção**. Monitorizar o estado de funcionamento da aplicação através do Portal do Azure (consulte o artigo [desempenho da aplicação web Monitor Azure][app-insights-web-apps]) ou implementar o [padrão de monitorização de ponto final de estado de funcionamento][health-endpoint-monitoring-pattern].

**Recuperação**. Utilizar várias [faixas de implementação] [ app-service-slots] e reverter para a implementação de condições de conhecida última. Para obter mais informações, consulte o artigo [arquitetura de referência de aplicação web básicas][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>Autenticação OpenID ligar (OIDC) falha.

**Deteção**. Modos de falha possíveis incluem:

1. Azure AD não está disponível ou não é possível alcançar devido a um problema de rede. Falha de redirecionamento para o ponto final de autenticação e o software OIDC intermédio inicia uma exceção.
2. Inquilino do Azure AD não existe. Redirecionamento para o ponto final de autenticação devolve um código de erro HTTP e o software OIDC intermédio inicia uma exceção.
3. Não é possível autenticar o utilizador. Sem estratégia de deteção é necessária; Azure AD trata falhas de início de sessão.

**Recuperação**

1. Capturas não processadas exceções a partir do software intermédio.
2. Processar `AuthenticationFailed` eventos.
3. Redirecione o utilizador para uma página de erro.
4. Número de tentativas de utilizador.


## <a name="azure-search"></a>Pesquisa Azure

### <a name="writing-data-to-azure-search-fails"></a>Dados de escrita para pesquisa Azure falha.

**Deteção**. Capturas `Microsoft.Rest.Azure.CloudException` erros.

**Recuperação**

A [Pesquisa .NET SDK] [ search-sdk] repetições automaticamente após falhas breves. Quaisquer exceções iniciadas pelo cliente SDK devem ser tratadas como não transitório erros.

A política predefinida para repetir utiliza exponencial back-off. Para utilizar uma política de repetir diferente, contacte o `SetRetryPolicy` na `SearchIndexClient` ou `SearchServiceClient` escolares. Para obter mais informações, consulte o artigo [Repetições automática][auto-rest-client-retry].

**Diagnósticos**. Utilizar [a análise de tráfego pesquisa][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>Ocorre uma falha na leitura de dados a partir da pesquisa do Azure.

**Deteção**. Capturas `Microsoft.Rest.Azure.CloudException` erros.

**Recuperação**

A [Pesquisa .NET SDK] [ search-sdk] repetições automaticamente após falhas breves. Quaisquer exceções iniciadas pelo cliente SDK devem ser tratadas como não transitório erros.

A política predefinida para repetir utiliza exponencial back-off. Para utilizar uma política de repetir diferente, contacte o `SetRetryPolicy` na `SearchIndexClient` ou `SearchServiceClient` escolares. Para obter mais informações, consulte o artigo [Repetições automática][auto-rest-client-retry].

**Diagnósticos**. Utilizar [a análise de tráfego pesquisa][search-analytics].



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>Leitura ou escrita para um nó falha.

**Deteção**. Capturas a exceção. Para clientes do .NET, este será normalmente `System.Web.HttpException`. Outro cliente poderá ter outros tipos de exceção.  Para mais informações, consulte o artigo [processamento de erros de Cassandra concluído para a direita](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Recuperação**

- Cada [cliente Cassandra](https://wiki.apache.org/cassandra/ClientOptions) tem as suas próprias repetir políticas e funcionalidades. Para obter mais informações, consulte o artigo [processamento de erros de Cassandra concluído para a direita][cassandra-error-handling].
- Utilize uma implementação deverá ter em consideração bastidor, com nós de dados distribuídos ao longo dos domínios falhas.
- Implemente em várias regiões com consistência quórum local. Se ocorrer uma falha de não transitório, sobre a falhar outro região.

**Diagnósticos**. Registos de aplicação


## <a name="cloud-service"></a>Serviço na nuvem

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Funções da Web ou de trabalho estão a ser inesperadamente.

**Deteção**. O [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] evento for accionado.

**Recuperação**. Substituir o [RoleEntryPoint.OnStop] [ RoleEntryPoint.OnStop] método para limpar correctamente. Para mais informações, consulte [A forma para a direita a alça de eventos do Azure OnStop] [ onstop-events] (blogue).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>Ler dados a partir do DocumentDB falha.

**Deteção**. Capturas `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Recuperação**

- O SDK automaticamente repetições tentativas falhadas. Para definir o número de tentativas e o tempo de espera máximo, configure `ConnectionPolicy.RetryOptions`. Exceções eleva o cliente são quer para além da política de repetição ou não são breves erros. 

- Se DocumentDB acelera o cliente, devolve um erro de HTTP 429. Verificar o código de estado `DocumentClientException`. Se está a obter o erro 429 forma consistente, considere aumentar o valor de débito da coleção de DocumentDB.

- Criar uma réplica da base de dados DocumentDB ao longo de duas ou mais regiões. Todas as réplicas são legíveis. Utilizar o cliente SDK, especifique o `PreferredLocations` parâmetro. Este é uma lista ordenada das regiões Azure. Todos os lê serão enviadas para a primeira região disponível na lista. Se o pedido falhar, o cliente vai tentar outras regiões na lista, ordem. Para obter mais informações, consulte o artigo [em desenvolvimento com contas de DocumentDB da região com várias][docdb-multi-region].

**Diagnósticos**. Inicie sessão todos os erros do lado do cliente.


### <a name="writing-data-to-documentdb-fails"></a>Escrever os dados DocumentDB falha.

**Deteção**. Capturas `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Recuperação**

- O SDK automaticamente repetições tentativas falhadas. Para definir o número de tentativas e o tempo de espera máximo, configure `ConnectionPolicy.RetryOptions`. Exceções eleva o cliente são quer para além da política de repetição ou não são breves erros. 

- Se DocumentDB acelera o cliente, devolve um erro de HTTP 429. Verificar o código de estado `DocumentClientException`. Se está a obter o erro 429 forma consistente, considere aumentar o valor de débito da coleção de DocumentDB.

- Criar uma réplica da base de dados DocumentDB ao longo de duas ou mais regiões. Se a região primária falhar, outro região será promovido a escrever. Também pode acionar uma activação pós-falha manualmente. O SDK faz deteção automática e encaminhamento, para que o código de aplicação continua a funcionar após uma activação pós-falha. Durante o período de activação pós-falha (normalmente minutos), operações de escrita terá latência superior, tal como o SDK localiza nova região de escrita. Para obter mais informações, consulte o artigo [em desenvolvimento com contas de DocumentDB da região com várias][docdb-multi-region].

- Como uma contingência, persistirem o documento para uma fila de cópia de segurança e processar a fila mais tarde.

**Diagnósticos**. Inicie sessão todos os erros do lado do cliente.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>Ler dados a partir do Elasticsearch falha.

**Deteção**. Capturas a exceção adequada para o determinado [Elasticsearch cliente] [ elasticsearch-client] a ser utilizado. 

**Recuperação**

- Utilize um mecanismo repetir. Cada cliente tem as suas próprias políticas de repetir. 

- Implementar vários Elasticsearch nós e utilizar a replicação para elevada disponibilidade.

Para obter mais informações, consulte o artigo [Elasticsearch em execução no Azure][elasticsearch-azure].

**Diagnósticos**. Pode utilizar as ferramentas de monitorização para Elasticsearch ou inicie todos os erros do lado do cliente com a carga útil. Consulte a secção 'Monitorização' [Elasticsearch em execução no Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Escrever os dados Elasticsearch falha.

**Deteção**. Capturas a exceção adequada para o determinado [Elasticsearch cliente] [ elasticsearch-client] a ser utilizado.  

**Recuperação**

- Utilize um mecanismo repetir. Cada cliente tem as suas próprias políticas de repetir. 
 
- Se a aplicação pode tolerar um nível de consistência reduzida, considere escrita com `write_consistency` a definição de `quorum`.

Para obter mais informações, consulte o artigo [Elasticsearch em execução no Azure][elasticsearch-azure].

**Diagnósticos**. Pode utilizar as ferramentas de monitorização para Elasticsearch ou inicie todos os erros do lado do cliente com a carga útil. Consulte a secção 'Monitorização' [Elasticsearch em execução no Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Armazenamento de fila de espera

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Escrever uma mensagem para falha de armazenamento do Azure fila forma consistente.

**Deteção**. Após *N* tentativas de repetição, a operação de escrita continuar a falhar.

**Recuperação**

- Armazenar os dados numa local cache e reencaminhar o escreve ao armazenamento mais tarde, quando o serviço de fica disponível.
- Criar uma fila secundária e escrever para essa fila se fila de espera principal não estiver disponível.

**Diagnósticos**. Utilizar [métricas de armazenamento][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>A aplicação não é possível processar uma mensagem específica da fila. 

**Deteção**. Específico da aplicação. Por exemplo, a mensagem contém dados inválidos, ou a lógica empresarial falha por algum motivo. 

**Recuperação**

Mova a mensagem para uma fila em separado. Execute um processo separado examinar as mensagens nessa fila.

Considere utilizar o serviço de mensagens do Azure Service Bus filas, que fornece uma [fila entregues] [ sb-dead-letter-queue] funcionalidade para esse efeito.

Nota: Se estiver a utilizar filas de armazenamento com WebJobs, o SDK WebJobs fornece processamento de mensagem corrompida foi incorporados. Veja [como utilizar o armazenamento de fila Azure com o SDK WebJobs][sb-poison-message].

**Diagnósticos**. Utilize o registo de aplicação.


## <a name="redis-cache"></a>Redis Cache

### <a name="reading-from-the-cache-fails"></a>Ocorre uma falha na leitura a partir da cache.

**Deteção**. Capturas `StackExchange.Redis.RedisConnectionException`.

**Recuperação**

1. Repetir no falhas breves. Azure Redis cache suporta repetir incorporado através de de consulte [diretrizes de repetir Redis Cache][redis-retry].
2. Trate não transitório falhas como uma falha de cache e reverter para a origem de dados original.

**Diagnósticos**. Utilizar os [Diagnósticos do Redis Cache][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>Falha de escrita na cache.

**Deteção**. Capturas `StackExchange.Redis.RedisConnectionException`.

**Recuperação**

1. Repetir no falhas breves. Azure Redis cache suporta repetir incorporado através de de consulte [diretrizes de repetir Redis Cache][redis-retry].
2. Se o erro é não transitório, ignore-a e permitir que outras operações escrever na cache mais tarde.

**Diagnósticos**. Utilizar os [Diagnósticos do Redis Cache][redis-monitor].


## <a name="sql-database"></a>Base de dados SQL

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Não consegue ligar à base de dados na região principal.

**Deteção**. Ocorre uma falha na ligação.

**Recuperação**

Pré-requisito: A base de dados tem de ser configurado para a replicação-geo ativa. Consulte o artigo [Da base de dados ativa Geo-a replicação SQL][sql-db-replication].

- Para consultas, leia a partir de uma réplica secundária. 
- Para inserções e atualizações, manualmente falhe sobre a uma réplica secundária. Consulte o artigo [Iniciar planeado ou não planeado activação pós-de uma falha, para a base de dados do SQL Azure][sql-db-failover].

A réplica utiliza uma cadeia de ligação diferente, por isso, terá de atualizar a cadeia de ligação na sua aplicação.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Cliente executa terminar ligações no conjunto de ligação.

**Deteção**. Capturas `System.InvalidOperationException` erros. 

**Recuperação**

- Repetir a operação.
- Como um plano de mitigação, isole os conjuntos de ligação para cada casos de utilização, para que um caso de utilização não é possível dominam todas as ligações.
- Aumente os conjuntos de máxima de ligação.

**Diagnósticos**. Registos de aplicação.


### <a name="database-connection-limit-is-reached"></a>Limite de ligação de base de dados é atingido. 

**Deteção**. Base de dados SQL Azure limita o número de trabalhadores em simultâneo, inícios de sessão e sessões. Os limites variam consoante a camada de serviço. Para mais informações, consulte o artigo [limites de recursos base de dados do SQL Azure][sql-db-limits].

Para detetar estes erros, captura `System.Data.SqlClient.SqlException` e verificar o valor de `SqlException.Number` para o código de erro SQL. Para uma lista de códigos de erro relevantes, consulte o artigo [códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados][sql-db-errors].

**Recuperação**. Estes erros são considerados como breves, para que a repetir pode resolver o problema. Se clicar em forma consistente estes erros, considere dimensionamento da base de dados.

**Diagnósticos**. -O [sys.event_log] [ sys.event_log] consulta devolve ligações de base de dados com êxito, falhas de ligação e bloqueios.

- Criar uma [regra de alerta] [ azure-alerts] para falhou ligações.

- Activar a [base de dados SQL auditoria] [ sql-db-audit] e verificar existência de falhados inícios de sessão.


## <a name="service-bus-messaging"></a>Bus mensagens de serviço

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Ocorre uma falha na leitura de uma mensagem a partir de uma fila Bus de serviço.

**Deteção**. Capturas exceções a partir do cliente SDK. A classe base para exceções serviço Bus é [MessagingException][sb-messagingexception-class]. Se o erro é breves, o `IsTransient` propriedade é verdadeira. 

Para obter mais informações, consulte o artigo [Bus de serviço de mensagens exceções][sb-messaging-exceptions].

**Recuperação**

1. Repetir no falhas breves. Consulte o artigo [Bus serviço repetir diretrizes][sb-retry].

2. As mensagens que não podem ser entregue a qualquer recetor são colocadas numa *fila entregues*. Utilize este fila de espera para ver as mensagens que não podem ser recebidas. Não existe nenhuma limpeza automática da fila entregues. Mensagens permanecem até a explicitamente obtê-las. Consulte o artigo [Descrição geral do serviço Bus entregues filas][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Escrever uma mensagem para um serviço Bus fila falha.

**Deteção**. Capturas exceções a partir do cliente SDK. A classe base para exceções serviço Bus é [MessagingException][sb-messagingexception-class]. Se o erro é breves, o `IsTransient` propriedade é verdadeira. 

Para obter mais informações, consulte o artigo [Bus de serviço de mensagens exceções][sb-messaging-exceptions].

**Recuperação**

1. O cliente de serviço Bus repetições automaticamente depois dos erros breves. Por predefinição, utiliza exponencial back-off. Após a contagem de repetições máxima ou período de tempo limite máximo, o cliente inicia uma exceção. Para obter mais informações, consulte o artigo [Bus serviço repetir diretrizes][sb-retry].

2. Se a quota da fila for excedida, o cliente inicia [QuotaExceededException][QuotaExceededException]. A mensagem de exceção fornece mais detalhes. Descarregar algumas mensagens de fila de espera antes de tentar novamente e, considere utilizar o padrão de circuito separador de palavras para evitar repetições de continuação enquanto a quota de for excedida. Além disso, certifique-se da que propriedade [BrokeredMessage.TimeToLive] não estiver definida demasiado elevada. 

3. Dentro de uma região RDP pode ser melhorado utilizando [filas com partições ou tópicos][sb-partition]. Um sem partições fila ou um tópico é atribuído a um arquivo de mensagens. Se este arquivo de mensagens não estiver disponível, todas as operações nessa fila ou tópico irão falhar. Uma fila com partições ou tópico é dividido através de vários arquivos de mensagens. 

4. Para RDP adicional, crie dois espaços de nomes de serviço Bus em diferentes regiões e criar uma réplica as mensagens. Pode utilizar a replicação do active ou replicação passiva.

    - A replicação do Active: O cliente envia todas as mensagens para ambas as filas. O destinatário recebe em ambas as filas. Marcar mensagens com um identificador exclusivo, para que o cliente pode eliminar mensagens duplicadas.

    - Replicação passiva: O cliente envia a mensagem para uma fila. Se existir um erro, o cliente reverte para outros fila de espera. O destinatário recebe em ambas as filas. Esta abordagem reduz o número de duplicados mensagens que são enviadas. No entanto, o destinatário ainda deve processar mensagens duplicadas.

    Para obter mais informações, consulte o artigo [GeoReplication exemplo] [ sb-georeplication-sample] e as [melhores práticas para aplicações isolamento contra Bus de serviço de corrente e catástrofes][sb-outages].


### <a name="duplicate-message"></a>Mensagem de duplicados.

**Deteção**. Examinar a `MessageId` e `DeliveryCount` propriedades da mensagem.

**Recuperação**

- Se possível, estruture a sua mensagem operações de transformação para ser idempotent. Caso contrário, armazene IDs de mensagem das mensagens que já estejam processados e verifique o ID antes de processamento de uma mensagem.

- Ativar a deteção de duplicados, através da criação de fila de espera com `RequiresDuplicateDetection` definida como verdadeiro. Com esta definição, o serviço Bus elimina automaticamente todas as mensagens são enviadas com o mesmo `MessageId` como uma mensagem anterior.  Tenha em atenção o seguinte procedimento:

    -  Esta definição impede que duplicados mensagens a ser colocados fila de espera. Um destinatário não o impede de processar mais de uma vez a mesma mensagem.

    - Deteção de duplicados tem uma janela de tempo. Se um duplicado for enviado para além nesta janela, não ser detetado.

**Diagnósticos**. Inicie sessão mensagens duplicadas.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>A aplicação não é possível processar uma mensagem específica da fila. 

**Deteção**. Específico da aplicação. Por exemplo, a mensagem contém dados inválidos, ou a lógica empresarial falha por algum motivo. 

**Recuperação**

Existem dois modos de falha a ter em conta. 

- No auscultador Deteta a falha. Neste caso, mova a mensagem para a fila entregues. Mais tarde, execute um processo separado examinar as mensagens na fila de espera entregues.

- Falha no auscultador no meio processar a mensagem &mdash; por exemplo, devido a uma exceção não processada. Para processar neste caso, utilize `PeekLock` modo. Neste modo, se o cadeado expirar, a mensagem fica disponível para outros destinatários. Se a mensagem exceder a contagem de entrega máximo ou o tempo de vida, a mensagem é movida automaticamente para a fila entregues.

Para obter mais informações, consulte o artigo [Descrição geral do serviço Bus entregues filas][sb-dead-letter-queue].

**Diagnósticos**. Sempre que a aplicação de move uma mensagem para a fila entregues, escreva um evento para os registos de aplicação.


## <a name="service-fabric"></a>Serviço ferro

### <a name="a-request-to-a-service-fails"></a>Falha um pedido para um serviço.

**Deteção**. O serviço devolve um erro.

**Recuperação**

- Localize um proxy novamente (`ServiceProxy` ou `ActorProxy`) e chamar o método de serviço/ator novamente. 

- **Serviço de estado**. Molde operações em coleções de fiáveis uma transação. Se existir um erro, a transação ser revertida. O pedido, se importados a partir de uma fila, será processado novamente. 
 
- **Serviço sem estado**. Se o serviço persistir dados para um arquivo externo, todas as operações precisam de ser idempotent.


**Diagnósticos**. Registo de aplicações

### <a name="service-fabric-node-is-shut-down"></a>Nó de serviço ferro é encerrar.

**Deteção**. Um token de cancelamento é transmitido para o serviço `RunAsync` método. Serviço ferro cancela a tarefa antes de encerrar o nó.

**Recuperação**. Utilize o token de cancelamento para detetar encerramento. Quando os pedidos de serviço ferro cancelamento, terminar qualquer trabalho e sair do `RunAsync` o mais rapidamente possível.

**Diagnósticos**. Registos de aplicação


## <a name="storage"></a>Armazenamento

### <a name="writing-data-to-azure-storage-fails"></a>Dados de escrita para falha de armazenamento do Windows Azure

**Deteção**. O cliente receba erros ao escrever.

**Recuperação**

1. Repetir a operação, para recuperar da falhas breves. [Volte a tentar política] [ Storage.RetryPolicies] no cliente do SDK processa isto automaticamente.
2. Implemente o padrão de circuito separador de palavras para evitar armazenamento confuso.
3. Se N tentativas de repetição falharem, execute uma contingência com êxito. Por exemplo:

    - Armazenar os dados numa local cache e reencaminhar o escreve ao armazenamento mais tarde, quando o serviço de fica disponível.
    - Se tiver sido a ação de escrita num âmbito transaccional, o Adquirente na íntegra a transação.

**Diagnósticos**. Utilizar [métricas de armazenamento][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>Ocorre uma falha na leitura de dados a partir do armazenamento do Windows Azure.

**Deteção**. O cliente receba erros durante a leitura.

**Recuperação**

1. Repetir a operação, para recuperar da falhas breves. [Volte a tentar política] [ Storage.RetryPolicies] no cliente do SDK processa isto automaticamente.
2. Para o armazenamento do GRS RT, se ocorre uma falha na leitura a partir do ponto final principal, experimente leitura a partir do ponto final secundário. O cliente SDK pode lidar com este automaticamente. Consulte [replicação de armazenamento do Windows Azure][storage-replication].
3. Se *N* tentativas de repetição falharem, efetuar uma ação contingência para diminuir. Por exemplo, se uma imagem de produto não é possível obter a partir do armazenamento, mostra uma imagem de marcador de posição genérico.

**Diagnósticos**. Utilizar [métricas de armazenamento][storage-metrics].


## <a name="virtual-machine"></a>Máquina virtual

### <a name="connection-to-a-backend-vm-fails"></a>Ligação para um back-end VM falha.

**Deteção**. Erros de ligação de rede.

**Recuperação**

- Implemente back-end, pelo menos, duas VMs num conjunto de disponibilidade, atrás de um balanceador de carga.

- Se o erro de ligação for breves, por vezes, TCP irá com êxito repetir enviar a mensagem. 

- Implementa uma política de repetir na aplicação. 

- Erros persistentes ou não transitório, implementar o [separador de palavras de circuito] [ circuit-breaker] padrão.

- Se a chamada VM exceder o limite de saída de rede, irão encher fila de saída. Se a fila de saída está cheio de forma consistente, considere escalar para fora. 

**Diagnósticos**. Inicie sessão eventos em limites de serviço.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Instância VM torna-se indisponível ou danificado.

**Deteção**. Configurar uma Balanceador de carga [sonda de estado de funcionamento] [ lb-probe] que sinais de se a instância VM está em bom estada. A sonda deve verificar se as funções essenciais estão a responder corretamente. 

**Recuperação**. Para cada camada de aplicação, colocar várias instâncias VM para o mesmo conjunto de disponibilidade e coloque um balanceador de carga à frente de VMs. Se a sonda de estado de funcionamento falhar, Balanceador de carga deixa de enviar novas ligações para a instância danificada.

**Diagnósticos**. -Utilize Balanceador de carga [registo analytics][lb-monitor].
- Configure o seu sistema de monitorização para monitorizar a todos o estado de funcionamento monitorizar os pontos finais.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Operador encerra acidentalmente uma VM.

**Deteção**. N/D

**Recuperação**. Definir um cadeado de recurso com `ReadOnly` nível. Consulte o artigo [recursos de bloqueio com o Gestor de recursos do Azure][rm-locks].

**Diagnósticos**. Utilizar [os registos de atividade Azure][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Tarefa contínua deixará de ser executada quando o anfitrião do SMS está inactivo.

**Deteção**. Passe um token de cancelamento para a função WebJob. Para obter mais informações, consulte o artigo [encerramento com êxito][web-jobs-shutdown]. 

**Recuperação**. Ativar o `Always On` definição no web app. Para obter mais informações, consulte o artigo [tarefas em segundo plano executar com WebJobs][web-jobs].


## <a name="application-design"></a>Estrutura de aplicação

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Aplicação não é possível processar um coletor no pedidos recebidos.

**Deteção**. Depende da aplicação. Sintomas habituais:

- O Web site é iniciado devolver códigos de erro de 5xx HTTP.
- Serviços dependentes, tal como a base de dados ou de armazenamento, começar a aceleração pedidos. Procure erros de HTTP como 429 HTTP (demasiado muitos pedidos), consoante o serviço.
- Comprimento da fila HTTP cresce.

**Recuperação**

- Dimensione saída para processar a carga aumentada. 

- Mitigar falhas para evitar ter falhas em cascata interromper a aplicação completa. Estratégias de mitigação incluem:

    - Implementar o [Padrão de limitação] [ throttling-pattern] para evitar confuso sistemas de back-end.
    - Utilizar o [nivelamento de carga baseados em filas] [ queue-based-load-leveling] para pedidos de memória intermédia e processá-las um adequado ritmo.
    - Atribuir prioridades a determinados clientes. Por exemplo, se a aplicação tem camadas gratuitas e pagas, optimizar clientes na camada gratuito, mas não pagos clientes. Consulte o artigo [padrão de fila de prioridade][priority-queue-pattern].

**Diagnósticos**. Utilizar o [registo de aplicação de serviço de diagnóstico][app-service-logging]. Utilizar um serviço como o [Azure registo Analytics][azure-log-analytics], [Informações de aplicação][app-insights], ou [Novo Relic] [ new-relic] para ajudar a compreender os registos de diagnóstico.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Uma das operações num fluxo de trabalho ou da transação distribuída falha.

**Deteção**. Após *N* tentativas de repetição, continuar a falhar.

**Recuperação**

- Como um plano de mitigação, implementar o [Scheduler agente autoridade] [ scheduler-agent-supervisor] padrão para gerir o fluxo de trabalho completo. 
- Não repetir no tempos limite. Existe uma taxa de sucesso baixa para este erro. 
- Trabalho fila de espera, para poder volte a tentar mais tarde.

**Diagnósticos**. Inicie sessão todas as operações (e sem êxito), incluindo compensadores ações. Utilize correlação IDs de modo a que pode controlar todas as operações dentro da mesma transação.


### <a name="a-call-to-a-remote-service-fails"></a>Falha uma chamada para um serviço remoto.

**Deteção**. Código de erro HTTP.

**Recuperação**

1. Repetir no falhas breves. 
2. Se a chamada falhar após *N* tentativas, efetuar uma ação de contingência. (Aplicação específica).
3. Implementar o [padrão de circuito separador de palavras] [ circuit-breaker] para evitar falhas em cascata. 

**Diagnósticos**. Inicie sessão todas as falhas de chamada remoto.


## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o processo FMA, consulte o artigo [resistência pela estrutura para serviços em nuvem] [ resilience-by-design-pdf] (transferir PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful


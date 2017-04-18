<properties
    pageTitle="Utilizar a análise de armazenamento para recolher dados de registos e métricas | Microsoft Azure"
    description="Análise de armazenamento permite-lhe para controlar os dados de métricas para todos os serviços de armazenamento e para recolher registos para o armazenamento de BLOBs, fila de espera e tabela."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Análise de armazenamento

## <a name="overview"></a>Descrição geral

Azure armazenamento Analytics executa o registo e fornece dados de métricas de uma conta de armazenamento. Pode utilizar estes dados para pedidos de rastreio, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

Para utilizar a análise de armazenamento, tem de ativá-lo individualmente para cada serviço que pretende monitorizar. Pode ativá-la a partir do [Azure Portal](https://portal.azure.com). Para obter detalhes, consulte [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Também pode ativar a análise de armazenamento através de programação através da API REST ou a biblioteca do cliente. Utilize as operações de [Propriedades do serviço de BLOBs obter](https://msdn.microsoft.com/library/hh452239.aspx), [Obter propriedades do serviço de fila de espera](https://msdn.microsoft.com/library/hh452243.aspx), [Obter propriedades da tabela serviço](https://msdn.microsoft.com/library/hh452238.aspx)e [Obter propriedades do ficheiro de serviço](https://msdn.microsoft.com/library/mt427369.aspx) para permitir a análise de armazenamento para cada serviço.

Os dados agregados são armazenados num blob famoso (para o registo) e em tabelas famoso (para métricas), que podem ser acedidas com o serviço de BLOBs e o serviço de tabela APIs.

Análise de armazenamento tem um limite de 20 TB a quantidade de dados armazenados que seja independentes do total dos limites para a sua conta de armazenamento. Para mais informações sobre faturação e de políticas de retenção de dados, consulte o artigo [faturação e de análise de armazenamento](https://msdn.microsoft.com/library/hh360997.aspx). Para mais informações sobre os limites de conta de armazenamento, consulte o artigo [escalabilidade de armazenamento do Azure e metas de desempenho](storage-scalability-targets.md).

Para um guia aprofundado sobre como utilizar e outras ferramentas de análise de armazenamento para identificar, diagnosticar e resolução de problemas relacionados com o armazenamento do Windows Azure, consulte o artigo [Monitor, diagnosticar e resolver problemas de armazenamento do Windows Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>Sobre o registo de análise de armazenamento

Armazenamento Analytics regista informações detalhadas acerca dos pedidos e sem êxito para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar pedidos individuais e para diagnosticar problemas com a um serviço de armazenamento. Pedidos são registados numa base melhor esforço.

Entradas de registo são criadas apenas se existir actividade de serviço de armazenamento. Por exemplo, se uma conta de armazenamento tem atividade no seu serviço de BLOBs mas não no seus serviços de tabela ou fila de espera, apenas os registos relativos ao serviço Blob serão criados.

Registo de análise de armazenamento não está disponível para o serviço de ficheiro do Azure.

### <a name="logging-authenticated-requests"></a>Pedidos de registo autenticado

Os seguintes tipos de pedidos autenticados são registados:

- Pedidos com êxito.

- Ocorreu uma falha pedidos, incluindo o tempo de espera, limitação, rede, autorização e outros erros.

- Pedidos de utilizando um partilhado acesso assinatura (SA), incluindo os pedidos de falhados e bem sucedidos.

- Pedidos de dados de análise.

Pedidos de efetuadas por armazenamento a análise de si só, tal como registo criação ou eliminação, não são registados. Uma lista completa dos dados com sessão iniciada é documentada nos tópicos de [operações de tem sessão iniciada a análise de armazenamento e mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Registo de pedidos anónimos
Os seguintes tipos de pedidos anónimos são registados:

- Pedidos com êxito.

- Erros do servidor.

- Erros de tempo limite para o cliente e servidor.

- Falhados pedidos GET com código de erro 304 (não modificado).

Todos os outros pedidos anónimos falhados não são registados. Uma lista completa dos dados com sessão iniciada é documentada nos tópicos de [armazenamento Analytics tem sessão iniciada operações e mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>Como os registos são armazenados
Todos os registos são armazenados em blobs bloco num contentor denominada $logs, que é criada automaticamente quando é activado Analytics de armazenamento para uma conta de armazenamento. O contentor $logs está localizado no espaço de nomes blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Neste contentor não pode ser eliminado depois de ter sido ativada a análise de armazenamento, apesar dos seus conteúdos podem ser eliminados.

>[Azure.NOTE] O contentor $logs não é apresentado quando um contentor listar operação é executado, tal como o método de [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Deve ser acedido diretamente. Por exemplo, pode utilizar o método de [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) para aceder a blobs no `$logs` contentor.
Como os pedidos são registados, o armazenamento Analytics carregar resultados intermédios como blocos. Periodicamente, armazenamento irão consolidar esses blocos e de análise disponibilizá-los como um blob.

Podem existir registos duplicados para registos criados com a mesma hora. Pode determinar se um registo é um duplicado ao selecionar o número de **RequestId** e de **operação** .

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de registo
Cada registo será escrito no seguinte formato.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

A tabela seguinte descreve cada atributo no nome do registo.

| Atributo         | Descrição                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < nome do serviço >    | O nome do serviço de armazenamento. Por exemplo: blob, tabela ou uma fila.                                                                                                                          |
| AAAA              | O ano de quatro dígitos para o registo. Por exemplo: 2011.                                                                                                                                           |
| MM                | O mês de dois dígitos para o registo. Por exemplo: 07.                                                                                                                                             |
| DD                | O mês de dois dígitos para o registo. Por exemplo: 07.                                                                                                                                             |
| hh                | A hora de dois dígitos que indica a hora de início para que os registos, no formato de UTC 24 horas. Por exemplo: 18.                                                                                     |
| mm                | O número de dois dígitos que indica os minutos de partida para que os registos. Este valor não é suportada na versão atual do armazenamento Analytics e o valor será sempre 00.     |
| <counter>         | Contador baseado em zero com seis dígitos que indica o número de blobs do registo gerado para o serviço de armazenamento numa hora período de tempo. Este contador começa no 000000. Por exemplo: 000001.     |

Segue-se um nome de registo de exemplo completos combina os exemplos anteriores.

    blob/2011/07/31/1800/000001.log

Segue-se um exemplo URI que pode ser utilizada para aceder ao registo anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Quando um pedido de armazenamento tem sessão iniciado, o nome do registo resultante relacionado com a hora concluída a operação pedida. Por exemplo, se um pedido de GetBlob foi concluído na 6:30 PM no 31/7/2011, o registo seria escrito com o prefixo seguinte:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de registo
Todos os registo blobs são armazenados com metadados que podem ser utilizados para identificar os dados de registo que contém o blob. A tabela seguinte descreve cada atributo de metadados.

| Atributo     | Descrição                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | Descreve se o registo contém informações relativas a ler, escrever ou eliminar operações. Este valor pode incluir um tipo ou uma combinação de todos os três, separados por vírgulas. Exemplo 1: escrever; Exemplo 2: ler, escrever; Exemplo 3: ler, escrever, eliminar.    |
| Hora de início     | A hora mais antiga de uma entrada no registo de, sob a forma de aaaa-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:21:46Z.                                                                                                                                             |
| Hora de fim       | A hora mais recente de uma entrada no registo de, sob a forma de aaaa-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | A versão do formato de registo. Atualmente, o valor de suportados apenas é 1.0.                                                                                                                                                                                     |

A seguinte lista apresenta metadados de exemplo completos utilizar os exemplos anteriores.

- LogType = escrita

- Hora de início = 2011-07-31T18:21:46Z

- Hora de fim = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>Aceder aos dados do registo

Todos os dados no `$logs` contentor podem ser acedidas utilizando o serviço de BLOBs APIs, incluindo as APIs .NET fornecida pelo Azure gerido biblioteca. O administrador de conta de armazenamento pode ler e eliminar registos, mas não é possível criar ou atualizá-las. Metadados do registo e nome de registo podem ser utilizadas quando consultar para um registo. É possível para registos de uma determinada hora seja apresentada fora de ordem, mas os metadados especificam sempre timespan de entradas de registo num registo. Por conseguinte, pode utilizar uma combinação de nomes de registo e metadados quando procurar um registo específico.

## <a name="about-storage-analytics-metrics"></a>Sobre métricas de armazenamento Analytics

Análise de armazenamento pode armazenar métricas que incluam da transação agregado estatísticas e a capacidade de dados sobre os pedidos para um serviço de armazenamento. Transações são reportadas tanto a nível API operação e ao nível do serviço de armazenamento e capacidade é comunicada ao nível do serviço de armazenamento. Dados de métricas podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnosticar problemas com pedidos de acordo com o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.

Para utilizar a análise de armazenamento, tem de ativá-lo individualmente para cada serviço que pretende monitorizar. Pode ativá-la a partir do [Azure Portal](https://portal.azure.com). Para obter detalhes, consulte [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Também pode ativar a análise de armazenamento através de programação através da API REST ou a biblioteca do cliente. Utilize as operações de **Obter propriedades do serviço** para permitir a análise de armazenamento para cada serviço.

### <a name="transaction-metrics"></a>Métricas da transação

Um conjunto robusto de dados estiver registado com intervalos de hora a hora ou minutos para cada serviço de armazenamento e pedida operação API, incluindo penetração/saída, disponibilidade, erros e categorizados percentagens pedido. Pode ver uma lista completa dos detalhes da transação no tópico de [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx) .

Dados da transação são registados em dois níveis – o nível de serviço e o nível de operação API. Ao nível do serviço, estatísticas resumir todos pedida operações de API são escritas para uma entidade de tabela cada hora mesmo se não existirem pedidos foram feitos ao serviço. Ao nível de operação API, estatísticas são apenas escritas uma entidade se a operação foram pedida dentro desse hora.

Por exemplo, se executar uma operação de **GetBlob** no seu serviço de BLOBs, métricas de análise de armazenamento irão registar o pedido e inclui-los nos dados agregados para tanto o serviço de BLOBs, bem como a operação de **GetBlob** . No entanto, não se for solicitada nenhuma operação **GetBlob** durante a hora, uma entidade irá não sejam escritos no `$MetricsTransactionsBlob` para essa operação.

Métricas da transação são registadas para pedidos de utilizador e pedidos efetuados pela análise de armazenamento própria. Por exemplo, são registados pedidos ao armazenamento Analytics para escrever registos e entidades de tabela. Para mais informações sobre como estes pedidos são faturados, consulte o artigo [faturação e de análise de armazenamento](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métricas capacidade

>[AZURE.NOTE] Atualmente, métricas de capacidade só estão disponíveis para o serviço de Blobs. Métricas capacidade para o serviço de tabela e o serviço de fila estarão disponíveis em versões futuras da análise de armazenamento.

Capacidade dados estiver registados diariamente para o serviço de BLOBs de uma conta de armazenamento e duas entidades de tabela são escritas. Uma entidade fornece estatísticas para os dados de utilizador e o outro fornece estatísticas sobre o `$logs` contentor blob utilizado pela análise de armazenamento. O `$MetricsCapacityBlob` tabela inclui as seguintes estatísticas:

- **Capacidade**: A quantidade de armazenamento utilizado pelo Blob serviço a conta de armazenamento, em bytes.

- **ContainerCount**: O número de BLOBs contentores num serviço de BLOBs a conta de armazenamento.

- **ObjectCount**: O número de consolidada e de repetição bloco ou página blobs num serviço de BLOBs a conta de armazenamento.

Para mais informações sobre as métricas de capacidade, consulte o [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Como métricas são armazenadas

Todos os dados de métricas para cada um dos serviços de armazenamento está armazenado em três tabelas reservadas para esse serviço: uma tabela para obter informações da transação, uma tabela para obter informações da transação minuto e outra tabela para obter informações de capacidade. Informações da transação da transação e minuto consiste em dados pedidos e respostas e informação sobre a capacidade consiste em armazenamento dados de utilização. Métricas de hora, minutos métricas e capacidade de Blob serviço uma conta de armazenamento podem ser acedidos nas tabelas que são com nome, tal como descrito na tabela seguinte.

| Nível de métricas                         | Nomes de tabelas                                                                                                                   | Versões suportadas                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Métricas de hora a hora, localização principal      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versões antes de 2013-08-15 apenas. Enquanto ainda são suportados os seguintes nomes, é recomendável que mude para utilizando as tabelas listadas abaixo.  |
| Métricas de hora a hora, localização principal      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Todas as versões, incluindo 2013-08-15.                                                                                                               |
| Métricas minutos, localização principal      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Todas as versões, incluindo 2013-08-15.                                                                                                               |
| Métricas de hora a hora, localização secundária    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Todas as versões, incluindo 2013-08-15. Tem de estar ativada replicação geo redundantes acesso de leitura.                                                    |
| Métricas minutos, localização secundária    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Todas as versões, incluindo 2013-08-15. Tem de estar ativada replicação geo redundantes acesso de leitura.                                                    |
| Capacidade (apenas para serviço Blob)          | $MetricsCapacityBlob                                                                                                          | Todas as versões, incluindo 2013-08-15.                                                                                                               |


Estas tabelas são criadas automaticamente quando a análise de armazenamento é ativada para uma conta de armazenamento. Estes são acedidos através do espaço de nomes da conta de armazenamento, por exemplo:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Aceder a dados de métricas

Todos os dados nas tabelas métricas podem ser acedidas utilizando o serviço de tabela APIs, incluindo as APIs .NET fornecida pelo Azure gerido biblioteca. O administrador de conta de armazenamento pode ler e eliminar entidades de tabela, mas não é possível criar ou atualizá-las.

## <a name="billing-for-storage-analytics"></a>Faturação para a análise de armazenamento

Análise do armazenamento está ativada por um proprietário da conta de armazenamento; não estiver ativado por predefinição. Todos os dados de métricas é escrito pelos serviços de uma conta de armazenamento. Como resultado, cada operação de escrita realizada por Analytics de armazenamento é cobrar. Para além disso, a quantidade de armazenamento utilizado pelos dados métricas também é cobrar.

As seguintes ações realizadas pela análise de armazenamento estão cobrar:

- Pedidos para criar blobs para o registo. 

- Pedidos para criar entidades de tabela para métricas.

Se tiver configurado uma política de retenção de dados, não será cobrado para eliminar transações quando a análise de armazenamento elimina os dados de registo e métricas antigos. No entanto, as transações de eliminar a partir de um cliente são cobrar. Para mais informações sobre as políticas de retenção, consulte [definir uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções sobre pedidos cobrar

Todos os pedidos de feitos ao serviço de armazenamento de uma conta são cobrar ou não faturável. Cada pedido individual feito para um serviço, incluindo uma mensagem de estado que indica a forma como o pedido foi lida com os registos de análise de armazenamento. Da mesma forma, a análise de armazenamento armazena métricas para um serviço e as operações de API desse serviço, incluindo as percentagens e a contagem de determinadas mensagens de estado. Em conjunto, estas funcionalidades podem ajudar a analisar pedidos de cobrar, efetuar melhorias na sua aplicação e diagnosticar problemas com os pedidos para os seus serviços. Para mais informações sobre faturação, consulte [Noções sobre Azure armazenamento faturação - largura de banda, as transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Quando a opção dados de análise de armazenamento, pode utilizar as tabelas no tópico de [operações de tem sessão iniciada a análise de armazenamento e mensagens de estado](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar quais os pedidos de são cobrar. Em seguida, pode comparar os registos de início e os dados de métricas às mensagens de estado para ver se foram cobradas para um determinado pedido. Também pode utilizar as tabelas no tópico anterior para investigar disponibilidade para um serviço de armazenamento ou uma operação de API individual.

## <a name="next-steps"></a>Próximos passos

### <a name="setting-up-storage-analytics"></a>Configurar a análise de armazenamento
- [Monitorizar a uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md)
- [Activar e configurar a análise de armazenamento](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Registo de análise de armazenamento  
- [Sobre o registo de análise de armazenamento](https://msdn.microsoft.com/library/hh343262.aspx)
- [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx)
- [Análise de armazenamento tem sessão iniciada operações e mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métricas de análise de armazenamento
- [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343258.aspx)
- [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx)
- [Análise de armazenamento tem sessão iniciada operações e mensagens de estado](https://msdn.microsoft.com/library/hh343260.aspx)  

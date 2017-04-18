<properties
    pageTitle="Ligação de dados: entradas a partir de uma sequência de um evento de fluxo de dados | Microsoft Azure"
    description="Saiba mais sobre como configurar uma ligação de dados para a análise da cadeia denominado 'entradas do tipo'. Entradas incluem um fluxo de dados a partir de eventos e também fazer referência a dados."
    keywords="fluxo de dados, a ligação de dados, a sequência de evento"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Ligação de dados: Saiba mais sobre os dados da cadeia entradas do tipo de eventos para a análise da cadeia

A ligação de dados para a análise da cadeia é um fluxo de dados dos eventos a partir de uma origem de dados. Esta opção é denominada "Introdução". Análise de sequência tem primeira classe integração com o Azure da cadeia origens concentrador de evento, IoT concentrador e Blob de armazenamento de dados que pode ser da subscrição Azure, mesmo ou diferente, como a tarefa de análise.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipos de entrada de dados: os dados da cadeia e referência dados
Como os dados são enviados para uma origem de dados, é consumida pela tarefa de análise da cadeia e transformado em tempo real. Entradas estão divididas em dois tipos de distintos: transmitir em fluxo entradas do tipo de dados e entradas de dados de referência.

### <a name="data-stream-inputs"></a>Entradas do tipo de fluxo de dados
Um fluxo de dados é corrigem sequência de eventos que vão ao longo do tempo. Tarefas de análise da cadeia devem incluir a introdução de dados pelo menos uma sequência ser consumidas e transformados através da tarefa. Armazenamento de BLOBs, evento concentradores e IoT Hubs são suportadas como origens de entrada de fluxo de dados. Evento concentradores são utilizados para recolher sequências de evento de vários dispositivos e serviços, como os feeds de atividades de redes sociais, informações comerciais cotações ou os dados provenientes de sensores. IoT concentradores estão otimizados para recolher dados de dispositivos ligados em cenários de Internet coisas (IoT).  Armazenamento de BLOBs pode ser utilizado como uma origem de entrada para ingesting em volume de dados como uma sequência.  

### <a name="reference-data"></a>Dados de referência
Análise da cadeia suporta um segundo tipo de entrada conhecido como dados de referência. Este é o auxiliares dados estática ou lentamente alteração ao longo do tempo e que são normalmente utilizados para efetuar correlação e look-ups. Armazenamento de Blobs do Azure está atualmente a origem de entrada suportada apenas para os dados de referência. Blobs de origem de dados de referência estão limitados a 100MB de tamanho.
Para saber como criar referência entradas de dados, consulte o artigo [Utilizar dados de referência](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Criar uma entrada de fluxo de dados com um concentrador de evento

[Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/) são altamente dimensionáveis publicar-subscrever ingestor do evento. -Pode recolher milhões de eventos por segundo, para que possa processar e analisar grandes quantidades de dados produzidos pela sua dispositivos ligados e aplicações. É uma das entradas utilizadas com mais frequência para análise da cadeia. Evento concentradores e a análise da cadeia em conjunto fornecem clientes uma solução de fim para fim para análise de tempo real. Evento concentradores permitir que os clientes feed eventos Azure em tempo real e tarefas de análise da cadeia podem processá-las em tempo real. Por exemplo, clientes podem enviar web cliques, medições sensor, eventos de registo online para o evento concentradores e criar tarefas de análise da cadeia para utilizar evento concentradores como as sequências de entrada de dados em tempo real de filtragem, Agregar e correlação.

É importante ter em atenção que o carimbo de hora predefinido dos eventos provenientes do evento concentradores em sequência Analytics é o carimbo de data/hora que o evento chegou no Centro de evento que é EventEnqueuedUtcTime. Para processar os dados como uma sequência de utilizar um carimbo de data/hora na carga útil evento, a palavra-chave [Carimbo por](https://msdn.microsoft.com/library/azure/dn834998.aspx) deve ser utilizada.

### <a name="consumer-groups"></a>Grupos do consumidor

Cada entrada da cadeia Analytics evento concentrador deverá estar configurada para ter os suas próprias grupo consumidor. Quando uma tarefa contém uma associação interna ou múltiplas entradas, pode ser lidos algumas entrada por mais do que um leitor de jusante, que impactos o número de leitores de um grupo do consumidor único. Para evitar que excedam o limite de evento concentrador de leitores de 5 por grupo do consumidor por partição, é aconselhável para designar um grupo do consumidor para cada tarefa de análise da cadeia. Tenha em atenção que também existe um limite de 20 grupos consumidor por evento concentrador. Para obter mais detalhes, consulte o [Guia de programação do evento concentradores](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Configurar eventos concentrador como uma sequência de dados de entrada

A tabela abaixo explica cada propriedade na eventualidade de concentrador de entrada separador com a respetiva descrição:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
|------|------|
| Alias de entrada | Um nome amigável que será utilizado na consulta tarefa para fazer referência a esta entrada |
| Espaço de nomes do serviço Bus | Um espaço de nomes de serviço Bus é um contentor para um conjunto de mensagens entidades. Quando tiver criado um novo concentrador de evento, também criado um espaço de nomes de serviço Bus. |
| Concentrador de evento | O nome do seu centro de evento de entrada. |
| Nome da política concentrador evento | A política de acesso partilhado, que pode ser criada no separador evento concentrador configurar. Cada política de acesso partilhado vai ter um nome, as permissões que definir e as teclas de acesso. |
| Chave de política de concentrador de evento | A tecla de acesso de partilhado utilizada para autenticar o acesso ao serviço Bus espaço de nomes. |
| Grupo de consumidores de concentrador de evento (opcional) | O grupo do consumidor para ingerir esta última dados a partir do centro do evento. Se não for especificado, trabalhos de análise da cadeia utilizará o grupo de consumidores predefinido para ingerir esta última dados a partir do centro do evento. Recomenda-se para utilizar um grupo de consumidor distinto para cada tarefa de análise da cadeia. |
| Formato de serialização de evento | Para se certificar de que as suas consultas funcionam da forma que esperava, precisa de análise da cadeia saber qual o formato serialização (JSON, CSV ou Avro) estiver a utilizar o recebidas sequências de dados. |
| Codificação | UTF-8 é o formato de codificação suportado apenas neste momento. |

Quando os dados provenientes de uma origem de concentrador de evento, pode aceder a alguns campos de metadados na sua consulta de análise da cadeia. A tabela abaixo apresenta os campos e a respectiva descrição.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| EventProcessedUtcTime | A data e hora em que o evento foi processado pela análise da cadeia. |
| EventEnqueuedUtcTime | A data e hora em que o evento foi recebido pelo concentradores do evento. |
| PartitionId | O ID da partição zero como base para a placa de entrada. |

Por exemplo, pode escrever uma consulta como o seguinte:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Criar uma entrada de fluxo de dados IoT concentrador

Azure Iot concentrador é altamente dimensionáveis publicar-subscrever ingestor evento otimizada para IoT cenários.
É importante ter em atenção que o carimbo de hora predefinido dos eventos provenientes do IoT concentradores em sequência Analytics é o carimbo de data/hora que o evento chegou no Centro de IoT que é EventEnqueuedUtcTime. Para processar os dados como uma sequência de utilizar um carimbo de data/hora na carga útil evento, a palavra-chave [Carimbo por](https://msdn.microsoft.com/library/azure/dn834998.aspx) deve ser utilizada.

> [AZURE.NOTE] Apenas as mensagens enviadas com uma propriedade de DeviceClient podem ser processadas.

### <a name="consumer-groups"></a>Grupos do consumidor

Cada entrada da cadeia Analytics IoT concentrador deverá estar configurada para ter os suas próprias grupo consumidor. Quando uma tarefa contém uma associação interna ou múltiplas entradas, algumas entrada pode ser lidos por mais do que um leitor de jusante, que impactos o número de leitores de um grupo do consumidor único. Para evitar que excedam o limite de IoT concentrador de leitores de 5 por grupo do consumidor por partição, é aconselhável para designar um grupo do consumidor para cada tarefa de análise da cadeia.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Configurar IoT concentrador como um fluxo de dados de entrada

A tabela abaixo explica cada propriedade na caixa de diálogo IoT concentrador teclado com a respetiva descrição:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
|------|------|
| Alias de entrada | Um nome amigável que será utilizado na consulta tarefa para fazer referência a esta entrada. |
| Concentrador IoT | Um concentrador de IoT é um contentor para um conjunto de entidades de mensagens. |
| Ponto final | O nome do seu ponto final IoT concentrador. |
| Nome da política de acesso partilhado | A política de acesso partilhado conceder acesso ao centro do IoT. Cada política de acesso partilhado vai ter um nome, as permissões que definiu, e as teclas de acesso. |
| Chave de política de acesso partilhado | A tecla de acesso de partilhado utilizada para autenticar o acesso ao concentrador IoT. |
| Consumidor de grupo (opcional) | O grupo do consumidor para ingerir esta última dados a partir do centro do IoT. Se não for especificado, trabalhos de análise da cadeia utilizará o grupo de consumidores predefinido para ingerir esta última dados a partir do centro do IoT. Recomenda-se para utilizar um grupo de consumidor distinto para cada tarefa de análise da cadeia. |
| Formato de serialização de evento | Para se certificar de que as suas consultas funcionam da forma que esperava, precisa de análise da cadeia saber qual o formato serialização (JSON, CSV ou Avro) estiver a utilizar o recebidas sequências de dados. |
| Codificação | UTF-8 é o formato de codificação suportado apenas neste momento. |

Quando os dados provenientes de uma origem de IoT concentrador, pode aceder a alguns campos de metadados na sua consulta de análise da cadeia. A tabela abaixo apresenta os campos e a respectiva descrição.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| EventProcessedUtcTime | A data e hora em que o evento foi processado. |
| EventEnqueuedUtcTime | A data e hora em que o evento foi recebido pelo concentrador IoT. |
| PartitionId | O ID da partição zero como base para a placa de entrada. |
| IoTHub.MessageId | Utilizado para correlacionar comunicação bidirecional no Centro de IoT. |
| IoTHub.CorrelationId | Utilizados nas respostas de mensagem e comentários no Centro de IoT. |
| IoTHub.ConnectionDeviceId | O id autenticado utilizado para enviar esta mensagem, carimbo de data / nas mensagens que servicebound por IoT concentrador. |
| IoTHub.ConnectionDeviceGenerationId | GenerationId do dispositivo autenticado utilizado para enviar esta mensagem, carimbo de data / nas mensagens que servicebound por IoT concentrador. |
| IoTHub.EnqueuedTime | Hora quando a mensagem foi recebida pelo IoT concentrador. |
| IoTHub.StreamId | Propriedade de evento personalizado adicionada pelo dispositivo de remetente. |

## <a name="create-a-blob-storage-data-stream-input"></a>Criar uma entrada de sequência de dados de armazenamento de BLOBs

Cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o armazenamento de BLOBs oferece uma solução rentável e dimensionável. Dados de [armazenamento de BLOBs](https://azure.microsoft.com/services/storage/blobs/) geralmente considera dados "at rest", mas pode ser processado como uma sequência de dados por análise da cadeia. Um cenário comuns para introduções de armazenamento de Blobs com a análise da cadeia é processamento do registo, onde telemetria é capturada a partir de um sistema e tem de ser analisado e processadas para extrair dados significativos.

É importante ter em atenção que o carimbo de hora predefinido dos eventos de armazenamento de BLOBs em sequência Analytics é o carimbo de hora a que o blob foi modificado pela última vez que *isBlobLastModifiedUtcTime*. Para processar os dados como uma sequência de utilizar um carimbo de data/hora na carga útil evento, a palavra-chave [Carimbo por](https://msdn.microsoft.com/library/azure/dn834998.aspx) deve ser utilizada.

Tenha em atenção que CSV formatado entradas **exigir** uma linha de cabeçalho para definir campos para o conjunto de dados. Mais campos de linha de cabeçalho tem de ser **exclusivo**.

> [AZURE.NOTE] Análise da cadeia não suporta a adicionar conteúdo a um blob existente. Análise da cadeia só irá ver um blob de uma vez e eventuais alterações concluídas depois deste leitura não será processada. É a melhor prática carregar todos os dados de uma vez e não adicionar quaisquer eventos adicionais ao arquivo de Blobs.

A tabela abaixo explica cada propriedade na caixa de diálogo do entrada de armazenamento de Blobs com a respetiva descrição:

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será utilizado na consulta tarefa para fazer referência a esta entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde se encontram os seus ficheiros de Blobs.</td>
</tr>
<tr>
<td>Chave de conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contentor de armazenamento
</td>
<td>Contentores fornecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Quando carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob.</td>
</tr>
<tr>
<td>Caminho do prefixo com padrão [opcional]</td>
<td>O caminho do ficheiro utilizado para localizar o seu blobs dentro do contentor especificado.
Dentro do caminho, pode optar por especificar um ou mais ocorrências de 3 variáveis que se seguem:<BR>{date}, {hora}<BR>{partição}<BR>Exemplo 1: cluster1/registos / {date} / {tempo} / {partição}<BR>Exemplo 2: cluster1/registos / {date}<P>Tenha em atenção que "*" não é um valor permitido para pathprefix. Só válido <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">carateres de Blobs do Azure</a> são permitidos.</td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data é utilizado no caminho do prefixo, pode selecionar o formato de data na qual os seus ficheiros estão organizados. Exemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se for utilizado o token de tempo no caminho do prefixo, especifique o formato da hora em que os seus ficheiros estão organizados. Atualmente, o valor de suportado apenas é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Para se certificar de que as suas consultas funcionam da forma que esperava, precisa de análise da cadeia saber qual o formato serialização (JSON, CSV ou Avro) estiver a utilizar o recebidas sequências de dados.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Para CSV e JSON, UTF-8 é o formato de codificação suportado apenas neste momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Análise da cadeia suporta um número de delimitadores comuns para dados serialização no formato CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.</td>
</tr>
</tbody>
</table>

Quando os seus dados está a chegar a partir de uma origem de armazenamento de BLOBs, pode aceder a alguns campos de metadados na sua consulta de análise da cadeia. A tabela abaixo apresenta os campos e a respectiva descrição.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| BlobName | O nome do blob entrado que o evento é proveniente de. |
| EventProcessedUtcTime | A data e hora em que o evento foi processado pela análise da cadeia. |
| BlobLastModifiedUtcTime | A data e hora em que o blob foi modificado pela última vez. |
| PartitionId | O ID da partição zero como base para a placa de entrada. |

Por exemplo, pode escrever uma consulta como o seguinte:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos
Aprendeu sobre as opções de ligação de dados no Azure para as tarefas de análise da cadeia. Para saber mais sobre a análise da cadeia, consulte o artigo:

- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

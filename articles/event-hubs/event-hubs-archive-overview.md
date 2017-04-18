<properties
    pageTitle="Evento Azure concentradores arquivo | Microsoft Azure"
    description="Descrição geral da funcionalidade Azure evento concentradores arquivo."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Evento Azure concentradores arquivo

Azure evento concentradores arquivo permite-lhe entregar automaticamente os dados de transmissão no seu concentradores de evento para uma conta de armazenamento de BLOBs da sua preferência com flexibilidade para especificar uma hora ou o tamanho do intervalo da sua escolha. Configurar o arquivo é rápido, não existem não existem custos administrativos executá-la e -escalas automaticamente com o seu evento concentradores [unidades débito](event-hubs-overview.md#capacity-and-security). Arquivo de concentradores evento é a forma mais fácil para carregar os dados em tempo real no Azure e permite-lhe focar-se em processamento de dados em vez de captura de dados.

Azure evento concentradores arquivo permite-lhe tubagens baseadas em lote e em tempo real no mesmo fluxo de processo. Permite-lhe criar soluções que podem crescer com as suas necessidades ao longo do tempo. Se estiver a compilar sistemas baseados no lote hoje com um olho no sentido futuro processamento em tempo real ou que pretende adicionar um caminho frio eficiente numa solução em tempo real existente, o evento concentradores arquivo faz com que trabalhar com dados mais fácil de transmissão.

## <a name="how-event-hubs-archive-works"></a>Como funciona o evento concentradores arquivo

Evento concentradores é uma tempo de retenção resistente a memória intermédia para penetração de telemetria, semelhante a um registo distribuído. A tecla para dimensionar no evento concentradores é o [modelo do consumidor com partições](event-hubs-overview.md#partition-key). Cada partição é um segmento independente dos dados e é consumida de forma independente. Ao longo do tempo estes dados idades desativar, com base no período de retenção configuráveis. Como resultado, um determinado concentrador de evento nunca obtém "demasiado cheio."

Evento concentradores arquivo permite-lhe especificar o seu próprio conta de armazenamento de Blobs do Azure e o contentor que será utilizada para armazenar os dados arquivados. Esta conta pode ser na mesma região como o concentrador de evento ou noutra região, adicionar a flexibilidade da funcionalidade evento concentradores arquivo.

Formato de [Apache Avro][] ; escritos dados arquivados um formato compacto, rápido binário que fornece estruturas de dados avançada com esquema inline. Este formato amplamente é utilizado no ecossistema Hadoop e por fábrica de dados do Azure e de análise da cadeia. Obter mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="archive-windowing"></a>Sistema baseado em janelas do arquivo

Evento concentradores arquivo permite-lhe configurar uma janela para controlar o arquivo. Esta janela é uma configuração de tempo com uma "primeira wins política," que significa que primeiro accionador encontrado causará uma operação de arquivo e tamanho mínimo. Se tiver uma janela de arquivo quinze-minuto/100 MB e enviar MB 1/s, irá acionar a janela tamanho antes da janela de tempo. Cada partição arquivos de forma independente e escreve um blob bloco concluída no momento da arquivo, com o nome para a hora de quando o intervalo de arquivo foi encontrado. A Convenção de nomenclatura é da seguinte forma:

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Dimensionamento as unidades de débito

Tráfego de concentradores evento é controlado pelo [unidades débito](event-hubs-overview.md#capacity-and-security). Uma unidade de débito única permite MB 1/s ou 1000 eventos por segundo de penetração e duas vezes a quantidade de saída. Padrão evento concentradores podem ser configurados com unidades de débito 1-20 e mais podem ser adquirido através de um [pedido de suporte][]de quota aumentar. A utilização para além das unidades compradas débito está limitada. Evento concentradores arquivo copia os dados diretamente a partir do armazenamento de evento concentradores interno, ignorar as quotas de saída de unidade débito e guardar a sua saída para outros leitores de processamento de como a análise da cadeia ou motores.

Depois de configurado, o evento concentradores arquivo é executado automaticamente assim que enviar o seu primeiro evento. Continua a ser executado sempre. Para tornar mais fácil para o seu processamento descendentes saber que o processo está a funcionar, o evento concentradores escreve ficheiros vazios quando não haja dados. Este procedimento fornece uma cadence previsível e o marcador que pode feed processadores lote.

## <a name="setting-up-event-hubs-archive"></a>Configurar o arquivo de concentradores de evento

Evento concentradores arquivo pode ser configurado na hora de criação de evento concentrador através do portal ou Gestor de recursos do Azure. Basta ativar arquivo ao clicar no botão **no** . Configurar uma conta de armazenamento e contentor ao clicar em secção de **contentor** da pá. Porque evento concentradores arquivo utiliza o serviço de serviço de autenticação com armazenamento, não tem de especificar uma cadeia de ligação de armazenamento. O selecionador de recurso seleciona o URI do recurso para a sua conta de armazenamento automaticamente. Se utilizar o Gestor de recursos do Azure, tem de fornecer este URI explicitamente como uma cadeia.

A janela de tempo predefinido é de cinco minutos. O valor mínimo é 1, o máximo 15. A janela **tamanho** tem um intervalo de 10 500 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Adicionar arquivo a um concentrador de evento existente

Arquivos podem ser configurados no concentradores evento existente que estão num espaço de nomes do evento concentradores. A funcionalidade não está disponível no mais antigos mensagens ou misto tipo espaços de nomes. Para ativar o arquivo num concentrador de evento existente ou para alterar as definições de arquivo, clique em seu espaço de nomes para carregar o pá **Ensinar e** , em seguida, clique no centro do evento para o qual pretende ativar ou altere a definição de arquivo. Por fim, clique na secção **Propriedades** do abrir pá, conforme apresentado na figura seguinte.

![][2]

Também pode configurar o arquivo de concentradores evento através do Gestor de recursos do Azure modelos. Para obter mais informações, consulte [Este artigo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Explorar o arquivo e trabalhar com Avro

Depois de configurado, o evento concentradores arquivo cria ficheiros na conta de armazenamento do Windows Azure e contentor fornecidos na janela do período de tempo configurado. Pode ver esses ficheiros em qualquer ferramenta de como o [Explorador de armazenamento do Azure][]. Pode transferir os ficheiros localmente para trabalhar nos mesmos.

Os ficheiros produzidos pelo evento concentradores arquivo têm o esquema de Avro seguinte:

![][3]

Uma forma fácil de explorar Avro ficheiros é utilizando a para [Avro ferramentas][] caixa a partir do Apache. Depois de transferir para esta caixa, pode ver o esquema de um determinado ficheiro Avro executando o seguinte comando:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Este comando devolver

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Também pode utilizar as ferramentas de Avro para converter o ficheiro de formato JSON e executar outras processamento.

Para executar o processamento mais avançado, transfira e instale Avro para sua escolha da plataforma. No momento deste escrita, existem implementações disponíveis para a C, C++, C\#, Java, NodeJS, Perl, PHP, Python e rubi.

Apache Avro tem concluídos guias de introdução para [Java][] e [Python][]. Também pode ler o artigo [Introdução ao arquivo de concentradores do evento](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Como é cobrado evento concentradores arquivo

Arquivo de concentradores evento é com tráfego limitado da mesma forma as unidades de débito, como um encargo de hora a hora. O encargo é proporcional diretamente para o número de unidades de débito compradas para o espaço de nomes. À medida unidades débito são aumentadas e diminuiu, evento concentradores arquivo aumenta e diminui para fornecer o desempenho correspondente. Os metros ocorrem em conjunto. O encargo de evento concentradores arquivo é 0,10 $ por hora por unidade de débito, oferecida com um desconto de 50% durante o período de pré-visualização.

Evento concentradores arquivo verdadeiramente é a forma mais fácil para colocar dados no Azure. Utilizar o Azure dados Lake, fábrica de dados do Azure e Azure HDInsight, pode efetuar processamento batch e outros análise da sua escolha utilizando ferramentas familiares e plataformas em qualquer escala precisar.

## <a name="next-steps"></a>Próximos passos

Pode obter mais informações sobre o evento concentradores visitando as ligações seguintes:

- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .
- [Descrição geral de concentradores do evento][]

[Apache Avro]: http://avro.apache.org/
[pedido de suporte]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Explorador de armazenamento Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Ferramentas de Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
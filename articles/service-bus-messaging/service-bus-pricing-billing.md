<properties 
    pageTitle="Serviço Bus preços e faturação | Microsoft Azure"
    description="Descrição geral do serviço Bus preços de estrutura."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Serviço Bus preços e faturação

Serviço Bus é disponibilizado em camadas Basic, padrão e [Premium](service-bus-premium-messaging.md) . Pode escolher uma camada de serviço para cada espaço de nomes de serviço de serviço Bus que criar e esta seleção camada aplica-se através de todas as entidades criadas dentro desse espaço de nomes.

>[AZURE.NOTE] Para obter informações detalhadas sobre preços de serviço Bus atual, consulte o artigo [Azure Service Bus preços página](https://azure.microsoft.com/pricing/details/service-bus/)e as [Perguntas mais frequentes Bus de serviço](service-bus-faq.md#service-bus-pricing).

Serviço Bus utiliza os metros duas seguintes para filas e tópicos/subscrições:

1. **Operações de mensagens**: definida como chamadas à API contra fila ou tópico/subscrição pontos finais de serviço. Medidor desta irá substituir mensagens enviadas ou recebidas como a unidade principal de utilização de cobrar para filas e tópicos/subscrições.

2. **Ligações controlada**: definida como o número máximo de ligações psom abre contra filas, tópicos ou subscrições durante um período de amostragem uma hora indicada. Este medidor só será aplicada na camada padrão, na qual pode abrir ligações adicionais (anteriormente, as ligações foram limitadas ao livro 100 por fila/tópico/subscrição) para uma taxa nominal por ligação.

A camada **padrão** apresenta preços crescentes para operações realizadas com filas e tópicos/subscrições, que resulta em baseados em volume descontos 80% nos níveis a utilização mais altos. Também existe uma taxa de base camada padrão de 10 $ por mês, que permite-lhe executar operações de 12,5 milhões por mês sem custos adicionais.

A camada **Premium** fornece isolamento de recursos na camada de memória e CPU para que cada carga de trabalho do cliente é executado no isolamento. Neste contentor recurso é denominado uma *unidade de mensagens*. O que cada espaço de nomes de premium é atribuído, pelo menos, uma unidade de mensagens. Pode comprar 1, 2 ou 4 unidades mensagens para cada espaço de nomes de serviço Bus Premium. Uma única carga de trabalho ou de uma entidade pode abranger múltiplas unidades de mensagens e o número de unidades mensagens pode ser alterado aleatoriamente, apesar de faturação está em taxas de taxa de 24 horas ou diariamente. O resultado é desempenho previsível e forma repetido para a sua solução baseada em Bus de serviço. Não só é este desempenho mais previsíveis e disponível, mas também é mais rápido. Mensagens de serviço Bus Premium Azure constrói do motor de armazenamento introduzido numa Azure evento concentradores de dados. Com mensagens Premium, o desempenho de pico é muito mais rápido que a camada padrão.

Note que o encargo de base padrão é cobrado apenas uma vez por mês por subscrição Azure. Isto significa que depois de criar um único padrão ou o espaço de nomes do Premium camada Bus de serviço, poderão criar tantas adicionais padrão ou Premium camada espaços de nomes à medida que pretende nessa mesma subscrição do Azure, sem sempre adicionais de taxas de base.

Todos os existentes serviço Bus espaços de nomes criados antes de 1 de Novembro de 2014 foram colocados automaticamente para a camada padrão. Este procedimento garante que continuar a ter acesso a todas as funcionalidades atualmente disponíveis com Bus de serviço. Posteriormente, pode utilizar o [portal clássica Azure][] para alterar para a camada básica, se pretender.

A tabela seguinte resume as diferenças funcionais entre as camadas Basic e padrão/Premium.

|Capacidade de|Básicas|Padrão/Premium|
|---|---|---|
|Filas|Sim|Sim|
|Mensagens agendadas|Sim|Sim|
|Tópicos/subscrições|N|Sim|
|Estações de retransmissão|N|Sim|
|Transações|N|Sim|
|A duplicação|N|Sim|
|Sessões|N|Sim|
|Mensagens de grandes dimensões|N|Sim|
|ForwardTo|N|Sim|
|SendVia|N|Sim|
|Ligações juntamente (incluídas)|100 por serviço Bus espaço de nomes|1.000 por subscrição Azure|
|Ligações juntamente (hiperdosagem termos permitidos)|N|Sim (cobrar)|

## <a name="messaging-operations"></a>Operações de mensagens

Como parte do novo modelo comparar, faturação filas tópicos/para subscrições do e está a alterar. Estas entidades são na transição a partir de faturação por mensagem a faturação por operação. "Operação" que se refere a qualquer chamada API efetuada contra uma fila ou tópico/subscrição ponto final de serviço. Isto inclui a gestão de envio/receção e sessão operações de estado.

|Tipo de operação|Descrição|
|---|---|
|Gestão|Crie, ler, atualizar, eliminar (CRUD) contra filas ou tópicos/subscrições.|
|Mensagens|Enviar e receber mensagens com filas ou tópicos/subscrições.|
|Estado da sessão|Introdução ou definir o estado da sessão num tópico/subscrição ou fila de espera.|

Os preços seguintes foram eficazes começando 1 de Novembro de 2014:

|Básicas|Custo|
|---|---|
|Operações|$0.05 por milhões de operações|

|Padrão|Custo|
|---|---|
|Encargo de base|$10/ mês|
|Em primeiro lugar, como milhões de 12,5 operações/mês|Incluído|
|12,5-100 milhões de operações/mês|$0.80 por milhões de operações|
|100 milhões - 2500 milhões de operações/mês|$0,50 por milhões de operações|
|Mais 2500 milhões operações/mês|$0,20 por milhões de operações|

|Premium|Custo|
|---|---|
|Diária|$11.13 taxa por mensagem de unidade fixa|

## <a name="brokered-connections"></a>Ligações juntamente

*Ligações de Brokered* acomodar padrões de utilização do cliente que envolvam um grande número de "forma persistente ligado" remetentes/destinatários contra filas, tópicos ou subscrições. Forma persistente ligada remetentes/são aqueles que ligar através do AMQP ou HTTP com um zero não recebem o limite de tempo (por exemplo, HTTP consulta longo). HTTP remetentes e destinatários com um limite de tempo imediata não geram juntamente ligações.

Anteriormente, filas e tópicos/subscrições tinham um limite de 100 ligações em simultâneo por URL. O esquema de faturação atual remove o limite de por URL para filas e tópicos/subscrições e implementa quotas e de medição nas ligações juntamente no serviço Bus espaço de nomes e níveis de subscrição Azure.

A camada básica inclui e está estritamente limitada a, 100 ligações juntamente por serviço Bus espaço de nomes. Ligações acima este número serão rejeitadas na camada básica. A camada padrão remove o limite de por espaço de nomes e conta a utilização de agregação ligação juntamente ao longo da subscrição Azure. Na camada padrão, as ligações juntamente 1.000 por subscrição Azure serão permitidas sem extra de custo (para além do encargo de base). Usar mais do que um total de 1.000 ligações juntamente em camadas padrão serviço Bus espaços de nomes numa subscrição do Azure vai ser faturados numa agenda crescentes, conforme apresentado na seguinte tabela.

|Ligações juntamente (camada padrão)|Custo|
|---|---|
|Primeiro 1.000/mês|Incluído com o encargo de base|
|1.000-100,000/mês|$0.03 por ligação/mês|
|500.000-100,000/mês|0,025 $ por mês/ligação|
|Ao longo de 500.000/mês|$0,015 ligação/mês|

>[AZURE.NOTE] 1.000 ligações juntamente estão incluídas com a camada mensagens padrão (através do encargo de base) e podem ser partilhadas através de todas as filas, tópicos e subscrições a subscrição do Azure associado.

<br />

>[AZURE.NOTE] Faturação é baseada no número máximo de ligações em simultâneo e é rateada por hora com base em horas 744 por mês.

|Camadas de Premium
|---|
|Ligações juntamente não são cobradas na camada Premium.|

Para mais informações sobre ligações juntamente, consulte a secção [FAQ](#faq) mais adiante neste tópico.

## <a name="relay"></a>Reencaminhamento

Estações de retransmissão só estão disponíveis na camada padrão espaços de nomes. Caso contrário, as quotas de preços e ligação de estações de retransmissão que permaneça inalteradas. Isto significa que estações de retransmissão irão continuar a ser cobrada no número de mensagens (não operações) e horas de reencaminhamento.

|Preços de reencaminhamento|Custo|
|---|---|
|Reencaminhamento de horas|$0,10 todas as horas de reencaminhamento de 100|
|Mensagens|$0,01 para todas as 10.000 mensagens|

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

### <a name="how-is-the-relay-hours-meter-calculated"></a>Como é calculado o indicador de reencaminhamento de horas?

Consulte o artigo [neste tópico](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>O que são controlada ligações e como posso obter cobrado por-los?

Uma ligação juntamente é definida como um dos seguintes procedimentos:

1. Uma ligação de AMQP a partir de um cliente para um fila de serviço Bus ou tópico/subscrição.

2. Numa chamada HTTP para receber uma mensagem a partir de um tópico de serviço Bus ou fila que tenha um valor de tempo de espera de receber maior que zero.

Taxas de Bus de serviço para o número máximo de ligações juntamente em simultâneo que exceda a quantidade de incluídos (1.000 na camada padrão). Picos são medidos numa base de hora a hora, rateados por divisão por 744 horas num mês e adição durante o período de faturação mensal. A quantidade de incluídos (ligações juntamente 1.000 por mês) é aplicada no final do período de faturação contra a soma dos Rateado picos preço por hora.

Por exemplo:

1. Cada um dos 10.000 dispositivos liga-se através de uma única ligação AMQP e recebe comandos a partir de um tópico Bus de serviço. Os dispositivos enviar eventos de telemetria a um concentrador de evento. Se todos os dispositivos de ligar para cada dia de 12 horas, aplicadas as taxas de ligação seguinte (para além de quaisquer outros serviço Bus tópico encargos): 10.000 ligações *12 horas* 31 dias / 744 = 5.000 controlada ligações. Após a compensação em mensal de 1.000 ligações juntamente, teria de Serei cobrado para 4.000 ligações juntamente, à taxa do 0.03 $ por juntamente ligação, para um total de $120.

2. 10.000 dispositivos recebem mensagens de uma fila de serviço Bus através de HTTP, especificar um limite de tempo não zero. Se todos os dispositivos ligue-se para todos os dias de 12 horas, irá ver as taxas de ligação seguinte (para além de outros encargos serviço Bus): 10.000 HTTP receber ligações *12 horas por dia* 31 dias / horas 744 = 5.000 controlada ligações.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>São aplicadas taxas de ligação juntamente filas e tópicos/subscrições?

Sim. Não existem sem taxas de ligação para o envio de eventos através de HTTP, independentemente do número de enviar sistemas ou dispositivos. Receber eventos com HTTP utilizando um tempo limite maior que zero, por vezes denominada "tempo de consulta," gera taxas de ligação juntamente. Ligações de AMQP geram taxas de ligação juntamente, independentemente de se as ligações estão a ser utilizadas para enviar ou receber. Tenha em atenção que são permitidas 100 ligações juntamente sem encargos num espaço de nomes básica. Também é o número máximo de ligações juntamente permitidas para a subscrição Azure. As ligações juntamente primeiros 1.000 através de todos os espaços de nomes de padrão numa subscrição do Azure são incluídas sem encargos extra (para além do encargo de base). Uma vez que das permissões suficiente para cobrir muitos cenários de mensagens do serviço de serviço, taxas de ligação juntamente apenas tornam-se normalmente relevantes se planeia utilizar inquéritos de longa AMQP ou HTTP com um grande número de clientes; Por exemplo, para alcançar mais eficientes transmissão de evento ou permitir a comunicação bidirecional com vários dispositivos ou instâncias da aplicação.

## <a name="next-steps"></a>Próximos passos

- Para obter mais detalhes sobre preços Bus de serviço, consulte o artigo [Azure Service Bus preços página](https://azure.microsoft.com/pricing/details/service-bus/).

- Consulte as [Perguntas mais frequentes do serviço Bus](service-bus-faq.md#service-bus-pricing) para algumas perguntas mais frequentes comuns à volta de bus serviço preços e faturação.

[Azure portal clássico]: http://manage.windowsazure.com
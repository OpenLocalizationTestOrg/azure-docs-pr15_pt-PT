<properties 
    pageTitle="Evento concentradores perguntas mais frequentes (FAQ) | Microsoft Azure"
    description="Perguntas mais frequentes do evento concentradores."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Evento concentradores perguntas mais frequentes

Evento concentradores fornece incorporação em grande escala, persistente e processamento de eventos de dados de origens de dados de alto débito e/ou milhões de dispositivos. Quando par com filas Bus de serviço e de tópicos, evento concentradores permite persistentes implementações do comando e controlo para cenários de [Internet coisas (IoT)](https://azure.microsoft.com/services/iot-hub/) .

Este artigo aborda informações sobre preços e responde a algumas perguntas mais frequentes sobre o evento concentradores:

## <a name="pricing-information"></a>Informação de preços

Para obter informações completas sobre preços concentradores de evento, consulte os [Detalhes do evento concentradores preços](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Como são calculados eventos de penetração concentradores evento?

Cada evento enviado a um concentrador de evento conta como uma mensagem cobrar. Um *evento de penetração* é definido como uma unidade de dados que são menor ou igual a 64 KB. Qualquer evento que é menor ou igual a 64 KB de tamanho é considerado como um evento cobrar. Se o evento for maior do que 64 KB, o número de eventos cobrar é calculado de acordo com o tamanho de evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o centro do evento é faturado como um evento, mas uma mensagem 96 KB enviada para o concentrador de evento é faturada como dois eventos.

Eventos consumidos a partir de um concentrador de evento, como bem como operações de gestão e controlam de chamadas, tais como os pontos de verificação, não são contabilizados como eventos de penetração cobrar, mas imputar até a compensação em unidade de débito.

## <a name="what-are-event-hubs-throughput-units"></a>Quais são as unidades de débito concentradores evento?

Selecionar explicitamente unidades de débito concentradores de evento, através do portal do Azure ou modelos do Gestor de evento concentradores recurso. Unidades de débito aplicam a todos os eventos concentradores num espaço de nomes do evento concentradores e cada unidade de débito dá-o direito o espaço de nomes para as seguintes funcionalidades:

- Para cima para 1 MB por segundo de eventos de penetração (eventos enviados a um concentrador de evento), mas não mais de 1000 eventos de penetração, operações de gestão de ou controlo de chamadas à API por segundo.

- Até 2 MB por segundo dos eventos de saída (eventos consumidos a partir de um concentrador de evento).

- 84 GB de armazenamento de evento (suficiente para o período de retenção de 24 horas predefinido).

Unidades de débito do evento concentradores são faturadas hora a hora, com base no número máximo de unidades seleccionadas durante a hora determinada.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Como são impostos evento concentradores débito unidade limites?

Se o débito penetração total ou a taxa de evento penetração total em todos os eventos concentradores num espaço de nomes exceder das permissões de unidade de agregação débito, remetentes irão estar limitados e recebem erros indicando que a quota de penetração foi excedida.

Se o débito saída total ou a taxa de saída de evento total em todos os eventos concentradores num espaço de nomes exceder das permissões de unidade de agregação débito, destinatários são limitados e recebem erros indicando que tenha sido ultrapassada a quota de saída. As quotas de entrada e saída são impostas separadamente, para que não remetente pode causar consumo de evento para diminuir a velocidade, nem pode um auscultador impedir que os eventos que está a ser enviados a um concentrador de evento.

Note que a seleção de unidade débito é independentemente do número de partições concentradores do evento. Enquanto cada partição oferece um débito máximo de 1 MB por segunda penetração (com um máximo de eventos de 1000 por segundo) e 2 MB por segunda saída, não existe nenhuma fixo Rateado pelas partições próprios. O encargo destina-se as unidades de débito agregado no todos os eventos concentradores num espaço de nomes do evento concentradores. Com este padrão, pode criar suficiente partições para suportar a carga máxima antecipada seus sistemas, sem sempre qualquer taxas de unidade débito até a carga de eventos no sistema realmente requer os números mais altos débito e, sem ter de alterar a estrutura e a arquitetura do seu sistemas como a carregar no sistema aumenta.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Existe um limite no número de unidades de débito que podem ser selecionados?

Existe uma quota predefinida de 20 unidades de débito por espaço de nomes. Pode pedir uma maior quota de unidades de débito através de apresentação de um bilhetes de suporte. Além do limite da unidade 20 débito, conjuntos estão disponíveis em 20 e 100 unidades de débito. Tenha em atenção que utilizar mais do que 20 unidades de débito remove a capacidade de alterar o número de unidades débito sem arquivar um bilhetes de suporte.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe um encargo para reter eventos de evento concentradores mais do que 24 horas?

A camada evento concentradores padrão permitir retenção mensagem períodos maiores do que 24 horas, para um máximo de 30 dias. Se o tamanho do número total de eventos armazenados exceder a compensação em armazenamento para o número de unidades débito selecionado (84 GB por unidade de débito), o tamanho que exceda a compensação em é cobrado à taxa de armazenamento de Blobs do Azure publicada. A compensação em armazenamento em cada unidade de débito abrange todos os custos de armazenamento para períodos de retenção de 24 horas (a predefinição), mesmo se a unidade de débito é utilizada para cima, para a compensação em penetração máximo.

## <a name="what-is-the-maximum-retention-period"></a>O que é o período de retenção máximo?

Camadas de concentradores padrão de evento suporta atualmente um período de retenção máximo de 7 dias. Tenha em atenção que concentradores evento não destinam-se como um arquivo de dados permanente. Períodos de retenção maiores do que 24 horas destinam-se para obter cenários em que é conveniente repetir uma sequência de um evento para sistemas de mesmo; Por exemplo, para formar ou verificar um novo modelo de aprendizagem máquina nos dados existentes.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é que o tamanho do armazenamento de evento concentradores é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo quaisquer sobrecarga interna para cabeçalhos de evento ou nas estruturas de armazenamento do disco em todos os eventos concentradores, é medido ao longo do dia. No final do dia, o tamanho máximo de armazenamento é calculado. A diária de armazenamento é calculada com base no número mínimo de unidades de débito que foram selecionadas durante o dia (cada unidade de débito proporciona um ajustamento de 84 GB). Se o tamanho total exceder calculados diária armazenamento, o armazenamento de excesso é faturado utilizando as taxas de armazenamento de Blobs do Azure (à taxa **Localmente armazenamento redundante** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>Pode utilizar uma ligação de AMQP simples para enviar e receber a partir do evento concentradores e serviço Bus filas/tópicos?

Sim, desde que todos os eventos concentradores, filas e tópicos estão no mesmo espaço de nomes. Como tal, pode implementar conectividade bidirecional, juntamente com vários dispositivos com latências subsecond, de forma rentável e altamente dimensionáveis.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>São aplicadas taxas de ligação juntamente a concentradores evento?

Remetentes, taxas de ligação aplicam-se apenas quando é utilizado o protocolo AMQP. Não existem sem taxas de ligação para o envio de eventos através de HTTP, independentemente do número de enviar sistemas ou dispositivos. Se planeia utilizar AMQP (por exemplo, para alcançar mais eficientes transmissão de evento ou para permitir a comunicação bidirecional em cenários de comando e o controlo de IoT), consulte a página de [informação de preços Bus de serviço](https://azure.microsoft.com/pricing/details/service-bus/) para obter informações sobre o que constitui uma ligação juntamente e como estes são com tráfego limitados.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>O que é a diferença entre evento concentradores básicas e camadas padrão?

Camadas de concentradores padrão de evento disponibiliza funcionalidades para além das que está disponível no evento concentradores básicas e no alguns sistemas competitivos. Estas funcionalidades incluem períodos de retenção de mais do que 24 horas e a capacidade de utilizar uma ligação de AMQP simples para enviar comandos para grandes quantidades de dispositivos com latências subsecond, bem como para enviar telemetria a partir desses dispositivos concentradores evento. Para a lista de funcionalidades, consulte os [Detalhes do evento concentradores preços](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilidade geográfica

Evento concentradores está disponível nas seguintes regiões:

|Geo|Regiões|
|---|---|
|Estados Unidos|Central (EUA), Leste dos EUA, Leste dos EUA 2, Sul Central (EUA), ocidental dos e.u.a.|
|Europa|Europa Norte, Europa Ocidental|
|Ásia-Pacífico|Este asiático, Sudeste asiático|
|Japão|Japan leste, oeste Japão|
|Brasil|Sul do Brasil|
|Austrália|Austrália Oriente, Sudeste Austrália|

## <a name="support-and-sla"></a>Suporte e SLA

Suporte técnico para evento concentradores está disponível através de do [fóruns da Comunidade](https://social.msdn.microsoft.com/forums/azure/home). Suporte a gestão de faturação e subscrição é fornecido nenhum custo.

Para saber mais sobre os nossos SLA, consulte a página [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre concentradores de evento, consulte os artigos seguintes:

- [Descrição geral de evento concentradores][].
- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.

[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<properties 
    pageTitle="Azure filas e serviço Bus filas - comparados e constrastados | Microsoft Azure"
    description="Analisa as diferenças e semelhanças entre dois tipos de filas disponibilizadas pelos Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure filas e serviço Bus filas - comparados e constrastados

Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidos pelo Microsoft Azure hoje: filas Azure filas e Bus de serviço. Ao utilizar estas informações, pode comparar e as respetivas tecnologias de contraste e possam fazer uma decisão mais informada sobre o qual a solução melhor às suas necessidades.

## <a name="introduction"></a>Introdução

Microsoft Azure suporta dois tipos de mecanismos de fila: **Azure filas** e **Filas de Bus de serviço**.

**Azure filas**, que fazem parte da infraestrutura do [armazenamento Azure](https://azure.microsoft.com/services/storage/) , a funcionalidade uma interface com base no resto colocar/obter/pré-visualização simples, fornecendo fiável, persistente mensagens no interior e entre os serviços.

**Serviço Bus filas** fazem parte de uma infraestrutura [Azure mensagens](https://azure.microsoft.com/services/service-bus/) mais amplo que suporte a colocação assim como publicar/subscrever, comunicação remota de serviço Web e padrões de integração. Para mais informações sobre o serviço Bus filas, tópicos/subscrições e estações de retransmissão, consulte o artigo [Descrição geral do serviço Bus messaging](service-bus-messaging-overview.md).

Apesar de ambas as tecnologias de colocação já existirem em simultâneo, Azure filas foram introduzidas em primeiro lugar, como um mecanismo de armazenamento de fila dedicada incorporado na parte superior dos serviços de armazenamento Azure. São criadas as filas de serviço Bus na parte superior a infraestrutura de "controlada mensagens" mais amplo concebida para integrar aplicações ou componentes de aplicação que podem abranger vários protocolos de comunicação, contratos de dados, confiar em domínios e/ou ambientes de rede.

Este artigo compara as tecnologias de duas fila disponibilizadas pelos Azure por debater as diferenças na comportamento e implementação das funcionalidades fornecidas pelo cada um. O artigo fornece também orientações para escolher quais as funcionalidades que poderão melhor que se adaptem às suas necessidades de desenvolvimento de aplicações.

## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia

Azure filas e serviço Bus filas são implementações da colocação de serviço previewhttp no Microsoft Azure. Cada tem um conjunto de funcionalidade ligeiramente diferente, o que significa que pode escolher uma ou de outra ou utilizar ambas, dependendo as necessidades da sua solução específica ou que está a resolução de problemas de empresas/técnicos.

Quando para determinar qual a tecnologia colocação se adequa ao objectivo para obter uma solução determinado, arquitectura de solução tanto os programadores deverão tomar em consideração as recomendações abaixo. Para obter mais detalhes, consulte a secção seguinte.

Como solução architect/programador, **considere utilizar Azure filas** quando:

- A aplicação tem de guardar mais 80 GB das mensagens na fila de espera, onde as mensagens de tem uma duração mais curta de 7 dias.

- Pretende que a aplicação controlar o progresso de processamento de uma mensagem dentro de fila de espera. Isto é útil se o trabalhador processamento de uma mensagem falha. Trabalhador subsequente, em seguida, pode utilizar essas informações para continuar a partir de onde parou trabalhador prévio.

- Exigir a registos do lado do servidor de todas as transações executadas em relação a suas filas.

Como solução architect/programador, **considere utilizar o serviço Bus filas** quando:

- A solução tem de conseguir receber mensagens sem ter de consultar fila de espera. Com Bus de serviço, este pode ser obtido através da utilização de inquéritos longa receber operação utilizando os protocolos com base em TCP que Bus de serviço de suporte.

- A solução requer fila de espera para fornecer uma garantia primeiro-em-primeiro-out (FIFO) encomendou entrega.

- Pretende que uma experiência simétrica no Azure e no Windows Server (nuvem privada). Para mais informações, consulte o artigo [Bus de serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- A solução tem de conseguir suporta a deteção automática de duplicados.

- Pretende que a aplicação para processar mensagens como paralelas sequências de execução longa (mensagens são associadas com uma sequência utilizando a propriedade de [ID de sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) na mensagem). Neste modelo, cada nó na aplicação consome compete de sequências de, por oposição a mensagens. Quando uma sequência é atribuída a um nó consome, o nó pode verificar o estado do Estado da aplicação da cadeia utilizando as transações.

- A solução requer o comportamento transaccional e atomicidade quando enviar ou receber várias mensagens a partir de uma fila.

- A time to live (TTL) característica em comum a carga de trabalho específicos da aplicação pode exceder o período de 7 dias.

- A aplicação processa as mensagens que podem exceder 64 KB, mas irá limitar a abordagem não provável 256 KB.

- Lidar com um requisito para fornecer um modelo de acesso baseado em funções para as filas e direitos/permissões diferentes para remetentes e destinatários.

- Não aumentará superior a 80 GB de tamanho do seu fila de espera.

- Que pretende utilizar o AMQP baseadas em normas mensagens protocolo 1.0. Para mais informações sobre AMQP, consulte o artigo [Descrição geral do serviço Bus AMQP](./service-bus-amqp-overview.md).

- Poderá visualizar uma migração eventual de comunicação ponto a ponto baseadas na fila de espera para um padrão de intercâmbio de mensagem que permite a integração do total de destinatários adicionais (subscritores), cada um dos quais recebe independentes cópias das algumas ou todas as mensagens enviadas para a fila. O último refere-se a funcionalidade de publicar/subscrever vierem fornecida pelo serviço Bus.

- A solução mensagens tem de conseguir suportar a garantia de entrega "Maioria-vez" sem ser necessário para criar os componentes de infraestrutura adicional.

- Pretender para conseguir publicar e consumir lotes de mensagens.

- Exigir a integração total com a pilha de comunicações do Windows Communication Foundation (WCF) no .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Comparar filas Azure filas e Bus de serviço

As tabelas nas secções seguintes fornecem um agrupamento lógico das funcionalidades de fila de espera e permitem-lhe comparar, de imediato, as funcionalidades disponíveis no Azure filas e filas Bus de serviço.

## <a name="foundational-capabilities"></a>Capacidades de base destinadas

Esta secção compara algumas das capacidades de colocação fundamentais fornecidas pela filas Azure filas e Bus de serviço.

|Critérios de comparação|Azure filas|Serviço Bus filas|
|---|---|---|
|Ordenação garantia|**N** <br/><br>Para mais informações, consulte o artigo na primeira nota a secção "Informações adicionais".</br>|**Sim - First no First-Out (FIFO)**<br/><br>(através da utilização de mensagens sessões)|
|Garantia de entrega|**Menos-vez**|**Menos-vez**<br/><br/>**Na maior parte de uma vez**|
|Suporte de operação Atómica|**N**|**Sim**<br/><br/>|
|Receber o comportamento|**Bloquear não**<br/><br/>(conclui imediatamente não se for encontrada nenhuma mensagem nova)|**Bloquear com/sem limite de tempo**<br/><br/>(ofertas longos inquéritos ou a ["Técnica errante"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bloquear não**<br/><br/>(através da utilização de .NET API gerida do apenas)|
|API Push de estilo|**N**|**Sim**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) e **OnMessage** sessões .NET API.|
|Modo de recepção|**Caixa de pré-visualização e locação financeira**|**Pré-visualização & Bloquear**<br/><br/>**Receber e eliminar**|
|Modo de acesso exclusivo|**Com base em locação financeira**|**Com base em bloquear**|
|Locação financeira/bloquear duração|**30 segundos (predefinição)**<br/><br/>**7 dias (máximos)** (Pode renovar ou libertar uma locação mensagem utilizando [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API.)|**60 segundos (predefinição)**<br/><br/>Pode renovar um cadeado mensagem utilizando [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API.|
|Locação financeira/bloquear precisão|**Nível de mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferente, em seguida, pode atualizar, conforme seja necessário durante o processamento de mensagem, utilizando o [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API)|**Nível de fila de espera**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas pode renovar o bloqueio utilizando [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API.)|
|Enviadas em batches receber|**Sim**<br/><br/>(explicitamente especificando o número de mensagens quando obtenção de mensagens, até um máximo de 32 mensagens)|**Sim**<br/><br/>(implicitamente ativar uma propriedade de obtenção de pré-lançamento ou explicitamente através da utilização de transações)|
|Enviar por lotes|**N**|**Sim**<br/><br/>(através da utilização de transações ou ao lado do cliente de lotes)|

### <a name="additional-information"></a>Informações adicionais

- Mensagens em filas Azure são normalmente primeiro-em-primeiro-out, mas, por vezes, pode ser fora de ordem; Por exemplo, quando duração de tempo limite de visibilidade de uma mensagem expira (por exemplo, como resultado de uma aplicação de cliente falhar durante o processamento). Quando o tempo limite visibilidade expira, a mensagem fica visível novamente na fila de espera para outro trabalhador descarregado-lo. Nesse momento, a mensagem recentemente visível pode ser colocada na fila de espera (para ser dequeued novamente) depois de uma mensagem que foi originalmente colocado em fila após-lo.

- Se já estiver a utilizar o Azure armazenamento de Blobs ou tabelas e pode começar a utilizar o filas, são garantir 99,9% de disponibilidade. Se utilizar Blobs ou tabelas com filas Bus de serviço, terá de disponibilidade inferior.

- O padrão de FIFO garantido em filas de serviço Bus necessita da utilização sessões de mensagens. Se que a aplicação falha durante o processamento de uma mensagem recebida no modo de **pré-visualização & Bloquear** , da próxima vez que um destinatário fila aceita uma sessão de mensagens, será iniciado com a mensagem falha após a expira respectivo período de time to live (TTL).

- Azure filas foram concebidas para suportar cenários de colocação padrão, tais como desacoplamento componentes da aplicação para aumentar escalabilidade e tolerância a falhas, carregar o nivelamento e de construção de fluxos de trabalho do processo.

- Serviço Bus filas suportam a garantia de entrega *Menos-vez* . Além disso, *a maior parte-vez* semântico podem ser suportados utilizando o estado da sessão para armazenar o estado da aplicação e utilizando as transações atomicamente receber mensagens e atualizar o estado da sessão.

- Azure filas fornecem um modelo de programação uniforme e consistente em filas, tabelas e BLOBs – para programadores e para as equipas de operações.

- Filas de serviço Bus fornecem suporte para transações locais no contexto de uma única fila.

- O modo de **receber e eliminar** suportado pelo serviço Bus fornece a capacidade para reduzir a contagem de operação mensagens (e custos associados) em troca de assurance descida entrega.

- Azure filas fornecem concessões com a capacidade para expandir as concessões para mensagens. Isto permite que os colaboradores na área manter as concessões breves nas mensagens. Assim, se um trabalhador falha, a mensagem pode ser rapidamente processada novamente por outro trabalhador. Além disso, um trabalhador pode expandir a locação numa mensagem se necessita processá-la mais da hora de locação financeira atual.

- Azure filas oferecem um visibilidade limite de tempo que pode definir relativamente a enqueueing ou a opção de retirar fila de uma mensagem. Além disso, pode atualizar uma mensagem com valores de diferentes locação financeira em tempo de execução e atualizar diferentes valores ao longo de mensagens na fila do mesmo. Tempos limite de bloqueio de serviço Bus é definido em metadados fila; No entanto, pode renovar o bloqueio ao contactar o método de [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- O tempo limite máximo para um bloqueio receber operação em filas de serviço Bus é 24 dias. No entanto, com base no resto tempos limite tem um valor máximo de 55 segundos.

- Lado do cliente de lotes fornecidos pelo serviço Bus permite que um cliente fila batch várias mensagens para uma operação de envio único. Só está disponível para enviar assíncrona operações de lotes.

- Funcionalidades, como o limite máximo de 200 TB de Azure filas (mais quando virtualizar em contas) e filas ilimitadas tornam uma plataforma ideal para fornecedores de SaaS.

- Fornecem uma flexível filas Azure e performant delegada mecanismo de controlo de acesso.

## <a name="advanced-capabilities"></a>Funcionalidades avançadas

Esta secção compara as funcionalidades avançadas fornecidas pela filas Azure filas e Bus de serviço.

|Critérios de comparação|Azure filas|Serviço Bus filas|
|---|---|---|
|Entrega agendada|**Sim**|**Sim**|
|Letras inactivo automáticas|**N**|**Sim**|
|Aumentar o valor de time to live fila de espera|**Sim**<br/><br/>(através de atualização no local de tempo limite de visibilidade)|**Sim**<br/><br/>(fornecido através de uma função da API dedicada)|
|Poison suporte de mensagem|**Sim**|**Sim**|
|Atualização no local|**Sim**|**Sim**|
|Registo de transações do lado do servidor|**Sim**|**N**|
|Métricas de armazenamento|**Sim**<br/><br/>**Métricas de minuto**: fornece métricas em tempo real para disponibilidade, TP, de API chamadas contagens, contagens de erro e muito mais, tudo em tempo real (agregado por minuto e comunicado dentro de alguns minutos a partir do que aconteceu apenas de produção. Para mais informações, consulte o artigo [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Sim**<br/><br/>(consultas em volume ao contactar o suporte [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Gestão de Estados|**N**|**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Reencaminhamento de automática de mensagens|**N**|**Sim**|
|Limpar a função de fila de espera|**Sim**|**N**|
|Grupos de mensagem|**N**|**Sim**<br/><br/>(através da utilização de mensagens sessões)|
|Estado da aplicação por grupo de mensagens|**N**|**Sim**|
|Deteção de duplicados|**N**|**Sim**<br/><br/>(configurável no lado remetente)|
|Integração de WCF|**N**|**Sim**<br/><br/>(oferece enlaces do out of box WCF)|
|Integração de WF|**Personalizada**<br/><br/>(requer a criação de uma atividade WF personalizada)|**Nativo**<br/><br/>(oferece atividades WF fora da caixa)|
|Navegação de mensagem de grupos|**N**|**Sim**|
|A obtenção de sessões de mensagens por ID|**N**|**Sim**|

### <a name="additional-information"></a>Informações adicionais

- Ambas as tecnologias de colocação de ativar uma mensagem ser agendado para entrega tarde.

- Fila de espera automaticamente-reencaminhamento de chamadas permite milhares de filas para reencaminhar automaticamente suas mensagens para uma única fila, a partir do qual a aplicação de recepção consome a mensagem. Pode utilizar este mecanismo para alcançar segurança, controlar o fluxo de e isolar armazenamento entre cada editor de mensagem.

- Azure filas fornecem suporte para atualizar o conteúdo da mensagem. Pode utilizar esta funcionalidade para persistentes informações de estado e atualizações do progresso utilizarão para a mensagem para que possam ser processada a partir do último conhecido ponto de verificação, em vez de começar de raiz. Com filas Bus de serviço, pode ativar o mesmo cenário através da utilização de sessões de mensagens. Sessões permitem-lhe guardar e obter o estado de processamento de aplicação (ao utilizar [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Inactivo letras](service-bus-dead-letter-queues.md), que apenas é suportado pelo filas Bus de serviço, podem ser úteis para isolar mensagens que não não possível processar com êxito pela aplicação de recepção ou quando as mensagens não é possível alcançar os respetivos destino devido a um expirados time to live (TTL) de propriedade. O valor TTL indica quanto tempo que uma mensagem permanece na fila de espera. Com o serviço Bus, a mensagem será movida para uma fila especial denominada $DeadLetterQueue quando o período TTL expirar.

- Para localizar mensagens "corrompida foi" no Azure filas, quando a opção de retirar fila uma mensagem a aplicação examina a propriedade **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** da mensagem. Se for **DequeueCount** acima de um determinado limiar, a aplicação move a mensagem para uma fila definidas pela aplicação "entregues".

- Azure filas permitem-lhe obter um registo detalhado de todas as transações executadas em relação a fila de espera, como métricas bem como agregadas. Ambas as opções são úteis para depurar e compreender como a aplicação utiliza Azure filas. Também são úteis para-Otimização do desempenho da aplicação e reduzindo os custos de utilização de filas.

- O conceito de "sessões de mensagem" suportado pelo serviço Bus permite que as mensagens que pertencem a um determinado grupo lógico de ser associadas um determinado recetor, que por sua vez cria uma afinidade sessão gosto entre mensagens e os respetivos respetivos destinatários. Pode ativar esta funcionalidade avançada no serviço Bus definindo a propriedade [identificador de sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) numa mensagem. Destinatários, em seguida, podem escutar um ID de sessão específica e receber mensagens que partilham o identificador de sessão especificada.

- A funcionalidade de detecção duplicação suportada pelo filas serviço Bus automaticamente remove duplicadas mensagens enviadas para uma fila ou tópico, baseado no valor da propriedade [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Capacidade e as quotas de

Esta secção compara filas Azure filas e Bus de serviço da perspetiva de [capacidade e as quotas de](service-bus-quotas.md) que pode aplicar.

|Critérios de comparação|Azure filas|Serviço Bus filas|
|---|---|---|
|Tamanho máximo da fila|**200 TB**<br/><br/>(limitado para uma capacidade de conta de armazenamento única)|**1 GB para 80 GB**<br/><br/>(definido após a criação de uma fila de espera e, em seguida, [Ativar a partições](service-bus-partitioning.md) –, consulte a secção "Informações adicionais")|
|Tamanho máximo da mensagem|**64 KB**<br/><br/>(48 KB ao utilizar a codificação **Base64** )<br/><br/>Azure suporta mensagens grandes combinando filas e blobs – altura em que pode colocar até 200GB para um único item.|**256 KB** ou **1 MB**<br/><br/>(incluindo o cabeçalho e corpo, o tamanho máximo de cabeçalho: 64 KB).<br/><br/>Depende da [camada de serviços](service-bus-premium-messaging.md).|
|TTL máximo da mensagem|**7 dias**|**`TimeSpan.Max`**|
|Número máximo de filas|**Ilimitado**|**10.000**<br/><br/>(por espaço de nomes de serviço, pode ser aumentado)|
|Número máximo de clientes em simultâneo|**Ilimitado**|**Ilimitado**<br/><br/>(100 ligação simultânea limite apenas aplica-se para a comunicação de baseada em protocolo TCP)|

### <a name="additional-information"></a>Informações adicionais

- Serviço Bus impõe limites de tamanho de fila de espera. O tamanho máximo da fila é especificado após a criação de fila de espera e pode ter um valor entre 1 e 80 GB. Se o valor de tamanho de fila de espera configurado na criação de fila de espera for atingido, serão rejeitadas adicionais mensagens recebidas e uma exceção será recebida pelo código de chamada. Para mais informações acerca de quotas no serviço Bus, consulte o artigo [Quotas de Bus do serviço](service-bus-quotas.md).

- Pode criar filas de serviço Bus em tamanhos de 1, 2, 3, 4 ou 5 GB (a predefinição é 1 GB). Com partições ativado (que é a predefinição), o serviço Bus cria a 16 partições para cada GB que especificar. Como tal, se criar uma fila que é de 5 GB de tamanho, com a 16 partições o tamanho máximo da fila torna-se (5 * 16) = 80 GB. Pode ver o tamanho máximo da fila com partições ou tópico verificando sua entrada no [portal do Azure][].

- Com o Azure filas, se o conteúdo da mensagem não estiver seguro de XML, em seguida, tem de ser codificada **Base64** . Se lhe **Base64**-descodificar a mensagem, a carga útil utilizador pode ser 48 KB, em vez de 64 KB.

- Com filas Bus de serviço, cada mensagem armazenada numa fila é composto por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo da mensagem suportado pela camada de serviço.

- Quando os clientes comunicar com filas Bus de serviço através do protocolo TCP, o número máximo de ligações em simultâneo para uma única fila de serviço Bus é limitado a 100. Este número é partilhado entre remetentes e destinatários. Se esta quota for atingido, serão rejeitados os pedidos subsequentes para ligações adicionais e uma exceção será recebida pelo código de chamada. Este limite não é imposta clientes com uma ligação para as filas utilizando com base em REST API.

- Se necessitar de mais do que 10.000 filas num único espaço de nomes de serviço Bus, pode contactar a equipa de suporte Azure e pedir um aumento. Para dimensionar para além das 10.000 filas com Bus de serviço, também pode criar espaços adicionais de nomes utilizando o [Azure portal][].

## <a name="management-and-operations"></a>Gestão e operações

Esta secção compara as funcionalidades de gestão fornecidas pela filas Azure filas e Bus de serviço.

|Critérios de comparação|Azure filas|Serviço Bus filas|
|---|---|---|
|Protocolo de gestão|**POSICIONE HTTP/HTTPS**|**POSICIONE HTTPS**|
|Protocolo de tempo de execução|**POSICIONE HTTP/HTTPS**|**POSICIONE HTTPS**<br/><br/>**AMQP 1.0 padrão (TCP com TLS)**|
|API gerida do .NET|**Sim**<br/><br/>(.NET geridos API do cliente de armazenamento)|**Sim**<br/><br/>(.NET gerido controlada messaging API)|
|C++ nativo|**Sim**|**N**|
|Java API|**Sim**|**Sim**|
|PHP API|**Sim**|**Sim**|
|NODE.js API|**Sim**|**Sim**|
|Suporte de metadados arbitrário|**Sim**|**N**|
|Regras de nomenclatura de fila de espera|**Até 63 carateres de comprimento**<br/><br/>(No nome de uma fila devem ter letras em minúsculas.)|**Até 260 carateres de comprimento**<br/><br/>(Caminhos de fila de espera e nomes são maiúsculas e minúsculas).|
|Obter a função de comprimento de fila de espera|**Sim**<br/><br/>(Valor aproximado se mensagens expiram para além do TTL sem ser eliminada.)|**Sim**<br/><br/>(Valor exato, o ponto no tempo).|
|Função de pré-visualização|**Sim**|**Sim**|

### <a name="additional-information"></a>Informações adicionais

- Azure filas fornecem suporte arbitrários atributos que podem ser aplicados à descrição da fila de espera, a forma de pares valor/nome.

- Ambas as tecnologias de fila oferecem a capacidade de surgir uma mensagem sem ter de bloqueá-lo, que pode ser útil quando implementar a uma ferramenta de explorer/browser fila de espera.

- Serviço Bus .NET controlada mensagens APIs aproveitar em full-duplex TCP ligações para um melhor desempenho quando comparado com as restantes através de HTTP e suportam o protocolo padrão AMQP 1.0.

- Nomes dos filas Azure pode ser carateres de 3-63 tempo, pode conter hífenes, números e letras em minúsculas. Para mais informações, consulte o artigo [atribuir nomes a filas e metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Nomes de fila de serviço Bus podem ser até 260 carateres de comprimento e têm regras de nomenclatura menos restritivas. Nomes de fila de serviço Bus podem conter letras, números, períodos, hífenes e sublinhado.

## <a name="authentication-and-authorization"></a>Autorização e autenticação

Esta secção descreve as funcionalidades de autenticação e autorização suportadas pelos filas Azure filas e Bus de serviço.

|Critérios de comparação|Azure filas|Serviço Bus filas|
|---|---|---|
|Autenticação|**Chave simétrica**|**Chave simétrica**|
|Modelo de segurança|Acesso delegado através de tokens SA.|SA|
|Federação de fornecedor de identidade|**N**|**Sim**|

### <a name="additional-information"></a>Informações adicionais

- Todos os pedidos para um das tecnologias de colocação têm de ser autenticados. Filas públicas com acesso anónimo não são suportadas. Utilizar [SA](service-bus-sas-overview.md), pode também abordar este cenário publicando uma SA só de leitura, SA só de leitura ou mesmo uma SA acesso total.

- O esquema de autenticação fornecido pela Azure filas envolve a utilização de uma chave simétrica, que é um baseadas em hash mensagem autenticação código (HMAC), calculados com o algoritmo de SHA-256 e codificado como uma cadeia **Base64** . Para mais informações sobre o protocolo respetivos, consulte o artigo [autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Serviço Bus filas suportam a um modelo semelhante com chaves simétricas. Para mais informações, consulte o artigo [Partilhado autenticação de assinatura do acesso com Bus de serviço](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Custo

Esta secção compara filas Azure filas e Bus de serviço a partir de uma perspetiva de custo.

|Critérios de comparação|Azure filas|Serviço Bus filas|
|---|---|---|
|Fila de espera da transação custo|**$0.0036**<br/><br/>(por 100.000 transações)|**Camada básica**: **$0.05**<br/><br/>(por milhões operações)|
|Operações cobrar|**Todos os**|**Marcar envio/receção apenas**<br/><br/>(sem Rateado pela outras operações)|
|Transações inactivas|**Cobrar**<br/><br/>(Consultar uma fila vazia é contabilizada como uma transação cobrar.)|**Cobrar**<br/><br/>(Uma receção contra uma fila vazia é considerada uma mensagem cobrar).|
|Custo de armazenamento|**$0,07**<br/><br/>(por GB/mês)|**$0,00**|
|Os custos de transferência de dados de saída|**$0,12 - $0.19**<br/><br/>(Consoante a geografia.)|**$0,12 - $0.19**<br/><br/>(Consoante a geografia.)|

### <a name="additional-information"></a>Informações adicionais

- As transferências de dados são cobradas com base na quantidade total de dados mantendo os centros de dados Azure através da internet num determinado período de faturação.

- As transferências de dados entre serviços Azure localizados dentro da mesma região não estão sujeitas gratuitas.

- Partir este escrita, todas as transferências de entrada de dados não estão sujeitas gratuitas.

- Tendo em conta o suporte técnico para inquéritos longo, utilizando serviço Bus filas pode ser rentável em situações onde a entrega de baixa latência é necessária.

>[AZURE.NOTE] Todos os custos estão sujeitos a alteração. Esta tabela reflete preços atual e não inclui qualquer ofertas promocionais que neste momento poderão estar disponíveis. Para obter informações atualizadas sobre Azure preços, consulte a página [Azure preços](https://azure.microsoft.com/pricing/) . Para mais informações sobre preços Bus de serviço, consulte [serviço Bus preços](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Conclusão

Obtendo uma mais aprofundada compreensão das duas tecnologias, poderão tomar uma decisão mais informada sobre a tecnologia de fila a utilizar e quando. Decisão quando deve utilizar filas Azure filas ou serviço Bus depende claramente um número de fatores. Seguintes fatores podem depender fortemente as necessidades da sua aplicação e sua arquitetura individuais. Se a sua aplicação já utiliza as funcionalidades principais do Microsoft Azure, poderá preferir selecione filas Azure, especialmente se necessitar de comunicação básica e mensagens entre serviços ou necessidade filas que podem ser superiores a 80 GB de tamanho.

Uma vez que filas serviço Bus fornecem um número de funcionalidades avançadas, tais como sessões, as transações, deteção, automática de duplicados Inativas letras e resistentes publicar/subscrever capacidades, podem ser uma opção preferida, se estiver a criar uma aplicação híbrida ou se a sua aplicação contrário requer estas funcionalidades.

## <a name="next-steps"></a>Próximos passos

Os artigos seguintes fornecem mais informações sobre como utilizar o Azure filas ou serviço Bus filas e orientações.

- [Como utilizar o serviço Bus filas](service-bus-dotnet-get-started-with-queues.md)
- [Como utilizar o serviço de armazenamento de fila de espera](../storage/storage-dotnet-how-to-use-queues.md)
- [Práticas recomendadas para utilizar o serviço Bus de melhorias de desempenho controlada mensagens](service-bus-performance-improvements.md)
- [Introdução aos filas e tópicos no serviço Azure Bus](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Guia do programador Bus de serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Com o serviço de colocação no Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Noções sobre o armazenamento Azure faturação – largura de banda, as transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Portal do Azure]: https://portal.azure.com
 

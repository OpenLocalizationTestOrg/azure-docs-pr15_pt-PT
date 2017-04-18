<properties 
    pageTitle="Serviço Azure Bus | Microsoft Azure" 
    description="Uma introdução à utilização Bus de serviço para se ligar Azure aplicações para o outro software." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/31/2016" 
    ms.author="sethm"/>

# <a name="azure-service-bus"></a>Serviço Azure Bus

Se uma aplicação ou serviço é executado na nuvem ou no local, necessita com frequência interagir com outras aplicações ou serviços. Fornece uma maneira útil ficarem para fazer isto, Microsoft Azure proporciona Bus de serviço. Este artigo leva um olhar sobre esta tecnologia, que descreve o que é e por que razão poderá pretender utilizá-la.

## <a name="service-bus-fundamentals"></a>Conceitos básicos da Bus de serviço

Chamar situações diferentes para diferentes estilos de comunicação. Por vezes, permitindo que as aplicações enviar e receber mensagens através de uma fila simple é a melhor solução. Em outras situações, uma fila normal não é suficiente; uma fila através de um publicar e subscrever é melhor. Em alguns casos, é tudo o que foi realmente necessário uma ligação entre aplicações; filas não são obrigatórios. Serviço Bus fornece três opções, permitindo suas aplicações para interagir várias maneiras diferentes.

Serviço Bus é um serviço de nuvem do inquilino com várias, o que significa que o serviço é partilhado por vários utilizadores. Cada utilizador, tal como um programador de aplicação, cria um *espaço de nomes*, em seguida, define os mecanismos de comunicação que precisa de dentro desse espaço de nomes. Figura 1 mostra este aspeto.

![][1]
 
**Figura 1: Serviço Bus fornece um serviço de inquilino com várias para estabelecer ligação aplicações através da nuvem.**

Dentro de um espaço de nomes, pode utilizar um ou mais ocorrências de quatro mecanismos de comunicação diferentes, cada um dos quais liga aplicações de uma forma diferente. As opções são:

- *Filas*, que permitir a comunicação de um direccional. Cada fila funciona como um intermédio (por vezes denominado um *corretor*), que armazena as mensagens enviadas até que são recebidas. Cada mensagem é recebida por um único destinatário.
- *Tópicos*, que disponibiliza a comunicação com um direccional utilizar *subscrições*- um único tópico pode ter múltiplas subscrições. Como uma fila, um tópico age como um corretor, mas cada subscrição, opcionalmente, pode utilizar um filtro para receber apenas as mensagens que correspondem aos critérios específicos.
- *Estações de retransmissão*, que fornecem comunicação bidirecional. Ao contrário filas e tópicos, um reencaminhamento não armazena mensagens em voo; Não é um corretor. Em vez disso, apenas os passa para a aplicação de destino.

Quando cria uma fila de espera, tópico ou reencaminhamento, pode dar um nome. Combinado com qualquer que denominado o espaço de nomes, este nome cria um identificador exclusivo para o objeto. Aplicações podem fornecer este nome aos Bus de serviço, em seguida, utilizar esse fila de espera, tópico ou reencaminhamento para comunicar com um do outro. 

Para utilizar qualquer um destes objectos no cenário de reencaminhamento, aplicações do Windows podem utilizar o Windows Communication Foundation (WCF). Para filas e tópicos, aplicações do Windows podem utilizar definidos pelo serviço Bus APIs mensagens. Para facilitar estes objetos utilizar a partir de aplicações não Windows, a Microsoft fornece SDK para Java, Node.js e outros idiomas. Também pode aceder a filas e tópicos utilizando REST APIs através de HTTP (s). 

É importante compreender que apesar de serviço Bus propriamente dito será executado na nuvem (ou seja, no centros de dados Azure da Microsoft), as aplicações que utilizam-podem executar qualquer lugar. Pode utilizar o serviço Bus para ligar a aplicações em execução no Azure, por exemplo, ou aplicações em execução no interior do seu próprio Centro de dados. Também pode utilizá-lo para ligar uma aplicação em execução no Azure ou outro plataforma nuvem com uma aplicação no local ou tablets e telemóveis. É possível mesmo para se ligar eletrodomésticos agregados, sensores e outros dispositivos, para uma aplicação central ou para si. Serviço Bus é um mecanismo de comunicação na nuvem que esteja acessível muito muito esteja onde estiver. Como utilizar depende o suas aplicações tem de fazer.

## <a name="queues"></a>Filas

Suponha que decida ligar duas aplicações utilizando uma fila Bus de serviço. Figura 2 ilustra esta situação.

![][2]
 
**Figura 2: Filas de serviço Bus fornecem colocação assíncrona unidirecional.**

O processo é simples: um remetente envia uma mensagem para uma fila Bus de serviço e um auscultador levantar o auscultador essa mensagem algumas altura posterior. Uma fila pode ter apenas uma única recetor, tal como ilustrado figura 2. Em alternativa, podem ler várias aplicações a partir da mesma fila. Na última situação, cada mensagem é de leitura por recetor apenas uma. Para um serviço de múltiplos modificado deve utilizar um tópico.

Cada mensagem tem duas partes: um conjunto de propriedades, cada um par de valor/chave e corpo de uma mensagem binário. Como estão habituados depende do que uma aplicação está a tentar fazer. Por exemplo, uma aplicação a enviar uma mensagem acerca de uma venda recente pode incluir as propriedades *Vendedor = "Ava"* e *quantidade = 10000*. Corpo da mensagem poderá conter uma imagem digitalizada do contrato com a sessão iniciada a venda ou, se não houver uma, basta permanecem vazio.

Um destinatário pode ler uma mensagem a partir de uma fila Bus de serviço de duas maneiras diferentes. A primeira opção, denominada *ReceiveAndDelete*, remove uma mensagem de fila de espera e elimina-o imediatamente. Este é simple, mas se o destinatário falha antes de terminar a processar a mensagem, a mensagem serão perdida. Uma vez que foi removido da fila, sem outros recetor pode aceder à mesma. 

A segunda opção, *PeekLock*, destina-se para o ajudar com este problema. Como **ReceiveAndDelete**, uma operação de leitura **PeekLock** remove uma mensagem de fila de espera. Não elimina a mensagem, no entanto. Em vez disso,-bloqueia a mensagem, tornando invisível para outros destinatários, em seguida, espera para um dos três eventos:

- Se o destinatário processa a mensagem com êxito, chamadas de **concluída**e fila de espera elimina a mensagem. 
- Se o destinatário decidir que não é possível processar a mensagem com êxito, chamadas **abandonar**. Fila de espera, em seguida, remove a bloquear a partir da mensagem e disponibiliza-o para outros destinatários.
- Se o destinatário chamadas nenhum destes dentro de um período de tempo configurável (por predefinição, 60 segundos), fila de espera assume que no auscultador falhou. Neste caso, funciona como se o destinatário tinha denominado **abandonar**, disponibilizar a mensagem aos outros destinatários.

Repare que o que pode acontecer aqui: a mesma mensagem poderá ser entregue duas vezes, talvez duas destinatários diferentes. Aplicações utilizando serviço Bus filas devem ser preparadas para esta situação. Para facilitar a deteção de duplicados, cada mensagem com uma propriedade **MessageID** exclusiva que, por predefinição, permaneça a mesma independentemente de como o número de vezes a mensagem é de leitura a partir de uma fila. 

Filas são úteis para se adequarem algumas situações. Estes permitem que as aplicações comunicar o mesmo quando ambos não estão a ser executado em simultâneo, algo que é especialmente útil com o lote e aplicações móveis. Uma fila com vários destinatários também fornece balanceamento de carga automática, uma vez que são distribuir a mensagens enviadas por estes destinatários.

## <a name="topics"></a>Tópicos

Útil tal como estão, filas não estão sempre a solução à direita. Por vezes, os tópicos de serviço Bus são melhor. Figura 3 ilustra este ideia.

![][3]
 
**Figura 3: Com base no filtro que especifica uma aplicação de subscrição,-pode receber algumas ou todas as mensagens enviadas para um tópico Bus de serviço.**

Um *tópico* é semelhante de diversas formas para uma fila. Mensagens de remetentes submeter um tópico da mesma forma que submeter mensagens para uma fila e essas mensagens procure o mesmo, tal como acontece com filas. A diferença grande é tópicos activem a cada aplicação receção para criar as suas próprias *subscrição* ao definir um *filtro*. Um subscritor, em seguida, verá apenas as mensagens que correspondem a esse filtro. Por exemplo, figura 3 mostra um remetente e um tópico com três subscritores, cada uma com os suas próprias filtro:

- 1 de subscritor recebe apenas as mensagens que contenham a propriedade *Vendedor = "Ava"*.
- Subscritor 2 recebe as mensagens que contenham a propriedade *Vendedor = "Rubi"* e/ou conter uma propriedade de *montante* cujo valor é maior do que 100,000. Talvez Rubi é o Gestor de vendas, para que ela pretende ver a sua própria vendas e todas as vendas grandes, independentemente de quem torna-as.
- Subscritor 3 tem defina o seu filtro como *Verdadeiro*, o que significa que recebe todas as mensagens. Por exemplo, esta aplicação poderá ser responsável por manter um registo de auditoria e, por isso, necessita de ver todas as mensagens.

Tal como acontece com filas, os subscritores a um tópico podem ler mensagens utilizando o **ReceiveAndDelete** ou **PeekLock**. No entanto, ao contrário filas, uma única mensagem enviada para um tópico pode ser recebida pelo múltiplas subscrições. Esta abordagem, geralmente denominado *publicar e subscrever* (ou *pub/sub*), é útil sempre que estão interessadas várias aplicações as mesmas mensagens. Ao definir o filtro à direita, cada subscritor pode tocar para a parte da sequência de mensagem que necessita para ver.

## <a name="relays"></a>Estações de retransmissão

Filas e tópicos fornecem comunicação assíncrona unidirecional através de um corretor. Os fluxos de tráfego apenas numa direção e não existe ligação direta entre remetentes e destinatários. Mas o que acontece se não quiser Isto? Suponha que as suas aplicações necessitarem enviar e receber mensagens, ou talvez que pretende uma ligação direta entre eles e não precisa de um corretor para armazenar mensagens. Para os cenários de endereço como estas, serviço Bus fornece *reencaminha*, tal como figura 4 mostra.

![][4]
 
**Figura 4: Reencaminhamento do serviço Bus fornece síncrona, bidirecional comunicações entre aplicações.**

Este é o óbvio questão perguntar sobre estações de retransmissão: por que motivo devo utilizar um? Mesmo se não precisar de filas, por que motivo tornar as aplicações comunicar através de um serviço na nuvem e não apenas interagir diretamente? A resposta é que a falar diretamente pode ser mais difícil que poderá pensar.

Suponha que pretende ligar duas de aplicações no local, ambos em execução no interior da empresa centros de dados. Cada um destas aplicações fica atrás uma firewall e cada centro de dados provavelmente utiliza endereços de rede tradução (NAT). A firewall bloqueia recebidos dados em todas as mas alguns portas e NAT implica que cada aplicação está em execução no computador não tem um endereço IP fixo, pode contactar diretamente a partir de fora do Centro de dados. Sem algumas ajuda adicional, é problemático ligar estas aplicações através da internet público.

Pode ajudar a um reencaminhamento Bus de serviço. Para comunicar bi bidireccionalmente através de um reencaminhamento, cada aplicação estabelece uma ligação de TCP saída com Bus de serviço, em seguida, mantém o abrir. Todas as comunicações entre as duas aplicações são transmitidos estas ligações. Uma vez que cada ligação foi estabelecida a partir de dentro o Centro de dados, a firewall permite tráfego de entrada para cada aplicação sem abrir novas portas. Esta abordagem também obtém corrigir o problema NAT, uma vez que cada aplicação tem um ponto final de consistente na nuvem ao longo de comunicação. Através do intercâmbio de dados através do reencaminhamento, as aplicações podem evitar problemas que iria caso contrário, dificultar a comunicação. 

Para utilizar o serviço Bus estações de retransmissão, aplicações confiam no Windows Communication Foundation (WCF). Serviço Bus fornece enlaces WCF que a tornam simples para aplicações do Windows interagir através do estações de retransmissão. As aplicações que já utilizam WCF podem normalmente apenas especificar um estes enlaces, em seguida, falar entre si através de um reencaminhamento. Ao contrário filas e tópicos, no entanto, utilizar estações de retransmissão a partir de aplicações não Windows, enquanto possível, requer alguns esforço de programação; sem bibliotecas padrão são fornecidas.

Ao contrário filas e tópicos, aplicações não criam explicitamente estações de retransmissão. Em vez disso, quando uma aplicação que pretenda receber mensagens estabelece uma ligação de TCP com Bus de serviço, um reencaminhamento é criado automaticamente. Quando a ligação é interrompida, o reencaminhamento é eliminado. Para ativar uma aplicação localizar o reencaminhamento criado por uma escuta específica, o serviço Bus fornece um registo que permite que as aplicações localizar um específico reencaminhamento pelo nome.

Estações de retransmissão são da solução certa quando precisar de direta comunicações entre aplicações. Por exemplo, considere um sistema de reserva companhia aérea a ser executada num centro de dados no local que deve ser acedido a partir de outros computadores, dispositivos móveis e dar entrada quiosques. Aplicações em execução no todos estes sistemas poderiam depender estações de retransmissão Bus serviço na nuvem para comunicar, onde quer que podem estar em execução.

## <a name="summary"></a>Resumo

Ligar a aplicações sempre foi parte da construção de soluções concluídas e o intervalo de cenários que necessitam de aplicações e serviços para comunicar com os outros está definido para aumentar como mais aplicações e dispositivos estiverem ligados à Internet. Ao fornecer tecnologias baseado na nuvem para alcançar isto através de filas, tópicos e estações de retransmissão, serviço Bus destina-se para tornar esta função essencial mais fácil implementar e divulgação.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os conceitos básicos do Azure Service Bus, siga estas ligações para mais informações.

- Como utilizar o [serviço Bus filas](service-bus-dotnet-get-started-with-queues.md)
- Como utilizar o [serviço Bus tópicos](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- Como utilizar o [serviço Bus reencaminhamento](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
- [Exemplos de Bus de serviço](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png

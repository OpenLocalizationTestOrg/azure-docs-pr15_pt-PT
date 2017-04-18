<properties 
    pageTitle="Criar uma API para as aplicações de lógica" 
    description="Criar uma API personalizada para utilizar com aplicações de lógica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Criar uma API personalizada para utilizar com aplicações de lógica

Se pretender expandir a plataforma de lógica aplicações, existem várias formas que pode ligar para APIs ou sistemas de que não estão disponíveis como um dos nossos muitas conexões out of box.  Uma dessas formas para criar uma aplicação de API que pode chamar a partir de um fluxo de trabalho de aplicação de lógica.

## <a name="helpful-tools"></a>Ferramentas úteis

Para APIs funcionar melhor com aplicações de lógica, recomendamos que num documento [swagger](http://swagger.io) com detalhes sobre os parâmetros e operações suportadas para o seu API a gerar.  Existem várias bibliotecas (como [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) que irão gerar automaticamente swagger por si.  Também pode utilizar [TRex](https://github.com/nihaue/TRex) para o ajudar a anotar swagger para trabalhar bem com aplicações de lógica (apresentar nomes, tipos de propriedades, etc.).  Para obter alguns exemplos de API aplicações criadas para as aplicações de lógica, certifique-se de que veja os nossos [GitHub repositório](http://github.com/logicappsio) ou [blogue](http://aka.ms/logicappsblog).

## <a name="actions"></a>Ações

A ação básica para uma aplicação de lógica é um controlador que irá aceitar um pedido de HTTP e devolver uma resposta (normalmente 200).  No entanto, existem padrões diferentes que pode seguir para expandir ações com base nas suas necessidades.

Por predefinição, o motor de lógica aplicação irá tempo limite de um pedido de após 1 minuto.  No entanto, pode ter o API executar na ações que demorar mais tempo e ter o motor de aguardar pela conclusão, seguindo uma assíncrono ou webhook padrão detalhado abaixo.

Para ações padrão, basta escreva um método de pedido HTTP na sua API que é apresentado quando se clica através do swagger.  Pode ver amostras, de API aplicações que funcionam com aplicações de lógica no nosso [GitHub repositório](https://github.com/logicappsio).  Abaixo estão formas de realizar padrões comuns com uma conexão personalizada.

### <a name="long-running-actions---async-pattern"></a>Ações longa - padrão de assíncrona

Quando a ser executado um passo longo ou tarefa, é a primeira coisa que precisa de fazer a certificar-se de que o motor sabe que ainda não excedeu. Também precisa de comunicar com o motor como saberá quando tiver terminado com a tarefa e, por fim, tem de devolver dados relevantes para o motor para que possa continuar com o fluxo de trabalho. Pode concluir que através de uma API ao seguir o fluxo abaixo. Estes passos sejam do ponto-de-vista da API do personalizado:

1. Quando um pedido de é recebido, imediatamente devolver uma resposta (antes de trabalho é feito). Esta resposta será um `202 ACCEPTED` resposta, permitindo que o motor de que tem os dados, aceite a carga de útil e o processamento de agora. A resposta 202 deve conter os seguintes cabeçalhos: 
 * `location`cabeçalho (obrigatório): Este é um absoluto caminho para as aplicações de lógica de URL, pode utilizar para verificar o estado da tarefa.
 * `retry-after`(opcional, irá predefinida para 20 para ações). Este é o número de segundos que o motor deverá aguardar antes do URL da localização cabeçalho para verificar o estado de consulta.

2. Quando um Estado da tarefa estiver selecionado, execute os seguintes controlos: 
 * Se a tarefa está concluída: devolver uma `200 OK` resposta, com a carga útil resposta.
 * Se a tarefa ainda está a processar: devolver outro `202 ACCEPTED` resposta, com os cabeçalhos mesmo como resposta inicial

Este padrão permite-lhe executar tarefas demasiado longas dentro de um tópico da sua API personalizado, mas manter uma ligação ativa vivo com o motor de lógica aplicações para que não-tempo limite ou continuar antes de trabalho é concluído. Quando adicionar isto para a sua aplicação de lógica, é importante ter em atenção que não necessita de fazer nada na sua definição para a aplicação de lógica continuar a pesquisar e verificar o estado. Assim que o motor vê uma resposta ACEITES 202 com um cabeçalho de localização válida, irá respeitar o padrão de assíncrona e continuam a consultar o cabeçalho de localização até um não 202 é devolvido.

Pode ver um exemplo deste padrão no GitHub [aqui](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Ações de Webhook

Durante o fluxo de trabalho, pode fazer com a aplicação de lógica, coloque o cursor e aguarde que uma "chamada de retorno" continuar.  Esta chamada de retorno é fornecido em forma de uma mensagem de HTTP.  Para implementar este padrão, tem de fornecer dois pontos finais no seu controlador: subscrever e anular a subscrição.

No 'subscrever', a aplicação de lógica cria e registar um URL de chamada de retorno que pode armazenar o seu API e uma chamada de retorno com pronto como uma mensagem de HTTP.  Qualquer conteúdo/cabeçalhos transmitir sessão na aplicação de lógica e podem ser utilizados no resto do fluxo de trabalho.  O motor de lógica aplicação irá efetuar a chamada o ponto de subscrever no execução assim-acertos esse passo.

Se foi cancelada a executar, o motor de lógica aplicação irá efetuar uma chamada para o ponto final 'Anular a subscrição'.  A API pode, em seguida, anular o registo o URL de chamada de retorno conforme necessário.

Atualmente o Estruturador da aplicação lógica não suporta a descobrir um ponto final de webhook através de swagger, para que para utilizar este tipo de ação tem de adicionar a ação "Webhook" e especifique o URL, cabeçalhos e corpo do seu pedido.  Pode utilizar o `@listCallbackUrl()` função de fluxo de trabalho em qualquer um desses campos, conforme necessário para passar no URL de chamada de retorno.

Pode ver um exemplo de um padrão de webhook GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Accionadores

Para além de ações, pode ter o act API personalizado como um accionador para uma aplicação de lógica.  Existem duas padrões que pode seguir abaixo para acionar uma aplicação de lógica:

### <a name="polling-triggers"></a>Inquéritos accionadores

Inquéritos accionadores agir semelhante as ações assíncrona longo a executar o acima.  O motor de lógica aplicação irá efetuar a chamada o ponto final de accionador após um determinado período de tempo decorrido (dependente SKU, 15 segundos para Premium, 1 minuto para padrão e 1 hora para Free).

Se não haja dados disponível, o accionador devolve um `202 ACCEPTED` resposta, com um `location` e `retry-after` cabeçalho.  No entanto, para accionadores recomenda-se a `location` cabeçalho contém um parâmetro de consulta de `triggerState`.  Este é algumas identificador para sua API saber quando a hora da última chama a aplicação de lógica.  Se existir dados disponível, o accionador devolve um `200 OK` resposta com a carga de útil conteúda.  Isto irá fogo a aplicação de lógica.

Por exemplo, se posso foi de consulta para ver se um ficheiro foi disponível, poderia criar um acionador de inquéritos seria efetue o seguinte procedimento:

* Se um pedido de foi recebido com sem triggerState API deve devolver uma `202 ACCEPTED` com um `location` cabeçalho que tem um triggerState da hora atual e uma `retry-after` de 15.
* Se foi recebido um pedido de com uma triggerState:
 * Verifique se quaisquer ficheiros foram adicionados após a triggerState DateTime. 
  * Se existir 1 ficheiro, é devolvido um `200 OK` resposta com a carga conteúda útil, incrementar triggerState para DateTime do ficheiro devolvido e definir o `retry-after` para 15.
  * Se existirem vários ficheiros, posso pode devolver 1 uma vez com uma `200 OK`, incrementar meu triggerState na `location` cabeçalho e configurar `retry-after` 0.  Isto irá permitir que o motor saber existe mais dados disponíveis e imediatamente irá pedir-o `location` cabeçalho especificado.
  * Se não forem encontrados ficheiros disponíveis, é devolvido um `202 ACCEPTED` resposta e deixar o `location` triggerState da mesma.  Definir `retry-after` para 15.

Pode ver um exemplo de um acionador de inquéritos GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Webhook accionadores

Webhook accionadores agir muito semelhante a ações Webhook acima.  O motor de lógica aplicação irá efetuar a chamada o ponto final 'subscrever' sempre que um accionador webhook é adicionado e guardado.  A API pode registar o URL de webhook e atribua-o através de HTTP POST sempre que os dados estão disponíveis.  Os cabeçalhos e carga útil conteúdo serão transmitidos sessão na aplicação de lógica executar.

Se nunca é eliminado um accionador webhook (a aplicação de lógica completamente ou apenas o accionador webhook), o motor irá efetuar uma chamada para o URL 'Anular a subscrição' onde pode anular o registo do seu API o URL de chamada de retorno e parar quaisquer processos, conforme necessário.

Atualmente o Estruturador da aplicação lógica não suporta a descobrir um accionador webhook através de swagger, para que para utilizar este tipo de ação tem de adicionar o accionador "Webhook" e especifique o URL, cabeçalhos e corpo do seu pedido.  Pode utilizar o `@listCallbackUrl()` função de fluxo de trabalho em qualquer um desses campos, conforme necessário para passar no URL de chamada de retorno.

Pode ver um exemplo de um acionador de webhook GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)
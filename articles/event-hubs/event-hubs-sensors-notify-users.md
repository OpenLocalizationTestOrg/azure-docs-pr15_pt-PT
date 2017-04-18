<properties 
   pageTitle="Notificar os utilizadores de dados que recebeu do sensores ou outros sistemas de | Microsoft Azure"
   description="Descreve como utilizar o evento concentradores a notificar utilizadores da dados sensor."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Notificar utilizadores da recebido a partir do sensores ou outros sistemas de dados

Suponha que tem uma aplicação que monitoriza dados em tempo real ou produz relatórios com base numa agenda. Se observe o Web site no qual esses relatórios ou gráficos em tempo real são apresentados, poderá ver algo que necessite de ação. O que acontece se tem de ser alertado para esses situações em vez de dependente no lembrar-se verificar o Web site? Imagine que tiver uma versão simplificada aumentar numa estufa e tem de conhecer imediatamente se a luz apagar. Uma forma de fazê-lo seria com um sensor simplificado estufa, dispor sejam enviadas uma mensagem de e-mail se a versão simplificada está desativada.

![][1]

Cenário de outra, imagine que executar uma instalação embarque companhia, e tem de ser alertado para níveis de alimentação de inventário baixa. Por exemplo, poderá dispor sejam enviadas uma mensagem de texto do seu sistema ERP se o seu inventário do armazém de alimentação cão atrasou-se a um nível de crítico. 

![][2]

Como obter informações críticas quando forem cumpridas determinadas condições, não quando chegar à volta ao dar saída de um mapa estático da é o problema. Se estiver a utilizar um [Concentrador de evento Azure][] ou [Azure IoT concentrador][] para receber dados a partir de dispositivos ou aplicações empresariais, tal como [Dynamics AX][], tem várias opções para saber como processá-las. Pode visualizá-los num Web site, pode analisá-los, pode armazená-los e utilizá-los para acionar comandos para fazer algo. Para executar esta tarefa, pode utilizar ferramentas eficientes como [Sites públicos do Azure][], [SQL Azure][], [HDInsight][], [Cortana conjunto de informações da empresa][], [IoT conjunto de aplicações][], [Aplicações de lógica][]ou [Concentradores de notificação do Azure][]. Mas, por vezes, tudo o que pretende fazer é enviar esses dados a pessoa com um mínimo de custos gerais. Para mostrar-lhe como fazê-lo com apenas um pouco de código, fornecemos uma nova amostra, [AppToNotifyUsers][]. Opções de incluídas são e-mail (SMTP), SMS e telemóveis.

## <a name="application-structure"></a>Estrutura de aplicação

A aplicação escrita c# e o ficheiro Leia-me no exemplo contém toda a informação de que precisa para modificar, criar e publicar a aplicação. As secções seguintes fornecem uma descrição geral do que faz a aplicação.

Vamos começar com partem do princípio de que tem eventos críticos a ser enviados para um concentrador de evento Azure ou IoT concentrador. Qualquer concentrador executará nenhuma, desde que tenha acesso à mesma e saiba a cadeia de ligação.

Se ainda não tiver um concentrador concentrador de evento ou IoT, pode facilmente de configurar um canteiro de teste com uma protecção Arduino e um Pi framboesa, seguindo as instruções no projeto [Pontos de ligação](https://github.com/Azure/connectthedots) . O sensor simplificado na protecção Arduino envia os níveis de simplificada através de Pi a um [Concentrador de evento Azure][] (**ehdevices**) e uma tarefa de [Análise da cadeia de Azure](https://azure.microsoft.com/services/stream-analytics/) emite alertas a um concentrador de evento segundo (**ehalerts**) se os níveis de simplificada recebidos de se situar abaixo de um determinado nível.

Quando inicia o **AppToNotify** , lê um ficheiro de configuração (App) para obter o URL e as credenciais para o concentrador de evento receber os alertas. -Em seguida, gera um processo para monitorizar continuamente que concentrador de evento para qualquer mensagem que é fornecido através de – desde tenha pode aceder ao URL do concentrador de evento ou IoT concentrador e credenciais válidas, este código de leitor de evento concentradores continuamente ler o que está a chegar. Durante o arranque, a aplicação lê também o URL e as credenciais para o serviço de mensagens (telefone de e-mail, SMS,) que pretende utilizar e o nome do endereço do remetente e uma lista de destinatários.

Assim que o monitor de evento concentrador Deteta uma mensagem, accionar um processo que envia mensagens utilizando o método especificado no ficheiro de configuração. Tenha em atenção que envia todas as mensagens detetar. Se definir o monitor que aponte a um concentrador de evento que recebe dez mensagens por segundo, o remetente enviará dez mensagens por segundo – dez mensagens de correio eletrónico por segundo, dez as mensagens SMS por segundo, chamadas telefónicas dez por segundo. Por que razão, certifique-se de que monitorizar um concentrador de evento que recebe apenas os alertas de que precisam de ser enviada, não um concentrador de evento que recebe os dados não processados do seu sensores ou aplicações.

## <a name="applicability"></a>Âmbito de aplicação

Apenas o código neste exemplo mostra como monitorizar eventos concentradores e como ligar a serviços de mensagens externos se que pretende adicionar esta funcionalidade para a sua aplicação. Note que esta solução é um DIY, exemplo com foco nos programador. Não abrange os requisitos de empresa, tais como redundância, falhas com sobre, reinicie relativamente a falha, etc. Para mais abrangente e de produção soluções, consulte o seguinte:

- Utilização de conectores ou notificações push através do serviço do [Azure lógica de aplicações](../app-service-logic/app-service-logic-connectors-list.md) .
- Utilizar o [Azure notificação concentradores](https://msdn.microsoft.com/library/azure/jj927170.aspx), conforme descrito a [difusão as notificações push para milhões de dispositivos móveis com o Azure notificação concentradores](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs)do blogue. 

## <a name="next-steps"></a>Próximos passos

É simples criar um serviço de notificações simples que envia mensagens de correio eletrónico ou mensagens de texto para os destinatários ou chamadas-los, para os dados de relay recebidos por um concentrador de evento ou IoT concentrador. Para implementar a solução para notificar os utilizadores com base nos dados recebidos por estes concentradores, visite [AppToNotifyUsers][].

Para mais informações sobre estes concentradores, consulte os artigos seguintes:

- [Evento Azure concentradores]
- [Azure IoT concentrador]
- Começar a trabalhar com um [evento concentradores tutorial].
- Uma [aplicação de exemplo que utiliza o evento concentradores]concluída.

Para implementar a solução para notificar os utilizadores com base em dados recebidos por estes concentradores, visite:

- [AppToNotifyUsers][]

[Tutorial do evento concentradores]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT concentrador]: https://azure.microsoft.com/services/iot-hub/
[Evento Azure concentradores]: https://azure.microsoft.com/services/event-hubs/
[Concentrador de evento Azure]: https://azure.microsoft.com/services/event-hubs/
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Sites públicos do Azure]: https://azure.microsoft.com/services/app-service/web/
[Do SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Conjunto de aplicações do Cortana informações da empresa]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT conjunto de aplicações]: https://azure.microsoft.com/solutions/iot-suite/
[Aplicações de lógica]: https://azure.microsoft.com/services/app-service/logic/
[Notificação Azure concentradores]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png
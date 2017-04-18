<properties
    pageTitle="Notificação Azure concentradores"
    description="Saiba como utilizar as notificações push no Azure. Exemplos de código escritos c# utilizando a API do .NET."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Notificação Azure concentradores

##<a name="overview"></a>Descrição geral

Azure notificação concentradores fornecem uma infraestrutura de push fáceis de utilizar múltiplas plataformas, dimensionada-out permite-lhe enviar notificações push móveis a partir de qualquer back-end (na nuvem ou no local) para qualquer plataforma móvel.

Com concentradores de notificação, pode facilmente enviar notificações push em diferentes plataformas, personalizada, abstracting os detalhes dos sistemas de notificação da plataforma diferente (PNS). Com uma chamada de API única, pode de destino que os utilizadores individuais ou segmentos de audiência inteira que contém milhões de utilizadores, em todos os seus dispositivos.

Pode utilizar concentradores de notificação para enterprise e consumidor cenários. Por exemplo:

- Envie notificações de notícias de divisão para milhões com a latência baixa (notificação concentradores oferece as aplicações do Bing pré-instalado em todos os dispositivos Windows e o Windows Phone).
- Envie cupões baseadas na localização para segmentos de utilizador.
- Envie notificações de eventos para utilizadores ou grupos para as aplicações de juros/desportos/jogos.
- Notificar os utilizadores de eventos de empresa como novos mensagens/e-mails e oportunidades potenciais.
- Envie um-time palavras-passe necessário para autenticação multifator.



##<a name="what-are-push-notifications"></a>Quais são as notificações de emissão?

Smartphones e tablets podem "notificar" os utilizadores quando ocorreu um evento. Estas notificações podem ter vários formatos.

Aplicações da loja Windows e o Windows Phone, a notificação de pode ser sob a forma de um _alerta_: é apresentada uma janela modal, com um som, para sinalizar uma notificação de novo. Outros tipos de notificação que são suportados incluem as notificações _de mosaico_, _não processados_e _distintivo_ . Para mais informações sobre os tipos de notificações suportadas em dispositivos com Windows, consulte o artigo [mosaicos, distintivos e notificações](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Em Apple iOS dispositivos, o push do mesmo modo notifica o utilizador com uma caixa de diálogo, que pede ao utilizador para ver ou fechar a notificação. Clicando em **vista** é aberta a aplicação que está a receber a mensagem. Para mais informações sobre iOS notificações, consulte o artigo [iOS notificações](http://go.microsoft.com/fwlink/?LinkId=615245).

Notificações push ajudam a apresentar informações frescas enquanto restante eficiente de energia de dispositivos móveis. Notificações por sistemas de back-end podem ser enviadas para dispositivos móveis mesmo quando não são ativas correspondentes aplicações num dispositivo. Notificações push são um componente essencial para as aplicações do consumidor, onde são utilizadas para aumentar Cativação de aplicação e a utilização. Notificações também são úteis para empresas, quando obter informações atualizadas aumentam consequência de empregado para eventos de empresas.

Alguns exemplos específicos dos cenários de Cativação móvel são:

1.  Atualizar um mosaico no Windows 8 ou Windows Phone com informações financeiras atuais.
2.  Alertas de um utilizador com um alerta que algumas item de trabalho foi atribuída a esse utilizador, numa aplicação empresarial baseada no fluxo de trabalho.
3.  Apresentar um distintivo com o número de vendas actuais orienta numa aplicação CRM (por exemplo, o Microsoft Dynamics CRM).

##<a name="how-push-notifications-work"></a>Como o trabalho de notificações de emissão

Notificações push são entregues através de infra-estruturas específicos da plataforma denominadas _Sistemas de notificação de plataforma_ (PNS). Um PNS oferece barebones funções (ou seja, não existe suporte para difusão, personalização) e tiver sem interface comuns. Por exemplo, para enviar uma notificação para uma aplicação da loja Windows, um programador terá de contactar o WNS (serviço de notificação do Windows). Para enviar uma notificação para dispositivo iOS, o programador da mesma tem de APNS (Apple serviço de notificações de emissão) de contacto e enviar a mensagem de uma segunda vez. Ajudam do Azure concentradores de notificação ao fornecer uma interface comuns, juntamente com outras funcionalidades para suportar as notificações push em cada plataforma.

No entanto, de alto nível, todos os sistemas de notificação de plataforma siga o mesmo padrão:

1.  A aplicação de cliente contacta PNS para obter a respectiva _alça_. O tipo de alça depende do sistema. Para WNS, é um URI ou "canal de notificação". Para APNS, é um token.
2.  A aplicação de cliente armazena esta alça na aplicação _back-end_ para utilização posterior. Para WNS, back-end normalmente é um serviço na nuvem. Para Apple, o sistema chama um _fornecedor_.
3.  Para enviar uma notificação de emissão, a aplicação back-end contacta PNS utilizando a alça de filtragem de uma instância da aplicação de cliente específico.
4.  O PNS reencaminha a notificação para o dispositivo especificado pela alça.

![][0]

##<a name="the-challenges-of-push-notifications"></a>Os desafios de notificações Push

Enquanto estes sistemas são muito poderosos, ainda deixarem a quantidade de trabalho para o programador da aplicação para implementar cenários de notificações push mesmo comuns, como difundi-la ou enviar notificações push para utilizadores segmentados.

Notificações push são um das funcionalidades mais solicitadas no cloud services para aplicações móveis. O motivo para isto é que a infraestrutura de necessário para torná-las trabalhar é bastante complexo e principalmente relacionado com a lógica empresarial principal da aplicação. Alguns dos desafios na criação de uma infraestrutura a pedido push são:

- **Dependência de plataforma.** Para poder enviar notificações para dispositivos diferentes plataformas, várias interfaces tem de ser codificadas no back-end. Não só são os detalhes feixe diferentes, mas a apresentação da notificação de (mosaico, alerta ou distintivo) também é dependente da plataforma. Estas diferenças podem conduzir a complexo e disco rígido manter o código de segurança.

- **Escala.** Dimensionamento este infraestrutura tem dois aspetos:
    + Segundo as diretrizes de PNS, tokens de dispositivo tem de ser atualizados sempre que a aplicação é iniciada. Isto leva a uma grande quantidade de tráfego (e base de dados consecutivas acessos) apenas para manter os tokens de dispositivo atualizados. Quando o número de dispositivos cresce (possivelmente para milhões), o custo de criação e manutenção este infraestrutura é nonnegligible.

    + A maior parte dos PNSs não suportam difusão para vários dispositivos. Segue-que uma difusão para milhões de dispositivos resulta num milhões de chamadas para os PNSs. Ser capaz de dimensionar estes pedidos não é fácil, uma vez que os programadores de aplicação que pretende manter a latência total para baixo. Por exemplo, o último dispositivo para receber a mensagem não deve receber a notificação de 30 minutos após ter sido enviada as notificações, tal como para muitos casos-teria combater o objetivo para que as notificações push.
- **Encaminhamento.** PNSs fornece uma maneira de enviar uma mensagem para um dispositivo. No entanto, na maior parte das aplicações notificações são direcionadas para utilizadores e/ou grupos de interesse (por exemplo, todos os seus funcionários atribuídos a uma determinada conta de cliente). Como tal, para encaminhar as notificações para os dispositivos corretos, aplicação back-end tem de manter um registo que associa grupos de interesse tokens de dispositivo. Adicionam este gerais para o tempo total para os custos de mercado e manutenção de uma aplicação.

##<a name="why-use-notification-hubs"></a>Porquê utilizar notificação concentradores?

Notificação concentradores eliminar complexidade: não possui que gerir os desafios de notificações push. Em vez disso, pode utilizar um concentrador de notificação. Notificação concentradores utilizem uma infraestrutura de notificações push completa múltiplas plataformas, dimensionado de saída e consideravelmente reduzir o código de push específica que é executado no aplicação back-end. Notificação concentradores implementam toda a funcionalidade de infraestrutura de push. Dispositivos apenas responsável por se ter registado PNS alças e back-end é responsável por enviar mensagens de plataforma independente a utilizadores ou grupos de interesse, conforme apresentado na figura seguinte:

![][1]


Concentradores notificação fornecem uma infraestrutura de notificações push prontos a utilizar com as seguintes vantagens:

- **Múltiplas plataformas.**
    +  Suporte para todas as plataformas de dispositivos móveis principais. Concentradores notificação podem enviar notificações push para as aplicações da loja Windows, iOS, Android e Windows Phone.

    +  Concentradores notificação fornecem uma interface comuns para enviar notificações para todas as plataformas suportadas. Específico da plataforma protocolos não são necessários. Aplicação back-end pode enviar notificações em formatos específicos da plataforma ou plataforma independente. A aplicação apenas comunica com concentradores de notificação.

    +  Gestão de dispositivos alça. Notificação concentradores mantém a alça de registo e comentários a partir do PNSs.

- **Funciona com qualquer back-end**: na nuvem ou no local, .NET, PHP, Java, nó, etc.

- **Escala.** Notificação concentradores Dimensionar para ajustar milhões de dispositivos sem necessidade de voltar Arquitectar ou shard.


- **Rich conjunto de padrões de entrega**:

    - *Difusão*: permite difusão perto simultânea para milhões de dispositivos com uma chamada de API único.

    - *Unicast/Multicast*: transmitir para etiquetas que representa os utilizadores individuais, incluindo todos os seus dispositivos; grupo mais largo; ou Por exemplo, formulário separado factores (tablet vs. telefone).

    - *Segmentação*: emissão segmento complexa definidas pela expressões de etiqueta (por exemplo, dispositivos em Nova Iorque seguir os Yankees).

    Cada dispositivo, quando enviar respectiva alça a um concentrador de notificação, pode especificar um ou mais _etiquetas_. Para mais informações sobre [etiquetas]. Etiquetas não tem de ser previamente aprovisionado ou eliminadas. Etiquetas fornecem uma forma simple para enviar notificações para utilizadores ou grupos de interesse. Uma vez que as etiquetas podem conter qualquer identificador específicas de aplicação (tal como IDs de utilizador ou grupo), a sua utilização liberta aplicação back-end os encargos resultantes de ter que armazenar e gerir as alças de dispositivo.

- **Personalização**: cada dispositivo pode ter um ou mais modelos para alcançar a localização por dispositivo e personalização sem afetar o código de back-end.

- **Segurança**: partilhados acesso secreta (SA) ou autenticação federada.

- **Telemetria Rich**: disponível no portal do e através de programação.


##<a name="integration-with-app-service-mobile-apps"></a>Integração com aplicações móveis do serviço de aplicação

Para facilitar a uma experiência totalmente integrada e utilização em serviços Azure, [Aplicações do serviço de aplicação Mobile] tem suporte incorporado para as notificações push utilizando concentradores de notificação. [Aplicações do serviço de aplicação Mobile] oferece uma plataforma de desenvolvimento da aplicação móvel altamente dimensionáveis, globalmente disponível para os programadores de empresa e integradores que relaciona um conjunto avançado de funcionalidades para programadores móveis.

Os programadores de aplicações móveis podem utilizar notificação concentradores com o fluxo de trabalho seguinte:

1. Obter o dispositivo PNS alça
2. Registar o dispositivo e [modelos] com notificação concentradores através de conveniente Mobile aplicações cliente SDK register API
    + Tenha em atenção que aplicações móveis revelar ausente todas as etiquetas em registos por motivos de segurança. Trabalhar com notificação concentradores a partir do seu back-end diretamente para associar etiquetas dispositivos.
3. Enviar notificações a partir do seu back-end da aplicação com concentradores de notificação

Aqui estão algumas instalações sanitárias importadas para os programadores com esta integração:

- **SDK do cliente móvel de aplicações.** Este SDK plataforma com várias fornece APIs simples para o registo e fale para o concentrador de notificação ligado para cima com a aplicação móvel automaticamente. Os programadores não necessita aprofundar através de credenciais de notificação concentradores e trabalhar com um serviço adicional.
    + Os SDK marcar automaticamente o dispositivo determinado com aplicações Mobile ID de utilizador autenticado para ativar push para o cenário de utilizador.
    + Os SDK utilizam automaticamente o ID de instalação de aplicações móveis como GUID para registar com concentradores de notificação, guardar os programadores de problemas de manutenção de vários GUID de serviço.
    
- **Modelo de instalação.** Aplicações móveis funciona com o modelo push mais recente dos concentradores de notificação para representar todas as propriedades de push associadas com um dispositivo numa instalação JSON que alinha com os serviços de notificações Push e é fácil de utilizar.

- **Flexibilidade.** Os programadores sempre podem escolher trabalhar com notificação concentradores diretamente, mesmo com a integração no local.

- **Experiência integrada no [Azure portal].** Notificações push como uma funcionalidade é representada visualmente nas aplicações do telemóvel e os programadores podem trabalhar facilmente com o concentrador de notificação associado através de aplicações Mobile.



##<a name="next-steps"></a>Próximos passos

Pode saber mais sobre notificação concentradores nestes tópicos:

+ **[Como clientes estão a utilizar concentradores de notificação]**

+ **[Tutoriais de concentradores de notificação e guias]**

+ **Notificação de introdução concentradores tutoriais** ([iOS], [Android], [Universal do Windows], [do Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

O .NET relevantes gerido referências de API para as notificações push estão localizadas aqui:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Como clientes estão a utilizar concentradores de notificação]: http://azure.microsoft.com/services/notification-hubs
  [Tutoriais de concentradores de notificação e guias]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Universal do Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Aplicações móveis do serviço de aplicação]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [modelos]: notification-hubs-templates-cross-platform-push-messages.md
  [Portal do Azure]: https://portal.azure.com
  [etiquetas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

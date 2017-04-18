<properties
 pageTitle="Descrição geral de IoT concentrador Azure | Microsoft Azure"
 description="Descrição geral do serviço do Azure IoT concentrador: o que é o concentrador iot, conectividade de dispositivo, internet de padrões de comunicação coisas e padrão de comunicação assistido de serviço"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>O que é o Azure IoT concentrador?

Bem-vindo ao Azure IoT concentrador. Este artigo fornece uma descrição geral do Azure IoT concentrador e descreve por que motivo deve utilizar este serviço para implementar uma solução de Internet coisas (IoT). Azure IoT concentrador é um serviço totalmente gerido que permite bidireccional fiável e seguro comunicações entre milhões de IoT dispositivos e solução back-end. Azure IoT concentrador:

- Fornece fiável dispositivo para nuvem e na nuvem-a-dispositivo mensagens em escala.
- Permite proteger as comunicações com as credenciais de segurança por dispositivo e controlo de acesso.
- Fornece extenso monitorização de conectividade de dispositivo e eventos de gestão de identidades do dispositivo.
- Inclui bibliotecas de dispositivo para os idiomas e plataformas mais populares.

O artigo [concentrador de comparação de IoT e evento concentradores] [ lnk-compare] descreve as diferenças entre estes dois serviços chaves e realça as vantagens da utilização IoT concentrador no seu IoT soluções.

![Azure IoT concentrador como gateway na nuvem na internet da solução coisas][img-architecture]

> [AZURE.NOTE] Para uma aprofundada IoT arquitetura, consulte o artigo a [Arquitetura de referência do Microsoft Azure IoT][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>IoT desafios de conectividade do dispositivo

Concentrador de IoT e as bibliotecas de dispositivo ajudá-lo para cumprir os desafios da como sujeito e segura ligar dispositivos a solução back-end. IoT dispositivos:

- São, muitas vezes, sistemas incorporados com sem operador humano.
- Pode estar em localizações remotas, onde está dispendioso acesso físico.
- Só pode ser acessível através de solução back-end.
- Podem ter uma limitada power e recursos de processamento.
- Poderá ter de conectividade de rede intermitentes, lenta ou dispendioso.
- Poderá ter de utilizar protocolos de aplicação especialidades, personalizado ou específicos da indústria.
- Pode ser criado utilizando um conjunto maior de plataformas de hardware e software populares.

Além dos requisitos de acima, qualquer solução IoT tem também de ser entregar fiabilidade, segurança e escala. O conjunto de requisitos de conectividade resultante é difícil e morosa implementar quando utilizar tecnologias tradicionais, tais como contentores de web e corretor de mensagens.

## <a name="why-use-azure-iot-hub"></a>Porquê utilizar Azure IoT concentrador

Azure IoT concentrador resolve os desafios de conectividade do dispositivo das seguintes formas:

-   **Por dispositivo autenticação e conectividade segura**. Pode aprovisionar o cada dispositivo com a sua própria [chave de segurança] [ lnk-devguide-security] para ativá-la ligar a IoT concentrador. O [registo de identidade do concentrador de IoT] [ lnk-devguide-identityregistry] armazena identidades do dispositivo e chaves numa solução. Solução back-end pode adicionar dispositivos individuais para permitir ou negar listas para ativar o controlo total sobre o acesso do dispositivo.

-   **Monitorização de operações de conectividade do dispositivo**. Pode receber registos de operação detalhadas sobre operações de gestão de identidades do dispositivo e eventos de conectividade do dispositivo. Esta funcionalidade monitorização permite a solução IoT identificar problemas de conectividade, tal como a dispositivos que tentam ligar-se com as credenciais mal, enviar mensagens com demasiada frequência ou rejeitar todas as mensagens de dispositivo de nuvem.

-   **Um conjunto amplo de bibliotecas do dispositivo**. [Azure IoT dispositivo SDK] [ lnk-device-sdks] estão disponíveis e suportados para vários idiomas e plataformas – C para muitos Linux distribuições, Windows e sistemas operativos em tempo real. Azure IoT dispositivo SDK também suporta idiomas geridos, como c#, Java e JavaScript.

-   **IoT protocolos e de expansão**. Se a solução não é possível utilizar as bibliotecas de dispositivo, IoT concentrador expõe protocolo público que permite dispositivos para utilizar vierem a MQTT v3.1.1, HTTP 1.1 ou protocolos AMQP 1.0. Também pode expandir IoT concentrador para fornecer suporte para protocolos personalizados por:

    - Criar um gateway de campo com o [Azure IoT Gateway SDK] [ lnk-gateway-sdk] que converte o protocolo personalizado para um dos três protocolos compreendidos por IoT concentrador. 
    - Personalizar o [gateway de protocolo Azure IoT][protocol-gateway], um componente de abrir origem que é executada na nuvem.

-   **Escala**. Azure IoT concentrador escalas milhões de dispositivos ligados em simultâneo e milhões de eventos por segundo.

Estas vantagens são genéricas para muitos padrões de comunicação. Concentrador IoT atualmente permite-lhe implementar os padrões de comunicação específico seguintes:

-   **Ingestão de nuvem de dispositivo com base no evento.** Concentrador IoT sujeito pode receber milhões de eventos por segundo de seus dispositivos. -Pode, em seguida, processá-los no seu caminho quente, utilizando um motor de processador de eventos. Também pode armazená-los no seu frio caminho para uma análise. Concentrador IoT mantém os dados do evento até sete dias garantir processamento fiável e absorve picos a carregar.

-   * *Mensagens de dispositivo de nuvem fiável (ou *comandos*). * * solução back-end pode utilizar IoT concentrador para enviar mensagens com uma garantia de entrega de menos-vez para dispositivos individuais. Cada mensagem tem uma definição de time to live individual e o back-end pode pedir recibos de entrega e expiração. Estes recibos Certifique-se a visibilidade do completa do ciclo de vida de uma mensagem de dispositivo de nuvem. Em seguida, pode implementar a lógica empresarial que inclua operações que são executadas em dispositivos.

-   **Carregar ficheiros e dados em cache sensor na nuvem.** Os dispositivos podem carregar ficheiros para o armazenamento do Windows Azure utilizando URIs SA gerido por si pelo IoT concentrador. IoT concentrador pode gerar notificações de chegada de ficheiros na nuvem para ativar o back-end para processá-los.

## <a name="gateways"></a>Gateways

Um gateway numa solução IoT normalmente é um [gateway protocolo] [ lnk-gateway] que é implementado na nuvem ou um [gateway campo] [ lnk-field-gateway] que é implementado localmente com os seus dispositivos. Um gateway protocolo executa a conversão de protocolo, por exemplo MQTT para AMQP. Um gateway campo pode executar a análise no limite, a tomar decisões de sensível ao tempo para reduzir a latência, fornecer serviços de gestão de dispositivos, impor restrições de privacidade e segurança e também efectuar a tradução de protocolo. Ambos os tipos de gateway agir como intermediários entre o concentrador IoT e os seus dispositivos.

Um gateway campo difere num dispositivo encaminhar tráfego simples (tal como um dispositivo de tradução de endereços de rede ou firewall) porque que executa normalmente uma função de ativa na gestão de acesso e informações fluem na sua solução.

Uma solução pode incluir o protocolo e campo gateways.

## <a name="how-does-iot-hub-work"></a>Como funciona o concentrador IoT?

Azure IoT concentrador implementa [assistido serviço comunicação] [ lnk-service-assisted-pattern] padrão para Media interações entre os dispositivos e o seu back-end da solução. É o objetivo de comunicação assistido de serviço estabelecer fidedigna, caminhos de comunicação bidirecional entre um sistema de controlo, tais como IoT concentrador e dispositivos de objetivo especiais que são implementadas no não fidedignos espaço físico. O padrão de estabelece os princípios seguintes:

- Segurança tem precedência sobre todas as outras funcionalidades.
- Dispositivos não aceitar a informações de rede não solicitado. Um dispositivo estabelece todas as ligações e rotas de uma forma apenas de saída. Para um dispositivo receber um comando de back-end, o dispositivo regularmente tem de iniciar uma ligação para verificar a existência de quaisquer comandos pendentes processar.
- Dispositivos devem apenas ligar a ou estabelecer rotas para serviços conhecidos que estão dispõe, como o concentrador IoT.
- O caminho de comunicação entre o dispositivo e o serviço ou entre o dispositivo e o gateway está protegido na camada do protocolo aplicação.
- Autorização de nível do sistema e autenticação são baseados no identidades por dispositivo. Fazem credenciais de acesso e permissões quase instantaneamente revogável.
- Comunicação bidirecional para dispositivos que se ligam esporadicamente devido ao power ou conectividade preocupações é facilitada mantendo comandos e notificações de dispositivo até um dispositivo estiver ligado ao recebê-las. Concentrador IoT mantém filas específicas do dispositivo para os comandos que envia.
- Dados da aplicação de carga útil estão protegidos separadamente para trânsito protegido através de gateways para um serviço específico.

A indústria móvel utilizou o padrão de comunicação assistido serviço em escala enorme para implementar os serviços de notificações push como [Serviços de notificação do Windows Push][lnk-wns], [Mensagens do Google Cloud][lnk-google-messaging]e o [Serviço de notificações Push da Apple][lnk-apple-push].

Concentrador IoT é suportado ao longo do caminho do ExpressRoute de efectuado público.

## <a name="next-steps"></a>Próximos passos

Para saber como o concentrador de IoT Azure permite baseadas em normas gestão de dispositivos IoT para o utilizador gerir remotamente, configurar e atualizar os seus dispositivos, consulte o artigo [Descrição geral do Azure IoT concentrador de tipos de gestão de dispositivos][lnk-device-management].

Para implementar aplicações de cliente numa grande variedade de sistemas operativos e plataformas de hardware do dispositivo, pode utilizar o dispositivo de IoT SDK. O dispositivo de IoT SDK incluir bibliotecas que facilitam o envio de telemetria a um concentrador de IoT e a receber na nuvem-a-dispositivo comandos. Quando utiliza os SDK, pode escolher a partir de vários protocolos de rede para comunicar com IoT concentrador. Para obter mais informações, consulte as [informações acerca do dispositivo SDK][lnk-device-sdks].

Para começar a escrever algum código e executar alguns exemplos, consulte o artigo [Introdução ao centro de IoT] [ lnk-get-started] tutorial.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Assistido a comunicação do serviço, mensagem de blogue por Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

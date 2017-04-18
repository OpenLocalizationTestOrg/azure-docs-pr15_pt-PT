<properties
 pageTitle="Activar dispositivos geridos por trás de um gateway IoT | Microsoft Azure"
 description="Tópico orientações utilizando um IoT Gateway criada utilizando o SDK do Gateway IoT juntamente com dispositivos geridos pelo IoT concentrador."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Activar dispositivos geridos por trás de um gateway IoT

## <a name="basic-device-isolation"></a>Isolamento de dispositivo básicos

As organizações utilizam frequentemente IoT gateways para aumentar a segurança geral das respetivas soluções IoT. Alguns dispositivos precisam enviar dados para a nuvem, mas não estão compatível com a proteger-se contra ameaças na internet. Pode proteger nestes dispositivos de threads externos por tê-los a comunicar com o mundo exterior através de um gateway.

O gateway encontra-se no limite entre um ambiente seguro e o aberto da internet. Falar com dispositivos para o gateway e o gateway transmite as mensagens ao longo de destino correto na nuvem. O gateway está puro contra threads externos, bloqueia pedidos não autorizados, permite que o tráfego de no vinculadas autorizado e reencaminha que no vinculadas o tráfego para o dispositivo correto.

![][1]

Também pode colocar os dispositivos que podem proteger próprios atrás de um gateway para uma camada adicionada de segurança. Neste cenário só tem de manter o gateway SO corrigido contra as vulnerabilidades mais recentes, em vez de atualizar o sistema operativo utilizado em cada dispositivo.

## <a name="isolation-plus-intelligence"></a>Isolamento plus informações da empresa

Um router puro é um gateway suficiente para identificar simplesmente dispositivos. No entanto, IoT soluções requerem que um gateway fornece mais informações da empresa que simplesmente isolar dispositivos. Por exemplo, poderá querer gerir os dispositivos a partir da nuvem. Está a conseguir utilizar [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), um protocolo de gestão de dispositivo padrão, para a parte da gestão de nuvem da solução. No entanto, os dispositivos enviam telemetria utilizando um TCP/IP não ativado protocolo. Além disso, os dispositivos produzem grandes quantidades de dados e pretende carregar um subconjunto de telemetria filtrado. Pode criar uma solução que incorpora um gateway IoT capaz de lidar com duas sequências distintas dos dados. O gateway deve:

-   Compreender a **telemetria**, filtrá-lo e, em seguida, carregue-o para a nuvem através do gateway. O gateway já não se encontra um router simple que reencaminha simplesmente dados entre o dispositivo e na nuvem.

-   Basta intercâmbio de **dados de gestão de dispositivo LWM2M** entre os dispositivos e na nuvem. O gateway não é necessário compreender os dados provenientes na mesma e apenas precisa para se certificar de que os dados obtém passados e para trás entre os dispositivos e na nuvem.

A figura seguinte ilustra este cenário:

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>Solução: gestão de dispositivos Azure IoT e o SDK do Gateway IoT 

Público pré-visualizar lançamento de [Gestão de dispositivos do Azure IoT] [ lnk-device-management] e versão beta do [Azure IoT Gateway SDK] ativar este cenário. O gateway trata cada fluxo de dados da seguinte forma:

-   **Telemetria**: pode utilizar o SDK do Gateway IoT para criar uma tubagem que compreende, filtros e envia dados de telemetria para a nuvem. O SDK de Gateway IoT fornece código que implementa partes deste pipeline em nome de programador. Pode encontrar mais informações sobre a arquitetura do SDK no [IoT Gateway SDK - introdução ao] [ lnk-gateway-get-started] tutorial.

-   **Gestão de dispositivos**: gestão de dispositivos Azure fornece um cliente LWM2M que é executada no dispositivo, bem como uma interface de nuvem para emitir comandos de gestão do dispositivo.
    
    Não requerem qualquer lógica especial no gateway, porque não ter os dados de LWM2M trocados entre o dispositivo e o seu centro IoT do processo. Pode ativar a ligação à internet partilha, uma funcionalidade de vários sistemas operativos modernos, no gateway para ativar o intercâmbio de dados de LWM2M. Pode escolher um sistema operativo adequado para este cenário de uma vez que o SDK do Gateway IoT suporta uma variedade de sistemas operativos. Seguem-se as instruções para ativar a partilha no [Windows 10] e [Ubuntu], dois dos sistemas operativos suportados muitos de ligação à internet.

A ilustração seguinte mostra a arquitetura de alto nível utilizada para ativar este cenário com [Gestão de dispositivos do Azure IoT] [ lnk-device-management] e o [Azure IoT Gateway SDK].

![][3]

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como utilizar o SDK do Gateway IoT, consulte os seguintes tutoriais:

- [IoT Gateway SDK - começar a utilizar Linux][lnk-gateway-get-started]
- [IoT Gateway SDK – enviar mensagens de nuvem de dispositivo com um dispositivo simulado com Linux][lnk-gateway-simulated]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md
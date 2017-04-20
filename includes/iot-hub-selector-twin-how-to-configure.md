> [AZURE.SELECTOR]
- [NODE.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>Introdução

Em [começar a trabalhar com concentrador IoT gémeos][lnk-twin-tutorial], ficou a saber como definir o dispositivo metadados da sua solução back-end utilizando *tags*, condições de dispositivo de relatório a partir de uma aplicação de dispositivo utilizando *Propriedades comunicadas*e estas informações utilizando uma linguagem semelhante de SQL da consulta.

Neste tutorial, vai aprender a utilizar o o twin *propriedades pretendidas* em conjunto com o *comunicado propriedades*, para configurar remotamente as aplicações de dispositivo. Mais especificamente, este tutorial mostra como twin comunicado e propriedades pretendidas activar uma configuração de vários passo de uma definição de aplicação do dispositivo e fornecem a visibilidade para a solução back-end do estado desta operação ao longo de todos os dispositivos.

A um nível elevado, esta iniciação segue o *padrão de estado pretendido* para a gestão do dispositivo. A ideia fundamental deste padrão é o solução back-end especificar o estado pretendido para os dispositivos geridos, em vez de enviar comandos específicos. Este procedimento coloca o dispositivo responsável pelo estabelecimento a melhor forma de alcançar o estado pretendido (muito importante na IoT cenários onde as condições de dispositivo específico afectar a capacidade para executar imediatamente comandos específicos), enquanto continuamente relatórios para o back-end, o estado actual e as condições de erro potencial. O padrão de estado pretendido é instrumental para a gestão de grandes conjuntos de dispositivos, como permite o back-end ter uma visibilidade completa do Estado do processo de configuração através de todos os dispositivos.
Pode encontrar mais informações sobre a função do padrão de estado pretendido na gestão de dispositivos na [Gestão de dispositivos de descrição geral de Azure IoT concentrador][lnk-dm-overview].

> [AZURE.NOTE] Em cenários em que os dispositivos são controlados de forma mais interactiva (activar uma ventoinha de uma aplicação controladas pelo utilizador), considere a utilização de [métodos de cloud-a-dispositivo][lnk-methods].

Neste tutorial, as alterações de back-end da aplicação a configuração de telemetria de um dispositivo de destino e, em resultado disso, a aplicação de dispositivo segue um processo de vários passo para aplicar uma actualização da configuração (por exemplo, necessidade de reinício de módulo de software), que esta iniciação simula com um atraso simple).

Back-end armazena a configuração de propriedades pretendidas a duplos de dispositivo do seguinte modo:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] Uma vez que as configurações podem ser objectos complexos, normalmente, são atribuídos ids exclusivos (hashes ou [GUIDs][lnk-guid]) para simplificar as comparações.

A aplicação de dispositivo comunica a respectiva configuração actual, espelhamento (mirroring) a propriedade desejada **telemetryConfig** nas propriedades do comunicado:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Repare como o reportado **telemetryConfig** tem uma propriedade adicionais de **Estado**, utilizado para indicar o estado do processo de actualização de configuração.

Quando é recebida uma nova configuração pretendida, a aplicação de dispositivo comunica uma configuração pendente alterando as informações:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Em seguida, em algum momento posterior, a aplicação do dispositivo apresentará um relatório o êxito da falha desta operação, actualizando a propriedade acima.
Repare como o back-end é capaz de, em qualquer altura, consultar o estado do processo de configuração através de todos os dispositivos.

Este tutorial mostra-lhe como para:

- Crie um dispositivo de simulação que recebe actualizações de configuração do back-end e relatórios de múltiplas actualizações como *Propriedades reportadas* sobre o processo de actualização de configuração.
- Crie uma aplicação de back-end que actualiza a configuração de um dispositivo pretendida e, em seguida, consulta o processo de actualização de configuração.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
<properties
 pageTitle="Programador guia - compreender gémeos dispositivo | Microsoft Azure"
 description="Guia do programador IoT concentrador Azure - gémeos de dispositivo de utilização para sincronizar dados distrito e configuração entre IoT concentrador e os seus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Compreender gémeos dispositivo - pré-visualizar

## <a name="overview"></a>Descrição geral

*Gémeos dispositivo* os documentos JSON que armazenam informações de estado do dispositivo (meta dados, configurações e condições). Concentrador IoT persiste um duplos dispositivo para cada dispositivo que se liga ao IoT concentrador. Este artigo irá descrever:

* a estrutura de duplos o dispositivo: *pretendida* e *etiquetas*, *comunicados propriedades*, e
* as operações que pode executar as aplicações do dispositivo e termina anterior gémeos dispositivo.

> [AZURE.NOTE] Neste momento, são acessíveis apenas a partir de dispositivos que se ligam a concentrador IoT utilizando o protocolo MQTT gémeos dispositivo. Consulte para [suportar MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter a aplicação de dispositivo existente utilizar MQTT.

### <a name="when-to-use"></a>Quando utilizar

Utilize gémeos dispositivo para:

* Armazenar dados específicos do dispositivo meta-na nuvem, por exemplo, localização de implementação de uma máquina de distribuidores.
* Comunicar informações de estado atual como funcionalidades disponíveis e condições da sua aplicação do dispositivo, por exemplo, um dispositivo ligar-se através de conta ou de rede móvel.
* Sincronize o estado dos fluxos de trabalho de execução longa entre o dispositivo aplicação e anterior final, por exemplo, back-end especificando a nova versão firmware para instalar e a aplicação de dispositivo de várias fases do processo de atualização de elaboração de relatórios.
* Metadados do dispositivo, a configuração ou o estado da consulta.

Utilize [as mensagens de dispositivo para nuvem] [ lnk-d2c] para sequências de eventos tem a hora marcada como série de tempo de dados sensor ou alarmes. Utilizar os [métodos de dispositivo de nuvem] [ lnk-methods] para controlo interativo dispositivos, tais como ativar uma ventoinha.

## <a name="device-twins"></a>Gémeos de dispositivo

Gémeos dispositivo armazenam informações relacionadas com o dispositivo que:

- Dispositivo e voltar atrás extremidades podem utilizar para sincronizar condições de dispositivo e configuração.
- Aplicação back-end pode utilizar para a consulta e de destino execução longa operações.

Ciclo de vida de um duplos dispositivo está ligado a [identidade do dispositivo]de correspondente[lnk-identity]. Gémeos implicitamente são criados e eliminados quando uma nova identidade do dispositivo é criada ou eliminada no Centro de IoT.

Duplos um dispositivo são um documento JSON que inclui:

* **Etiquetas**. Um documento JSON lida e escrita pela back-end. Etiquetas não estão visíveis para as aplicações do dispositivo.
* **Propriedades de desejado**. Utilizada em conjunto com as propriedades do denunciado para sincronizar a configuração do dispositivo ou condição. Propriedades pretendidas só podem ser definidas pela aplicação novamente fim e podem ser lidos pela aplicação do dispositivo. Também pode ser notificada a aplicação de dispositivo em tempo real das alterações nas propriedades pretendidas.
* **Declarado propriedades**. Utilizada em conjunto com as propriedades do pretendido para sincronizar a configuração do dispositivo ou condição. Propriedades denunciadas só podem ser definidas pela aplicação do dispositivo e podem ser lida e consultadas através da aplicação back-end.

Para além disso, a raiz de duplos o dispositivo contém as propriedades de só de leitura a partir de identidade correspondente, como contidas no [registo de identidade do dispositivo][lnk-identity].

![][img-twin]

Eis um exemplo de um documento do dispositivo duplos JSON:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

O objeto de raiz, são as propriedades do sistema e contentor objectos para `tags` e ambos `reported` e `desired properties`. O `properties` contentor contém alguns elementos só de leitura (`$metadata`, `$etag`, e `$version`) que são descritos respetivamente os [metadados duplos] [ lnk-twin-metadata] e [simultaneidade optimista] [ lnk-concurrency] secções.

### <a name="reported-property-example"></a>Exemplo de propriedade denunciado

No exemplo acima, o duplos dispositivo contém um `batteryLevel` propriedade é comunicada pela aplicação do dispositivo. Esta propriedade torna possível a consulta e funcionam em dispositivos com base no último nível baterias denunciado. Outro exemplo teria o capacidades de dispositivo de relatório de aplicação do dispositivo ou as opções de conectividade.

Tenha em atenção propriedades como denunciadas simplificar cenários onde o back-end está interessado no último valor conhecido de uma propriedade. Utilize [as mensagens de dispositivo para nuvem] [ lnk-d2c] se o back-end tem de telemetria do dispositivo sob a forma de sequências de eventos de tem a hora marcada, tal como séries de tempo do processo.

### <a name="desired-configuration-example"></a>Exemplo de configuração pretendida

No exemplo acima, a `telemetryConfig` propriedades pretendidas e denunciadas são utilizadas pela aplicação de fim e o dispositivo de volta para sincronizar a configuração de telemetria para este dispositivo. Por exemplo:

1. Aplicação back-end define a propriedade pretendida com o valor de configuração pretendida. Eis a parte do documento com a propriedade pretendido:

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. A aplicação de dispositivo é notificada da alteração imediatamente se ligado ou a primeira restabelecer a ligação. A aplicação de dispositivo, em seguida, relatórios de configuração actualizada (ou uma condição de erro utilizando o `status` propriedade). Eis a parte da denunciado propriedades:

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. Pode manter o back-end da aplicação monitorizar os resultados da operação de configuração através de vários dispositivos, [consultando] [ lnk-query] gémeos.

> [AZURE.NOTE] Os fragmentos acima são exemplos, otimizados para legibilidade, de uma forma possível para codificar uma configuração de dispositivo e o respetivo estado. Concentrador IoT não impor um esquema específico para as propriedades pretendidas e denunciados gémeos o dispositivo.

Em muitos casos gémeos são utilizados para sincronizar operações de execução longa, tais como o firmware atualizações. Referir-se para [utilizar as propriedades pretendidas para configurar os dispositivos] [ lnk-twin-properties] para obter mais informações sobre como utilizar as propriedades para sincronizar e controlar longo a executar operações em todos os dispositivos.

## <a name="back-end-operations"></a>Operações de back-end

O back-end funciona no duplos utilizando as seguintes operações Atómica, expostas através de HTTP:

1. **Obter duplos por id**. Esta operação devolve o conteúdo do documento a duplos, incluindo etiquetas e deseje, comunicados e propriedades do sistema.
2. **Atualizar parcialmente duplos**. Esta operação permite que o back-end parcialmente atualizar o duplos etiquetas ou propriedades pretendidas. A atualização parcial é expresso como um documento JSON que adiciona ou actualiza uma propriedade de mencionados. As propriedades definidas para `null` são removidos. Por exemplo, o seguinte cria uma nova propriedade pretendida com valor `{"newProperty": "newValue"}`, substitui o valor existente do `existingProperty` com `"otherNewValue"`e remove completamente `otherOldProperty`. Sem alterações ocorrem para outras propriedades pretendidas existentes ou etiquetas:

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Substituir as propriedades pretendidas**. Esta operação permite que o back-end para completamente substituir existentes todas as propriedades pretendidas e substitua um novo documento JSON para `properties/desired`.
4. **Substituir etiquetas**. Forma análoga para substituir as propriedades pretendidas, este operações permite que o back-end para completamente substituir todas as etiquetas existentes e substitua um novo documento JSON para `tags`.

Suportam de todas as operações acima [simultaneidade optimista] [ lnk-concurrency] e exigir permissão **ServiceConnect** , tal como foi definido na [segurança] [ lnk-security] artigo.

Para além destas operações back-end pode consultar gémeos utilizando um gosto SQL de [idioma de consulta][lnk-query]e desempenham operações em grandes conjuntos de gémeos utilizando [processos][lnk-jobs].

## <a name="device-operations"></a>Operações de dispositivos

A aplicação de dispositivo funciona no duplos utilizando as seguintes operações atómica:

1. **Obter duplos**. Esta operação devolve o conteúdo do documento a duplos (incluindo etiquetas e pretender, comunicados e propriedades do sistema) para o dispositivo ligado atualmente.
2. **Parcialmente atualização comunicado propriedades**. Esta operação permite a atualização parcial das propriedades do dispositivo atualmente ligada denunciadas. Esta opção utiliza o mesmo formato de atualização JSON como o back-end opostas atualizações parciais de propriedades pretendidas.
3. **Observar pretendido propriedades**. O dispositivo ligado atualmente pode optar por ser notificado de atualizações para as propriedades pretendidas assim em tempo real. O dispositivo recebe o mesmo formulário da atualização (substituição parcial ou completa) executado por back-end.

Todas as operações acima exigem permissão **DeviceConnect** , tal como foi definido na [segurança] [ lnk-security] artigo.

O [dispositivo Azure IoT SDK] [ lnk-sdks] tornam mais fácil de utilizar as operações acima a partir de vários idiomas e plataformas. Podem encontrar mais informações sobre os detalhes de primitivas do IoT concentrador para a sincronização de propriedades pretendidas no [fluxo de restabelecer a ligação do dispositivo][lnk-reconnection].

> [AZURE.NOTE] Atualmente, gémeos dispositivo são acessíveis apenas a partir de dispositivos que se ligam a concentrador IoT utilizando o protocolo MQTT.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre a controlar o acesso ao seu centro IoT.

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades

Etiquetas, as propriedades pretendidas e denunciadas são objetos JSON com as seguintes restrições:

* Todas as chaves de objetos JSON são cadeias UNICODE de 128 CARÁCT entre maiúsculas e minúsculas. Permitido carateres excluir carateres de controlo UNICODE (segmentos C0 e C1), e `'.'`, `' '`, e `'$'`.
* Todos os valores do objeto JSON podem ser dos seguintes tipos de JSON: booleano, número, cadeia, objeto. Não são permitidas matrizes.

## <a name="twin-size"></a>Tamanho duplos

Concentrador IoT impõe uma limitação de tamanho de 8KB nos valores de `tags`, `properties/desired`, e `properties/reported`, excluindo elementos só de leitura.
O tamanho é calculado contando controlam todos os carateres excluindo UNICODE carateres (segmentos C0 e C1) e o espaço `' '` quando aparecer fora uma constante de cadeia.
Concentrador IoT irá rejeitar todas as operações que iria aumentar o tamanho desses documentos acima do limite com o erro.

## <a name="twin-metadata"></a>Duplos metadados

Concentrador IoT mantém o carimbo de data/hora da última atualização para cada objeto JSON nas propriedades pretendidas e denunciados. As marcas de hora estão a ser UTC e codificado no formato de [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Estas informações são mantidas cada nível (não apenas as folhas da estrutura JSON) para manter as atualizações que remova chaves do objeto.

## <a name="optimistic-concurrency"></a>Simultaneidade optimista

Etiquetas, as propriedades pretendidas e denunciadas todos os suportam simultaneidade optimista.
Etiquetas de ter um etag, de acordo com o [RFC7232], que representa a representação de JSON a etiqueta. Pode utilizar esta nas atualização condicional operações de back-end para garantir a consistência.

As propriedades pretendidas e denunciadas não possui etags, mas tem uma `$version` valor que é garantia de serem utilizarão. Forma análoga para um etag, a versão pode ser utilizada pela parte atualizar como (uma aplicação de dispositivo para uma propriedade denunciada) ou o back-end para uma propriedade pretendido para impor a consistência das atualizações.

Versões também são úteis quando um agente observing (como a aplicação de dispositivo observar as propriedades pretendidas) tem de reconciliar corridas entre o resultado de uma operação de recuperar e uma notificação de atualização. Na secção [fluxo de restabelecer a ligação do dispositivo] [ lnk-reconnection] fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de restabelecer a ligação do dispositivo

Concentrador IoT não preserva as notificações de atualização de propriedades pretendidas para dispositivos desligados. Segue-se um dispositivo que está a ligar terá de obter documento completo propriedades pretendidas, para além de subscrever para as notificações de atualização. Dada a possibilidade de corridas entre as notificações de atualização e obtenção completa, deve assegurar o fluxo seguinte:

1. Aplicação do dispositivo liga-se a um concentrador de IoT.
2. Aplicação do dispositivo subscreve para propriedades pretendidas as notificações de atualização.
3. Aplicação do dispositivo obtém o documento completo para as propriedades pretendidas.

A aplicação de dispositivo pode ignorar todas as notificações com `$version` inferior ou igual a que a versão do documento obtido completo. O que é possível porque IoT concentrador garantias versões sempre incrementar.

> [AZURE.NOTE] Esta lógica já é implementada no [dispositivo Azure IoT SDK][lnk-sdks]. Esta descrição é útil apenas se a aplicação de dispositivo não é possível utilizar qualquer um dos Azure IoT dispositivo SDK e tem de programa a interface MQTT diretamente.

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Agora que tem aprendeu gémeos de dispositivo, que lhe pode interessar os seguintes tópicos de guia do programador:

- [Invocar um método direto num dispositivo][lnk-methods]
- [Agendar tarefas em múltiplos dispositivos][lnk-jobs]

Se quiser experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado os tutoriais IoT concentrador seguintes:

- [Como utilizar o duplos de dispositivo][lnk-twin-tutorial]
- [Como utilizar as propriedades de duplos][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
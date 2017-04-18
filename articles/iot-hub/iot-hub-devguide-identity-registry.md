<properties
 pageTitle="Guia do programador - registo de identidade do dispositivo | Microsoft Azure"
 description="Guia de programador IoT concentrador Azure - descrição do registo de identidade do dispositivo e como utilizá-la para gerir os seus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="manage-device-identities-in-iot-hub"></a>Gestão de identidades do dispositivo no Centro de IoT

## <a name="overview"></a>Descrição geral

Cada concentrador IoT tem um registo de identidade do dispositivo que armazena informações sobre os dispositivos que são permitidos para ligar para o concentrador. Antes de um dispositivo pode ligar a um concentrador, tem de ter uma entrada para esse dispositivo no registo de identidade do dispositivo do concentrador. Também tem de autenticar um dispositivo com o concentrador com base nas credenciais armazenadas no registo de identidade do dispositivo.

De alto nível, o registo de identidade do dispositivo é uma coleção de capacidade para o resto dos recursos de identidade do dispositivo. Quando adiciona uma entrada ao registo, o concentrador IoT cria um conjunto de recursos por dispositivo no serviço tal como na fila que contém as mensagens de dispositivo de nuvem em voo.

### <a name="when-to-use"></a>Quando utilizar

Utilize o registo de identidade do dispositivo quando precisar de aprovisionamento de dispositivos que ligue a IoT concentrador e quando necessita de controlar o acesso por dispositivo para os pontos finais dispositivo destinado no seu centro.

> [AZURE.NOTE] Registo de identidade do dispositivo não contém quaisquer metadados específicos da aplicação.

## <a name="device-identity-registry-operations"></a>Operações de registo de identidade do dispositivo

O registo de identidade do dispositivo IoT concentrador expõe as seguintes operações:

* Criar identidade do dispositivo
* Atualizar a identidade do dispositivo
* Obter a identidade do dispositivo por ID
* Eliminar a identidade do dispositivo
* Lista de até 1000 identidades
* Exportar todas as identidades ao armazenamento de Blobs do Azure
* Importar identidades do armazenamento de Blobs do Azure

Todas estas operações podem utilizar simultaneidade optimista, como especificado no [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] A única forma para obter todas as identidades no registo de identidade de um concentrador é utilizar a [Exportar] [ lnk-export] funcionalidade.

Um registo de identidade do concentrador de IoT dispositivo:

- Não contém qualquer metadados da aplicação.
- Pode ser acedido como um dicionário, utilizando o **deviceId** como chave.
- Não suporta modelar consultas.

Uma solução IoT normalmente tem um arquivo de solução específicas em separado, que contém metadados específicos da aplicação. Por exemplo, o arquivo de solução específicas numa solução modular inteligente seria registo a sala em que um sensor de temperatura é implementado.

> [AZURE.IMPORTANT] Utilize apenas o registo de identidade do dispositivo para gestão de dispositivos e operações de aprovisionamento. Operações de alta débito em tempo de execução não devem depender efetuar operações no registo de identidade do dispositivo. Por exemplo, a verificar o estado da ligação de um dispositivo antes de enviar um comando não é um padrão suportado. Certifique-se verificar a [limitação taxas] [ lnk-quotas] para o registo de identidade do dispositivo e o [heartbeat de dispositivo] [ lnk-guidance-heartbeat] padrão.

## <a name="disable-devices"></a>Desactivar dispositivos

Pode desativar dispositivos ao atualizar a propriedade de **Estado** de uma identidade no registo. Normalmente, pode utilizar esta propriedade em dois cenários:

- Durante o processo de orchestration aprovisionamento. Para obter mais informações, consulte o artigo [Dispositivo aprovisionamento][lnk-guidance-provisioning].
- Se, por qualquer motivo, pode optar por um dispositivo está comprometido ou tornou não autorizado.

## <a name="import-and-export-device-identities"></a>Importar e Exportar identidades do dispositivo

Pode exportar identidades do dispositivo em volume a partir do registo de identidade de um concentrador IoT, utilizando as operações assíncronas sobre o [ponto final do fornecedor de recurso IoT concentrador][lnk-endpoints]. Exportações são tarefas de execução longa que utilizam um contentor blob fornecidas pelo cliente para guardar dados de identidade do dispositivo ler a partir do registo de identidade.

Pode importar identidades do dispositivo em massa para o registo de identidade de um concentrador IoT, utilizando as operações assíncronas sobre o [ponto final do fornecedor de recurso IoT concentrador][lnk-endpoints]. Importações são tarefas de execução longa que utilizam dados num contentor de BLOBs fornecidas pelo cliente para escrever os dados de identidade do dispositivo para o registo de identidade do dispositivo.

- Para informações detalhadas sobre a importação e exportação APIs, consulte o artigo [fornecedor de recursos IoT concentrador REST APIs][lnk-resource-provider-apis].
- Para saber mais sobre como executar importar e exportar tarefas, consulte o artigo [em volume Gestão de identidades do dispositivo de IoT concentrador][lnk-bulk-identity].

## <a name="device-provisioning"></a>Dispositivo de aprovisionamento

Os dados do dispositivo que armazena uma solução IoT determinada depende os requisitos específicos da solução. No entanto, no mínimo, uma solução tem armazenar identidades do dispositivo e chaves de autenticação. Azure IoT concentrador inclui um registo de identidade que pode armazenar valores para cada dispositivo como IDs, chaves de autenticação e os códigos de estado. Uma solução pode utilizar outros serviços como o armazenamento de tabela do Azure, armazenamento de Blobs do Azure ou Azure DocumentDB Azure para armazenar quaisquer dados adicionais dispositivo.

*Aprovisionamento de dispositivo* é o processo de adicionar os dados de dispositivo inicial para as lojas na sua solução. Para permitir que um novo dispositivo ligar ao seu centro, tem de adicionar um novo ID de dispositivo e chaves para o registo de identidade IoT concentrador. Como parte do processo de aprovisionamento, poderá ter de inicialização dados específicos do dispositivo de outros arquivos de solução.

## <a name="device-heartbeat"></a>Heartbeat de dispositivo

O registo de identidade IoT concentrador contém um campo denominado **connectionState**. Deve utilizar apenas o campo **connectionState** durante o desenvolvimento e depuração, IoT soluções devem não consultar o campo em tempo de execução (por exemplo, para verificar se um dispositivo está ligado para decidir se pretende enviar uma mensagem de dispositivo de nuvem ou um SMS).

Se a solução IoT precisa saber se um dispositivo estiver ligado (em tempo de execução, ou com mais exatidão do que a propriedade **connectionState** fornece), a solução deve implementar o *padrão de heartbeat*.

No seu padrão de heartbeat, o dispositivo envia mensagens de nuvem de dispositivo, pelo menos, uma vez por cada valor fixo de tempo (por exemplo, pelo menos uma vez a cada hora). Isto significa que a mesmo se um dispositivo não tiver quaisquer dados para enviar, ainda envia uma mensagem de dispositivo para nuvem vazia (normalmente com uma propriedade que identifica-o como um heartbeat). No lado do serviço, a solução mantém um mapa com o último heartbeat recebido para cada dispositivo e parte do princípio de que existe um problema com um dispositivo se não receber uma mensagem de heartbeat dentro do prazo esperado.

Uma implementação mais complexa pode incluir as informações da [monitorização de operações] [ lnk-devguide-opmon] para identificar os dispositivos que está a tentar ligar ou comunicar, mas a falhar. Quando implementar o padrão de heartbeat, certifique-se verificar [IoT concentrador Quotas e limitações][lnk-quotas].

> [AZURE.NOTE] Se necessita de uma solução IoT o estado da ligação dispositivo exclusivamente para determinar se pretende enviar mensagens de dispositivo de nuvem e mensagens não são difundir para grandes conjuntos de dispositivos, um padrão muito mais simples da ter em conta é utilizar um tempo de expiração breve. Isto atinge o mesmo resultado que manter um registo de estado de ligação de dispositivo utilizando o padrão de heartbeat, enquanto a ser significativamente mais eficaz. Também é possível, solicitando notificações de mensagens, para ser notificado por IoT concentrador das quais os dispositivos que são conseguir receber mensagens e que não sejam online ou são falhou.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações acerca do registo de identidade devcie.

## <a name="device-identity-properties"></a>Propriedades de identidade do dispositivo

Identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades.

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| deviceId | necessária, só de leitura atualizações | Uma cadeia de maiúsculas/minúsculas (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | necessária, só de leitura | Uma cadeia de maiúsculas/minúsculas, gerados pelo concentrador até 128 caracteres. Isto é utilizado para distinguir dispositivos com o mesmo **deviceId**, quando tenham sido eliminadas e recriadas. |
| ETag | necessária, só de leitura | Uma cadeia que representa um etag fraco para a identidade do dispositivo, por [RFC7232][lnk-rfc7232].|
| auth | opcional | Um objeto composto que contém os materiais de segurança e informações de autenticação. |
| auth.symkey | opcional | Um objecto composto que contém uma principal e uma tecla secundária, armazenadas no formato base64. |
| Estado | obrigatório | Um indicador de acesso. Pode ser **ativado** ou **desativado**. Se **ativada**, o dispositivo é permitido para ligar. Se **desativado**, este dispositivo não é possível aceder a qualquer ponto final de dispositivo destinado. |
| statusReason | opcional | Uma cadeia de carateres longas 128 que armazena a razão para o estado de identidade do dispositivo. Todos os carateres UTF-8 são permitidos. |
| statusUpdateTime | só de leitura | Um indicador temporal, que mostra a data e hora da última atualização de estado. |
| connectionState | só de leitura | Um campo que indica o estado da ligação: **ligado** ou **desligado**. Este campo representa a vista de IoT Hub do Estado de ligação do dispositivo. **Importante**: Este campo deve ser utilizada apenas para fins de desenvolvimento/depuração. O estado da ligação é atualizado apenas para dispositivos utilizando MQTT ou AMQP. Além disso, baseia-se ao nível do protocolo pings (MQTT pings ou AMQP pings) e pode ter um atraso máximo de apenas 5 minutos. Para estes motivos, podem existir falsos, tais como dispositivos comunicado como estando ligada mas que realmente são desligados. |
| connectionStateUpdatedTime | só de leitura | Um indicador de temporal, que mostra a data e hora da última o estado da ligação foi atualizado. |
| lastActivityTime  | só de leitura | Um indicador de temporal, que mostra a data e hora da última o dispositivo ligado, recebido ou enviado uma mensagem. |

> [AZURE.NOTE] Estado da ligação só pode representar a vista de IoT Hub do Estado da ligação. Atualizações para este estado podem ser atrasadas consoante as condições da rede e configurações.

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar o registo de identidade do dispositivo IoT concentrador, poderá estar interessado os seguintes tópicos de guia do programador:

- [Controlar o acesso a IoT concentrador][lnk-devguide-security]
- [Utilizar gémeos dispositivo para sincronizar distrito e configurações][lnk-devguide-device-twins]
- [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
- [Agendar tarefas em múltiplos dispositivos][lnk-devguide-jobs]

Se gostaria experimentar a alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial IoT concentrador seguinte:

- [Introdução ao Azure IoT concentrador][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
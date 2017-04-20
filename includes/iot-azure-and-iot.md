
# <a name="azure-and-internet-of-things"></a>Azure e Internet de coisas

Bem-vindo ao Microsoft Azure e a Internet de coisas (IoT). Este artigo apresenta uma arquitectura de solução de IoT que descreve as características comuns de uma solução de IoT que pode implementar o utilizando os serviços Azure. Soluções de IoT requerem comunicação bidireccional entre dispositivos, possivelmente numeração em milhões e solução back-end, segura. Por exemplo, solução back-end pode utilizar analytics automático, previsão, para descobrir informações a partir da transmissão em sequência de eventos de nuvem de dispositivo.

Concentrador de IoT Azure é um bloco modular de chave quando implementar esta arquitectura de solução de IoT utilizando os serviços Azure. IoT Suite fornece completas, ponto-a-ponto, implementações desta arquitectura para cenários de IoT específicos. Por exemplo: 

- A solução de *monitorização remota* permite-lhe monitorizar o estado de dispositivos, tais como máquinas de venda. 
- A solução de *manutenção preventiva* ajuda-na antecipar as necessidades de manutenção dos dispositivos, tais como bombas nas estações de bombagem remotos e para evitar a interrupção não agendada.

## <a name="iot-solution-architecture"></a>Arquitectura de solução de IoT

O diagrama seguinte mostra uma arquitectura de solução IoT típica. O diagrama não inclui os nomes dos serviços Azure específicos, mas descreve os elementos chave numa arquitectura de solução IoT genérico. Nesta arquitectura, dispositivos de IoT recolhem dados que enviam para um gateway de nuvem. O gateway de nuvem torna os dados disponíveis para transformação por outros serviços back-end de onde os dados são entregues a outras aplicações de linha de negócio ou aos operadores humanos através de um dashboard ou outro dispositivo de apresentação.

![Arquitectura de solução de IoT][img-solution-architecture]

> [AZURE.NOTE] Para um debate aprofundado da arquitectura de IoT, consulte [Arquitectura de referência do Microsoft Azure IoT][lnk-refarch].

### <a name="device-connectivity"></a>Conectividade do dispositivo

Nesta arquitectura de solução IoT, dispositivos enviam telemetria, tais como leituras do sensor de uma estação de bombagem, para um ponto final de nuvem para processamento e armazenamento. Num cenário de manutenção preventiva, back-end poderá utilizar a sequência de dados de sensor para determinar quando uma bomba específica necessita de manutenção. Dispositivos também podem receber e responder a comandos cloud-a-dispositivo por ler mensagens a partir de um ponto final de nuvem. Por exemplo, no cenário de manutenção preventiva o back-end da solução poderá enviar comandos para outras bombas na estação de bombagem para começar a reclassificação fluxos antes da manutenção é devem começar para se certificar de que o engenheiro de manutenção pode começar a trabalhar quando ela é recebida.

É um dos maiores desafios opostas IoT projectos como fiável e segura ligar dispositivos de back-end da solução. Dispositivos de IoT têm características diferentes em comparação com outros clientes como browsers e aplicações móveis. Dispositivos de IoT:

- São muitas vezes os sistemas incorporados sem operador humano.
- Pode ser implementada em localizações remotas, em que o acesso físico é dispendioso.
- Só pode ser acedido pelo back-end da solução. Existe outra forma de interagir com o dispositivo.
- Pode ter limitado a energia e recursos de processamento.
- Poderá ter conectividade de rede intermitente, lenta ou dispendiosa.
- Poderá ter de utilizar protocolos de aplicações de proprietário, personalizada ou específicos da indústria.
- Pode ser criado utilizando um conjunto grande de plataformas de hardware e software populares.

Para além dos requisitos acima referidos, qualquer solução IoT também deve emitir escala, segurança e fiabilidade. O conjunto de requisitos de conectividade resultante é difícil e moroso implementar utilizando tecnologias tradicionais, por exemplo web e corretores de processamento de mensagens. Azure IoT concentrador e o SDK do dispositivo IoT tornam mais fácil de implementar soluções que cumprem estes requisitos.

Um dispositivo pode comunicar directamente com um ponto final de gateway de nuvem, ou se o dispositivo não é possível utilizar qualquer um dos protocolos de comunicações que suporte o gateway de nuvem, pode ligar através de um gateway intermediário. Por exemplo, o [gateway de protocolo Azure IoT] [ lnk-protocol-gateway] possa efectuar a conversão de protocolo se dispositivos não é possível utilizar qualquer um dos protocolos que suporta IoT concentrador.

### <a name="data-processing-and-analytics"></a>Tratamento de dados e analytics

A nuvem, um IoT terminar novamente a solução é onde ocorre a maior parte do processamento de dados, tais como a filtragem e agregação de telemetria e encaminhá-lo para outros serviços. A solução IoT anterior terminar:

- Recebe telemetria à escala dos dispositivos e determina como processar e armazenar os dados. 
- Pode activar enviar comandos da nuvem para o dispositivo específico.
- Fornece capacidades de registo de dispositivo que permitem aos dispositivos de disposição e para controlar quais os dispositivos que têm permissão para ligar a sua infra-estrutura.
- Permite-lhe controlar o estado dos dispositivos e monitorizar as suas actividades.

No cenário de manutenção preventiva, o back-end da solução armazena dados de telemetria históricos. O back-end pode utilizar estes dados para utilizar para identificar padrões que indicam a manutenção é devida sobre uma bomba específica.

Soluções de IoT podem incluir ciclos de realimentação automática. Por exemplo, pode identificar um módulo de analytics no back-end de telemetria que a temperatura de um dispositivo específico for superior a níveis normais de funcionamento. A solução, em seguida, pode enviar um comando para o dispositivo, dando-lhe para executar uma acção correctiva instruções.

### <a name="presentation-and-business-connectivity"></a>Conectividade de apresentação e de negócio

A camada de ligação à apresentação e de negócio permite aos utilizadores finais interagir com a solução IoT e os dispositivos. Permite aos utilizadores ver e analisar os dados recolhidos dos seus dispositivos. Estas vistas podem assumir a forma de dashboards, relatórios BI que podem apresentar tanto os dados históricos ou perto de dados em tempo real. Por exemplo, um operador pode verificar o estado da estação de bombagem específico e ver todos os alertas criados pelo sistema. Esta camada também permite a integração dos IoT solução back-end com aplicações de linha de negócio existentes de equiparar em processos de negócio da empresa ou fluxos de trabalho. Por exemplo, pode integrar a solução de manutenção preventiva com um sistema de agendamento que livros um engenheiro para visitar uma estação de bombagem quando a solução identifica uma bomba que necessitem de manutenção.

![Dashboard de solução de IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

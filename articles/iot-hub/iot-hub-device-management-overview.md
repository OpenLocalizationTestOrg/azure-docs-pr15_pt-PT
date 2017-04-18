<properties
 pageTitle="Descrição geral da gestão de dispositivo de IoT concentrador | Microsoft Azure"
 description="Este artigo fornece uma descrição geral da gestão de dispositivos no Azure IoT Hub: ciclo de vida do enterprise dispositivo, reinicie, repor de fábrica do mesmo, actualização de firmware, configuração, gémeos de dispositivo, consultas, tarefas"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Descrição geral da gestão de dispositivos do Azure IoT concentrador (pré-visualização)

## <a name="introduction"></a>Introdução

Azure IoT concentrador fornece as funcionalidades e um modelo de expansão que permitem o dispositivo e os programadores de back-end para criar soluções de gestão de dispositivos do robustas IoT. IoT dispositivos variam de sensores restritos e microcontroladores objectivo único, a poderosas gateways que encaminha comunicações para grupos de dispositivos.  Além disso, a casos de utilização e os requisitos para operadores IoT variam significativamente entre sectores.  Apesar desta variante, gestão de dispositivos do Azure IoT concentrador fornece as capacidades, padrões e bibliotecas de código para responder a um conjunto de diversificados de dispositivos e utilizadores finais.

Uma parte fundamental da criação de uma empresa com êxito IoT solução para fornecer uma estratégia de como os operadores lidar a gestão contínua da sua coleção de dispositivos. Operadores IoT requerem simples e fiáveis ferramentas e aplicações que lhe permitem concentrar-se a mais estratégicos aspetos das suas tarefas. Este artigo fornece:

- Uma breve descrição geral da gestão de dispositivos abordagem Azure IoT concentrador.
- Uma descrição dos princípios de gestão comuns do dispositivo.
- Uma descrição do ciclo de vida do dispositivo.
- Uma descrição geral de padrões de gestão comuns do dispositivo.

## <a name="iot-device-management-principles"></a>Princípios de gestão de dispositivo IoT

IoT abre com o mesmo um conjunto único de desafios de gestão de dispositivos e todas as soluções de classe empresarial terá de resolver os princípios seguintes:

![Gráfico do princípios de gestão de dispositivo do Azure IoT concentrador][img-dm_principles]

- **Escala e automatização**: IoT soluções requerem ferramentas simples que podem automatizar tarefas de manutenção de rotina e ativar um relativamente pequeno operações de pessoal para gerir milhões de dispositivos. No dia a dia, operadores esperar processar operações do dispositivo remotamente, em massa e apenas receber um alerta quando ocorrerem problemas que requerem a sua atenção direta.

- **Compatibilidade e transparência**: ecossistema de dispositivos de IoT é extraordinariamente diversificada. Ferramentas de gestão tem de ser adaptadas para acomodar inúmeros de classes de dispositivos, plataformas e protocolos. Operadores tem de conseguir suporta vários tipos de dispositivos, a partir de mais restritos incorporados único processo circuitos integrados, para computadores totalmente funcionais e avançadas.

- **Deteção de contexto**: IoT ambientes são dinâmicos e alterar alguma vez. Fiabilidade do serviço é fundamental. Operações de gestão de dispositivo tem factor no windows de manutenção, Estados rede e power, em utilização condições e geolocalização de dispositivo para se certificar de que tempo de inatividade da manutenção não afeta operações de negócio crítico ou criar condições perigosas SLA.

- **Muitas funções de serviço**: suporte para os fluxos de trabalho exclusivos e processos IoT das funções de operações é crucial. O pessoal de operações tem de trabalhar harmoniosa com as restrições de departamentos de TI internos determinadas.  Estes também têm encontrar formas sustentáveis de informações de operações de dispositivo de tempo real superfície supervisão e outras funções de gestão de negócio.

## <a name="iot-device-lifecycle"></a>Ciclo de vida do IoT dispositivo

Existe um conjunto de fases de gestão de geral no dispositivo que são comuns a todos os projetos de IoT enterprise. No Azure IoT, existem cinco fases do ciclo de vida de dispositivo IoT:

![As cinco fases do ciclo de vida de dispositivo do Azure IoT: planear, aprovisionar, configurar, monitorizar, retirar][img-device_lifecycle]

Dentro de cada um dos seguinte cinco fases, existem vários requisitos de operador de dispositivo que devem ser preenchidos para fornecer uma solução completa:

- **Planear**: permitir que os operadores criar um esquema de metadados de dispositivo permite-lhes facilmente e com exatidão de consulta para e um grupo de dispositivos para operações de gestão de em volume de destino. Pode utilizar o duplos dispositivo para armazenar metadados este dispositivo sob a forma de etiquetas e propriedades.

    *Leitura adicional*: [Introdução ao gémeos dispositivo][lnk-twins-getstarted], [gémeos de dispositivo entender][lnk-twins-devguide], [como utilizar as propriedades de duplos][lnk-twin-properties]

- **Aprovisionar**: aprovisionar novos dispositivos a IoT concentrador de forma segura e permitir que os operadores para imediatamente descobrir as capacidades de dispositivo.  Utilizar o registo de dispositivo IoT concentrador para criar identidades do dispositivo flexíveis e as credenciais e executar esta operação em massa utilizando uma tarefa. Construa dispositivos para comunicar as respetivas capacidades e condições através das propriedades do dispositivo no duplos dispositivo.

    *Leitura adicional*: [Gerir identidades de dispositivo][lnk-identity-registry], [Gestão de em volume de identidades do dispositivo][lnk-bulk-identity], [como utilizar as propriedades de duplos][lnk-twin-properties]

- **Configurar**: facilitar a alterações na configuração em volume e atualizações do firmware dispositivos mantendo do Estado de funcionamento e segurança. Execute estes operações de gestão de dispositivos em massa utilizando as propriedades pretendidas ou com diretos métodos e tarefas de difusão.

    *Leitura adicional*: [utilizar os métodos de diretos][lnk-c2d-methods], [invocar um método direto num dispositivo][lnk-methods-devguide], [como utilizar as propriedades de duplos][lnk-twin-properties], [agenda e tarefas de implementação de difusão][lnk-jobs], [agendar tarefas em múltiplos dispositivos][lnk-jobs-devguide]

- **Monitor**: monitorizar dispositivo coleção estado de funcionamento geral, o estado de operações em curso, e alertar os operadores problemas poderão implicar a sua atenção.  Aplica o duplos dispositivo para permitir a condições de funcionamento de tempo real de relatório e o estado de operações de atualização de dispositivos. Crie relatórios de dashboards poderosas que revelar os problemas mais imediatos utilizando dispositivo duplos consultas.

    *Leitura adicional*: [como utilizar as propriedades de duplos][lnk-twin-properties], [linguagem de consulta para gémeos e tarefas de implementação][lnk-query-language]

- **Retire**: substituir ou encerrar dispositivos após uma falha, atualizar ciclo, ou no final do tempo de vida do serviço.  Utilizar o duplos dispositivo para manter informações sobre o dispositivo se o dispositivo físico está a ser substituído ou arquivadas se a ser foi removida. Utilize o registo de dispositivo IoT concentrador de forma segura revogação identidades do dispositivo e as credenciais.

    *Leitura adicional*: [como utilizar as propriedades de duplos][lnk-twin-properties], [Gerir identidades de dispositivo][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Padrões de gestão de dispositivo IoT concentrador

Concentrador IoT ativa o conjunto seguinte de padrões de gestão de dispositivo.  Os [Tutoriais de gestão de dispositivo] [ lnk-get-started] mostrar-lhe mais detalhadamente como expandir estes padrões para se ajustar ao seu cenário exato e como estruturar o novos padrões com base nestes modelos de core.

- **Reinicie** - a aplicação de back-end informa o dispositivo através de um método direto que iniciou um reinício.  O dispositivo utiliza o dispositivo duplos comunicado propriedades para atualizar o estado de reiniciar o computador do dispositivo.

    ![Gestão de dispositivos do Azure IoT concentrador reinicie o gráfico padrão][img-reboot_pattern]

- **Repor a fábrica** - a aplicação de back-end informa o dispositivo através de um método direto que iniciou a reposição de fábrica do mesmo.  O dispositivo utiliza o duplos dispositivo denunciadas propriedades para atualizar a fábrica de repor estado do dispositivo.

    ![Fábrica de gestão de dispositivos do Azure IoT concentrador repor gráfico padrão][img-facreset_pattern]

- **Configuração** – a aplicação de back-end utiliza as propriedades do dispositivo duplos pretendido para configurar o software em execução no dispositivo.  O dispositivo utiliza o dispositivo duplos comunicado propriedades para atualizar o estado da configuração do dispositivo.

    ![Azure IoT concentrador dispositivo gestão configuração padrão gráfico][img-config_pattern]

- **Atualizar o firmware** - a aplicação de back-end informa o dispositivo através de um método direto que iniciou uma atualização de firmware.  O dispositivo inicia um processo de nomes para transferir o pacote de firmware, aplicar o pacote de firmware e finalmente restabelecer a ligação ao serviço IoT concentrador.  Ao longo do processo de multi passo, o dispositivo utiliza o dispositivo duplos comunicado propriedades para atualizar o progresso e o estado do dispositivo.

    ![Atualizar o firmware de gestão de dispositivo do Azure IoT concentrador gráfico padrão][img-fwupdate_pattern]

- **Relatórios de progresso e o estado** - aplicação back-end é executado consultas do dispositivo duplos, através de um conjunto de dispositivos, reportar o estado e progresso de ações a executar nos dispositivos.

    ![Gestão de dispositivos IoT concentrador Azure elaboração de relatórios de progresso e o estado gráfico padrão][img-report_progress_pattern]

## <a name="next-steps"></a>Próximos passos

Pode utilizar as capacidades, padrões e bibliotecas de código que fornece de gestão de dispositivos do Azure IoT concentrador, para criar aplicações IoT que cumpram os requisitos de operador de IoT do enterprise dentro em cada etapa de ciclo de vida do dispositivo.

Para continuar a obter informações sobre as funcionalidades de gestão de dispositivos do Azure IoT concentrador, consulte o artigo [começar a trabalhar com gestão de dispositivos do Azure IoT concentrador] [ lnk-get-started] tutorial.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
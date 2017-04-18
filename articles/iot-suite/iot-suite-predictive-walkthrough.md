<properties
 pageTitle="Tutorial de manutenção aspeto do Office | Microsoft Azure"
 description="Instruções sobre a manutenção de aspeto do Office Azure IoT pré-configurado solução."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Aspeto do Office manutenção pré-configurado solução guiadas

## <a name="introduction"></a>Introdução

A solução de manutenção aspeto do Office pré-configurado IoT Suite é uma solução de fim para fim para um cenário de negócio prevê, com um ponto de quando falha é provável que ocorra. Pode utilizar esta solução pré-configurado importante para atividades como otimizar a manutenção. A solução combina serviços de conjunto de aplicações do Azure IoT chave, incluindo uma [Azure máquina aprendizagem] [ lnk_machine_learning] área de trabalho. Esta área de trabalho contém experiências, com base num conjunto de dados de exemplo público, para prever o restante útil vida (RUL) de um motor de avião. A solução totalmente implementa o cenário de negócio IoT como ponto de partida para planear e implementar uma solução que corresponde às suas necessidades próprio específicas da empresa.

## <a name="logical-architecture"></a>Arquitetura de lógica

O diagrama seguinte descreve os componentes lógicos da solução pré-configurado:

![][img-architecture]

Os itens azuis são Azure serviços que configurações para a localização que seleciona quando aprovisionar o a solução pré-configurado. Pode aprovisionar a solução pré-configurado na região o Leste dos EUA, Europa Norte ou Ásia.

Alguns recursos não estão disponíveis nas regiões onde pode aprovisionar a solução pré-configurado. Os itens laranja no diagrama representam os serviços Azure configurações para o mais próximo região disponíveis (Sul Central-nos, oeste Europa ou idiomas do Sudeste asiático) tendo em conta a região seleccionada.

O item verde é um dispositivo simulado que representa um motor de avião. Pode obter mais informações sobre estes dispositivos simulados na secção seguinte.

Os itens cinzentos representam componentes implementam capacidades de *Administração do dispositivo* . Versão atual da solução de manutenção aspeto do Office pré-configurado não aprovisionar estes recursos. Para saber mais acerca da administração de dispositivo, referir-se para a [monitorização de solução pré-configurada remota][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurado, um dispositivo simulado representa um motor de avião. A solução está aprovisionada com dois motores de mapeiam para um único avião. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e 15 de Sensor fornecem os dados necessários para o modelo de aprendizagem automática calcular o restante útil vida (RUL) para o motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria a IoT concentrador:

*Contagem de ciclo*. Um ciclo representa um voo concluído de comprimento variável entre 2-10 horas na qual os dados de telemetria são capturados cada meia hora durante o voo.

*Telemetria*. Existem quatro sensores que representam motor atributos. Os sensores forma genérica estão identificados como Sensor 9, Sensor 11, Sensor 14 e 15 de Sensor. Estes 4 sensores representam telemetria suficiente para obter resultados úteis a partir do modelo de formação de máquina RUL. Este modelo é criado a partir de um conjunto de dados público, que inclui dados nos sensor motor real. Para mais informações sobre como o modelo foi criado a partir do conjunto de dados original, consulte o artigo o [Modelo de manutenção Cortana análise de Galeria de aspeto do Office][lnk-cortana-analytics].

Os dispositivos simulados podem processar os seguintes comandos enviados a partir de um concentrador de IoT:

| Comando | Descrição |
|---------|-------------|
| StartTelemetry | Controla o estado de simulação.<br/>Inicia o dispositivo de envio de telemetria     |
| StopTelemetry  | Controla o estado de simulação.<br/>Deixa o dispositivo de envio de telemetria |

Concentrador de IoT fornece confirmação de comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Tarefa de análise da cadeia Azure

**Tarefa: telemetria** opera sobre a sequência de telemetria dispositivo recebidas com duas instruções. O primeiro seleciona todas as telemetria a partir de dispositivos e envia estes dados ao blob armazenamento a partir de onde são visualizada no web app. A segunda instrução fórmula calcula a média sensor valores ao longo de uma janela deslizante de dois minutos e envia estes dados através do concentrador de evento para um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos

**Processador de eventos** leva os valores de sensor média de um ciclo concluído. -As fases esses valores para uma API que expõe a aprendizagem máquina formação modelo para calcular RUL um motor.

## <a name="azure-machine-learning"></a>Formação de máquina Azure

Para mais informações sobre como o modelo foi criado a partir do conjunto de dados original, consulte o artigo o [Modelo de manutenção Cortana análise de Galeria de aspeto do Office][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Vamos começar andar

Esta secção orienta-o através de componentes da solução, descreve as maiúsculas/minúsculas de utilização prevista e fornece exemplos.

### <a name="predictive-maintenance-dashboard"></a>Dashboard de manutenção aspeto do Office

Esta página na aplicação web utiliza controlos JavaScript obter (consulte o artigo [obter piscam repositório][lnk-powerbi]) para visualizar:

- Os dados de saída das tarefas de análise da cadeia armazenamento de Blobs.
- A contagem RUL e ciclo por motor de avião.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observar o comportamento da solução na nuvem

No portal do Azure, navegue para o grupo de recursos com o nome de solução que escolheu para ver os recursos aprovisionados.

![][img-resource-group]

Quando aprovisionar a solução pré-configurado, recebe uma mensagem de e-mail com uma ligação para a área de trabalho de máquina aprendizagem. Também pode navegar para a área de trabalho de aprendizagem automática a partir do [azureiotsuite.com] [ lnk-azureiotsuite] página para a sua solução aprovisionada quando esta for no estado de **pronto** .

![][img-machine-learning]

No portal do solução, pode ver que a amostra é aprovisionada com quatro dispositivos simulados para representar duas avião com dois motores por avião, cada uma com quatro sensores. Quando pela primeira vez navega para o portal de solução, simulação está parada.

![][img-simulation-stopped]

Clique em **simulação de início** para começar a simulação na qual pode ver o histórico de sensor, RUL, ciclos, e histórico RUL povoar o dashboard.

![][img-simulation-running]

Quando RUL for inferior a 160 (uma arbitrário limiar escolhido para fins de demonstração), o portal de solução apresenta um símbolo de aviso junto a apresentação RUL e realça o motor de avião amarelo. Repare como os valores RUL têm tendência para baixo geral global, mas tendência para dar toques para cima e para baixo. Este comportamento resulta da comprimentos do ciclo de variados e a precisão do modelo.

![][img-simulation-warning]

Simulação completa demora cerca de 35 minutos a concluir 148 ciclos. O limiar RUL 160 é cumprida pela primeira vez em cerca de 5 minutos e ambos os motores de visitas o limiar de cerca de 8 minutos.

Simulação é executado através do conjunto de dados completo para 148 ciclos e liquidar nos valores RUL e ciclo finais.

Pode deixar a simulação em qualquer ponto, mas clicando em **Iniciar simulação** repete a simulação desde o início do conjunto de dados.

## <a name="next-steps"></a>Próximos passos

Agora que tiver executado a solução de manutenção aspeto do Office pré-configurado poderá pretender modificá-lo, consulte o artigo [orientações sobre como personalizar as soluções pré-configurado][lnk-customize].

A mensagem de blogue de [Manutenção de aspeto do Office IoT Suite - em de definições avançadas -](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) TechNet fornece detalhes adicionais sobre a solução de manutenção aspeto do Office pré-configuradas automaticamente.

Também pode explorar algumas das outras funcionalidades e capacidades de soluções de conjunto de aplicações IoT pré-configuradas automaticamente:

- [Perguntas mais frequentes do IoT conjunto de aplicações][lnk-faq]
- [Segurança IoT aprofundadamente][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

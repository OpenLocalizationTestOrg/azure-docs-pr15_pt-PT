<properties
 pageTitle="Manutenção do aspeto do Office pré-configurado solução | Microsoft Azure"
 description="Uma descrição da manutenção de aspeto do Office Azure IoT pré-configurado solução."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Descrição geral da solução do aspeto do Office manutenção pré-configurado

A solução de *Manutenção aspeto do Office* pré-configurado é uma das [soluções pré-configurado] [ lnk_preconfigured_solutions] disponibilizadas como parte do [Conjunto de aplicações do Microsoft Azure IoT][lnk_iot_suite]. Esta solução integra-se a coleção de telemetria do dispositivo em tempo real com um modelo de aspeto do Office criado utilizando o [Azure máquina aprendizagem][lnk_machine_learning].


Com o Azure IoT Suite, empresas podem forma rápida e fácil ligar a e monitorizar recursos e analisar dados em tempo real. A solução de manutenção aspeto do Office pré-configurado leva-o até que os dados e utiliza ricos dashboards e visualizações para fornecer empresas com novas informações da empresa que podem unidade eficiência e melhorar sequências de receitas.

## <a name="the-scenario"></a>O cenário

Fabrikam é uma companhia aérea regional que foca-se em experiência do cliente excelente preços competitiva. Uma causa atrasos em voos é problemas de manutenção e manutenção do motor de avião é particularmente um desafio. Motor Ocorreu uma falha durante voo deve ser evitada todos os costs, para que Fabrikam inspeciona o respetivos motores regularmente e adira a um programa de manutenção agendada. No entanto, motores de avião não sempre o seu papel da mesma. Algumas manutenção desnecessária é executada em motores de. Ainda mais importante, problemas surgirem que pode ligue à terra um avião até que seja realizada manutenção. Isto faz com que dispendioso atrasos, especialmente se um avião numa localização onde técnicos direito ou peças não estão disponíveis.

Os motores de avião do Fabrikam são implementados com sensores que monitorizar condições motor durante voo. Fabrikam utilizar o conjunto de aplicações do Azure IoT para recolher dados sensor recolhidos durante o voo. Depois de anos gozadas acumuláveis do motor de operacional e dados de falhas, cientistas de dados do Fabrikam tem modelado uma forma de prever o restante útil vida (RUL) de um motor de avião. O que tenham identificadas são uma correlação entre os dados de quatro sensores motor com o desgaste de motor oportunidades potenciais para eventual falha. Enquanto Fabrikam continua a executar controlos normais para garantir a segurança, agora pode utilizar os modelos de para calcular RUL para cada motor depois de cada voo utilizando a telemetria recolhidas a partir dos motores de durante o voo. Fabrikam agora pode prever futuros pontos de falha e planear manutenção e reparar com antecedência.

> [AZURE.NOTE] O modelo de solução utiliza dados de desgaste motor real.

Por as previsões o ponto de quando é necessária uma manutenção, Fabrikam pode otimizar a suas operações para reduzir os custos. Os coordenadores de manutenção trabalhar com responsáveis pelo agendamento para planear manutenção coincidindo com um avião parar numa localização específica e garantindo que tempo suficiente para que o avião estar fora serviço sem causar interrupção de agenda. Fabrikam pode agendar técnicos em conformidade; avião garantir sejam assistido eficientemente sem tempo de espera. Os gestores de controlo de inventário recebem planos de manutenção, para que possam otimizar o processo de encomenda e poupar inventário de peças. Tudo isto permite Fabrikam para minimizar o tempo de terra avião e para reduzir os custos de funcionamento, garantindo que a segurança dos passageiros e pessoal.

Para compreender como [Conjunto de aplicações do Azure IoT] [ lnk_iot_suite] fornece os clientes de capacidades necessitam compreender as potencialidades dos manutenção aspeto do Office, reveja esta [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Como é criada a solução de manutenção aspeto do Office

A solução tira partido de um modelo de aprendizagem do Azure máquina existente disponível como um modelo para mostrar estas capacidades trabalhar a partir de telemetria do dispositivo recolhida através dos serviços de IoT conjunto de aplicações. A Microsoft criou um [modelo de regressão] [ lnk_regression_model] de um motor de avião e o modelo concluído, dados de publicados<sup>\[1\]</sup>e orientações passo a passo sobre como utilizar o modelo.

A solução de manutenção aspeto do Office pré-configurado Azure IoT utiliza o modelo de regressão criado a partir deste modelo; é implementado na sua subscrição do Azure e exposto através de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de testes que representa 4 (de 100 total) motores e 4 (de 21 total) sequências de dados de sensor que fornecem um resultado preciso a partir do modelo de formação.

*\[1\] respostas. Saxena e j. Goebel (2008). "Turbohélice motor degradação simulação conjunto de dados", NASA Ames Prognostics dados repositório (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), Centro de pesquisa de Ames NASA, Moffett campo, CA*

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como Azure IoT permite cenários de manutenção aspeto do Office, leia [capturar valor a partir da Internet de coisas][lnk_capture_value].

Tomar [guiadas] [ lnk-predictive-walkthrough] da previsão manutenção pré-configurado solução.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Também pode explorar algumas das outras funcionalidades e capacidades de soluções de conjunto de aplicações IoT pré-configuradas automaticamente:

- [Perguntas mais frequentes do IoT conjunto de aplicações][lnk-faq]
- [Segurança IoT aprofundadamente][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

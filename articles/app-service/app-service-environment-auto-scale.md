<properties
    pageTitle="Autoscaling e ambiente de aplicação de serviço | Microsoft Azure"
    description="Autoscaling e ambiente de aplicação de serviço"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>Autoscaling e ambiente de aplicação de serviço

Azure ambientes de aplicação de serviço de suporte *autoscaling*. Pode agrupamentos de trabalho individuais autoscale com base no métricas ou agenda.

![Opções de Autoscale para um conjunto de trabalho.][intro]

Autoscaling optimiza utilização de recursos por automaticamente aumentar e diminuir um ambiente de aplicação de serviço para ajustar o seu orçamento e de ou carregar o perfil.

## <a name="configure-worker-pool-autoscale"></a>Configurar autoscale de agrupamento de trabalho

Pode aceder à funcionalidade de autoscale a partir do separador **Definições** do conjunto de trabalho.

![Separador de definições do conjunto de trabalho.][settings-scale]

A partir daí, a interface deve ser bastante familiar uma vez que é a mesma experiência que está a ver quando dimensionar um plano de serviço de aplicação. 

![Definições de escala manual.][scale-manual]

Também pode configurar um perfil autoscale.

![Definições de Autoscale.][scale-profile]

Os perfis de Autoscale são úteis para definir limites na sua escala. Desta forma, pode ter um desempenho consistente deparar ao definir um valor de escala vínculo inferior (1) e um chapéu de gastar previsíveis definindo um vínculo superior (2).

![Definições de escala no perfil.][scale-profile2]

Depois de definir um perfil, pode adicionar regras de autoscale para dimensionar para cima ou para baixo o número de instâncias do grupo de trabalho dentro dos limites definidos pelo perfil. Regras de Autoscale são baseadas em métricas.

![Regra de escala.][scale-rule]

 Qualquer conjunto de trabalho ou métricas front-end podem ser utilizadas para definir regras autoscale. Estes métricas são as mesmas métricas pode monitorizar nos gráficos pá recurso ou definir alertas para.

## <a name="autoscale-example"></a>Exemplo de Autoscale

Melhor pode ser ilustrada Autoscale um aplicação de ambiente de serviço por observar um cenário.

Este artigo explica as considerações necessárias ao configurar o autoscale. O artigo explica as interações que entram na play quando factor no autoscaling ambientes de aplicação de serviço que estão alojados num ambiente do serviço de aplicação.

### <a name="scenario-introduction"></a>Introdução cenário

Tiago é uma sysadmin para uma empresa quem tem migrado uma parte das cargas de trabalho que ele gere para um ambiente de aplicação de serviço.

O ambiente de aplicação de serviço está configurado para dimensionar manual da seguinte forma:

* **Frente extremidades:** 3
* **Agrupamento de trabalho 1**: 10
* **Agrupamento de trabalho 2**: 5
* **Conjunto de trabalho 3**: 5

Agrupamento de trabalho 1 é utilizado para cargas de trabalho de produção, conjunto de trabalho 2 e 3 de conjunto de trabalho são utilizados para garantia (das perguntas e respostas) e qualidade das cargas de trabalho de desenvolvimento.

Os planos do serviço de aplicação para as perguntas e respostas e Dev Center estão configurados para escala manual. A plano de serviço de aplicação de produção está definida para autoscale para lidar com as variações de tráfego e carregar.

Tiago está familiarizado com a aplicação. Ele sabe que as horas de pico de carregamento estão entre 9:00 AM e 6:00 porque esta é uma aplicação (LOB) de linha de negócio que os funcionários utilizam enquanto se encontram no office. A utilização desce após que quando os utilizadores terminados para esse dia. Fora as horas de expediente, ainda existe algum carga uma vez que os utilizadores podem aceder a aplicação remotamente utilizando os seus dispositivos móveis ou PCs principal. A plano de serviço de aplicação de produção já está configurada para autoscale com base em utilização da CPU com as seguintes regras:

![Definições específicas para a aplicação LOB.][asp-scale]

|   **Perfil Autoscale – os dias úteis – plano de serviço de aplicação.**     |   **Perfil Autoscale – os fins de semana – plano de serviço de aplicação**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Nome:** Perfil do dia da semana                               |   **Nome:** Perfil de fim de semana                               |
|   **Dimensionar por:** Regras de agenda e desempenho            |   **Dimensionar por:** Regras de agenda e desempenho            |
|   **Perfil:** Dias úteis.                                   |   **Perfil:** Fim de semana                                    |
|   **Tipo:** Periodicidade                                    |   **Tipo:** Periodicidade                                    |
|   **Intervalo de destino:** instâncias de 5 a 20                     |   **Intervalo de destino:** instâncias 3 a 10                     |
|   **Dias:** Segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira  |   **Dias:** Sábado, Domingo                              |
|   **Hora de início:** 9:00 AM                                 |   **Hora de início:** 9:00 AM                                 |
|   **Fuso horário:** UTC-08                                   |   **Fuso horário:** UTC-08                                   |
|                                                           |                                                           |
|   **Regra de Autoscale (escala para cima)**                           |   **Regra de Autoscale (escala para cima)**                           |
|   **Recurso:** Produção (ambiente de aplicação de serviço)      |   **Recurso:** Produção (ambiente de aplicação de serviço)      |
|   **Métrica:** % DA CPU                                       |   **Métrica:** % DA CPU                                       |
|   **Operação:** Maior que 60%                         |   **Operação:** Superior a 80%                         |
|   **Duração:** 5 minutos                                 |   **Duração:** 10 minutos                                |
|   **Agregação de tempo:** Média                           |   **Agregação de tempo:** Média                           |
|   **Ação:** Aumentar a contagem de por 2                         |   **Ação:** Aumentar a contagem de 1                         |
|   **Interessantes para baixo (minutos):** 15                             |   **Interessantes para baixo (minutos):** 20                             |
|                                                           |                                                           |
  	|   **Regra de Autoscale (escala para baixo)**                     |   **Regra de Autoscale (escala para baixo)**                         |
|   **Recurso:** Produção (ambiente de aplicação de serviço)      |   **Recurso:** Produção (ambiente de aplicação de serviço)      |
|   **Métrica:** % DA CPU                                       |   **Métrica:** % DA CPU                                       |
|   **Operação:** Menos de 30%                            |   **Operação:** Menos de 20%                            |
|   **Duração:** 10 minutos                                |   **Duração:** 15 minutos                                |
|   **Agregação de tempo:** Média                           |   **Agregação de tempo:** Média                           |
|   **Ação:** Diminuir a contagem de 1                         |   **Ação:** Diminuir a contagem de 1                         |
|   **Interessantes para baixo (minutos):** 20                             |   **Interessantes para baixo (minutos):** 10                             |

### <a name="app-service-plan-inflation-rate"></a>Taxa de enchimento de plano de serviço de aplicação

Planos do serviço de aplicação que estão configurados para autoscale fazê-lo a uma velocidade máxima por hora. Esta taxa pode ser calculada com base nos valores fornecidos na regra autoscale.

Noções sobre e calcular a *taxa de enchimento de plano de serviço de aplicação* são importante para autoscale de ambiente de aplicação de serviço porque não estão instantâneas escala alterações a um conjunto de trabalho.

A taxa de enchimento de plano de serviço de aplicação é calculada da seguinte forma:

![Aplicação de serviço plano enchimento taxa no cálculo.][ASP-Inflation]

Com base em Autoscale – regra escala para cima para o perfil do dia da semana de produção plano de aplicação de serviço:

![Taxa de enchimento de plano de serviço de aplicação para dias da semana com base em Autoscale – regra escala para cima.][Equation1]

No caso de Autoscale – regra escala para cima para o perfil de fim de semana de produção plano de serviço de aplicação, a fórmula deverá ser resolvido para:

![Taxa de enchimento de plano de aplicação de serviço para fins de semana com base em Autoscale – regra escala para cima.][Equation2]

Também pode ser calculado este valor para operações de escala descendente.

Com base em Autoscale – regra escala para baixo para o perfil do dia da semana de produção plano de serviço de aplicação, isto seria da seguinte forma:

![Taxa de enchimento de plano de serviço de aplicação para dias da semana com base em Autoscale – regra escala para baixo.][Equation3]

No caso de Autoscale – regra escala para baixo para o perfil de fim de semana de produção plano de serviço de aplicação, a fórmula deverá ser resolvido para:  

![Taxa de enchimento de plano de aplicação de serviço para fins de semana com base em Autoscale – regra escala para baixo.][Equation4]

A plano de serviço de aplicação de produção pode aumentar uma velocidade máxima de oito instâncias por hora durante a semana e quatro instâncias por hora durante o fim de semana. -Pode liberte instâncias taxa de juro um máximo de quatro instâncias por hora durante a semana e seis instâncias por hora durante os fins de semana.

Se vários planos de serviço de aplicação estão a ser alojados num conjunto de trabalho, tem de calcular a *taxa de enchimento total* como a soma da taxa de enchimento para todos os planos de aplicação de serviço que estão a ser alojamento desse conjunto de trabalho.

![Cálculo de taxa de enchimento total para vários planos de aplicação de serviço alojado num conjunto de trabalho.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utilizar a taxa de enchimento de plano de serviço de aplicação para definir regras de autoscale do conjunto de trabalho

Anfitrião planos do serviço de aplicação que estão configurados para autoscale precisa de ser atribuída um intervalo de tempo da capacidade de agrupamentos de trabalho. A memória intermédia permite que as operações de autoscale aumentar e diminuir o plano de serviço de aplicação, conforme necessário. O mínimo da memória intermédia seria a Total aplicação serviço planear enchimento taxa calculada.

Uma vez que operações de escala de ambiente de aplicação de serviço demorar algum tempo para aplicar, qualquer alteração deve contabilizar ainda mais a pedido as alterações que podem ocorrer a uma operação de escala está em curso. Para acomodar esta latência, recomendamos que utilize a Total aplicação serviço planear enchimento taxa calculada como o número mínimo de instâncias são adicionados para cada operação autoscale.

Com esta informação, Francisco pode definir as seguintes autoscale perfil e as regras:

![Regras de perfil de Autoscale por exemplo LOB.][Worker-Pool-Scale]

|   **Perfil Autoscale – os dias úteis.**                        |   **Perfil Autoscale – os fins de semana**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Nome:** Perfil do dia da semana                               |   **Nome:** Perfil de fim de semana                       |
|   **Dimensionar por:** Regras de agenda e desempenho            |   **Dimensionar por:** Regras de agenda e desempenho    |
|   **Perfil:** Dias úteis.                                   |   **Perfil:** Fim de semana                            |
|   **Tipo:** Periodicidade                                    |   **Tipo:** Periodicidade                            |
|   **Intervalo de destino:** instâncias de 13 a 25                    |   **Intervalo de destino:** instâncias de 6 a 15             |
|   **Dias:** Segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira  |   **Dias:** Sábado, Domingo                      |
|   **Hora de início:** 7:00 AM                                 |   **Hora de início:** 9:00 AM                         |
|   **Fuso horário:** UTC-08                                   |   **Fuso horário:** UTC-08                           |
|                                                           |                                                   |
|   **Regra de Autoscale (escala para cima)**                           |   **Regra de Autoscale (escala para cima)**                   |
|   **Recurso:** Agrupamento de trabalho 1                             |   **Recurso:** Agrupamento de trabalho 1                     |
|   **Métrica:** WorkersAvailable                            |   **Métrica:** WorkersAvailable                    |
|   **Operação:** Menor do que 8                              |   **Operação:** Menos de 3                      |
|   **Duração:** 20 minutos                                |   **Duração:** 30 minutos                        |
|   **Agregação de tempo:** Média                           |   **Agregação de tempo:** Média                   |
|   **Ação:** Aumentar a contagem de 8                         |   **Ação:** Aumentar a contagem de 3                 |
|   **Interessantes para baixo (minutos):** 180                            |   **Interessantes para baixo (minutos):** 180                    |
|                                                           |                                                   |
|   **Regra de Autoscale (escala para baixo)**                         |   **Regra de Autoscale (escala para baixo)**                 |
|   **Recurso:** Agrupamento de trabalho 1                             |   **Recurso:** Agrupamento de trabalho 1                     |
|   **Métrica:** WorkersAvailable                            |   **Métrica:** WorkersAvailable                    |
|   **Operação:** Maior do que 8                           |   **Operação:** Maior que 3                   |
|   **Duração:** 20 minutos                                |   **Duração:** 15 minutos                        |
|   **Agregação de tempo:** Média                           |   **Agregação de tempo:** Média                   |
|   **Ação:** Diminuir a contagem de 2                         |   **Ação:** Diminuir a contagem de 3                 |
|   **Interessantes para baixo (minutos):** 120                            |   **Interessantes para baixo (minutos):** 120                    |

O intervalo de destino definido no perfil é calculado às instâncias mínimas definidas no perfil para o plano de serviço de aplicação + memória intermédia.

O intervalo máximo seria a soma de todos os intervalos máximos para todos os planos de aplicação de serviço alojados no conjunto de trabalho.

A contagem de aumentar para a escala de regras deve ser configurada para 1x, pelo menos, a taxa de enchimento de plano ao serviço de aplicação de escala.

Contagem de diminuição pode ser ajustada para algo entre 1/2 X ou 1x a taxa de enchimento de plano ao serviço de aplicação de escala para baixo.

### <a name="autoscale-for-front-end-pool"></a>Autoscale para conjunto de dados front-end

Regras para autoscale front-end são mais simples para agrupamentos de trabalho. Basicamente, deverá  
Certifique-se de que a duração de medição e os temporizadores cooldown considere que operações de escala no plano de uma aplicação de serviço não estão instantâneas.

Para este cenário, Francisco sabe que a taxa de erro aumenta depois extremidades frente chegar a utilização da CPU 80% e define a regra autoscale para aumentar as instâncias da seguinte forma:

![Definições de Autoscale para conjunto de dados front-end.][Front-End-Scale]

|   **Perfil Autoscale – frente termina**              |
|   --------------------------------------------    |
|   **Nome:** Autoscale – frente termina                |
|   **Dimensionar por:** Regras de agenda e desempenho    |
|   **Perfil:** Todos os dias                           |
|   **Tipo:** Periodicidade                            |
|   **Intervalo de destino:** instâncias 3 a 10             |
|   **Dias:** Todos os dias                              |
|   **Hora de início:** 9:00 AM                         |
|   **Fuso horário:** UTC-08                           |
|                                                   |
|   **Regra de Autoscale (escala para cima)**                   |
|   **Recurso:** Agrupamento de front-end                    |
|   **Métrica:** % DA CPU                               |
|   **Operação:** Maior que 60%                 |
|   **Duração:** 20 minutos                        |
|   **Agregação de tempo:** Média                   |
|   **Ação:** Aumentar a contagem de 3                 |
|   **Interessantes para baixo (minutos):** 120                    |
|                                                   |
|   **Regra de Autoscale (escala para baixo)**                 |
|   **Recurso:** Agrupamento de trabalho 1                     |
|   **Métrica:** % DA CPU                               |
|   **Operação:** Menos de 30%                    |
|   **Duração:** 20 minutos                        |
|   **Agregação de tempo:** Média                   |
|   **Ação:** Diminuir a contagem de 3                 |
|   **Interessantes para baixo (minutos):** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

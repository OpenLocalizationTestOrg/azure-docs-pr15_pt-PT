<properties
    pageTitle="Contagem de instâncias de dimensionar automaticamente ou manualmente | Microsoft Azure"
    description="Saiba como dimensionar os seus serviços Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Contagem de instâncias de dimensionar automaticamente ou manualmente

No [Portal do Azure](https://portal.azure.com/), pode definir manualmente a contagem de instância do seu serviço ou, pode definir parâmetros para que seja automaticamente escala com base no pedido. Normalmente, isto é referido como *escala saída* ou *escala no*.

Antes de dimensionamento com base na contagem de instância, deverá tomar em consideração de que dimensionamento é afetado pelos **preços camadas** para além da contagem de instâncias. Diferentes camadas comparar podem ter núcleos de números diferentes e da memória e, por isso, terão um melhor desempenho para o mesmo número de instâncias (que é a *escala para cima* ou *escala para baixo*). Este artigo abrange especificamente *escala no* ' e ' *saída*.

Pode dimensionar no portal do e também pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para ajustar a escala manualmente ou automaticamente.

> [AZURE.NOTE] Este artigo descreve como criar uma definição de autoscale no portal na [http://portal.azure.com](http://portal.azure.com). Definições de Autoscale criadas neste portal não podem ser editados-lo ao portal clássico ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Dimensionamento manualmente

1. No [Portal do Azure](https://portal.azure.com/), clique em **Procurar**, em seguida, navegue para o recurso que quer dimensionar, tal como um **plano de serviço de aplicação**.

2. O mosaico de **escala** no **operações** irá indicar o estado de dimensionamento: **desativar** para quando é dimensionamento manualmente, **** para quando são dimensionamento por uma ou mais métricas de desempenho.
    ![Mosaico de escala](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Clicar no mosaico será direcionado para o pá **escala** . No topo da pá a escala pode ver um histórico das ações autoscale o serviço.  
    ![Pá de escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Apenas as ações que são executadas por autoscale irão aparecer neste gráfico. Se ajustar a contagem de instância manualmente, a alteração não será refletida neste gráfico.

4. Pode ajustar manualmente as **instâncias** de número com o controlo de deslize.
5. Clique no comando **Guardar** e vai ser dimensionados para esse número de instâncias quase imediatamente.

## <a name="scaling-based-on-a-pre-set-metric"></a>Dimensionamento com base numa métrica predefinida

Se pretender que o número de instâncias para ajustar automaticamente com base numa métrica, selecione a métrica do que pretende na lista pendente de **escala por** . Por exemplo, para um **plano de serviço de aplicação** pode dimensionar por **Percentagem de CPU**.

1. Quando seleciona uma métrica irá obter um controlo de deslize e/ou, caixas de texto para introduzir o número de instâncias que quer Dimensionar entre:

    ![Pá escala com percentagem de CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Autoscale nunca entrarão em seu serviço abaixo ou acima os limites definida por si, independentemente da sua carga.

2. Em segundo lugar, escolher o intervalo de destino para a métrica do. Por exemplo, se tiver optado por **percentagem de CPU**, pode definir um alvo para a média CPU através de todas as ocorrências no seu serviço. Uma escala de saída irá acontecer quando a média CPU excede o máximo que define, do mesmo modo, uma escala numa irá acontecer sempre que a média CPU desce abaixo do mínimo.

3. Clique no comando **Guardar** . Autoscale verificará em poucos minutos para se certificar de que se encontra de intervalo instância e de destino para sua métrica. Quando o seu serviço recebe tráfego adicional, obterá mais ocorrências sem ter de fazer nada.

## <a name="scale-based-on-other-metrics"></a>Escala com base nas outras métricas

É possível dimensionar métricas com base no que não seja predefinições que aparecem na lista pendente **Dimensionar por** e podem ter um conjunto de escala de saída complexo e dimensionar no regras.

### <a name="adding-or-changing-a-rule"></a>Adicionar ou alterar uma regra

1. Selecione as **regras de agenda e o desempenho** na lista pendente de **escala por** : ![as regras de desempenho](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Se tinha autoscale anteriormente, na verá uma vista das regras exatas que tinha.

3. Para dimensionar com base no outro clique métrico a linha **Adicionar regra** . Também pode clicar em uma das linhas existentes para alterar de métrica que tinha anteriormente a métrica do que quer Dimensionar por.
![Adicionar regra](./media/insights-how-to-scale/Insights_AddRule.png)

4. Agora, precisa de selecionar qual métrica que quer Dimensionar por. Quando escolher uma métrica existem algumas coisas a ter em conta:
    * O *recurso* a métrica do provém. Normalmente, isto serão os mesmos como o recurso que é dimensionamento. No entanto, se pretender dimensionar pela profundidade de uma fila de armazenamento, o recurso é a escala por fila de espera.
    * O *métrica nome* próprio.
    * A *agregação de tempo* da métrica. Esta é a forma como os dados são combinar ao longo da *duração*.

5. Depois de escolher o seu métrica escolher o limiar de para a métrica e o operador. Por exemplo, poderia dizer **superior a** **80%**.

6. Em seguida, selecione a ação que pretenda retirar. Existem algumas tipo diferente das ações:
    * Aumentar ou diminuir - Isto irá adicionar ou remover o número de **valor** de instâncias que pode definir
    * Aumentar ou diminuir ntagem - Isto irá alterar a contagem de instância por uma percentagem. Por exemplo, pode incluir 25 no campo de **valor** e, se tinha 8 instâncias atualmente, 2 seria adicionado.
    * Aumentar ou diminuir para – esta opção permite configurar a contagem de instância para o **valor** que define.

7. Por fim, pode escolher interessantes para baixo - quanto tempo esta regra deverá aguardar após a acção anterior da escala para dimensionar novamente.

8. Depois de configurar a regra de acertos **OK**.

9. Depois de ter configurado todas as regras que pretende, certifique-se de que visitas o comando **Guardar** .

### <a name="scaling-with-multiple-steps"></a>Dimensionamento com vários passos

Os exemplos acima são muito básicos. No entanto, se quiser ser mais agressivas sobre dimensionamento para cima (ou para baixo), ainda pode adicionar múltiplas regras de escala para a métrica do mesmo. Por exemplo, pode definir regras da escala de duas percentagem de CPU:

1. Dimensionar saída pela 1 instância se a percentagem de CPU estiver acima 60%
2. Dimensionar saída por 3 instâncias se a percentagem de CPU estiver acima 85%

![Múltiplas regras de escala](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Com esta regra adicional, se a sua carga exceder 85% antes de uma ação de escala, obterá duas instâncias adicionais em vez de um.

## <a name="scale-based-on-a-schedule"></a>Escala com base numa agenda


Por predefinição, quando cria uma regra de escala-lo será sempre aplicada. Pode ver que, quando clica no cabeçalho de perfil:

![Perfil](./media/insights-how-to-scale/Insights_Profile.png)

No entanto, poderá pretender ter mais agressivas dimensionamento durante o dia ou da semana, que no fim de semana. Mesmo poderia encerrar o seu serviço completamente fora do horário de trabalho.

1. Para fazer isto, no perfil que tem, selecione **Periodicidade** em vez de **sempre** e escolha as horas que pretende que o perfil a aplicar.

2. Por exemplo, para que um perfil que se aplica durante a semana, na lista pendente **dias** desmarque **Sábado** e **Domingo**.

3. Para ter um perfil que se aplica durante o dia, defina a **hora de início** para a hora do dia que pretende iniciar em.
    ![Periodicidade predefinido](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Clique em **OK**.

5. Em seguida, terá de adicionar o perfil que pretende aplicar noutras alturas. Clique na linha de **Adicionar o perfil** .
    ![Não estou a trabalhar](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Atribuir um nome a sua novo, em segundo, perfil, por exemplo pode chamar- **não estou a trabalhar**.

7. Em seguida, selecione novamente **Periodicidade** e selecione o intervalo de contagem de instância que pretende durante este período de tempo.

8. Como com o perfil predefinido, selecione os **dias** que pretende este perfil para aplicar a e a **hora de início** durante o dia.

>[AZURE.NOTE] Autoscale irá utilizar as regras de poupanças Verão para independentemente **Fuso horário** que selecionar. No entanto, durante a hora de Verão a em relação à UTC irá mostrar o desvio de fuso horário base, não o desvio de poupanças UTC Verão.

9. Clique em **OK**.

10. Agora, terá de adicionar qualquer regras que pretende aplicar durante o seu perfil do segundo. Clique em **Adicionar regra**e, em seguida, poderá construir a mesma regra que tiver durante o perfil predefinido.
    ![Adicionar regra para desligado trabalho](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Certifique-se de que criar ambos os uma regra para escala de saída e escala no, ou durante o perfil a contagem de instância só será crescem em sequência (ou diminuir).

12. Por fim, clique em **Guardar**.

## <a name="next-steps"></a>Próximos passos

* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.
* [Activar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para recolher detalhadas métricas de alta frequência no seu serviço.
* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que eventos operacionais ocorrem ou métricas cruzada um determinado limiar.
* [Monitorizar o desempenho de aplicação](../application-insights/app-insights-azure-web-apps.md) se pretender compreender exatamente como o código está a executar na nuvem.
* [Ver eventos e registos de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Disponibilidade de monitor e consequência de qualquer página web](../application-insights/app-insights-monitor-web-app-availability.md) com informações de aplicação para que pode saber se a página é premida.

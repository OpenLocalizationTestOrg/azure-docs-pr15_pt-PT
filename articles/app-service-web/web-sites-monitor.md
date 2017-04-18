<properties
    pageTitle="Aplicações de monitorização no Azure de aplicação de serviço"
    description="Saiba como monitorizar aplicações na aplicação de serviço de Azure utilizando o Portal do Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Como: monitorizar aplicações no Azure de aplicação de serviço

[Aplicação de serviço de](http://go.microsoft.com/fwlink/?LinkId=529714) fornece a funcionalidade de monitorização incorporada no [Portal do Azure](https://portal.azure.com).
Isto inclui a capacidade de rever **as quotas** e **métricas** para uma aplicação, bem como o plano de serviço de aplicação, a configurar **alertas** e até mesmo **dimensionamento** automaticamente com base nestes métricas.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Noções sobre Quotas e a métrica

### <a name="quotas"></a>Quotas de

Aplicações alojadas na aplicação de serviço são sujeitos a determinados *limites* de recursos podem utilizar. Os limites são definidos pelo **plano de serviço de aplicação** associados com a aplicação.

Se a aplicação estiver alojada num plano de **Free** ou **partilhado** , os limites de recursos que pode utilizar a aplicação são definidos pelas **Quotas**.

Se a aplicação estiver alojada num **básicas**, plano **padrão** ou **Premium** , em seguida, os limites nos recursos podem utilizar são definidos pelo **tamanho** (pequeno, médio, grande) e a **contagem de instâncias** (1, 2, 3,...) do **plano de serviço de aplicação**.

**Quotas** para as aplicações **Free** ou **partilhados** são:

* **CPU(short)**
   * Quantidade de CPU permitida para esta aplicação num intervalo de 3 minutos. Esta quota novamente define cada 3 minutos.
* **CPU(Day)**
   * Quantidade total da CPU permitida para esta aplicação num dia. Esta quota define voltar a cada 24 horas à meia-noite UTC.
* **Memória**
   * Quantidade total da memória permitida para esta aplicação.
* **Largura de banda**
   * Quantidade total da largura de banda saída permitida para esta aplicação num dia.
   Esta quota define voltar a cada 24 horas à meia-noite UTC.
* **Sistema de ficheiros**
   * Quantidade total de armazenamento permitido.

A quota apenas aplicável às aplicações alojadas no **básicos**, **padrão** e planos **Premium** é o **sistema de ficheiros**.

Obter mais informações sobre as funcionalidades disponíveis para as diferentes SKUs de serviço de aplicação quotas específicas, limites e podem ser encontradas aqui: [Limites de serviço de subscrição do Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Imposição de quota

Se uma aplicação na respetiva utilização exceder a **CPU (Abreviação)**, **CPU (dia)**, ou quota de **largura de banda** , em seguida, a aplicação vai ser parada até que a quota de conjuntos de novamente. Durante este período de tempo, todos os pedidos recebidos irão resultar numa **HTTP 403**.
![][http403]

Se a quota de **memória** aplicação for excedida, a aplicação será iniciada novamente.

Se for excedida a quota de **sistema de ficheiros** , em seguida, qualquer escrever operação irá falhar, isto inclui escrita aos registos.

As quotas de podem ser maior ou removidas da sua aplicação ao atualizar o seu plano de serviço de aplicação.

### <a name="metrics"></a>Métricas

**Métricas** fornecem informações sobre a aplicação ou o comportamento do plano de serviço de aplicação.

Para uma **aplicação**, as métricas disponíveis são:

* **Tempo de resposta médio**
   * O tempo médio despendido para a aplicação servir pedidos em ms.
* **Memória média conjunto de trabalho**
   * O montante médio de memória em MIB utilizado pela aplicação.
* **Tempo de CPU**
   * A quantidade de CPU em segundos média consumida pela aplicação. Para mais informações sobre este assunto métrica, consulte: [percentagem de CPU CPU tempo vs](#cpu-time-vs-cpu-percentage)
* **Dados**
   * A quantidade de receção da largura de banda média consumida por da aplicação de MIB.
* **Dados de saída**
   * A quantidade de saída da largura de banda média consumida por da aplicação de MIB.
* **Http 2xx**
   * Contagem de pedidos de que resulta num código de estado de http > = 200 mas < 300.
* **Http 3xx**
   * Contagem de pedidos de resulte um código de estado de http > = 300 mas < 400.
* **HTTP 401**
   * Contagem de pedidos de que resulta no código de estado de HTTP 401.
* **HTTP 403**
   * Contagem de pedidos de que resulta no código de estado de HTTP 403.
* **HTTP 404**
   * Contagem de pedidos de que resulta em código de estado de HTTP 404.
* **HTTP 406**
   * Contagem de pedidos de que resulta no código de estado de HTTP 406.
* **Http 4xx**
   * Contagem de pedidos de que resulta num código de estado de http > = 400 mas < 500.
* **Erros do servidor de HTTP**
   * Contagem de pedidos de que resulta num código de estado de http > = 500 mas < 600.
* **Conjunto de memória funcional**
   * Quantidade atual de memória utilizada da aplicação de MIB.
* **Pedidos de**
   * Número total de pedidos de independentemente respectivo código de estado HTTP resultante.

Para um **plano de serviço de aplicação**, as métricas disponíveis são:

>[AZURE.NOTE] Métricas de plano de serviço de aplicação só estão disponíveis para planos no **básicas**, **padrão** e **Premium** SKU.

* **Percentagem de CPU**
   * Médio de CPU utilizado em todas as ocorrências do plano.
* **Percentagem de memória**
   * A memória média utilizada em todas as ocorrências do plano.
* **Dados**
   * A média recebida largura de banda utilizada em toda a todas as instâncias do plano.
* **Dados de saída**
   * A média de envio de largura de banda utilizada em todas as ocorrências do plano.
* **Comprimento da fila do disco**
   * O número médio de ler e escrever pedidos que foram em fila em armazenamento. Um comprimento de fila de disco alta é uma indicação de uma aplicação que poderá ser lento devido a excessivo disco e/s.
* **Comprimento da fila de HTTP**
   * O número médio de pedidos de HTTP que tinha sentar na fila de espera antes de a ser preenchidas. Um comprimento de fila de HTTP alto ou crescente é um sintoma de um plano de carga elevada.

### <a name="cpu-time-vs-cpu-percentage"></a>Percentagem de vs CPU do tempo de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existem 2 métricas que reflitam a utilização da CPU. **Tempo de CPU** e **percentagem de CPU**

**Tempo de CPU** é útil para aplicações alojadas nos planos do **Free** ou **partilhado** , uma vez que é definida uma das suas quotas em minutos de CPU utilizados pela aplicação.

**Percentagem de CPU** outro lado é útil para aplicações alojadas nos planos do **básicas**, **padrão** e **premium** , uma vez que podem ser dimensionados saída e esta métrica é uma boa indicação de utilização de geral através de todas as instâncias.

##<a name="metrics-granularity-and-retention-policy"></a>Métricas granularidade e a política de retenção

Métricas para uma aplicação e o plano de serviço de aplicação são tem sessão iniciadas e agregadas pelo serviço com as seguintes granularidades devem ser e políticas de retenção:

 * Métricas de granularidade **minuto** estão retidas para **48 horas**
 * Métricas de granularidade **hora** estão retidas para **30 dias**
 * Métricas do **dia** granularidade estão retidas para **cerca de 90 dias**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorizar Quotas e métricas no Portal do Azure.

Pode rever o estado dos diferentes **quotas** e **métricas** afetar uma aplicação no [Portal do Azure](https://portal.azure.com).

![][quotas]
**As quotas de** pode ser encontrado em Definições >**Quotas**. O UX permite-lhe rever: (1) o nome de quotas, (2) o intervalo de reposição, (3) o seu limite atual e valor (4) atual.

![][metrics]
**Métricas** pode ser acesso diretamente a partir de pá o recurso. Também pode personalizar o gráfico por: (1) **clique** no mesmo e, selecione (2) **Editar gráfico**.
A partir daqui pode alterar o **intervalo de tempo**(3), o **tipo de gráfico**de (4) e (5) **métricas** para apresentar.  

Pode obter mais informações sobre métricas aqui: [métricas de serviço do Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertas e Autoscale
Métricas para um plano de aplicação ou serviço de aplicação pode estar ligada a grande até o alerta, para obter mais informações sobre isto, consulte o artigo [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Aplicações de serviço de aplicação alojadas no básica, padrão ou premium aplicação de serviço planos suportam **autoscale**. Esta opção permite-lhe configurar regras que monitorizar as métricas de plano de serviço de aplicação e podem aumentar ou diminuir a contagem de instância fornecer recursos adicionais, conforme necessário, ou dinheiro guardar quando a aplicação estiver aprovisionar excessiva. Pode obter mais informações sobre a escala de automática aqui: [como escala](../monitoring-and-diagnostics/insights-how-to-scale.md) e aqui [melhores práticas para autoscaling Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png

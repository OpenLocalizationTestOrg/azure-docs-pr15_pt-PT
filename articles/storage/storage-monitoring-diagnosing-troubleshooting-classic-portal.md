<properties
    pageTitle="Monitorizar, diagnosticar e resolver problemas de armazenamento | Microsoft Azure"
    description="Utilize funcionalidades tais como a análise de armazenamento, registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar em resolução de problemas relacionados com o armazenamento do Windows Azure."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorizar, diagnosticar e resolver problemas de armazenamento do Windows Azure

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Descrição geral

Diagnosticar e resolução de problemas de uma aplicação distribuída alojado num ambiente na nuvem, podem ser mais complexas do que nos ambientes tradicionais. Aplicações podem ser implementadas no infraestrutura PaaS ou IaaS, no local, num dispositivo móvel ou em algumas combinação dos seguintes procedimentos. Normalmente, o tráfego de rede da sua aplicação, pode percorrer redes públicas e privadas e a sua aplicação poderá utilizar vários armazenamento tecnologias, tais como tabelas do Microsoft Azure armazenamento de Blobs, filas ou ficheiros para além de outros dados armazena tal como relacionais e bases de dados do documento.

Para gerir esses aplicações com êxito deve monitorizá-los importante e compreender como diagnosticar e resolver problemas de todos os aspetos da-los e as suas tecnologias dependentes. Como um utilizador dos serviços de armazenamento do Windows Azure, deve continuamente monitorizar os serviços de armazenamento que a aplicação utiliza para quaisquer alterações inesperadas nos comportamento (por exemplo, mais lento do que os tempos de resposta habitual) e, utilizar o registo para recolher dados mais detalhados e analisar um problema aprofundadamente. As informações de diagnóstico, são obtidos a partir de monitorização e o registo irão ajudá-lo para determinar a causa de raiz do problema se tenha deparado ou a aplicação. Em seguida, pode resolver o problema e determinar os passos adequados que pode tomar para solucionar uma-lo. Armazenamento Azure é um serviço Azure núcleo e formulários de uma parte da maioria dos soluções que os clientes implementar a infraestrutura do Azure importante. Armazenamento Azure inclui capacidades para simplificar a monitorização, diagnóstico e resolução de problemas de armazenamento nas suas aplicações baseadas na nuvem.

> [AZURE.NOTE] Contas de armazenamento com um tipo de replicação de zona redundantes armazenamento (ZRS) não possui a métricas ou a funcionalidade de registo ativado neste momento. 

Para um guia prático para resolução de problemas de fim para fim nas aplicações de armazenamento do Windows Azure, consulte o artigo [Resolução de problemas com o métricas de armazenamento do Azure e registo, AzCopy e mensagem Analyzer fim para fim](storage-e2e-troubleshooting.md).

+ [Introdução]
    + [Como este guia está organizado]
+ [Monitorizar o seu serviço de armazenamento]
    + [Monitorizar o estado de funcionamento do serviço]
    + [Capacidade de monitorização]
    + [Disponibilidade de monitorização]
    + [Monitorizar o desempenho]
+ [Diagnosticar problemas de armazenamento]
    + [Problemas de estado de funcionamento de serviço]
    + [Problemas de desempenho]
    + [Diagnosticar erros]
    + [Problemas de emulador de armazenamento]
    + [Ferramentas de registo de armazenamento]
    + [Utilizar ferramentas de registo de rede]
+ [Rastreio ponto a ponto]
    + [Dados do registo correlação]
    + [ID de pedido de cliente]
    + [ID de pedido de servidor]
    + [Selos de tempo]
+ [Resolução de problemas de orientação]
    + [Métricas mostram AverageE2ELatency alta e AverageServerLatency baixa]
    + [Métricas mostram AverageE2ELatency baixa e AverageServerLatency baixa, mas o cliente está a experimentar latência alta]
    + [Métricas mostram AverageServerLatency alta]
    + [Ocorreu inesperados atrasos na entrega de mensagens numa fila]
    + [Métricas de mostram um aumento no PercentThrottlingError]
    + [Métricas de mostram um aumento no PercentTimeoutError]
    + [Métricas de mostram um aumento no PercentNetworkError]
    + [O cliente está a receber mensagens de HTTP 403 (proibido)]
    + [O cliente está a receber mensagens de HTTP 404 (não encontrado)]
    + [O cliente está a receber mensagens de HTTP 409 (conflito)]
    + [Métricas mostram PercentSuccess baixa ou entradas de registo de analytics tem operações com o estado da transação da ClientOtherErrors]
    + [Métricas de capacidade mostram um aumento inesperado na utilização de capacidade de armazenamento]
    + [Ocorreu for reiniciado inesperado de máquinas virtuais que tenham um grande número de VHDs anexados]
    + [Surge o problema de utilizar o emulador de armazenamento para desenvolvimento ou de teste]
    + [Encontrar problemas ao instalar o SDK do Azure para .NET]
    + [Tem um problema diferente com um serviço de armazenamento]
+ [Anexos]
    + [Anexo 1: Utilizar Fiddler para capturar tráfego HTTP e HTTPS]
    + [Anexo 2: Utilizar Wireshark para capturar tráfego de rede]
    + [Anexo 3: Utilizar o Microsoft mensagem Analyzer para capturar tráfego de rede]
    + [Anexo 4: Utilizar o Excel para ver métricas e registar os dados]
    + [Anexo 5: Monitorizar com informações de aplicação para serviços de equipa do Visual Studio]

## <a name="introduction"></a>Introdução

Este guia mostra-lhe como utilizar funcionalidades como a análise de armazenamento do Azure, lado do cliente registo no Azure armazenamento a biblioteca do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas de armazenamento do Windows Azure relacionados com problemas.

![][1]

*Figura 1 monitorização, diagnósticos e resolução de problemas*

Este guia destina-se a serem lidos principalmente pelos programadores de serviços online que utilizam dos serviços de armazenamento do Azure e profissionais de TI responsável pela gestão desses serviços onlinehttps. Os objetivos deste guia são:

- Para o ajudar a manter a Estado de funcionamento e o desempenho das suas contas de armazenamento do Windows Azure.
- Para fornecer-lhe os processos necessários e ferramentas para ajudar a decidir se uma questão ou problema numa aplicação está relacionada com a armazenamento do Windows Azure.
- Para fornecer-lhe acionáveis orientações para resolver problemas relacionados com o armazenamento do Windows Azure.

### <a name="how-this-guide-is-organized"></a>Como este guia está organizado

A secção "[monitorizar o seu serviço de armazenamento]" descreve como monitorizar o estado de funcionamento e o desempenho dos seus serviços de armazenamento do Windows Azure utilizando métricas de análise de armazenamento do Azure (métricas de armazenamento).

A secção "[diagnosticar problemas de armazenamento]" descreve como diagnosticar problemas com o Azure armazenamento a análise de registo (armazenamento de registo). Também descreve como ativar o registo do lado do cliente utilizando as funcionalidades de uma das bibliotecas do cliente como a biblioteca de cliente de armazenamento para .NET ou o SDK do Azure para Java.

A secção "[rastreio ponto a ponto]" descreve como pode correlacionar as informações contidas no vários ficheiros de registo e dados de métricas.

A secção "[orientações de resolução de problemas]" fornece sugestões de resolução de problemas para alguns dos problemas relacionados com armazenamento comuns que poderá encontrar.

"[Anexos]" incluem informações sobre como utilizar outras ferramentas, como Wireshark e Netmon para analisar dados de pacotes de rede, Fiddler para analisar as mensagens de HTTP/HTTPS e Microsoft mensagem Analyzer para correlações de dados do registo.


## <a name="monitoring-your-storage-service"></a>Monitorizar o seu serviço de armazenamento

Se estiver familiarizado com a monitorização de desempenho Windows, poderá pensar métricas de armazenamento como sendo equivalente a um armazenamento do Windows Azure contadores Monitor de desempenho do Windows. Métricas de armazenamento, encontrará um conjunto abrangente de métricas (contadores no terminologia de Monitor de desempenho do Windows) como disponibilidade do serviço, número total de pedidos de serviço, ou a percentagem de bem sucedidas pedidos de serviço (para uma lista completa de métricas disponíveis, consulte o artigo <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Esquema de tabela de métricas de análise de armazenamento</a> no MSDN). Pode especificar se pretende que o serviço de armazenamento para recolher e agregar métricas cada hora ou minuto. Para mais informações sobre como ativar métricas e monitorizar as suas contas de armazenamento, consulte o artigo <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">métricas de armazenamento de activar</a> MSDN.

Pode escolher quais métricas de hora a hora que pretende apresentar no Portal clássica do Azure e configurar regras que notificar os administradores por correio eletrónico sempre que uma métrica horária excede um determinado limiar (para obter mais informações, consulte a página <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">como: receber notificações de alerta e gerir um alerta de regras no Azure</a>). O serviço de armazenamento recolhe métricas utilizando um esforço de melhor, mas não pode gravar cada operação de armazenamento.

Figura 2 abaixo mostra a página de Monitor no Portal clássica do Azure onde pode ver métricas como disponibilidade, total de pedidos e números de latência de em média de uma conta de armazenamento. Uma regra de notificação também tenha sido configurada para alertar o administrador se disponibilidade descer abaixo de um determinado nível. A partir de ver estes dados, uma área possível para inquérito é a percentagem de sucesso do serviço de tabela a ser inferiores a 100% (para obter mais informações, consulte a secção "[métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado da transação da ClientOtherErrors]").

![][2]

*Figura 2 ver métricas de armazenamento no Portal clássica do Azure*

Deverá continuamente monitorizar as aplicações Azure para garantir que são saudável e desempenho, tal como é esperado ao:

- Estabelecer algumas métricas do plano base para a aplicação que permite-lhe comparar dados atuais e identificar quaisquer alterações significativas no comportamento do Azure armazenamento e a sua aplicação. Em muitos casos, os valores dos seus métricas do plano base será específico da aplicação e deverá estabelecer quando estiver a aplicação de testes de desempenho.
- Gravação métricas minutos e utilizá-los para monitorizar a ativamente erros inesperados e anomalias como picos erro contagens de palavras ou pedir taxas.
- Gravação métricas de hora a hora e utilizá-los para monitorizar os valores médios tais como fazer a média de contagens de erro e solicite taxas.
- Investigar possíveis problemas com ferramentas de diagnóstico, tal como é abordado mais tarde, na secção "[diagnosticar problemas de armazenamento]."

Os gráficos na figura 3 abaixo ilustram como a calcular a média que ocorre para comunicação métricas pode ocultar picos numa atividade. As preço por hora métricas são apresentados mostrar uma constante taxa de pedidos de, enquanto o minuto métricas revelarem as variações que estão a demorar muito local.

![][3]

O resto desta secção descreve o que métricas deverá monitorizar e por que motivo.

### <a name="monitoring-service-health"></a>Monitorizar o estado de funcionamento do serviço

Pode utilizar o [Portal clássica Azure](https://manage.windowsazure.com) para ver o estado de funcionamento do serviço de armazenamento (e outros serviços do Azure) em todas as regiões Azure em todo o mundo. Permite-lhe ver imediatamente se um problema fora da sua controlo que afetam o serviço de armazenamento na região que utilizar para a sua aplicação.

Também pode fornecer Portal clássica do Azure com as notificações de incidentes que afetam os vários serviços Azure.
Nota: Esta informação não estava anteriormente disponível, juntamente com dados de histórico, no Dashboard de serviço do Azure na <a href="http://status.azure.com" target="_blank">http://status.azure.com</a>.

Enquanto Portal clássica do Azure recolhe informações sobre o estado de funcionamento de dentro os Azure centros de dados (interior-out monitorização), também poderá considerar aprovar uma abordagem de exterior para gerar transações síntese que periodicamente aceder à sua aplicação web alojado no Azure a partir de várias localizações. Os serviços oferecidos pelo <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">pontos-chave</a>, <a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a>e informações de aplicação para serviços de equipa do Visual Studio são exemplos desta abordagem exterior-in. Para mais informações sobre as informações de aplicação para o Visual Studio Team Services, consulte o artigo o anexo "[anexo 5: monitorizar com informações de aplicação para serviços de equipa do Visual Studio]."

### <a name="monitoring-capacity"></a>Capacidade de monitorização

Métricas de armazenamento apenas armazena métricas de capacidade para o serviço de BLOBs porque blobs normalmente de conta para a maior proporção dos dados armazenados (no momento da escrita, não é possível utilizar métricas de armazenamento para monitorizar a capacidade das suas tabelas e filas). Pode encontrar estes dados na tabela **$MetricsCapacityBlob** se ativou o monitorização do serviço de Blobs. Métricas de armazenamento regista estes dados de uma vez por dia, e pode utilizar o valor da **RowKey** para determinar se a linha contiver uma entidade que está relacionada com a dados de utilizador (valor **dados**) ou os dados de análise ( **análise**do valor). Cada entidade armazenada contém informações sobre a quantidade de armazenamento utilizado (**capacidade** medida em bytes) e o número atual de contentores (**ContainerCount**) e blobs (**ObjectCount**) utilizado na conta de armazenamento. Para mais informações sobre as métricas de capacidade armazenadas na tabela **$MetricsCapacityBlob** , consulte o <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Esquema de tabela de métricas de análise de armazenamento</a> no MSDN.

> [AZURE.NOTE] Deve monitorizar estes valores para um alerta que está a atingir a capacidade da sua conta de armazenamento. No Portal clássica do Azure, na página **Monitor** para a sua conta de armazenamento, pode adicionar regras de alertas a notificá-lo se a utilização de armazenamento de agregação excede ou desce abaixo limiares que especificar.

Para obter ajuda a calcular o tamanho de vários objetos de armazenamento como blobs, consulte o blogue <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">Noções sobre Azure armazenamento faturação – largura de banda, as transações e capacidade</a>.

### <a name="monitoring-availability"></a>Disponibilidade de monitorização

Deverá monitorizar a disponibilidade dos serviços de armazenamento na sua conta de armazenamento através da monitorização do valor na coluna **disponibilidade** as tabelas de hora a hora ou minuto métricas — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. A coluna de **disponibilidade** contém um valor de percentagem que indica a disponibilidade do serviço ou a operação de API representada pela linha (a **RowKey** mostra se a linha contiver métricas serviço como um todo ou para uma operação de API específica).

Qualquer valor inferior a 100% indica que estão a falhar alguns pedidos de armazenamento. Pode ver por que motivo está a falhar ao examinar as outras colunas dos dados de métricas que mostram os números de pedidos de tipos de erro diferente, tais como **ServerTimeoutError**. Devo esperar ver a **disponibilidade** de se situar temporariamente abaixo de 100% por razões como tempos limite do servidor breves enquanto o serviço move-se a partições a melhor pedido de balanceamento de carga; a lógica de repetição na sua aplicação de cliente deve processar essas intermitentes condições. A página <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> lista os tipos da transação que inclui métricas de armazenamento no seu cálculo **disponibilidade** .

No Portal clássica do Azure, na página **Monitor** para a sua conta de armazenamento, pode adicionar regras de alertas para notificá-lo se **disponibilidade** para um serviço de se situar abaixo de um determinado limiar que especificar.

A secção "[orientações de resolução de problemas]" deste guia descreve alguns problemas comuns do serviço de armazenamento relacionadas com a disponibilidade.

### <a name="monitoring-performance"></a>Monitorizar o desempenho

Para monitorizar o desempenho dos serviços de armazenamento, pode utilizar as seguintes métricas das tabelas métricas horários e minuto.

- Os valores na **AverageE2ELatency** e **AverageServerLatency** mostram o tempo médio o serviço de armazenamento ou tipo de operação API está a demorar a processar pedidos. **AverageE2ELatency** é uma medida do fim para fim latência que inclui o tempo despendido para ler o pedido e enviar a resposta para além do tempo despendido para processar o pedido (, por conseguinte, a inclui latência da rede assim que o pedido de atinge o serviço de armazenamento); **AverageServerLatency** é uma medida da apenas o tempo de processamento e exclui, portanto, qualquer latência da rede relacionados com a comunicar com o cliente. Consulte a secção "[métricas mostram AverageE2ELatency alta e baixa AverageServerLatency]" mais tarde neste guia para um debate por que razão poderá haver uma diferença significativa entre estes dois valores.
- Os valores nas colunas **TotalIngress** e **TotalEgress** mostram a quantidade total de dados, em bytes, chegar ao e aceder fora do seu serviço de armazenamento ou através de um tipo de operação API específico.
- Os valores na coluna **TotalRequests** mostram o número total de pedidos de que o serviço de armazenamento de operação de API está a receber. **TotalRequests** é o número total de pedidos de que recebe o serviço de armazenamento.

Normalmente, irá monitorizar para inesperados alterações em qualquer um destes valores como um indicador de que tem um problema de que necessita de inquérito.

No Portal clássica do Azure, na página **Monitor** para a sua conta de armazenamento, pode adicionar regras de alertas a notificá-lo se qualquer um das métricas de desempenho para este serviço de se situar abaixo ou exceder um determinado limiar que especificar.

A secção "[orientações de resolução de problemas]" deste guia descreve alguns problemas comuns do serviço de armazenamento relacionados com o desempenho.


## <a name="diagnosing-storage-issues"></a>Diagnosticar problemas de armazenamento

Existem várias formas que poderá torna-se em mente um problema de problema ou na sua aplicação, estes incluem:

- Uma falha principal que faz com que a aplicação para uma falha de sistema ou deixarão de funcionar.
- Alterações significativas a partir de valores do plano base na métricas que está a monitorizar, tal como descrito na secção anterior "[monitorizar o seu serviço de armazenamento]."
- Relatórios de utilizadores da sua aplicação que algumas operação em particular não concluir como esperado ou que algumas funcionalidade não está a funcionar.
- Os erros gerados dentro da aplicação que aparecem nos ficheiros de registo ou através de qualquer outro método de notificação.

Normalmente, problemas relacionados com os serviços de armazenamento Azure abrange uma das quatro vastas categorias:

- A aplicação tem um problema de desempenho, quer comunicado os seus utilizadores, quer reveladas pela alterações nas métricas de desempenho.
- Existe um problema com a infraestrutura de armazenamento do Windows Azure em regiões de uma ou mais.
- A aplicação está a encontrar um erro, quer comunicado os seus utilizadores, quer reveladas por um aumento de uma das métricas de contagem de erro que monitorizar.
- Durante a desenvolvimento e teste, pode de estar a utilizar o emulador armazenamento local; poderá encontrar alguns problemas que se relacionam com especificamente para a utilização da emulador de armazenamento.

As secções seguintes destacam os passos que deve seguir para diagnosticar e resolução de problemas em cada uma das seguintes quatro categorias. A secção "[orientações de resolução de problemas]" mais adiante neste guia fornece mais detalhes para alguns problemas comuns que poderá encontrar.

### <a name="service-health-issues"></a>Problemas de estado de funcionamento de serviço

Problemas de estado de funcionamento do serviço são normalmente fora da sua controlo. Portal clássica do Azure fornece informações sobre problemas em curso, com os serviços Azure, incluindo os serviços de armazenamento. Caso tenha optado por armazenamento Geo redundantes acesso de leitura quando criou a sua conta de armazenamento, em seguida, em caso dos seus dados não estar disponível na localização principal, a aplicação foi mudar temporariamente para a cópia só de leitura na localização secundária. Para fazer isto, a aplicação tem de conseguir mudar entre as localizações de armazenamento principais e secundários de utilizar e consigam trabalhar em modo de funcionalidade reduzida com dados só de leitura. As bibliotecas do cliente de armazenamento do Azure permitem-lhe definir uma política de repetir que pode ler a partir do armazenamento secundário, no caso de falha de uma operação de leitura a partir do armazenamento principal. A aplicação também tem de estar tenha em atenção que os dados na localização secundário eventualmente consistentes. Para obter mais informações, consulte a mensagem de <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">Opções de redundância de armazenamento do Azure e acesso de leitura Geo redundantes armazenamento</a>do blogue.

### <a name="performance-issues"></a>Problemas de desempenho

O desempenho de uma aplicação do pode ser subjectivo, especialmente de uma perspetiva de utilizador. Por conseguinte, é importante ter métricas do plano base disponíveis para o ajudar a identificar onde poderá haver um problema de desempenho. Vários fatores poderão afetar o desempenho de um serviço de armazenamento Azure da perspetiva de aplicação de cliente. Seguintes fatores poderão funcionar no serviço de armazenamento, no cliente do ou a infraestrutura de rede Por conseguinte, é importante ter uma estratégia de identificar a origem do problema de desempenho.

Depois de ter identificado a localização da causa do problema de desempenho de métricas provável que, em seguida, pode utilizar os ficheiros de registo para encontrar informações detalhadas para diagnosticar e resolver o problema ainda mais.

A secção "[orientações de resolução de problemas]" mais adiante neste guia fornece mais informações sobre alguns desempenho comuns relacionados com problemas poderá encontrar.

### <a name="diagnosing-errors"></a>Diagnosticar erros

Os utilizadores da sua aplicação poderão notificá-lo de erros comunicados pela aplicação cliente. Métricas de armazenamento também regista as contagens de tipos de erro diferente a partir do seu serviços de armazenamento como **NetworkError**, **ClientTimeoutError**ou **AuthorizationError**. Enquanto métricas de armazenamento registos apenas contagens de tipos de erro diferente, pode obter mais detalhes acerca dos pedidos de individuais através da análise do lado do servidor, lado do cliente e registos de rede. Normalmente, o código de estado HTTP devolvido pelo serviço de armazenamento irá dar-indicação da porque é que o pedido falhou.

> [AZURE.NOTE] Lembre-se de que deve esperar ver alguns erros intermitentes: por exemplo, os erros devido a condições da rede breves ou os erros de aplicação.

Os seguintes recursos no MSDN são úteis para Noções sobre códigos de erro e estado relacionados com armazenamento:

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Códigos de erro API REST comuns</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Códigos de erro do serviço de BLOBs</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">Códigos de erro do serviço de fila de espera</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">Códigos de erro do serviço de tabela</a>

### <a name="storage-emulator-issues"></a>Problemas de emulador de armazenamento

O SDK do Azure inclui um emulador de armazenamento que pode ser executados numa estação de trabalho de desenvolvimento. Este emulador simula a maior parte do comportamento dos serviços de armazenamento Azure e é útil durante desenvolvimento e teste, permitindo-lhe executar as aplicações que utilizam os serviços de armazenamento Azure sem necessidade de uma subscrição do Azure e uma conta de armazenamento Azure.

A secção "[orientações de resolução de problemas]" deste guia descreve alguns problemas comuns encontrados utilizando o emulador de armazenamento.

### <a name="storage-logging-tools"></a>Ferramentas de registo de armazenamento

Registo de armazenamento fornece registo do lado do servidor de pedidos de armazenamento na sua conta de armazenamento Azure. Para mais informações sobre como ativar o registo do lado do servidor e acesso os dados do registo, consulte o artigo <a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">registo do lado do servidor utilizando</a> no MSDN.

A biblioteca de cliente de armazenamento para .NET permite-lhe recolher dados de registo do lado do cliente que está relacionada com a operações de armazenamento efetuadas pela aplicação. Para mais informações sobre como ativar o registo do lado do cliente e acesso os dados do registo, consulte o artigo <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">utilizar a biblioteca de cliente do armazenamento de registo do lado do cliente</a> no MSDN.

> [AZURE.NOTE] Em determinadas circunstâncias (por exemplo, falhas de autorização de SA), um utilizador poderá reportar um erro para os quais pode encontrar sem dados pedido nos registos de armazenamento do lado do servidor. Pode utilizar as capacidades de registo da biblioteca de cliente de armazenamento para investigar se está a causar o problema no cliente ou utilizar ferramentas de monitorização de rede para investigar a rede.

### <a name="using-network-logging-tools"></a>Utilizar ferramentas de registo de rede

Pode capturar o tráfego entre o cliente e servidor para fornecer informações detalhadas acerca dos dados que trocam de cliente e no servidor e as condições da rede subjacente. Ferramentas de registo de rede úteis incluem:

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) é uma web gratuita depuração proxy permite-lhe examinar os cabeçalhos e os dados de carga útil das mensagens de pedidos e respostas HTTP e HTTPS. Para obter mais informações, consulte o artigo "[anexo 1: utilizar Fiddler para capturar tráfego HTTP e HTTPS]".
- Microsoft Network Monitor (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/download/details.aspx?id=4865</a>) e Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) são analisadores de protocolos de rede gratuito permitem-lhe ver informações detalhadas de pacote para uma vasta gama de protocolos de rede. Para mais informações sobre Wireshark, consulte o artigo "[anexo 2: utilizar Wireshark para capturar tráfego de rede]".
- Microsoft mensagem Analyzer é uma ferramenta da Microsoft que substitui o Netmon e que além capturar dados de pacotes de rede, ajuda-o para ver e analisar os dados do registo capturados a partir de outras ferramentas. Para obter mais informações, consulte o artigo "[anexo 3: utilizar o Microsoft mensagem Analyzer para capturar tráfego de rede]".
- Se pretender executar um teste de conectividade básica para verificar que o computador cliente pode ligar-se ao serviço de armazenamento Azure através da rede, não é possível fazer isto utilizando a ferramenta de padrão **ping** no cliente. No entanto, pode utilizar a ferramenta de **tcping** para verificar conectividade. **Tcping** está disponível para transferência nas <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>.

Em muitos casos, os dados do registo a partir do registo de armazenamento e a biblioteca do cliente de armazenamento irão ser suficientes para diagnosticar um problema, mas em alguns cenários, poderá ter as informações mais detalhadas, que podem fornecer estas ferramentas de registo de rede. Por exemplo, utilizar Fiddler para ver mensagens HTTP e HTTPS permite-lhe ver dados de cabeçalho e carga útil enviados para e dos serviços de armazenamento, que seriam permitem-lhe examinar como uma aplicação de cliente repetições de operações de armazenamento. Trabalhar com analisadores de protocolo como Wireshark ao nível do pacote, permitindo-lhe ver dados TCP, que seriam permitem-lhe resolver problemas de conectividade e de perda de pacotes. Mensagem Analyzer pode operar a camadas HTTP e TCP.

## <a name="end-to-end-tracing"></a>Rastreio ponto a ponto

Rastreio de ponto a ponto utilizando uma variedade de ficheiros de registo é uma técnica útil para investigar possíveis problemas. Pode utilizar as informações de data/hora dos seus dados de métricas como indicação de onde pretende começar a procurar nos ficheiros de registo para as informações detalhadas que irá ajudá-lo a resolver o problema.

### <a name="correlating-log-data"></a>Dados do registo correlação

Quando ver registos de início de aplicações de cliente, rede controla e os pedidos de registo do lado do servidor de armazenamento é fundamental para conseguir correlacionar ao longo de ficheiros de registo diferente. Os ficheiros de registo incluem um número de diferentes campos que são úteis como identificadores de correlação. O id de pedido de cliente é o campo mais úteis para utilizar para correlacionar entradas nos registos do diferentes. No entanto, por vezes, pode ser útil utilizar o id do servidor de pedido ou selos de tempo. As secções seguintes fornecem mais detalhes sobre estas opções.

### <a name="client-request-id"></a>ID de pedido de cliente

A biblioteca do cliente armazenamento gera automaticamente um id de pedido de cliente exclusivo para cada pedido.

- No registo do lado do cliente que cria a biblioteca de cliente do armazenamento, o id de cliente do pedido é apresentado no campo de **ID do pedido de cliente** de cada entrada de registo relativo ao pedido.
- Num rastreio de rede como um capturado por Fiddler, o id de pedido de cliente é visível nas mensagens de pedido como o valor de cabeçalho **x-ms-cliente-pedido-id** HTTP.
- No registo de armazenamento de registo do lado do servidor, o id de cliente do pedido é apresentado na coluna de ID do pedido do cliente.

> [AZURE.NOTE]É possível para vários pedidos para partilhar o mesmo id de pedido de cliente, porque o cliente pode atribuir este valor (apesar da biblioteca do cliente armazenamento atribui automaticamente um novo valor). No caso de tentativas a partir do cliente, todas as tentativas de partilham o mesmo id de pedido de cliente. No caso de um lote de enviados a partir do cliente, o lote tem um id de pedido de cliente único.


### <a name="server-request-id"></a>ID de pedido de servidor

O serviço de armazenamento gera automaticamente os ids de pedido de servidor.

- No registo de armazenamento de registo do lado do servidor, o id do pedido de servidor, é apresentada a coluna **ID de pedido de cabeçalho** .
- Num rastreio de rede como um capturado pelo Fiddler, o id do pedido de servidor é apresentada nas mensagens de resposta como o valor de cabeçalho **x-ms-pedido-id** HTTP.
- No registo do lado do cliente que cria a biblioteca de cliente do armazenamento, o id do pedido de servidor é apresentado na coluna **Operação de texto** para a entrada do registo a apresentar detalhes da resposta do servidor.

> [AZURE.NOTE] O serviço de armazenamento sempre atribui um servidor exclusivo pedido id a todos os pedidos recebe, para que cada tentativa de repetir a partir do cliente e cada operação incluído num lote de tem um id de pedido de servidor exclusivo.

Se a biblioteca do cliente armazenamento inicia uma **StorageException** no cliente, a propriedade **RequestInformation** contém um objeto de **RequestResult** que inclui uma propriedade **ServiceRequestID** . Também pode aceder a um objeto de **RequestResult** a partir de uma instância de **OperationContext** .

O código de exemplo abaixo demonstra como definir um valor de **ClientRequestId** personalizado ao como anexar um objeto de **OperationContext** o pedido para o serviço de armazenamento. Também mostra como obter o valor de **ServerRequestId** a partir da mensagem de resposta.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Selos de tempo

Também pode utilizar selos de tempo para localizar entradas de registo relacionados, mas tenha o cuidado de qualquer distorção de relógio entre o cliente e servidor que possam existir. Deve procurar mais ou menos 15 minutos para as entradas de lado do servidor com base no carimbo de data/hora no cliente correspondentes. Lembre-se de que os metadados blob para blobs que contém métricas indicam o intervalo de tempo para métricas armazenadas no blob; Isto é útil se tiver muitos blobs de métricas para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a>Resolução de problemas de orientação

Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de alguns dos problemas comuns da aplicação poderá encontrar ao utilizar os serviços de armazenamento Azure. Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

**Resolução de problemas de árvore de decisões**

----------

O problema se relacionam com de desempenho de um dos serviços de armazenamento?

- [Métricas mostram AverageE2ELatency alta e AverageServerLatency baixa]
- [Métricas mostram AverageE2ELatency baixa e AverageServerLatency baixa, mas o cliente está a experimentar latência alta]
- [Métricas mostram AverageServerLatency alta]
- [Ocorreu inesperados atrasos na entrega de mensagens numa fila]

----------

O problema se relacionam com a disponibilidade de um dos serviços de armazenamento?

- [Métricas de mostram um aumento no PercentThrottlingError]
- [Métricas de mostram um aumento no PercentTimeoutError]
- [Métricas de mostram um aumento no PercentNetworkError]

----------

Está a sua aplicação cliente receber uma resposta de 4XX (como 404) HTTP a partir de um serviço de armazenamento?

- [O cliente está a receber mensagens de HTTP 403 (proibido)]
- [O cliente está a receber mensagens de HTTP 404 (não encontrado)]
- [O cliente está a receber mensagens de HTTP 409 (conflito)]

----------

[Métricas mostram PercentSuccess baixa ou entradas de registo de analytics tem operações com o estado da transação da ClientOtherErrors]

----------

[Métricas de capacidade mostram um aumento inesperado na utilização de capacidade de armazenamento]

----------

[Ocorreu for reiniciado inesperado de máquinas virtuais que tenham um grande número de VHDs anexados]

----------

[Surge o problema de utilizar o emulador de armazenamento para desenvolvimento ou de teste]

----------

[Encontrar problemas ao instalar o SDK do Azure para .NET]

----------

[Tem um problema diferente com um serviço de armazenamento]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostram AverageE2ELatency alta e AverageServerLatency baixa

A bBaixa ilustração da ferramenta de monitorização de Portal clássica Azure mostra um exemplo onde o **AverageE2ELatency** é significativamente mais elevados que os **AverageServerLatency**.

![][4]

Repare que o serviço de armazenamento apenas calcula a métrica **AverageE2ELatency** para pedidos com êxito e, ao contrário **AverageServerLatency**, inclui o tempo que demora cliente para os dados de enviar e receber a confirmação do serviço de armazenamento. Por conseguinte, pode ser uma diferença entre **AverageE2ELatency** e **AverageServerLatency** devido a aplicação de cliente que está a ser lenta responder ou devido a condições da rede.

> [AZURE.NOTE] Também pode ver **E2ELatency** e **ServerLatency** para operações de armazenamento individuais nos dados do registo de registo de armazenamento.

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de desempenho do cliente

Motivos possíveis para o cliente responder lentamente incluem ter um número limitado de ligações disponíveis ou threads. Poderá conseguir resolver o problema ao modificar o código do cliente para ser mais eficiente (por exemplo utilizando assíncronas chamadas para o serviço de armazenamento), ou ao utilizar uma Máquina Virtual maior (com mais tarolos e mais memória).

Para os serviços de tabela e fila de espera, o algoritmo de Nagle também pode causar alta **AverageE2ELatency** em comparação com **AverageServerLatency**: para obter mais informações consulte o artigo da mensagem <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">algoritmo do Nagle é não amigável no sentido pedidos pequenas</a> o blogue da equipa do Microsoft Azure armazenamento. Pode desativar o algoritmo de Nagle no código utilizando a classe de **ServicePointManager** no espaço de nomes **System.Net** . Deve fazê-lo antes de efetuar todas as chamadas para a tabela ou serviços de fila de espera na aplicação de uma vez que não afeta ligações que já estejam a abrir. O exemplo seguinte provém o método de **Application_Start** numa função de trabalho.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Deve verificar os registos do lado do cliente para ver quantas solicitações é submeter a sua aplicação cliente e verificar existência de geral .NET relacionados congestionamentos de desempenho no seu cliente como CPU, .NET recolha de lixo, de utilização da rede ou memória (como ponto de partida para aplicações de cliente .NET de resolução de problemas, consulte o artigo <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">depuração, rastreio e criação de perfis</a> no MSDN).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latência da rede

Normalmente, latência alta de ponto a ponto causada pela rede é devido à breves condições. Pode investigar ambos os problemas de rede persistentes e breves como pacotes ignorados utilizando ferramentas como Wireshark ou Microsoft mensagem Analyzer.

Para obter mais informações sobre como utilizar Wireshark para resolver problemas de rede, consulte o artigo "[anexo 2: utilizar Wireshark para capturar tráfego de rede]."

Para obter mais informações sobre como utilizar o Microsoft mensagem Analyzer para resolver problemas de rede, consulte o artigo "[anexo 3: utilizar o analisador de Microsoft Message para capturar tráfego de rede]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostram AverageE2ELatency baixa e AverageServerLatency baixa, mas o cliente está a experimentar latência alta

Neste cenário, a causa mais provável é um atraso nos pedidos de armazenamento que o serviço de armazenamento. Deverá investigar por que motivo pedidos do cliente são não se tornou através do serviço blob.

Motivos possíveis para o cliente atrasar enviar pedidos de incluem ter um número limitado de ligações disponíveis ou threads. Também deve verificar se o cliente está a executar várias tentativas e investigar a razão se este for o caso. Que pode fazer através de programação está à procura no objeto **OperationContext** associado ao pedido e obter o valor de **ServerRequestId** . Para obter mais informações, consulte o artigo o código de exemplo na secção "[ID do servidor de pedido]".

Se não existem problemas no cliente, deverá investigar possíveis problemas de rede, tal como a perda de pacotes. Pode utilizar ferramentas como Wireshark ou Microsoft mensagem Analyzer para investigar problemas de rede.

Para obter mais informações sobre como utilizar Wireshark para resolver problemas de rede, consulte o artigo "[anexo 2: utilizar Wireshark para capturar tráfego de rede]."

Para obter mais informações sobre como utilizar o Microsoft mensagem Analyzer para resolver problemas de rede, consulte o artigo "[anexo 3: utilizar o analisador de Microsoft Message para capturar tráfego de rede]."

### <a name="metrics-show-high-AverageServerLatency"></a>Métricas mostram AverageServerLatency alta

No caso de alta **AverageServerLatency** para pedidos de transferência de BLOBs, deve utilizar os registos de registo de armazenamento para ver se existem repetidos pedidos de Blobs do mesmo (ou conjunto de blobs). Para blob carregar pedidos, deverá investigar que Bloquear tamanho o cliente está a utilizar (por exemplo, bloqueia menos de 64 mil de tamanho pode resultar em transparências, a menos que o lê está também em menos de 64K blocos), e se vários clientes são carregar blocos para o mesmo blob em paralelo. Também deve verificar as métricas por minuto picos o número de pedidos de que resultam excedem o por segundo destinos de escalabilidade: também ver "[métricas de mostram um aumento no PercentTimeoutError]."

Se estiver a ver alta **AverageServerLatency** para blob transferir pedidos quando existem pedidos repetidos a mesma blob ou conjunto de blobs, deve considerar colocação em cache estes blobs utilizando Azure Cache ou a rede Azure entrega conteúdo (CDN). Para pedidos de carregamento, pode melhorar o débito utilizando um tamanho de bloco maior. Para consultas a tabelas, também é possível implementar o lado do cliente colocação em cache clientes que efetuar as mesmas operações de consulta e onde os dados não alterados com frequência.

Alto **AverageServerLatency** valores podem também ser um sintoma de concebida tabelas ou consultas que origina operações de pesquisa ou que siga o anti-padrão de / anexar como prefixo acrescentar. Para mais informações, consulte "[métricas de mostram um aumento no PercentThrottlingError]".

> [AZURE.NOTE] Pode encontrar uma lista de verificação exaustiva desempenho lista de verificação aqui: [Microsoft Azure armazenamento desempenho e escalabilidade lista de verificação](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Ocorreu inesperados atrasos na entrega de mensagens numa fila

Se estiver a ter um atraso entre o tempo de que uma aplicação adiciona uma mensagem para uma fila e a hora torna-se disponível para ler a partir de fila de espera, em seguida, deverá siga os passos seguintes para diagnosticar o problema:

- Certifique-se de que a aplicação com êxito é adicionar as mensagens a fila de espera. Verifique que a aplicação é não repetir o método de **AddMessage** várias vezes antes que se seguem. Os registos de biblioteca de armazenamento do cliente irão apresentar qualquer repetidas tentativas de operações de armazenamento.
- Certifique-se de que não haja nenhuma relógio distorção entre a função de trabalho que adiciona a mensagem para a fila e a função de trabalho que leia a mensagem da fila de que o torna aparecer como se existe um atraso de processamento.
- Verifique se está a falhar a função de trabalho que lê as mensagens a partir de fila de espera. Se um cliente de fila chama o método de **GetMessage** , mas deixar de responder com uma confirmação dos, a mensagem permanecem invisível na fila de espera até que o período de **invisibilityTimeout** expirar. Neste momento, a mensagem fica disponível para processamento novamente.
- Verifique se o comprimento da fila está em crescimento ao longo do tempo. Isto pode ocorrer se não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros trabalhadores estão colocar na fila de espera. Também deve verificar métricas para ver se pedidos de eliminação são falhar e a contagem de fila em mensagens, que podem indicar repetidos tentativas falhadas para eliminar a mensagem.
- Examine os registos de registo de armazenamento para quaisquer operações de fila de espera que tenham superiores aos valores **E2ELatency** e **ServerLatency** esperados após um longo período de tempo que o habitual.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Métricas de mostram um aumento no PercentThrottlingError

Optimização erros ocorrem quando exceder os destinos escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isto para se certificar de que nenhuma único cliente ou inquilino pode utilizar o serviço cargo outras pessoas. Para mais informações, consulte o artigo <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">escalabilidade de armazenamento do Azure e desempenho destinos</a> para obter detalhes sobre destinos escalabilidade para contas de armazenamento e metas de desempenho para que as partições no prazo de contas de armazenamento.

Se a métrica do **PercentThrottlingError** mostrar um aumento da percentagem de pedidos de que está a falhar com um erro de optimização, é necessário investigar um dos dois cenários:

- [Aumento breves PercentThrottlingError]
- [Aumentar permanente PercentThrottlingError erro]

Um aumento no **PercentThrottlingError** ocorre frequentemente em simultâneo como um aumento no número de pedidos de armazenamento ou quando começam por ser carregado testes da aplicação. Isto poderá também manifestar-se no cliente do como "503 servidor ocupado" ou mensagens de estado "500 tempo limite da operação" HTTP de operações de armazenamento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento breves PercentThrottlingError

Se estiver a ver picos o valor de **PercentThrottlingError** que coincidam com períodos de alta actividade para a aplicação, deve implementar um anterior (não linear) exponencial desativar estratégia para o número de tentativas no seu cliente: Isto irá reduza a carga de imediata na partição e ajudar a sua aplicação para lisa saída períodos de tráfego elevado. Para mais informações sobre como implementar políticas de repetição utilizando a biblioteca de cliente do armazenamento, consulte o artigo <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Microsoft.WindowsAzure.Storage.RetryPolicies espaço de nomes</a> no MSDN.

> [AZURE.NOTE] Também poderá ver picos o valor de **PercentThrottlingError** que não coincidir com períodos de alta actividade para a aplicação: aqui a causa mais provável é o serviço de armazenamento mover a partições para melhorar a balanceamento de carga.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumentar permanente PercentThrottlingError erro

Se estiver a ver um valor alto de forma consistente para **PercentThrottlingError** a seguir um aumento permanente na sua volumes da transação ou quando estiver a executar o carregamento inicial testa na sua aplicação, em seguida, é necessário avaliar como a aplicação está a utilizar a partições de armazenamento e se mesmo se está a aproximar os destinos escalabilidade para uma conta de armazenamento. Por exemplo, se estiver a ver limitação erros numa fila (que conta como uma única partição), em seguida, deve utilizar filas adicionais para distribuir a conclusão das transações por várias partições. Se estiver a ver limitação erros numa tabela, terá de considere utilizar um esquema de partições diferentes para distribuir as suas transações por várias partições utilizando uma vasta gama de valores chave partição. Uma das causas comuns deste problema é o prepend/acrescentar anti-padrão onde, selecione a data como chave partição e, em seguida, todos os dados num dia em particular é escritos uma partição de: em caso de carga, isto pode resultar num estrangulamento escrita. Deve ter em consideração uma estrutura de criação de partições diferente ou avaliar se utilizar o armazenamento de BLOBs poderá ser uma melhor solução. Também deve verificar se a limitação está a ocorrer estatístico como resultado da picos no seu tráfego e investigar formas de suavização seu padrão de pedidos de.

Se distribuir as suas transações em múltiplas partições, tem ainda tenha em atenção os limites de escalabilidade definida para a conta de armazenamento. Por exemplo, se tiver utilizado dez filas cada o número máximo de 2000 mensagens 1KB por segundo processamento, será no limite de 20.000 mensagens por segundo para a conta de armazenamento geral. Se precisar de mais de 20.000 entidades por segundo do processo, deverá tomar em consideração de através de várias contas de armazenamento. Também devem conter Lembre-se de que o tamanho dos seus pedidos e entidades tem um impacto na quando o serviço de armazenamento acelera os seus clientes: Se tiver maior pedidos e entidades, poderá ser mais cedo limitada.

Estrutura da consulta ineficaz também pode causar visitas os limites da escalabilidade da partições de tabela. Por exemplo, uma consulta com um filtro que seleciona apenas uma percentagem das entidades numa partição mas que analisa todas as entidades uma partição terá de aceder a cada entidade. Cada entidade de leitura será contabilizados número total de transações nessa partição; Por conseguinte, pode facilmente alcançar os destinos escalabilidade.

> [AZURE.NOTE] Testes de desempenho devem revelar qualquer estruturas de consulta ineficaz na sua aplicação.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Métricas de mostram um aumento no PercentTimeoutError

As métricas mostram um aumento no **PercentTimeoutError** para um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um grande volume de mensagens de estado "500 tempo limite da operação" HTTP de operações de armazenamento.

> [AZURE.NOTE] Poderá ver erros de tempo excedido temporariamente como o serviço de armazenamento carregar saldos pedidos ao mover uma partição para um novo servidor.

A métrica do **PercentTimeoutError** é uma agregação das seguintes métricas: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**e **SASServerTimeoutError**.

Os tempos de limite do servidor são causados por um erro no servidor. Os tempos limite cliente ocorrem porque uma operação no servidor tiver excedido o limite de tempo especificado pelo cliente; Por exemplo, um cliente utilizando a biblioteca do cliente armazenamento pode definir um tempo limite de uma operação de utilizando a propriedade **ServerTimeout** a classe de **QueueRequestOptions** de.

Tempos limite do servidor indica um problema com o serviço de armazenamento que requer o inquérito. Pode utilizar métricas para ver se está a atingir os limites de escalabilidade do serviço e para identificar qualquer picos no tráfego que estão a causar este problema. Se o problema for intermitente, pode ser devido ao balanceamento de carga atividade no serviço. Se o problema é persistente e não é causado pela aplicação atingir os limites de escalabilidade do serviço, deverá aumentar um problema de suporte. Tempos limite de cliente, tem de decidir se o tempo limite está definido para um valor adequado no cliente e quer alterar o valor de limite de tempo definido no cliente do ou investigar como pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo por otimizar as suas consultas de tabela ou reduzir o tamanho das suas mensagens.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Métricas de mostram um aumento no PercentNetworkError

As métricas mostram um aumento no **PercentNetworkError** para um dos seus serviços de armazenamento. A métrica do **PercentNetworkError** é uma agregação das seguintes métricas: **NetworkError**, **AnonymousNetworkError**e **SASNetworkError**. Estas ocorrem quando o serviço de armazenamento Deteta um erro de rede, quando o cliente faz com que um pedido de armazenamento.

A causa mais comuns deste erro é um cliente desligar antes de um limite de tempo expira dentro do serviço de armazenamento. Deverá investigar o código no seu cliente para compreender por que motivo e quando o cliente desliga do serviço de armazenamento. Também pode utilizar Wireshark, Microsoft mensagem Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. Estas ferramentas são descritas nos [anexos].

### <a name="the-client-is-receiving-403-messages"></a>O cliente está a receber mensagens de HTTP 403 (proibido)

Se a sua aplicação cliente é deitar erros de HTTP 403 (proibido), uma causa provável é que o cliente está a utilizar um expirados partilhado acesso assinatura (SA) quando envia um pedido de armazenamento (embora outras possíveis causas incluem o relógio distorção, inválido teclas e cabeçalhos vazios). Se uma tecla SA expirada a causa, não verá quaisquer entradas dos dados de registo de armazenamento registo do lado do servidor. A tabela seguinte mostra um exemplo a partir do registo do lado do cliente gerado pela biblioteca do cliente de armazenamento que ilustra a este problema ocorrer:

Origem|Verbosidade|Verbosidade|Id de pedido de cliente|Texto de operação
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Iniciar a operação com a localização principal modo localização PrimaryOnly por.
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Iniciar o pedido síncrono para https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;assinatura = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;versão api = 2014-02-14.
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Aguardar resposta.
Microsoft.WindowsAzure.Storage|Aviso|2|85d077ab-...|Excepção enquanto a aguardar resposta: O servidor remoto devolvido um erro: (403) proibido...
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Foi recebida uma resposta. Código de estado = 403, pedir ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, conteúdo MD5 = ETag =.
Microsoft.WindowsAzure.Storage|Aviso|2|85d077ab-...|Excepção durante a operação: O servidor remoto devolvido um erro: (403) proibido...
Microsoft.WindowsAzure.Storage|Informações|3 |85d077ab-...|Verificar se a operação deve ser repetida. Contagem de repetições = 0, o código de estado HTTP = 403, exceção = o servidor remoto devolvido um erro: (403) proibido...
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|A seguinte localização foi definida para primária, com base no modo de localização.
Microsoft.WindowsAzure.Storage|Erro|1|85d077ab-...|Política de repetir não permitia para uma nova tentativa. Falhar com o servidor remoto devolvido um erro: proibido (403).

Neste cenário, deverá investigar por que motivo o token de SA estiver prestes a expirar antes do cliente envia o token para o servidor:

- Normalmente, deverá não definir uma hora de início quando cria uma SA para um cliente utilizar imediatamente. Se existirem pequeno relógio as diferenças entre o anfitrião gerar as associações de segurança com a hora atual e o serviço de armazenamento, em seguida, é possível para o serviço de armazenamento para receber uma SA que não não válida.
- Não deve definir uma hora de expiração muito curto numa SA. Novamente, pequeno relógio as diferenças entre o anfitrião gerar as associações de segurança e o serviço de armazenamento podem levar a uma SA aparentemente expirar anterior que o esperado.
- É que o parâmetro de versão na chave SA (por exemplo **sv = 2012-02-12**) corresponder à versão da biblioteca de armazenamento do cliente que está a utilizar. Deve utilizar sempre a versão mais recente da biblioteca de cliente de armazenamento. Para mais informações sobre o controlo de versões token SA, consulte o artigo [o que é novo para o armazenamento do Windows Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx).
- 
- Se voltar a gerar as teclas de acesso de armazenamento (clique em **Gerir as teclas de acesso** em qualquer página na sua conta de armazenamento no Portal clássica do Azure) Isto pode invalidar qualquer tokens SA existentes. Isto pode ser um problema se gerar tokens SA com um tempo de expiração longo para a cache de aplicações de cliente.

Se estiver a utilizar a biblioteca do cliente de armazenamento para gerar tokens SA, em seguida, é fácil criar um token válido. No entanto, se estiver a utilizar o armazenamento REST API e construir desenhada à mão os tokens SA cuidadosamente deverá ler o tópico <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">Delegar acesso com uma assinatura de acesso partilhado</a> no MSDN.

### <a name="the-client-is-receiving-404-messages"></a>O cliente está a receber mensagens de HTTP 404 (não encontrado)
Se a aplicação de cliente recebe uma mensagem de HTTP 404 (não encontrado) do servidor, isto significa que o objeto que o cliente estava a tentar utilizar (tal como uma entidade, tabela, blob, contentor ou fila) não existe no serviço de armazenamento. Existem várias razões possíveis para esta ação, tais como:

- [O cliente ou por outro processo anteriormente eliminada o objeto]
- [Um problema de autorização de assinatura partilhadas do Access (SA)]
- [Código de JavaScript do lado do cliente não tem permissão para aceder ao objecto]
- [Falha de rede]

#### <a name="client-previously-deleted-the-object"></a>O cliente ou por outro processo anteriormente eliminada o objeto
Em cenários onde o cliente está a tentar ler, atualizar ou eliminar dados de um serviço de armazenamento é normalmente fácil identificar nos registos do lado do servidor de uma operação anterior eliminado o objeto em questão do serviço de armazenamento. Muito muitas vezes, os dados do registo mostram que outro utilizador ou processo eliminou o objeto. No registo de armazenamento de registo do lado do servidor, o tipo de operação e pedida objeto-colunas de chave mostram quando um cliente eliminado um objeto.

O cenário onde um cliente estiver a tentar inserir um objeto, poderá não ser imediatamente óbvio por que é que isto resulta numa resposta de HTTP 404 (não encontrado) dado que o cliente está a criar um novo objeto. No entanto, se o cliente está a criar um blob tem de ser capaz de localizar o contentor blob, se o cliente é criar uma mensagem tem de ser capaz de localizar uma fila, e se o cliente está a adicionar uma linha tem de ser capaz de localizar a tabela.

Pode utilizar o registo do lado do cliente a partir da biblioteca de cliente de armazenamento para obter uma compreensão mais detalhada das quando o cliente envia pedidos específicos ao serviço de armazenamento.

No registo do lado do cliente seguinte gerado pela biblioteca do cliente de armazenamento ilustra o problema quando o cliente não consegue localizar no contentor para blob está a criar. Este registo inclui detalhes das seguintes operações de armazenamento:

ID do pedido|Operação
---|---
07b26a5d-...|Método de **DeleteIfExists** para eliminar o contentor blob. Note que esta operação inclui um pedido de **cabeça** para verificar a existência do contentor.
e2d06d78...|Método de **CreateIfNotExists** para criar o contentor blob. Note que esta operação inclui um pedido de **cabeça** que verifica a existência do contentor. A **cabeça** devolve uma mensagem de 404 mas continua.
de8b1c3c-...|Método de **UploadFromStream** para criar o blob. O pedido de **colocar** falha com uma mensagem 404

Entradas de registo:

ID do pedido |  Texto de operação
---|---
07b26a5d-...|Iniciar o pedido síncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 de Junho de 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|Aguardar resposta.
07b26a5d-... | Foi recebida uma resposta. Código de estado = 200, ID de pedido = eeead849... MD5 conteúdo = ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | Cabeçalhos de resposta processados com êxito, processo com o resto da operação de.
07b26a5d-... | Transferir o corpo de resposta.
07b26a5d-... | Operação concluída com êxito.
07b26a5d-... | Iniciar o pedido síncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 de Junho de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | Aguardar resposta.
07b26a5d-... | Foi recebida uma resposta. Código de estado = 202 de ID de pedido = 6ab2a4cf-..., conteúdo MD5 = ETag =.
07b26a5d-... | Cabeçalhos de resposta processados com êxito, processo com o resto da operação de.
07b26a5d-... | Transferir o corpo de resposta.
07b26a5d-... | Operação concluída com êxito.
e2d06d78-... | Iniciar o pedido assíncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 de Junho de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| Aguardar resposta.
de8b1c3c-... | Iniciar o pedido síncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... |  StringToSign = colocar... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-blob-type:BlockBlob.x-MS-Client-Request-ID:de8b1c3c-...x-MS-Date:TUE, 03 de Junho de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Preparar a escrever dados pedido.
e2d06d78-... | Excepção enquanto a aguardar resposta: O servidor remoto devolvido um erro: (404) não foi encontrado...
e2d06d78-... | Foi recebida uma resposta. Código de estado = 404, pedir ID = 353ae3bc-..., conteúdo MD5 = ETag =.
e2d06d78-... | Cabeçalhos de resposta processados com êxito, processo com o resto da operação de.
e2d06d78-... | Transferir o corpo de resposta.
e2d06d78-... | Operação concluída com êxito.
e2d06d78-... | Iniciar o pedido assíncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = colocar... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE, 03 de Junho de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | Aguardar resposta.
de8b1c3c-... | Dados de pedido de escrita.
de8b1c3c-... | Aguardar resposta.
e2d06d78-... | Excepção enquanto a aguardar resposta: O servidor remoto devolvido um erro: (409) conflito...
e2d06d78-... | Foi recebida uma resposta. Código de estado = 409, pedir ID = c27da20e-..., conteúdo MD5 = ETag =.
e2d06d78-... | Transferir o corpo de resposta do erro.
de8b1c3c-... | Excepção enquanto a aguardar resposta: O servidor remoto devolvido um erro: (404) não foi encontrado...
de8b1c3c-... | Foi recebida uma resposta. Código de estado = 404, pedir ID = 0eaeab3e-..., conteúdo MD5 = ETag =.
de8b1c3c-...| Excepção durante a operação: O servidor remoto devolvido um erro: (404) não foi encontrado...
de8b1c3c-... | Política de repetir não permitia para uma nova tentativa. Falhar com o servidor remoto devolvido um erro: (404) não foi encontrado...
e2d06d78-... | Política de repetir não permitia para uma nova tentativa. Falhar com o servidor remoto devolvido um erro: (409) conflito...

Neste exemplo, o registo mostra que o cliente é intercalada pedidos a partir do método de **CreateIfNotExists** (pedido id e2d06d78...) com os pedidos a partir do método de **UploadFromStream** (de8b1c3c-...); Isto acontece porque a aplicação de cliente está a invocar modo assíncrono estes métodos. Deve modificar o código assíncrono ao cliente para se certificar de que cria o contentor antes de tentar carregar quaisquer dados para um blob desse contentor. Idealmente, deverá criar todos os seus contentores com antecedência.

#### <a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura partilhadas do Access (SA)

Se a aplicação de cliente tentar utilizar uma chave de SA não inclui as permissões necessárias para a operação, o serviço de armazenamento devolve uma mensagem de HTTP 404 (não encontrado) para o cliente. Ao mesmo tempo, também verá um valor diferente de zero para **SASAuthorizationError** no métricas.

A tabela seguinte mostra um exemplo de uma mensagem de registo do lado do servidor do ficheiro de registo de armazenamento de registo:

<table>
  <tr>
    <td>Pedir a hora de início</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Tipo de operação</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Estado do pedido</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Código de estado HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Tipo de autenticação</td>
    <td>SA</td>
  </tr>
  <tr>
    <td>Tipo de serviço</td>
    <td>Blob</td>
  </tr>
  <tr>
    <td>URL do pedido</td>
    <td>
    https://domemaildist.blob.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp; sr = c&amp;amp; si = mypolicy&amp;amp; assinatura = XXXXX&amp;amp; versão api = 2014-02-14&amp;estudo</td>
  </tr>
  <tr>
    <td>Cabeçalho de id do pedido</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID de pedido de cliente</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Deverá investigar porque é que a aplicação de cliente estiver a tentar executar uma operação que não foi lhe concedeu permissões para.

#### <a name="JavaScript-code-does-not-have-permission"></a>Código de JavaScript do lado do cliente não tem permissão para aceder ao objecto

Se estiver a utilizar um cliente JavaScript e o serviço de armazenamento está a devolver HTTP 404 mensagens, procure os seguintes erros de JavaScript no browser:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] Pode utilizar as ferramentas de programador F12 no Internet Explorer para analisar as mensagens trocadas entre o browser e o serviço de armazenamento quando estiver a resolver problemas de JavaScript do lado do cliente.

Estes erros ocorrerem porque o web browser implementa a restrição de segurança de <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">política da mesma origem</a> que impede o chamar uma API num domínio diferente a partir do domínio que a página provém de uma página web.

Para resolver o problema de JavaScript, pode configurar Cross origem recurso partilha (CORS) para o serviço de armazenamento que está a aceder ao cliente. Para mais informações, consulte o artigo <a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">suporte de partilha de recursos por comparação origem (CORS) para serviços de armazenamento do Azure</a> no MSDN.

O código de exemplo seguinte mostra como configurar o seu serviço de BLOBs para permitir que JavaScript em execução no domínio Contoso aceder a um blob no seu serviço de armazenamento de BLOBs:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Falha de rede

Em determinadas circunstâncias, pacotes de rede perdidas podem conduzir ao serviço de armazenamento devolver HTTP 404 mensagens para o cliente. Por exemplo, quando a sua aplicação cliente está a eliminar uma entidade de serviço de tabela verá o cliente gerar um relatório de exceção de armazenamento um "HTTP 404 (não encontrado)" mensagem de estado do serviço de tabela. Quando investigar a tabela no serviço de armazenamento de tabela, verá que o serviço foi eliminada a entidade conforme pedido.

Os detalhes de exceção no cliente do incluem o id de pedido (7e84f12d...) atribuído pelo serviço da tabela para o pedido: pode utilizar estas informações para localizar os detalhes do pedido nos registos do lado do servidor armazenamento através da procura na coluna **cabeçalho de pedido de id** no ficheiro de registo. Também pode utilizar as métricas para identificar quando falhas como estas ocorrem e em seguida, procure os ficheiros de registo com base na data e hora que métricas gravadas este erro. Esta entrada do registo mostra que a eliminar Ocorreu uma falha com uma mensagem de estado "HTTP (404) cliente outro tipo de erro". A entrada do registo mesmo também inclui o id de pedido gerado pelo cliente na coluna **id do pedido de cliente** (813ea74f...).

Registo do lado do servidor também inclui outra entrada com o mesmo valor de **id do pedido de cliente** (813ea74f...) para uma operação de eliminação com êxito para a mesma entidade e a partir do cliente do mesmo. Esta operação de eliminação bem sucedida substituiu muito pouco tempo antes de elimina a falha pedido.

Este cenário a causa mais provável é que o cliente de estação de enviado um pedido de eliminação para a entidade para o serviço de tabela, que foi concluída com êxito, mas não recebeu uma confirmação do servidor (talvez devido a um problema temporário na rede). O cliente, em seguida, repetidas automaticamente a operação (utilizando o mesmo **id de pedido de cliente**) e este repetir falhou porque a entidade já foi eliminada.

Se este problema ocorre frequentemente, deverá investigar porque é que o cliente está a falhar receber notificações de serviço de tabela. Se o problema for intermitente, deverá interceptar o erro "HTTP (404) não encontrado" e iniciar a sessão no cliente, mas permitir que o cliente continuar.

### <a name="the-client-is-receiving-409-messages"></a>O cliente está a receber mensagens de HTTP 409 (conflito)

A tabela seguinte mostra um extrair a partir do registo do lado do servidor para duas operações de cliente: **DeleteIfExists** imediatamente seguido **CreateIfNotExists** utilizando o mesmo nome de contentor blob. Note que cada resultados da operação de cliente em duas pedidos enviado para o servidor, primeiro um pedido de **GetContainerProperties** para verificar se existe no contentor, seguido de pedido de **DeleteContainer** ou **CreateContainer** .

Data/hora|Operação|Resultado|Nome do contentor|Id de pedido de cliente
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

O código de na aplicação cliente elimina e, em seguida, recria imediatamente um contentor de BLOBs utilizando o mesmo nome: o método de **CreateIfNotExists** (pedido do cliente de ID bc881924-...) são eventualmente falhar com o erro de HTTP 409 (conflito). Quando um cliente elimina blob contentores, tabelas ou filas existe um breve período de tempo antes do nome fica disponível novamente.

A aplicação de cliente deve utilizar nomes do contentor exclusivo sempre que cria novos contentores se o padrão de eliminar/recrie for comuns.

### <a name="metrics-show-low-percent-success"></a>Métricas mostram PercentSuccess baixa ou entradas de registo de analytics tem operações com o estado da transação da ClientOtherErrors

A métrica do **PercentSuccess** captura a percentagem de operações que tenham sido efetuada com êxito com base no seu código de estado de HTTP. Operações com os códigos de estado do 2XX contam como concluído com êxito, Considerando que operações com códigos de estado no intervalos 3XX, 4XX e 5XX são contadas como concluída com êxito e diminua o valor métrico **PercentSucess** . Os ficheiros de registo do lado do servidor de armazenamento, estas operações são registadas com o estado da transação **ClientOtherErrors**.

É importante ter em atenção que estas operações tem concluído com êxito e, consequentemente, não vai afetar outras métricas como disponibilidade. Alguns exemplos de operações que executar com êxito, mas que pode resultar em códigos de estado HTTP ter êxito incluem:
- **ResourceNotFound** (Não encontrado 404), por exemplo, a partir de um pedido de obter para um blob que não existe.
- **ResouceAlreadyExists** (Conflito 409), por exemplo, a partir de uma operação de **CreateIfNotExist** onde o recurso já existe.
- **ConditionNotMet** (Não modificado 304), por exemplo, a partir de uma operação de condicional tal como quando um cliente envia um valor de **ETag** e um cabeçalho HTTP **Se nenhum-correspondência** para pedir uma imagem apenas se foi atualizado desde a última operação.

Pode encontrar uma lista de códigos de erro REST API comuns que devolvem os serviços de armazenamento na página de <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Códigos de erro de API REST comuns</a>.

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Métricas de capacidade mostram um aumento inesperado na utilização de capacidade de armazenamento


Se vir repentina sobre, inesperadas alterações na utilização de capacidade na sua conta de armazenamento, pode investigar as razões verificando primeiro as métricas de disponibilidade; Por exemplo, um aumento no número de eliminar falha pedidos podem conduzir a um aumento armazenamento de Blobs do que está a utilizar como operações de limpeza específico de aplicações que poderá ter espera poder ser libertar espaço em poderão não estar a funcionar como esperado (por exemplo, uma vez que expiraram os tokens de SA utilizados para libertar espaço).

### <a name="you-are-experiencing-unexpected-reboots"></a>Ocorreu for reiniciado inesperado de máquinas virtuais do Azure que tenham um grande número de VHDs anexados

Se uma Máquina Virtual (VM Azure) tem um grande número de VHDs anexados que estão na mesma conta de armazenamento, poderia exceder os destinos escalabilidade para uma conta de armazenamento individuais a causar a VM falha. Deve verificar as métricas minutos para a conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) picos que excedam os destinos escalabilidade para uma conta de armazenamento. Consulte a secção "[que métricas de mostram um aumento no PercentThrottlingError]" para obter assistência para determinar se limitação ocorreu na sua conta de armazenamento.

Em geral, cada entrada individual ou uma operação de exportação num VHD a partir de uma Máquina Virtual corresponde a função operações **Obter página** ou **Colocar** o blob de página subjacente. Por conseguinte, pode utilizar o IOPS estimado para o seu ambiente para o gráfico VHDs quantos que pode ter numa conta armazenamento única baseado no seu comportamento específico da sua aplicação. Recomendamos que não está a ter mais de 40 discos numa conta armazenamento única. Consulte o artigo <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">escalabilidade de armazenamento do Azure e desempenho destinos</a> para obter detalhes sobre os destinos escalabilidade atual para contas de armazenamento, em particular a taxa de pedido de total e largura de banda total para o tipo de conta de armazenamento que está a utilizar.
Se são excedem os destinos escalabilidade para a sua conta de armazenamento, deverá colocar o seu VHDs em várias contas de armazenamento diferente para reduzir a atividade em cada conta individual.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Surge o problema de utilizar o emulador de armazenamento para desenvolvimento ou de teste

Utilizar o emulador armazenamento durante o desenvolvimento normalmente e testar para evitar o requisito de uma conta de armazenamento Azure. Os problemas comuns que podem ocorrer quando estiver a utilizar o emulador armazenamento são:

- [Funcionalidade "X" não está a trabalhar no emulador de armazenamento]
- [Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" ao utilizar o emulador de armazenamento]
- [A executar o emulador armazenamento requer privilégios administrativos]

#### <a name="feature-X-is-not-working"></a>Funcionalidade "X" não está a trabalhar no emulador de armazenamento

O emulador de armazenamento não suporta todas as funcionalidades dos serviços Azure armazenamento como o serviço de ficheiros. Para mais informações, consulte o artigo <a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">diferenças entre armazenamento emulador Azure serviços e armazenamento</a> no MSDN.

Para as funcionalidades que não suporta o emulador de armazenamento, utilize o serviço de armazenamento Azure na nuvem.

#### <a name="error-HTTP-header-not-correct-format"></a>Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" ao utilizar o emulador de armazenamento

Estiver a testar a aplicação que utilizam a biblioteca do cliente armazenamento contra emulador de armazenamento local e chamadas de método, tais como **CreateIfNotExists** falhar com a mensagem de erro "o valor para um dos cabeçalhos de HTTP não está no formato correto". Isto indica que a versão do emulador de armazenamento que está a utilizar não suporta a versão da biblioteca de cliente do armazenamento que está a utilizar. A biblioteca do cliente armazenamento adiciona de cabeçalho **x-ms-versão** para todos os pedidos de torna. Se o emulador de armazenamento não reconhecer o valor no cabeçalho **x-ms-versão** ,-rejeita o pedido.

Pode utilizar os registos de cliente da biblioteca de armazenamento para ver o valor do **cabeçalho x-ms-versão** que está a enviar. Também pode ver o valor de **cabeçalho x-ms-versão** se utilizar Fiddler para analisar os pedidos a partir da sua aplicação de cliente.

Este cenário normalmente ocorre se instalar e utilizar a versão mais recente da biblioteca de cliente de armazenamento sem atualizar o emulador de armazenamento. Deve instalar a versão mais recente do emulador armazenamento ou utilizar armazenamento em nuvem em vez do emulador para desenvolvimento e teste.

#### <a name="storage-emulator-requires-administrative-privileges"></a>A executar o emulador armazenamento requer privilégios administrativos

Lhe for pedido para introduzir as credenciais de administrador quando executa o emulador de armazenamento. Isto só ocorre quando são inicialização emulador de armazenamento pela primeira vez. Depois de ter inicializado emulador de armazenamento, não precisa privilégios administrativos executá-la novamente.

Para mais informações, consulte <a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">Iniciar o emulador armazenamento utilizando a ferramenta de linha de comandos</a> no MSDN (também pode iniciar o emulador de armazenamento no Visual Studio, também irá necessitar de privilégios administrativos).

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Encontrar problemas ao instalar o SDK do Azure para .NET

Quando tenta instalar o SDK, falha tentar instalar o emulador de armazenamento no computador local. Registo de instalação do contém uma das seguintes mensagens:

- CAQuietExec: Erro: não é possível aceder a instância do SQL
- CAQuietExec: Erro: não é possível criar a base de dados

A causa é um problema com a instalação LocalDB existente. Por predefinição, o emulador armazenamento utiliza LocalDB para persistirem dados quando-simula os serviços de armazenamento Azure. Pode repor a sua instância LocalDB executando os seguintes comandos numa janela de linha de comandos antes de tentar instalar o SDK.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

O comando **Eliminar** remove quaisquer ficheiros de base de dados antigo de instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Tem um problema diferente com um serviço de armazenamento

Se as secções de resolução de problemas anterior não inclua o problema que está a ter com um serviço de armazenamento, deve aprovar a seguinte abordagem para diagnosticar e o problema de resolução de problemas.

- Verifique o seu métricas para ver se existir alguma alteração a partir do seu comportamento esperado da linha de base. A partir de métricas, talvez consiga determinar se o problema for breves ou permanentes e quais as operações armazenamento o problema é afetar.
- Pode utilizar as informações de métricas para ajudá-lo a procurar os dados de registo do lado do servidor para obter informações mais detalhadas sobre erros que estão a ocorrer. Estas informações podem ajudá-lo a resolver problemas e resolver o problema.
- Se as informações nos registos do lado do servidor não não suficientes para resolver o problema com êxito, pode utilizar os registos do lado do cliente de biblioteca de armazenamento do cliente para investigar o comportamento da sua aplicação de cliente e as ferramentas de como Fiddler, Wireshark e Microsoft mensagem Analyzer para investigar a sua rede.

Para obter mais informações sobre como utilizar Fiddler, consulte o artigo "[anexo 1: utilizar Fiddler para capturar tráfego HTTP e HTTPS]."

Para obter mais informações sobre como utilizar Wireshark, consulte o artigo "[anexo 2: utilizar Wireshark para capturar tráfego de rede]."

Para obter mais informações sobre como utilizar o analisador de mensagem da Microsoft, consulte o artigo "[anexo 3: utilizar o analisador de Microsoft Message para capturar tráfego de rede]."

## <a name="appendices"></a>Anexos

Os anexos descrevem várias ferramentas pode ser úteis quando são diagnóstico e resolução de problemas com o armazenamento do Windows Azure (e outros serviços). Estas ferramentas não fazem parte de armazenamento do Windows Azure e algumas são produtos de terceiros. Como tal, as ferramentas de outros fabricantes referidas presentes anexos não estejam tapadas por qualquer contrato de suporte que poderá ter com o Microsoft Azure ou armazenamento do Windows Azure e, consequentemente, como parte do processo de avaliação deverá examinar as licenciamento e opções de suporte disponíveis a partir de fornecedores destas ferramentas.

### <a name="appendix-1"></a>Anexo 1: Utilizar Fiddler para capturar tráfego HTTP e HTTPS

Fiddler é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre a aplicação de cliente e o serviço de armazenamento Azure que estiver a utilizar. Pode transferir Fiddler do <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>.

> [AZURE.NOTE] Fiddler pode descodificar tráfego HTTPS; deve ler a documentação Fiddler cuidadosamente para compreender como é que isto e para compreender as implicações de segurança.

Este anexo fornece instruções breve sobre como configurar Fiddler para capturar tráfego entre o computador local onde instalou Fiddler e os serviços de armazenamento Azure.

Depois de ter iniciado Fiddler, será iniciado captar o tráfego HTTP e HTTPS no seu computador local. Seguem-se alguns comandos úteis para controlar Fiddler:

- Pare e inicie a captura tráfego. No menu principal, aceda a **ficheiro** e, em seguida, clique em **Captura o tráfego** para ativar/desativar capturar e desativar.
- Guarde dados de tráfego capturada. No menu principal, aceda a **ficheiro**, clique em **Guardar**e, em seguida, clique em **Todas as sessões**: Isto permite-lhe guardar o tráfego de um ficheiro de arquivo de sessão. Pode recarregar um arquivo de sessão mais tarde para uma análise ou enviá-lo, se lhe for solicitado para o suporte da Microsoft.

Para limitar a quantidade de tráfego que Fiddler captura, pode utilizar filtros que configurar no separador de **filtros** . A captura de ecrã seguinte mostra um filtro que apenas o tráfego enviado para o ponto final de armazenamento de **contosoemaildist.table.core.windows.net** para capturar:

![][5]

### <a name="appendix-2"></a>Anexo 2: Utilizar Wireshark para capturar tráfego de rede

Wireshark é um analisador de protocolo de rede que permite-lhe ver informações detalhadas de pacote para uma vasta gama de protocolos de rede. Pode transferir Wireshark do <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>.

O procedimento que se segue mostra-lhe como capturar informações detalhadas de pacote para o tráfego do computador local onde instalou o Wireshark para o serviço de tabela na sua conta de armazenamento Azure.

1.  Inicie o Wireshark no seu computador local.
2.  Na secção de **começar** , selecione a interface de rede local ou interfaces que estão ligadas à internet.
3.  Clique em **captura opções**.
4.  Adicione um filtro para a caixa de texto do **Filtro de captura** . Por exemplo, o **anfitrião contosoemaildist.table.core.windows.net** irá configurar Wireshark para capturar apenas pacotes enviados para ou a partir do ponto final de serviço de tabela na conta de armazenamento **contosoemaildist** . Para uma lista completa dos filtros capturar consulte <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>.

    ![][6]

5.  Clique em **Iniciar**. Wireshark agora irá capturar todos os pacotes de enviar a ou a partir do ponto final de serviço a tabela ao utilizar a aplicação de cliente no seu computador local.
6.  Quando tiver terminado, no menu principal clique **capturar** e, em seguida, **Parar**.
7.  Para guardar os dados capturados num ficheiro Wireshark de capturar, no menu principal, clique em **ficheiro** e, em seguida, **Guardar**.

WireShark irá realçar algum erro que existam na janela do **packetlist** . Também pode utilizar a janela **Informações de especialista** (clique em **Analisar**, em seguida, **Informações de especialista**) para ver um resumo dos erros e avisos.

![][7]

Também pode selecionar ver os dados TCP, tal como a camada de aplicação vê-la ao clicar nos dados TCP e selecionar a **seguir da cadeia TCP**. Isto é particularmente útil se capturada o seu informação sem um filtro de captura. Consulte <a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">aqui</a> para obter mais informações.

![][8]

> [AZURE.NOTE] Para obter mais informações sobre como utilizar Wireshark, consulte o <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Guia de utilizadores Wireshark</a>.

### <a name="appendix-3"></a>Anexo 3: Utilizar o Microsoft mensagem Analyzer para capturar tráfego de rede

Pode utilizar o Microsoft mensagem Analyzer para capturar tráfego HTTP e HTTPS de forma semelhante para Fiddler e capturar tráfego de rede de forma semelhante para Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurar uma sessão de rastreio de web utilizando o Microsoft mensagem Analyzer

Para configurar uma sessão de rastreio de web para o tráfego de HTTP e HTTPS utilizar Microsoft analisador de mensagem, execute a aplicação Microsoft Analyzer de mensagem e, em seguida, no menu **ficheiro** , clique em **Captura/rastreio**. Na lista de cenários de rastreio disponíveis, selecione **O Proxy Web**. Em seguida, no painel de **Configuração do cenário de rastreio** , na caixa de texto **HostnameFilter** , adicione os nomes dos seus os pontos finais de armazenamento (pode consultar estes nomes no Portal clássica do Azure). Por exemplo, se o nome da sua conta de armazenamento Azure for **contosodata**, deve adicionar o seguinte procedimento para a caixa de texto **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Um caráter de espaço separa os nomes de anfitriões.

Quando estiver pronto para começar a recolher dados de rastreio, clique no botão **Começar com** .

Para mais informações sobre o rastreio Microsoft mensagem Analyzer **Proxy da Web** , consulte o artigo <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">PEF WebProxy fornecedor</a> no TechNet.

O rastreio de **Web Proxy** incorporado no Microsoft mensagem Analyzer é baseado no Fiddler; Este pode captar o tráfego do lado do cliente HTTPS e apresenta mensagens HTTPS não encriptadas. O rastreio de **Web Proxy** funciona através da configuração de um proxy local para o tráfego HTTP e HTTPS todos os que dá-lhe acesso a mensagens não encriptadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticar problemas de rede com o Microsoft mensagem Analyzer

Além de utilizar o rastreio Microsoft mensagem Analyzer **Web Proxy** para capturar os detalhes do tráfego HTTP/HTTPs entre a aplicação de cliente e o serviço de armazenamento, também pode utilizar o rastreio do **Local de ligação de camada** incorporado para capturar informações de pacote de rede. Esta permite-lhe para capturar dados semelhantes ao que pode capturar com Wireshark e diagnosticar problemas de rede tais como pacotes ignorados.

A captura de ecrã seguinte mostra um exemplo rastreio do **Local de ligação de camada** com algumas mensagens **informativas** na coluna **DiagnosisTypes** . Clicar num ícone na coluna **DiagnosisTypes** mostra os detalhes da mensagem. Neste exemplo, o servidor retransmissão de mensagem #305 porque não recebeu uma confirmação do cliente:

![][9]

Quando cria a sessão de rastreio no Microsoft analisador de mensagem, pode especificar filtros para reduzir a quantidade de ruído no rastreio. Na página **capturar / rastrear** onde pode definir o rastreio, clique na ligação **Configurar** junto a **Microsoft Windows-NDIS PacketCapture**. A captura de ecrã seguinte mostra uma configuração que filtra tráfego TCP para os endereços IP dos três serviços de armazenamento:

![][10]

Para mais informações sobre o rastreio camada de ligação Microsoft mensagem Analyzer Local, consulte o artigo <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PEF-NDIS-PacketCapture fornecedor</a> no TechNet.

### <a name="appendix-4"></a>Anexo 4: Utilizar o Excel para ver métricas e registar os dados

Muitas ferramentas permitem-lhe transferir os dados de métricas de armazenamento do armazenamento de tabela do Azure num formato delimitada que torna mais fácil carregar os dados para o Excel para visualização e análise. Dados de registo de armazenamento de armazenamento de Blobs do Azure já estão num formato delimitada que pode carregar para o Excel. No entanto, terá de adicionar cabeçalhos de coluna adequado com base nas informações no <a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">Formato de registo de análise de armazenamento</a> e <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Esquema de tabela de métricas de análise de armazenamento</a>.

Para importar os dados de registo de armazenamento para o Excel depois de transferir a partir do armazenamento de BLOBs:

- No menu **dados** , clique na **Partir do texto**.
- Navegue para o ficheiro de registo que pretende ver e clique em **Importar**.
- No passo 1 do **Assistente de importação de texto**, selecione **delimitado**.

No passo 1 do **Assistente de importação de texto**, selecione o **ponto e vírgula** como o delimitador apenas e escolha aspas como o **qualificador de texto**. Em seguida, clique em **Concluir** e escolha onde pretende colocar os dados no seu livro.

### <a name="appendix-5"></a>Anexo 5: Monitorizar com informações de aplicação para serviços de equipa do Visual Studio

Também pode utilizar a funcionalidade de informações de aplicação para serviços de equipa do Visual Studio como parte do seu desempenho e disponibilidade de monitorização. Esta ferramenta pode:

- Certifique-se o serviço web disponível e responder. Se a sua aplicação está num web site ou uma aplicação do dispositivo que utiliza um serviço web, pode testar o seu URL em poucos minutos a partir de localizações no mundo e permitem-lhe saber se existe um problema.
- Diagnosticar rapidamente quaisquer problemas de desempenho ou exceções no seu serviço web. Descubra se estão a ser for esticados CPU ou outros recursos, obter rastreios de exceções e facilmente pesquisar através de rastreios de registo. Se o desempenho da aplicação descer abaixo limites aceitáveis, podemos pode enviar-lhe uma mensagem de e-mail. Pode monitorizar .NET e Java serviços web.

At a hora da gravação de informações da aplicação é na pré-visualização. Pode encontrar mais informações em <a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">Informações de aplicação para o Visual Studio Team Services no MSDN</a>.


<!--Anchors-->
[Introdução]: #introduction
[Como este guia está organizado]: #how-this-guide-is-organized

[Monitorizar o seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitorizar o estado de funcionamento do serviço]: #monitoring-service-health
[Capacidade de monitorização]: #monitoring-capacity
[Disponibilidade de monitorização]: #monitoring-availability
[Monitorizar o desempenho]: #monitoring-performance

[Diagnosticar problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de estado de funcionamento de serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Diagnosticar erros]: #diagnosing-errors
[Problemas de emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de registo de armazenamento]: #storage-logging-tools
[Utilizar ferramentas de registo de rede]: #using-network-logging-tools

[Rastreio ponto a ponto]: #end-to-end-tracing
[Dados do registo correlação]: #correlating-log-data
[ID de pedido de cliente]: #client-request-id
[ID de pedido de servidor]: #server-request-id
[Selos de tempo]: #timestamps

[Resolução de problemas de orientação]: #troubleshooting-guidance
[Métricas mostram AverageE2ELatency alta e AverageServerLatency baixa]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Métricas mostram AverageE2ELatency baixa e AverageServerLatency baixa, mas o cliente está a experimentar latência alta]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Métricas mostram AverageServerLatency alta]: #metrics-show-high-AverageServerLatency
[Ocorreu inesperados atrasos na entrega de mensagens numa fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Métricas de mostram um aumento no PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento breves PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumentar permanente PercentThrottlingError erro]: #permanent-increase-in-PercentThrottlingError
[Métricas de mostram um aumento no PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Métricas de mostram um aumento no PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está a receber mensagens de HTTP 403 (proibido)]: #the-client-is-receiving-403-messages
[O cliente está a receber mensagens de HTTP 404 (não encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou por outro processo anteriormente eliminada o objeto]: #client-previously-deleted-the-object
[Um problema de autorização de assinatura partilhadas do Access (SA)]: #SAS-authorization-issue
[Código de JavaScript do lado do cliente não tem permissão para aceder ao objecto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está a receber mensagens de HTTP 409 (conflito)]: #the-client-is-receiving-409-messages

[Métricas mostram PercentSuccess baixa ou entradas de registo de analytics tem operações com o estado da transação da ClientOtherErrors]: #metrics-show-low-percent-success
[Métricas de capacidade mostram um aumento inesperado na utilização de capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[Ocorreu for reiniciado inesperado de máquinas virtuais que tenham um grande número de VHDs anexados]: #you-are-experiencing-unexpected-reboots
[Surge o problema de utilizar o emulador de armazenamento para desenvolvimento ou de teste]: #your-issue-arises-from-using-the-storage-emulator
[Funcionalidade "X" não está a trabalhar no emulador de armazenamento]: #feature-X-is-not-working
[Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" ao utilizar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[A executar o emulador armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Encontrar problemas ao instalar o SDK do Azure para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Tem um problema diferente com um serviço de armazenamento]: #you-have-a-different-issue-with-a-storage-service

[Anexos]: #appendices
[Anexo 1: Utilizar Fiddler para capturar tráfego HTTP e HTTPS]: #appendix-1
[Anexo 2: Utilizar Wireshark para capturar tráfego de rede]: #appendix-2
[Anexo 3: Utilizar o Microsoft mensagem Analyzer para capturar tráfego de rede]: #appendix-3
[Anexo 4: Utilizar o Excel para ver métricas e registar os dados]: #appendix-4
[Anexo 5: Monitorizar com informações de aplicação para serviços de equipa do Visual Studio]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-2.png

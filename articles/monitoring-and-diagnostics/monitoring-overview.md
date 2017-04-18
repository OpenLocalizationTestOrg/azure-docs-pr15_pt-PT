<properties
    pageTitle="Descrição geral da monitorização no Microsoft Azure | Microsoft Azure"
    description="Descrição geral da nível superior de monitorização e diagnósticos no Microsoft Azure incluindo alertas, webhooks, autoscale e muito mais."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Descrição geral da monitorização no Microsoft Azure

Este artigo fornece uma descrição geral conceptual dos recursos Azure de monitorização. Fornece ponteiros para obter informações sobre tipos específicos de recursos.  Para informações sobre como monitorizar a aplicação a partir de que não sejam Azure ponto de vista de alto nível, consulte [orientações de monitorização e diagnóstico](../best-practices-monitoring.md).

Aplicações de nuvem são complexas com muitas partes mover. Monitorização fornece dados para se certificar de que a sua aplicação permanece para cima e está em execução num estado Saudável. Também ajuda-o a stave desativar potenciais problemas ou resolver os problemas anteriores uns. Além disso, pode utilizar dados de monitorização para obter informações abrangente sobre a aplicação. Conhecimentos podem ajudá-lo a melhorar o desempenho da aplicação ou manutenção ou automatizar ações que iria caso contrário, necessitar de intervenção manual.

O diagrama seguinte mostra uma perspectiva conceptual do Azure monitorização, incluindo o tipo de registos que pode recolher e o que pode fazer com que os dados.   

![Modelo lógico de monitorização e diagnóstico para recursos de que não sejam cluster](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figura 1: Modelo Conceptual para a monitorização e diagnóstico para recursos de que não sejam cluster

<br/>

![Modelo lógico de monitorização e diagnóstico para recursos de cluster](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figura 2: Modelo Conceptual para a monitorização e diagnóstico para recursos de cluster


## <a name="monitoring-sources"></a>Origens de monitorização
### <a name="activity-logs"></a>Registos de atividade
Pode procurar o registo de atividade (anteriormente denominado operacional ou registos de auditoria) para obter informações sobre o seu recurso conforme visto por infraestrutura do Azure. O registo contém informações como horas quando os recursos são criados ou destruídos.  

### <a name="host-vm"></a>Anfitrião VM
**Calcular apenas**


Calcular alguns recursos, como a serviços em nuvem, máquinas virtuais, e serviço ferro ter uma VM anfitrião dedicada que interagem com. A VM anfitrião é o equivalente a raiz VM no modelo de Hipervisor de Hyper-V. Neste caso, pode recolher métricas apenas a VM anfitrião para além do SO convidado.  

Para outros serviços do Azure, não existe necessariamente um mapeamento de 1:1 entre o seu recurso e de uma determinada VM de anfitrião, de modo a métrica do anfitrião VM não está disponível.


### <a name="resource---metrics-and-diagnostics-logs"></a>Recurso - métricas e registos de diagnóstico
Métricas discriminar variam consoante o tipo de recurso. Por exemplo, máquinas virtuais fornece estatísticas do es de disco e a percentagem de CPU. Mas não existe esse estatística para uma fila Bus de serviço, que fornece em vez disso métricas como débito fila de tamanho e a mensagem.

Para recursos de cluster pode obter métricas nos módulos SO convidado e diagnósticos de como diagnósticos do Azure. Diagnósticos do Azure ajuda-o a recolher e encaminha reunir dados de diagnóstico para outras localizações, incluindo armazenamento Azure.

Uma lista de métricas atualmente discriminar está disponível em [suportadas métricas](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicação - os registos de diagnóstico, registos de aplicações e métricas
**Calcular apenas**

Podem executar aplicações na parte superior do SO convidado no modelo de cluster. Estes emitem o seu próprio conjunto de registos e métricas.

Tipos de métricas incluem

- Contadores de desempenho
- Registos de aplicação
- Registos de eventos do Windows
- Origem do evento .NET
- Registos do IIS
- Manifesto com base ETW
- Informações de estado da falha de sistema
- Registos de erros de cliente


## <a name="uses-for-monitoring-data"></a>Utilizações para monitorizar os dados

### <a name="visualize"></a>Visualizar
Visualizar os seus dados em gráficos e gráficos monitorização ajuda-o a localizar tendências muito mais depressa que consultar os dados propriamente ditos.  

Alguns métodos de visualização incluem:

- Utilizar o portal do Azure
- Encaminhar os dados para informações de aplicação do Azure
- Encaminhar dados para o Microsoft PowerBI
- Encaminhar os dados para uma ferramenta de visualização de terceiros utilizando direto transmissão ou através de uma ferramenta de ler a partir de um arquivo no armazenamento Azure

### <a name="archive"></a>Arquivo
Monitorizar os dados são normalmente escritos ao armazenamento Azure e guardados aí até eliminá-lo.

Algumas formas de utilizar estes dados:

- Depois de escrita, pode ter outras ferramentas de dentro ou fora do Azure lê-la e processá-la.
- Transfira os dados localmente para um arquivo local ou alterar a política de retenção na nuvem para manter os dados por adicional períodos de tempo.  
- Deixar os dados no Azure armazenamento indefinidamente, apesar de ter que pagar a Azure armazenamento baseado na quantidade de dados que manter.
-

### <a name="query"></a>Consulta
Pode utilizar a API do Azure Monitor resto, cruzada plataforma Interface da linha de comandos (CLI) comandos, os cmdlets do PowerShell ou o .NET SDK para aceder aos dados no sistema ou armazenamento Azure

Alguns exemplos incluem:

-  Obter dados para uma aplicação de monitorização personalizada que escreveu
-  Criar consultas personalizadas e enviar esses dados a uma aplicação de terceiros.

### <a name="route"></a>Encaminhar
É possível transmitir a necessidade de dados de monitorização para outras localizações em tempo real.

Alguns exemplos incluem:

- Envie para informações de aplicação para que possa utilizar as ferramentas de visualização aí.
- Envie para o evento concentradores, de modo que possa encaminhar para ferramentas de terceiros para efetuar uma análise em tempo real.

### <a name="automate"></a>Automatizar
Pode utilizar dados de monitorização para eventos de accionador ou processos inteiro par exemplos incluem:

- Utilize dados de instâncias do cluster autoscale para cima ou para baixo com base na aplicação carga.
- Envie e-mails quando uma métrica este se cruza um determinado limiar predeterminado.
- Ligar um URL da web (webhook) para executar uma ação num sistema de fora do Azure
- Iniciar um livro de execuções no Azure automatização para executar qualquer variedade de tarefas

## <a name="methods-of-use"></a>Métodos de utilização
Em geral, pode manipular controlo de dados, encaminhamento e obtenção utilizando um dos seguintes métodos. Estão disponíveis para todas as ações ou tipos de dados não em todos os métodos.

- [Portal do Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [Interface de linha de comandos em diferentes plataformas (CLI)](insights-cli-samples.md)
- [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Ofertas de monitorização do Azure
Azure tem ofertas disponíveis para monitorizar os seus serviços de infraestrutura de base para telemetria de aplicação. A melhor estratégia de monitorização combina a utilização de todos os três para obter abrangente, detalhada visão o estado de funcionamento dos seus serviços.

- [Azure Monitor](http://aka.ms/azmondocs) – ofertas de visualização, consulta, encaminhamento, alertar, autoscale e automatização dados tanto a partir do infraestrutura do Azure (registo de atividade) e cada recurso Azure individual (registos de diagnóstico). Este artigo faz parte da documentação do Azure Monitor. O nome do Azure Monitor foi disponibilizado 25 de Setembro na Ignite 2016.  O nome anterior foi "Azure informações".  
- [Informações de aplicação](https://azure.microsoft.com/documentation/services/application-insights/) – disponibiliza deteção avançada e diagnósticos de existência de problemas na camada de aplicação do serviço, bem integrada na parte superior de dados a partir do Azure monitorização. É a plataforma de diagnóstico predefinido para a aplicação de serviço Web Apps.  Pode encaminhar dados a partir de outros serviços para o mesmo.  
- Parte de [Análise de registo](https://azure.microsoft.com/documentation/services/log-analytics/) do [Conjunto de aplicações de gestão de operações](https://www.microsoft.com/cloud-platform/operations-management-suite) – fornece uma solução de gestão de TI holística para no local e terceiros baseado na nuvem infraestrutura (como AWS) para além de recursos Azure.  Dados a partir do Azure Monitor podem ser encaminhados diretamente para a análise de registo para que possa ver registos de métricas de e para todo o seu ambiente num só local.     


## <a name="next-steps"></a>Próximos passos
Saber mais sobre

- [Azure Monitor num vídeo a partir do Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Introdução ao Azure Monitor](monitoring-get-started.md)
- [Azure diagnóstico](../azure-diagnostics.md) se estiver a tentar diagnosticar problemas na sua aplicação de serviço na nuvem, Máquina Virtual ou serviço ferro.
- [Informações de aplicação](https://azure.microsoft.com/documentation/services/application-insights/) se está a tentar diagnóstico problemas na sua aplicação Web do serviço de aplicação.
- [Resolução de problemas de armazenamento do Windows Azure](../storage/storage-e2e-troubleshooting.md) quando utilizar o armazenamento de Blobs, tabelas ou filas
- E o [conjunto de aplicações de gestão de operações](https://www.microsoft.com/cloud-platform/operations-management-suite) de [análise de registo](https://azure.microsoft.com/documentation/services/log-analytics/)

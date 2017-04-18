<properties
   pageTitle="O que é a análise de registo? | Microsoft Azure"
   description="Registo é um serviço no conjunto de aplicações de gestão de operações (OMS) que o ajuda a recolher e analisar dados operacionais gerados por recursos no seu cloud e de análise ambiente no local.  Este artigo fornece uma breve descrição geral dos diferentes componentes de ligações para conteúdos detalhadas e de análise de registo."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="what-is-log-analytics"></a>O que é a análise de registo?
Análise de registo é um serviço na [conjunto de aplicações de gestão de operações \(OMS\) ](../operations-management-suite/operations-management-suite-overview.md) que lhe permite recolher e analisar dados gerados por recursos no seu cloud e ambientes no local. Dá-lhe informações em tempo real utilizar pesquisa integrada e dashboards personalizados para analisar facilmente milhões de registos através de todas as cargas de trabalho e os servidores, independentemente da sua localização física.


## <a name="log-analytics-components"></a>Componentes de análise de registo
No Centro de análise do registo é o repositório OMS que está alojado na nuvem Azure.  Recolher dados para o repositório de origens ligadas ao configurar as origens de dados e soluções adicionar à sua subscrição.  Origens de dados e soluções irão cada criar diferentes tipos de registo que têm o seu próprio conjunto de propriedades, mas ainda ser analisados em conjunto em consultas para o repositório.  Esta opção permite-lhe utilizar as mesmas ferramentas e métodos para trabalhar com diferentes tipos de dados recolhidos pelo origens diferentes.


![Repositório OMS](media/log-analytics-overview/overview.png)


Origens ligadas são os computadores e outros recursos que geram os dados recolhidos pelo registo de análise.  Isto pode incluir agentes instalados no [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md) computadores que se ligam diretamente ou agentes de um [grupo de Gestor de operações do Centro de sistema de gestão de ligado](log-analytics-om-agents.md).  Análise de registo também pode recolher dados a partir [do armazenamento Azure](log-analytics-azure-storage.md).

[Origens de dados](log-analytics-data-sources.md) são os diferentes tipos de dados recolhidos a partir de cada origem ligada.  Isto inclui eventos e [dados de desempenho](log-analytics-data-sources-performance-counters.md) a partir do [Windows](log-analytics-data-sources-windows-events.md) e agentes de Linux para além de origens de [registos do IIS](log-analytics-data-sources-iis-logs.md)e [registos de texto personalizado](log-analytics-data-sources-custom-logs.md).  Configurar cada origem de dados que pretende recolher e a configuração é entregue automaticamente a cada origem ligada.


## <a name="analyzing-log-analytics-data"></a>Analisar dados de análise de registo
A maior parte da sua interação com a análise de registo será através do portal do OMS que é executado em qualquer browser e fornece-o com o acesso às definições de configuração de várias ferramentas e para analisar e agir sobre dados recolhidos.  A partir do portal pode tirar partido [registo pesquisas](log-analytics-log-searches.md) onde que construir consultas para analisar os dados recolhidos, [dashboards](log-analytics-dashboards.md) que pode personalizar com vistas gráficas das suas pesquisas jogador e [soluções](log-analytics-add-solutions.md) que fornecem funcionalidades adicionais e ferramentas de análise.

![OMS portal](media/log-analytics-overview/portal.png)


Análise de registo fornece uma sintaxe de consulta para obter e consolidar os dados no repositório rapidamente.  Pode criar e guardar o [Registo de pesquisas](log-analytics-log-searches.md) para diretamente analisar dados no portal do OMS ou tem registo pesquisas executado automaticamente para criar um alerta se os resultados da consulta indicam uma condição importante.

![Pesquisa de registo](media/log-analytics-overview/log-search.png)

Para dar uma vista rápida gráfica o estado de funcionamento do seu ambiente global, pode adicionar visualizações para pesquisas de registo guardado para o [dashboard](log-analytics-dashboards.md).   

![Dashboard](media/log-analytics-overview/dashboard.png)

Para analisar dados fora do registo de análise, pode exportar os dados a partir do repositório OMS para ferramentas de como o [Power BI](log-analytics-powerbi.md) ou Excel.  Também pode tirar partido do [Registo de pesquisa API](log-analytics-log-search-api.md) para criar soluções personalizadas que tirar partido de dados de análise de registo ou para integrar com outros sistemas de.

## <a name="solutions"></a>Soluções
Soluções adicionam funcionalidade a análise de registo.  Estes principalmente executar na nuvem e fornecem análise dos dados recolhidos no repositório de OMS. Também poderá definem novos tipos de registo para recolhidas que podem ser analisados com registo de pesquisas ou pela interface de utilizador adicionais fornecida pela solução no dashboard de OMS.  

![Alterar a solução de controlo](media/log-analytics-overview/change-tracking.png)


Existem soluções disponíveis para uma variedade de funções e facilmente pode procurar soluções disponíveis e [adicioná-los à área de trabalho OMS](log-analytics-add-solutions.md) a partir da Galeria de soluções.  Muitos serão automaticamente implementados e começar a trabalhar imediatamente enquanto outras pessoas podem exigir alguma configuração.

![Galeria de soluções](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Arquitetura de análise de registo
Os requisitos de implementação do registo de análise são mínimos uma vez que são alojados os componentes centrais na nuvem Azure.  Isto inclui o repositório para além dos serviços que lhe permitem correlacionar e analisar os dados recolhidos.  O portal pode ser acedido a partir de qualquer browser, pelo que não existe nenhuma requisitos de software de cliente.

Tem de instalar agentes em computadores com [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md) , mas não existe nenhuma agente adicional necessário para computadores que já estejam a membros de um [ligado grupo de gestão de SCOM](log-analytics-om-agents.md).  Agentes SCOM irão continuar a comunicar com servidores de gestão reencaminhará os seus dados para a análise de registo.  Embora algumas soluções irão necessitar agentes comunicar diretamente com a análise de registo.  A documentação para cada solução irá especificar os requisitos de comunicação.

Quando a [Inscrever-se para a análise de registo](log-analytics-get-started.md), irá criar uma área de trabalho OMS.  Poderá pensar da área de trabalho como um ambiente do OMS exclusivo com as suas próprias repositório de dados, origens de dados e soluções. Pode criar várias áreas de trabalho na sua subscrição suporta vários ambientes como produção e testar.

![Arquitetura de análise de registo](media/log-analytics-overview/architecture.png)


## <a name="next-steps"></a>Próximos passos

- [Inscrever-se para uma conta de registo de análise gratuita](log-analytics-get-started.md) para testar a sua própria ambiente.
- Ver as diferentes [Origens de dados](log-analytics-data-sources.md) disponíveis para recolher dados para o repositório OMS.
- [Procure as soluções disponíveis na Galeria de soluções](log-analytics-add-solutions.md) para adicionar funcionalidades a análise de registo.

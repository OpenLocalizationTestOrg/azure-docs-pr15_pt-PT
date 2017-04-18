<properties 
   pageTitle="Monitorização de comparação de produto do Microsoft | Microsoft Azure"
   description="Microsoft operações de gestão de conjunto de aplicações (OMS) é a nuvem da Microsoft com base solução de gestão de TI que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura.  Este artigo identifica os diferentes serviços incluídos no OMS e fornece ligações para aos respetivos conteúdos detalhadas."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Monitorização comparação de produtos Microsoft

Este artigo fornece uma comparação entre o Gestor de operações de centro do sistema (SCOM) e a análise de registo no conjunto de aplicações de gestão de operações (OMS) em termos da sua arquitetura, a lógica do como monitorizam estes recursos e como se comportam análise dos dados que recolherem.  Isto é para dar-lhe uma fundamental compreensão das suas diferenças e forças relativas.  

## <a name="basic-architecture"></a>Arquitetura básica
### <a name="system-center-operations-manager"></a>Gestor de operações do Centro de sistema

Todos os componentes SCOM estão instalados no seu centro de dados.  [Agentes de estão instalados](http://technet.microsoft.com/library/hh551142.aspx) no Windows e Linux máquinas que são geridos pelo SCOM.  Agentes de ligar aos [Servidores de gestão de](https://technet.microsoft.com/library/hh301922.aspx) comunicar com o armazém de base de dados e dados SCOM.  Agentes dependem de autenticação para ligar aos servidores de gestão de domínios.  Aqueles fora de um domínio fidedigno podem executar a autenticação de certificado de ou ligar a um [Servidor de Gateway](https://technet.microsoft.com/library/hh212823.aspx).

SCOM requer dois bases de dados do SQL, uma para dados operacionais e outro armazém de dados para elaboração de relatórios e análise de dados de suporte.  Um [Servidor de relatórios](https://technet.microsoft.com/library/hh298611.aspx) é executado SQL Reporting Services reportar dados a partir do armazém de dados. 

SCOM pode monitorizar recursos de nuvem utilizando os pacotes de gestão de produtos, como o [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708)e [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Estes packs gestão utilizam um ou mais agentes locais como proxies de identificação de nuvem de recursos e fluxos de trabalho em execução para medir as respetivas desempenho e disponibilidade.  Agentes de proxy também são utilizadas para [Monitorizar dispositivos de rede](https://technet.microsoft.com/library/hh212935.aspx) e outros recursos externos.

A consola de operações é uma aplicação do Windows que liga a um dos servidores de gestão e permite ao administrador ver e analisar os dados recolhidos e configurar o ambiente do SCOM.  Uma consola baseada na web pode ser alojada em qualquer servidor IIS e fornece uma análise de dados através de um browser.

![Arquitetura SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Análise de registo

A maioria dos componentes OMS estão todas na nuvem Azure para que possa implementar e geri-lo com custos mínimos e esforço administrativo.  Todos os dados recolhidos pela análise de registo está armazenado no repositório de OMS.

Análise de registo pode recolher dados de uma das três origens:

- Física e máquinas virtuais executar o Windows e o [Microsoft monitorização agente MMA ()](https://technet.microsoft.com/library/mt484108.aspx) ou Linux e o [Agente de conjunto de aplicações de gestão de operações para Linux](https://technet.microsoft.com/library/mt622052.aspx).  Estes máquinas podem ser no local ou máquinas virtuais no Azure ou noutra na nuvem.
- Uma conta de armazenamento do Windows Azure com dados de [Diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) recolhidos pela função trabalhador Azure, função web ou máquina virtual.
- [Ligação a um grupo de gestão de SCOM](https://technet.microsoft.com/library/mt484104.aspx).  Nesta configuração, os agentes de comunicam com servidores de gestão de SCOM entregam os dados para a base de dados SCOM onde, em seguida, é entregue ao arquivo de dados OMS.
Os administradores de analisar os dados recolhidos e configurar a análise de registo com o portal OMS que está alojado no Azure e pode ser acedido a partir de qualquer browser.  Aplicações móveis para aceder a estes dados estão disponíveis para as plataformas padrão.

![Arquitetura de análise de registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrar o SCOM e a análise de registo

Quando SCOM é utilizado como uma origem de dados para análise de registo pode tirar partido as funcionalidades de ambos os produtos numa híbrida do ambiente de monitorização.  Pode configurar agentes SCOM existentes através da consola de operações a ser gerido pelo OMS, para além de continuar a executar o pacotes de gestão de SCOM.  
Dados a partir de um grupo de gestão de SCOM ligado são fornecidos para a análise de registo utilizando um dos quatro métodos:

- Eventos e dados de desempenho são recolhidos pelo agente e entregue SCOM.  Servidores de gestão de SCOM entregam, em seguida, os dados para a análise de registo.
- Alguns eventos como registos do IIS e eventos de segurança continuam a ser entregue diretamente para o registo de análise do agente de.
- Algumas soluções irão entregar software adicional para o agente ou exigir que software de estar instalado para recolher dados adicionais.  Estes dados normalmente serão enviados diretamente para a análise de registo.
- Algumas soluções irão recolher dados diretamente a partir de servidores de gestão de SCOM que não tenha origem do agente de.  Por exemplo, a [solução de gestão de alerta](https://technet.microsoft.com/library/mt484092.aspx) recolhe alertas de SCOM depois de ter criados.

## <a name="monitoring-logic"></a>Monitorização de lógica
SCOM e a análise de registo de trabalharem com dados semelhantes recolhidos a partir do agentes mas têm diferenças fundamentais de como definir e implementar os respetivos lógica para recolha de dados e como são analisam os dados que eles recolherem.

### <a name="operations-manager"></a>Gestor de operações
Lógica de monitorização para SCOM está implementada [pacotes de gestão](https://technet.microsoft.com/library/hh457558.aspx) de que contêm lógica para descobrir componentes para monitorizar, medir o estado de funcionamento desses componentes e para recolha de dados para analisar.  Monitorizar os dados podem ser tão simples como recolher um evento ou um desempenho contador ou poderia utilizam lógica complexa implementada um script.  Pacotes de gestão que incluem a monitorização concluída estão disponíveis para uma variedade de [Microsoft e aplicações de terceiros](http://go.microsoft.com/fwlink/?LinkId=82105) para além de dispositivos de hardware e de rede.  Pode [criar o seus próprio pacotes de gestão](http://aka.ms/mpauthor) de aplicações personalizadas.

Pacotes de gestão de conter vários fluxos de trabalho que cada executa algumas função distinct monitorização como amostragem um contador de desempenho, verificar o estado de um serviço ou executar um script.  Cada fluxo de trabalho é executado independentemente e define os suas próprias resultados tal como a base de dados-gravará e se irá gerar um alerta. 

Pode substituir os detalhes de fluxo de trabalho como a frequência que são executadas, o limiar de onde são considerarem um erro e gravidade do alerta que geram.  Também pode fornecer funcionalidades adicionais ao adicionar o seus próprio fluxos de trabalho.

![Substitui](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Pacotes de gestão estão instalados da base de dados do Gestor de operações e distribuídos automaticamente a agentes por servidores de gestão.  Cada agente irá pacotes de gestão de transferir e carregar fluxos de trabalho relevantes para as aplicações que essas pessoas têm instalado automaticamente.  Os dados recolhidos pelo agente são entregue novamente para o servidor de gestão de inserção para o armazém de base de dados e dados SCOM.  Consola de operações permite-lhe ver e analisar estes dados através de vistas personalizadas, dashboards e relatórios incluídos no pacote de gestão.

A distribuição dos pacotes de gestão é ilustrada no diagrama seguinte.

![Fluxo de pacote de gestão](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Análise de registo
#### <a name="event-and-performance-collection"></a>Recolha de desempenho e de evento
Análise de registo recolhe eventos e contadores de desempenho de sistemas de agente utilizar origens de registo de eventos do Windows, IIS registos e do sistema.  Pode definir critérios para a qual os dados são recolhidas através do portal de análise de registo e, em seguida, criar consultas de registo para analisar os dados recolhidos.  Um conjunto de critérios padrão é definido quando criar a área de trabalho OMS e pode definir dados adicionais para aplicações específicas. 

![Definir registos dos eventos no registo de análise](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Enquanto SCOM tiver muitos fluxos de trabalho detalhados que normalmente definem critérios específicos dos dados e a ação que deve ser executada em resposta, o registo de análise tem critérios mais gerais para recolha de dados.  Consultas de registo e soluções de fornecer mais nos critérios para analisar e serve no dados específicos na nuvem, depois de serem foram recolhido-lo.

#### <a name="solutions"></a>Soluções
Soluções fornecem lógica adicional para recolha de dados e análise.  Pode selecionar soluções para adicionar à sua subscrição de OMS a partir da Galeria de soluções.

![Galeria de soluções](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

As soluções principalmente executadas na nuvem fornecer análise de eventos e contadores de desempenho recolhidos no repositório de OMS.  Pode definir também dados adicionais para recolhidas que podem ser analisados com registo de consultas ou pela interface de utilizador adicionais fornecida pela solução no dashboard de OMS. 

Por exemplo, a [solução alterações](https://technet.microsoft.com/library/mt484099.aspx) Deteta alterações na configuração nos sistemas de agente e escreve eventos para o repositório OMS que podem ser analisadas com várias vistas de gráficas que resumem detetado alterações.  Pode desagregar a partir da vista resumida para consultas de registo que apresentam os dados detalhados recolhidos pela solução.


Enquanto pode selecionar quais as soluções adicionar à sua subscrição, atualmente não tem a capacidade de criar o seus próprio soluções.  Pode selecionar os eventos e contadores de desempenho para recolher e criar vistas personalizadas com base no seus próprio consultas de registo.

A lógica de monitorização para análise de registo é resumida no diagrama seguinte.

![Fluxo de solução de análise de registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Estado de funcionamento de monitorização
### <a name="operations-manager"></a>Gestor de operações
SCOM pode modelar os diferentes componentes de uma aplicação e fornecer um Estado de funcionamento em tempo real para cada um.  Isto permite-lhe não apenas ver detetado erros e o desempenho ao longo do tempo, mas também para validar o estado de funcionamento real de uma aplicação ou sistema e cada um dos seus componentes a qualquer momento.  Uma vez que compreende pode utilizar os períodos de tempo que está disponível uma aplicação, o motor de estado de funcionamento do SCOM também suporta o nível de serviço acordos (SLA) que analisar e elaborar relatórios sobre a disponibilidade de uma aplicação ao longo do tempo.

Por exemplo, a vista abaixo mostra o estado de funcionamento em tempo real dos motores de base de dados SQL controlado pelo SCOM.  O estado de funcionamento de cada uma das bases de dados para um dos motores de base de dados é apresentado na parte inferior metade da vista.

![Vista de estado](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

O Explorador de estado de funcionamento de um dos motores de base de dados é apresentado abaixo com os monitores que são utilizados para determinar o seu estado de funcionamento geral.  Estes monitores estão definidos no pacote de gestão de SQL e executar em todos os motores de base de dados SQL descobertos pelo SCOM.

![Explorador do Estado de funcionamento](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Componentes em vários sistemas podem ser combinados medir o estado de funcionamento de uma aplicação distribuída.  Isto pode ser particularmente útil para a linha de aplicações empresariais que incluem várias componentes distribuídos.  Pode criar um modelo de que as medidas o estado de funcionamento de cada componente esse rollup para disponibilidade para a aplicação.

Active Directory é um exemplo de um pacote de gestão de que fornece um modelo para analisar os seus componentes distribuídos.  O exemplo de diagrama abaixo mostra o estado de funcionamento do ambiente global e a relação entre florestas, domínios e controladores de domínio.  Cada um dos seguintes componentes inclui sub-componentes e vários monitores semelhantes ao exemplo SQL acima.

![Vista de diagrama SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Análise de registo
OMS não incluir um motor comuns das aplicações do modelo ou medir o seu estado de funcionamento em tempo real.  Soluções individuais podem avalie o estado de funcionamento geral dos serviços determinados com base em dados recolhidos e poderá instalarem lógica personalizada num agente para efetuar uma análise em tempo real.  Uma vez que executar soluções na nuvem com o access para o repositório OMS, muitas vezes podem fornecer uma análise mais aprofundada que é normalmente realizado por pacotes de gestão. 

Por exemplo, as [soluções de avaliação de AD e avaliação de SQL](https://technet.microsoft.com/library/mt484102.aspx) analisar os dados recolhidos e forneça uma classificação para aspetos diferentes do ambiente.  Inclui recomendações para melhorias que podem ser efetuadas para melhorar a disponibilidade e o desempenho do ambiente.

![Solução de avaliação de AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Análise de dados
SCOM e a análise de registo fornecem diferentes funcionalidades para analisar os dados recolhidos.  SCOM tem vistas e Dashboards na consola de operações para analisar dados recentes numa variedade de formatos e relatórios para apresentar dados a partir do armazém de dados no formato tabular.  Análise de registo fornece uma interface e de idioma de consulta registo completo para analisar dados no repositório de OMS.  Quando SCOM é utilizado como uma origem de dados para análise de registo, o repositório inclui os dados recolhidos pelo SCOM para que as ferramentas de análise de registo podem ser utilizadas para analisar dados de ambos os sistemas.

### <a name="operations-manager"></a>Gestor de operações

#### <a name="views"></a>Vistas
Vistas na consola de operações permitem-lhe ver diferentes tipos de dados recolhidos pelas SCOM em diferentes formatos, normalmente tabulares para eventos, alertas e dados de estado e gráficos de dados de desempenho da linha.  Vistas realizar a análise mínima ou consolidação dos dados mas permitem-lhe filtrar de acordo com critérios específicos. 

![Vistas](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Pacotes de gestão normalmente irão fornecer múltiplas vistas de suporte a aplicação ou sistema monitoriza.  Isto pode incluir vistas de estado para os diferentes objetos que o pacote de gestão de detetar, vistas de alertas para problemas detectados e vistas de desempenho para contadores.

Vistas são particularmente adequadas para analisar o estado atual do ambiente incluindo abrir alertas e o estado de funcionamento de sistemas monitorizados e objetos.  Pode Desagregue detalhado evento ou um alerta específico de suporte para poder diagnosticar o problema de raiz de dados de desempenho. Da mesma forma, pode ver o desempenho e o estado de funcionamento do diferentes componentes de uma aplicação para avaliar o respetivo estado de funcionamento atual.

#### <a name="dashboards"></a>Dashboards
Dashboards na consola de operações principalmente trabalhem com os mesmos dados como vistas, mas são mais personalizáveis e podem incluir visualizações mais ricas.  Um conjunto de dashboards padrão estão disponíveis que pode personalizar facilmente para os seus próprios fins.  Também pode utilizar um widget de PowerShell que pode apresentar os dados devolvidos por uma consulta de PowerShell.

![Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Os programadores têm a capacidade de adicionar componentes personalizados a incluírem no respetivos pacotes de gestão de dashboards.  Estes podem ser altamente especializadas para uma aplicação específica, tal como o dashboard no pacote de gestão de SQL apresentado abaixo.  Este dashboard pode também ser utilizado como um modelo para versões personalizados.

![Dashboard SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Relatórios
Relatórios no SCOM analisar dados a partir do armazém de dados no formato tabular.  Pode ser impresso e agendados para entrega automatizada em formatos de ficheiro diferente, incluindo PDF, CSV e Word.  Relatórios de trabalhar com dados a partir do armazém de dados para que fiquem especialmente adequados para análise de tendências de longo prazo.

Pacotes de gestão normalmente irão fornecer relatórios personalizados para uma aplicação específica.  Também pode selecionar a partir de uma biblioteca de relatórios genéricos que pode personalizar para as suas próprias aplicações ou para efetuar análises ad hoc.

Segue-se um relatório de desempenho de exemplo que mostra os dados recolhidos pelo Active Directory Management Pack.

![Relatório](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Análise de registo
Análise de registo tem um [idioma de consulta](https://technet.microsoft.com/library/mt484120.aspx) que pode utilizar para executar a análise dados a partir de várias aplicações sem ter de criar uma vista personalizada ou relatório.  Uma vez que é implementado OMS na nuvem, desempenho das consultas e de análise de dados não estão sujeitos a quaisquer limitações de hardware e pode analisar rapidamente consultas incluindo milhões de registos. 

Consultas na análise de registo também são a base de outras funcionalidades.  Pode guardar uma consulta, exportar os seus resultados para o Excel ou a tenha automaticamente executar a intervalos regulares e gerar um alerta se os seus resultados correspondem aos critérios específicos.  

![Fluxo de consulta de registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Segue-se um exemplo de uma consulta de análise de registo.  Neste exemplo, todos os eventos com "ao" no nome do são devolvidos e agrupados por evento ID.  O utilizador fornece simplesmente a consulta e o registo de análise gera dinamicamente a interface de utilizador para executar a análise.  Selecionar qualquer item na lista irá devolver os dados do evento detalhadas.

![Consulta de registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Para além de fornecer análises ad hoc, consultas na análise de registo podem ser guardadas para utilização futura e também adicionadas ao [OMS dashboard](http://technet.microsoft.com/library/mt484090.aspx) , conforme mostrado no exemplo seguinte.

![Dashboard OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Próximos passos

- Implemente o [Gestor de operações do Centro de sistema (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
- Inscreva-se para [A análise de registo](https://azure.microsoft.com/documentation/services/log-analytics).  

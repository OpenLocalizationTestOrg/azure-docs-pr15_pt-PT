<properties 
   pageTitle="Gestão de no Microsoft monitorizar os produtos de alerta | Microsoft Azure"
   description="Um alerta indica algum problema de que necessita de atenção a partir de um administrador.  Este artigo descreve as diferenças nos como alertas são criados e geridos no Gestor de operações de centro do sistema (SCOM) e a análise de registo e fornece os procedimentos recomendados na tirar partido dos dois produtos para uma estratégia de gestão de alerta de híbrido." 
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
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Gerir alertas com Microsoft de monitorização 

Um alerta indica algum problema de que necessita de atenção a partir de um administrador.  Existem diferenças distintas entre o Gestor de operações de centro do sistema (SCOM) e a análise de registo no conjunto de aplicações de gestão de operações (OMS) em termos como alertas são criados, como são geridas e analisadas e como o utilizador será notificado que foi detectado um problema crítico.

## <a name="alerts-in-operations-manager"></a>Alertas no Gestor de operações
Alertas no SCOM são geradas por regras individuais ou monitores para indicar um problema em específico.  Um monitor pode gerar um alerta quando-introduz um Estado de erro enquanto uma regra pode gerar um alerta para indicar algum problema crítico que não é diretamente relacionados com o estado de funcionamento de um objecto gerido.  Pacotes de gestão incluem uma variedade de fluxos de trabalho que criar alertas para a aplicação ou serviço que gerir.  Parte do processo de configurar um novo pacote de gestão é de sintonização-a para assegurar que não receber alertas excessivos para problemas que não considere crítica.

![Vista de alerta de SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM fornece gestão de alerta completa com os alertas está a ter um Estado que pode ser alterado pelos administradores à medida que trabalham para resolver o problema.  Quando tiver sido resolvido o problema, o administrador define o alerta para fechado momento em que já não irá aparecer nas vistas de apresentação alertas ativas.  Alertas que são gerados a partir de monitores podem ser resolvidos automaticamente quando o monitor devolve a um estado Saudável.

![Alerta de estado](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertas no registo Analytics
Um alerta no registo de análise é criado a partir de uma consulta de registo que é executada automaticamente a intervalos regulares.  Pode criar uma regra de alerta de consulta qualquer registo.  Se a consulta devolve os resultados que correspondem aos critérios que especificar, em seguida, será criado um alerta.  Isto pode ser uma consulta específica que cria um alerta se é detetado um determinado evento ou pode utilizar uma consulta mais geral que procura um evento de erro relacionado com uma aplicação específica.

Inicie sessão Analytics alertas estão escritas para o repositório OMS como um evento e podem ser obtidas com uma consulta de registo.  Estas não têm um Estado como SCOM eventos para que pode indicar quando tiver sido resolvido o problema.

![Alerta OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Quando SCOM utilizado como uma origem de dados de análise de registo, alertas SCOM são escritas para o repositório OMS como são criados e modificados.  

![Alerta SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

A [solução de gestão de alerta](http://technet.microsoft.com/library/mt484092.aspx) fornece um resumo dos alertas ativas e várias consultas comuns para obter diferentes conjuntos de alertas.  Isto fornece análise mais eficaz dos alertas do que um relatório no SCOM.  Pode desagregar de resumos para dados detalhados e criar consultas ad hoc para obter diferentes conjuntos de alertas.

![Solução de gestão de alerta](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notificações
Notificações no SCOM enviarem-lhe um correio ou o texto em resposta a alertas que correspondem aos critérios específicos.  Pode criar subscrições de notificação diferente que têm diferentes pessoas notificadas dependendo critérios tais como o objeto a ser monitorizadas, gravidade do alerta, o tipo de problema detetado ou a hora do dia.

Subscrições alguns podem ser utilizadas para implementar uma estratégia de notificação completa para um grande número de pacotes de gestão.

![Alertas SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Análise de registo pode notificá-lo através de correio que foi criado um alerta definindo uma ação de notificação de correio eletrónico em cada [regra de alerta](http://technet.microsoft.com/library/mt614775.aspx).  Não tem a capacidade de SCOM subscrever para múltiplos alertas com uma única regra.  Também precisa de criar as suas próprias regras alertas, uma vez que OMS não fornece qualquer pré-configuradas automaticamente.

![Alertas de análise de registo](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Não consegue completamente gerir alertas SCOM no registo de análise embora uma vez que apenas pode modificá-los na consola de operações.  Análise de registo é útil como parte de uma alerta de gestão do processo apesar para fornecer ferramentas de análise que SCOM sozinho não têm.

## <a name="alert-remediation"></a>Alerta remediação
[Remediação](http://technet.microsoft.com/library/mt614775.aspx) que se refere a uma tentativa para corrigir o problema identificado por um alerta automaticamente.
  
SCOM permite-lhe executar de diagnóstico e Cobranças em resposta a um monitor introduzir mau estado de funcionamento.  Isto acontece simultânea para o monitor de criar o alerta.  De diagnóstico e Cobranças normalmente são implementadas como um script que é executada no agente.  Tentativas de diagnóstico para recolher mais informações sobre o problema detectado enquanto uma recuperação tentativas para corrigir o problema.

Análise de registo permite-lhe iniciar um [livro de execuções do Azure automatização](https://azure.microsoft.com/documentation/services/automation/) ou uma chamada um webhook em resposta a um alerta de análise de registo.  Runbooks podem conter lógica complexa implementada no PowerShell.  O script é executado no Azure e pode aceder a qualquer recursos Azure ou recursos externos disponíveis a partir da nuvem.  Automatização Azure tiver capacidade de executar runbooks num servidor no seu centro de dados local, mas esta funcionalidade não está disponível neste momento ao iniciar o livro de execuções em resposta a alertas de análise de registo.

Ambos reembolsos no SCOM e runbooks no OMS podem conter scripts de PowerShell, mas reembolsos são mais difícil criar e gerir uma vez que tem de ser contidos dentro de um pacote de gestão.  Runbooks são armazenadas no Azure automatização que fornece funcionalidades para criação, testar e gerir runbooks.

Se utilizar SCOM como uma origem de dados para análise de registo, podia criar um alerta de análise de registo utilizando uma consulta de registo para obter alertas SCOM armazenadas no repositório de OMS.  Isto permite-lhe executar um livro de execuções do Azure automatização em resposta a um alerta de SCOM.  Obviamente, uma vez que o livro de execuções será executado no Azure, este não seria uma estratégia viável de reembolsos dos problemas no local.

## <a name="next-steps"></a>Próximos passos

- Obter informações sobre os detalhes de [alertas no Gestor de operações de centro do sistema (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).
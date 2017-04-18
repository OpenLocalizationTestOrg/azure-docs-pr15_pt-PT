<properties
    pageTitle="Solução de avaliação de configuração no registo de análise | Microsoft Azure"
    description="A solução de avaliação de configuração no registo de análise fornece informações detalhadas sobre o estado atual da sua infraestrutura de servidor do Gestor de operações do Centro de sistema quando utilizar o Gestor de operações agentes ou um grupo de gestão do Gestor de operações."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="configuration-assessment-solution-in-log-analytics"></a>Solução de avaliação de configuração no registo de análise

A solução de avaliação de configuração no registo de análise ajuda-o a localizar potenciais problemas de configuração de servidor através de alertas e recomendações de dados de conhecimento.

Esta solução requer o Gestor de operações do Centro de sistema. Avaliação de configuração não está disponível se utilizar apenas agentes directamente ligados.

Ver algumas informações numa solução de configuração de avaliação requer o plug-in do Silverlight para o seu browser.

>[AZURE.NOTE] Início de Julho de 5 de 2016, já não pode ser adicionada a solução de avaliação de configuração para áreas de trabalho de análise de registo e esta solução deixará de estar disponível para utilizadores existentes após 1 de Agosto de 2016. Para clientes com esta solução para SQL Server ou do Active Directory, recomendamos que utilize em vez disso, o [SQL Server avaliação](log-analytics-sql-assessment.md), soluções de [Avaliação do Active Directory](log-analytics-ad-assessment.md) e o [Estado de replicação do Active Directory](log-analytics-ad-replication-status.md) . Para clientes que utilizam avaliação de configuração para Windows, Hyper-V e o Gestor de Máquina Virtual do Centro de sistema, recomendamos que utilize a coleção de eventos e registo de alterações capacidades para obter uma vista holística dos problemas no seu ambientes.

![Mosaico da avaliação de configuração](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Dados de configuração são reuniu do servidores monitorizados e, em seguida, enviados para o serviço OMS na nuvem para processamento. Lógica é aplicada aos dados recebidos e o serviço de nuvem regista os dados. Dados processados para os servidores de são mostrados para as seguintes áreas:

- **Alertas:** Mostra os alertas relacionados com a configuração, pro-activos levantadas para os servidores de monitorizada. Estas são produzidas por regras criadas por Customer da Microsoft e organização de suporte (CSS) com as melhores práticas do campo.
- **Recomendações de dados de conhecimento:** Mostra os artigos da Base de dados de conhecimento da Microsoft que lhe foram recomendados das cargas de trabalho que sejam encontram na sua infraestrutura; Estes são automaticamente sugeridos com base na sua configuração guiada a utilização de aprendizagem automática.
- **Servidores e das cargas de trabalho analisadas:** Mostra os servidores e das cargas de trabalho que estão a ser monitorizadas pelo OMS.

![Dashboard de avaliação de configuração](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>Tecnologias que pode analisar com avaliação de configuração

Avaliação de configuração de OMS analisa as cargas de trabalho seguintes:

- Windows Server 2012 e o Microsoft Hyper-V Server 2012
- Windows Server 2008 e Windows Server 2008 R2, incluindo:
    - O Active Directory
    - Anfitrião Hyper-V
    - Gerais do sistema operativo
- SQL Server 2008 e posterior
    - Motor de base de dados do SQL Server
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 e o Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 e o Lync Server 2010
- Sistema Centro 2012 SP1 – Virtual Machine Manager

Para o SQL Server, as seguintes edições de 32 bits e 64 bits são suportadas para uma análise:

- SQL Server 2016 - todas as edições
- SQL Server 2014 - todas as edições
- SQL Server 2008 e 2008 R2 - todas as edições

Motor de base de dados do SQL Server é analisado em todas as edições suportadas. Além disso, a edição de 32 bits do SQL Server é suportada quando a ser executado na aplicação WOW64.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

- Gestor de operações é necessário para a solução de avaliação de configuração.
- Tem de ter um agente do Gestor de operações em todos os computadores em que pretende avaliar a sua configuração.
- Adicione a solução de avaliação de configuração para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.

## <a name="configuration-assessment-data-collection-details"></a>Detalhes de recolha de dados de avaliação de configuração

Configuração avaliação recolhe dados de configuração, metadados e dados de estado utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para a configuração de avaliação.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![N](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![N](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|            ![Sim](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| duas vezes por dia|

A tabela seguinte mostra exemplos dos tipos de dados recolhidos pela configuração de avaliação:

|**Tipo de dados**|**Campos**|
|---|---|
|Configuração|IDCliente, AgentID, ID da entidade, ManagedTypeID, ManagedTypePropertyID, Valoratual, ChangeDate|
|Metadados|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NomeRede, endereço IP, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, endereço IP, NetbiosDomainName, LogicalProcessors, NomeDNS, DisplayName em grupos, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Estado|StateChangeEventId, StateId, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, última modificação, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="configuration-assessment-alerts"></a>Alertas de avaliação de configuração
Pode ver e gerir alertas a partir da avaliação de configuração com a página de alertas. Alertas indicam-lhe o problema que foi detetado, a causa e como resolver o problema. Também fornecem informações sobre as definições de configuração no seu ambiente que podem causar problemas de desempenho.

![Vista de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] Os alertas de avaliação de configuração são diferentes a partir dos outros alertas na análise de registo. Ver alertas requer um plug-in do Silverlight para o seu browser.

Quando selecionar um item ou uma categoria de itens na página alertas, verá uma lista de servidores ou das cargas de trabalho com os alertas que se aplicam a cada item.

![lista de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Cada alerta inclui uma ligação para um artigo na Base de dados de conhecimento da Microsoft. Estes artigos fornecem informações adicionais sobre o alerta.

>[AZURE.TIP] Por predefinição, o número máximo de alertas apresentados é 2.000. Para ver mais alertas, clique na barra de notificação acima da lista de alertas.

Pode clicar em qualquer item na lista para ver o artigo BDC que poderão ajudá-lo a resolver o problema do problema que gerou o alerta.

![Artigo BDC](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Pode gerir regras de alertas para ignorar regras específicas ou um classes das regras.

![Gerir regras de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>Dados de conhecimento recomendações
Quando visualiza recomendações de dados de conhecimento, ser-lhe está a apresentadas resultados de pesquisa de registo listar os artigos Microsoft KB recomendados para as cargas de trabalho e computadores que fornecem informações adicionais sobre o alerta.

![resultados da pesquisa para obter recomendações de dados de conhecimento](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>Servidores e das cargas de trabalho analisadas
Quando visualiza recomendações de dados de conhecimento, ser-lhe está a apresentadas listar todos os servidores e das cargas de trabalho são conhecidas por OMS do Gestor de operações de resultados de pesquisa de registo.

![Servidores e das cargas de trabalho](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo Analytics](log-analytics-log-searches.md) para ver dados da avaliação detalhado da configuração.

<properties
    pageTitle="Solução de avaliação de atualização do sistema no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução actualizações do sistema no registo Analytics para ajudá-lo a aplicar atualizações em falta para servidores na sua infraestrutura."
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="system-update-assessment-solution-in-log-analytics"></a>Solução de avaliação de atualização do sistema no registo de análise

Pode utilizar a solução actualizações do sistema no registo Analytics para ajudá-lo a aplicar atualizações em falta para servidores na sua infraestrutura. Depois de instalar a solução, pode ver as atualizações que estão em falta de servidores monitorizados utilizando o mosaico de **Avaliação de atualização do sistema** na página **Descrição geral** na OMS.

Se forem encontradas atualizações em falta, detalhes são apresentados no dashboard de **atualizações** . Pode utilizar o dashboard de **atualizações** para trabalhar com actualizações em falta e desenvolver um plano para aplicam-se aos servidores que precisar deles.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

- Adicione a solução de avaliação de atualização do sistema para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.

## <a name="system-update-data-collection-details"></a>Detalhes da coleção de sistema atualizar dados

Avaliação de atualização do sistema recolhe metadados e dados de estado utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para avaliação de atualização do sistema.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-system-update/oms-bullet-green.png)|![Sim](./media/log-analytics-system-update/oms-bullet-green.png)|![N](./media/log-analytics-system-update/oms-bullet-red.png)|            ![N](./media/log-analytics-system-update/oms-bullet-red.png)|![Sim](./media/log-analytics-system-update/oms-bullet-green.png)| Pelo menos de 2 vezes por dia e 15 minutos depois de instalar uma atualização|

A tabela seguinte mostra exemplos dos tipos de dados recolhidos pelo avaliação de atualização do sistema:

|**Tipo de dados**|**Campos**|
|---|---|
|Metadados|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NomeRede, endereço IP, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, endereço IP, NetbiosDomainName, LogicalProcessors, NomeDNS, DisplayName em grupos, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Estado|StateChangeEventId, StateId, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, última modificação, LastGreenAlertGenerated, DatabaseTimeModified|


### <a name="to-work-with-updates"></a>Para trabalhar com as atualizações

1. Na página **Descrição geral** , clique no mosaico de **Avaliação de atualização do sistema** .  
    ![Mosaico da avaliação de atualização de sistema](./media/log-analytics-system-update/sys-update-tile.png)
2. No dashboard de **atualizações** , ver as categorias de atualização.  
    ![Dashboard de atualizações](./media/log-analytics-system-update/sys-updates02.png)
3. Desloque-se para a direita da página para ver o pá **Atualizações críticas/segurança do Windows** e, em seguida, em **classificação**, clique em **Atualizações de segurança**.  
    ![Atualizações de segurança](./media/log-analytics-system-update/sys-updates03.png)
4. Na página de pesquisa de registo, é apresentada uma variedade de informações sobre as atualizações de segurança que foram encontradas em falta de servidores na sua infraestrutura. Clique em **lista** para ver informações detalhadas sobre as atualizações.  
    ![Resultados da pesquisa - lista](./media/log-analytics-system-update/sys-updates04.png)
5. Na página de pesquisa de registo, informações detalhadas sobre cada atualização são apresentadas. Junto a número KBID, clique em **vista** para ver o artigo correspondente no site Web de suporte da Microsoft.  
    ![Inicie sessão resultados da pesquisa - KB de vista](./media/log-analytics-system-update/sys-updates05.png)
6. Browser da web é aberta a página Web de suporte da Microsoft para a atualização num novo separador. Ver as informações sobre a atualização que está em falta.  
    ![Página de web do Microsoft Support](./media/log-analytics-system-update/sys-updates06.png)
7. Utilizar o utilizando as informações de ter encontrado, pode criar um plano para aplicar manualmente a atualização em falta ou pode continuar a seguir os restantes passos para aplicar automaticamente a atualização.
8. Se pretender aplicar automaticamente a atualização em falta, regresse ao dashboard de **atualizações** e em **Atualizar é executada**, clique em **clique aqui para agendar uma atualização executar**.  
    ![Será executado de atualização - separador agendada](./media/log-analytics-system-update/sys-updates07.png)
9. Na página **Update é executada** no separador **agendada** , clique em **Adicionar** para criar um novo executar de atualização.  
    ![Agendado separador - Adicionar](./media/log-analytics-system-update/sys-updates08.png)
10. Na página **Novo atualizar executar** , escreva um nome para a atualização executar, adicionar computadores individuais ou grupos, definir uma agenda e, em seguida, clique em **Guardar**.  
    ![Novo executar de actualização](./media/log-analytics-system-update/sys-updates09.png)
11. No separador **agendada** na página de **Atualizar será executado** mostra a nova actualização executar que tenha agendada.  
    ![Separador agendada](./media/log-analytics-system-update/sys-updates10.png)
12. Quando inicia a atualização executar, verá informações para o mesmo no separador **em execução** .  
    ![Separador em execução](./media/log-analytics-system-update/sys-updates11.png)
13. Após a atualização executar for concluída, o separador de **concluídas** apresenta o estado.
14. Se as atualizações foram aplicadas a partir da atualizar a executar, na pá **Atualizações críticas/segurança do Windows** , verá que o número de atualizações é reduzido.  
    ![Pá atualizações críticas/segurança do Windows - reduzida de contagem de actualização](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>Próximos passos

- [Registos de pesquisa](log-analytics-log-searches.md) para ver sistema detalhadas atualizar dados.

<properties
    pageTitle="Ver eventos e registos de auditoria"
    description="Saiba como ver todos os eventos que ocorrem na sua subscrição do Azure."
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>Ver eventos e registos de auditoria

Todas as operações efetuadas no Azure recursos são totalmente auditadas pelo Azure Gestor de recursos, a partir de criação e eliminações para conceder ou revogar o acesso. Pode procurar estes registos no portal do Azure e também pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para aceder ao conjunto completo de eventos através de programação.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Procure os eventos que afetam a sua subscrição do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar** e selecione **registos de auditoria**.  
    ![Navegue até concentrador](./media/insights-debugging-with-events/Insights_Browse.png)
3. Isto vai abrir para cima uma pá a mostrar todos os eventos que tenham afetada qualquer uma das suas subscrições dos últimos 7 dias. Na parte superior é um gráfico que mostra dados por nível e, por baixo do que é a lista completa dos registos de início:  ![todos os eventos](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] Só podem ver os 500 eventos mais recentes para uma subscrição determinado no portal do Azure.

4. Pode clicar em qualquer entrada do registo para ver os eventos que constituídos por-lo. Por exemplo, quando implementar algo um grupo de recursos, muitos recursos diferentes poderão ser criou ou modificou. Para cada entrada pode ver:
    * O **nível** de evento - por exemplo,-pode ser apenas uma mensagem para controlar (**informativo**) ou quando algo desaparece mal-se de que precisa de saber sobre (**erro**).
    * O **Estado** - o estado final geralmente será **bem sucedida** ou **falhou**, mas também pode ser **aceite** para operações de execução longa.
    * *Quando* o evento ocorreu.
    * *Quem* efetuada a operação se qualquer pessoa. Nem todas as operações são executadas por utilizadores, algumas são executadas por back-end serviços para que não teria um **autor da chamada**.
    * O **ID de correlação** do evento - este é o identificador exclusivo para este conjunto de operações.

5. A partir desse local pode aceder à pá detalhes para ver os detalhes do evento.

    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Para eventos **falhou** , esta página normalmente inclui um **subestado** e uma secção de **Propriedades** que incluem detalhes útil para fins de depuração.

## <a name="filter-to-specific-logs"></a>Filtrar registos específicos

Para poder ver os eventos que se aplicam para uma entidade específica ou de um tipo específico, pode filtrar a pá de registos de auditoria clicando no comando de **filtro** . Também é utilizar a pá de filtro para alterar o **tempo do intervalo** de pá de registos de auditoria.

Ao clicar neste comando, será aberto um novo pá:

![Filtro](./media/insights-debugging-with-events/Insights_EventFilter.png)

Existem quatro tipos de filtros:

1. Através de uma subscrição
2. Por um **grupo de recursos**
3. Por um **tipo de recurso**
4. Por um determinado **recurso** - para este tem de anteriores no *ID do recurso* completo do recurso que lhe interessam

Além disso, também pode filtrar eventos ao quem executou o evento ou, ao nível do evento.

Uma vez tiver concluído a escolher o que pretende ver, clique no botão **Atualizar** na parte inferior da pá.

## <a name="monitor-events-impacting-specific-resources"></a>Monitorizar eventos que afetam recursos específicos

1. Clique em **Procurar** para localizar o recurso que lhe interessam. Também pode ver todos os registos para todo um **grupo de recursos**.
2. No pá do recurso, desloque-se para baixo até encontrar o mosaico de **eventos** .  
    ![Mosaico de eventos](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Clique no que mosaico para ver os eventos filtrados para apenas o recurso que selecionou. Pode utilizar o comando **Filtrar** para alterar o intervalo de tempo ou aplicar filtros mais específicos.

## <a name="next-steps"></a>Próximos passos

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que um evento acontece.
* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.
* [Estado de funcionamento do serviço de pista](insights-service-health.md) para saber quando Azure Ocorreu interrupções degradação ou serviço de desempenho.  

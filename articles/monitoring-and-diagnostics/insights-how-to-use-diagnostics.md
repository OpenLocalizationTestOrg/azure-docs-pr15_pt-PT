<properties
    pageTitle="Ativar monitorização e diagnósticos de no Microsoft Azure | Microsoft Azure "
    description="Saiba como configurar o diagnósticos para os recursos no Azure."
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

# <a name="enable-monitoring-and-diagnostics"></a>Activar a monitorização e diagnóstico

No [Portal do Azure](https://portal.azure.com), pode configurar avançada, frequente, monitorização e dados de diagnóstico sobre os recursos. Também pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar diagnóstico através de programação.

Dados de diagnóstico, monitorização e métrica no Azure são guardados para uma conta de armazenamento da sua escolha. Esta opção permite-lhe utilizar qualquer ferramentas que pretende lerem os dados, a partir do Explorador de armazenamento, ao Power BI para ferramentas de terceiros.

## <a name="when-you-create-a-resource"></a>Quando cria um recurso

A maioria dos serviços permitem-lhe ativar diagnósticos quando criar primeiro no [Portal do Azure](https://portal.azure.com).

1. Ir para **Novo** e selecione o recurso que lhe interessam.

2. Selecione **configuração opcional**.
    ![Pá de diagnóstico](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Selecione **Diagnósticos**e clique **em**. É necessário escolher a conta de armazenamento que pretende que o diagnóstico para serem guardados. Vai ser cobrada taxas de dados normal para armazenamento e transações quando envia diagnósticos para uma conta de armazenamento.

4. Clique em **OK** e criar o recurso.

## <a name="change-settings-for-an-existing-resource"></a>Alterar as definições de um recurso existente

Se já tiver criado um recurso e que pretende alterar as definições de diagnósticos (para alterar o nível de recolha de dados, por exemplo), que pode fazer essa diretamente no Portal do Azure.

1. Vá para o recurso e clique no comando **Definições** .

2. Selecione **Diagnósticos**.

3. O pá **Diagnósticos** tem todos os diagnósticos possíveis e monitorização de recolha de dados para esse recurso. Para alguns recursos também pode escolher uma política de **retenção** para os dados, de limpar da sua conta de armazenamento.
    ![Diagnósticos do armazenamento](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Uma vez que escolheu as definições, clique no comando **Guardar** . Poderá demorar um pouco enquanto dos dados de monitorização para ser apresentada se estiver a activá-la pela primeira vez.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Categorias de recolha de dados para máquinas virtuais
Para máquinas virtuais todos os registos e métricas serão registados em intervalos de um minuto, pelo que pode tem sempre as informações mais atualizadas sobre o seu computador.

- **Métricas básicas** : métricas de estado de funcionamento sobre máquina como processador e de memória virtual
- **Métricas de rede e da web** : métricas sobre as suas ligações de rede e serviços web
- **Métricas .NET** : métricas sobre as aplicações .NET e ASP.NET em execução no seu computador virtual
- **Métricas SQL** : Se estiver a executar o Microsoft SQL serviço, respetiva métricas de desempenho
- **Registos de aplicação de eventos do Windows** : eventos do Windows que são enviados para o canal de aplicação
- **Registos de sistema de eventos do Windows** : eventos do Windows que são enviados para o canal de sistema. Isto também inclui todos os eventos do [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Registos de segurança de eventos do Windows** : eventos do Windows que são enviados para o canal de segurança
- **Registos de infraestrutura de diagnóstico** : registo sobre a infraestrutura de coleção de diagnóstico
- **Registos do IIS** : registos de servidor do IIS

Repare que neste momento determinadas distribuições de Linux não são suportadas e, tem de estar instalado o agente de convidado na máquina virtual.

## <a name="next-steps"></a>Próximos passos

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que eventos operacionais ocorrem ou métricas cruzada um determinado limiar.
* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.
* [Dimensionar automaticamente a contagem de instâncias](insights-how-to-scale.md) para se certificar de que a escala de serviço baseada na procura.
* [Monitorizar o desempenho de aplicação](../application-insights/app-insights-azure-web-apps.md) se pretender compreender exatamente como o código está a executar na nuvem.
* [Ver eventos e registos de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Estado de funcionamento do serviço de pista](insights-service-health.md) para saber quando Azure Ocorreu interrupções degradação ou serviço de desempenho.

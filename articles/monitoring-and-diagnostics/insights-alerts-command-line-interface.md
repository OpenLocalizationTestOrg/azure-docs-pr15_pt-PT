<properties
    pageTitle="Utilizar a Interface de linha de comandos em diferentes plataformas (CLI) para criar alertas para serviços Azure | Microsoft Azure"
    description="Utilize a interface de linha de comandos para criar alertas Azure, que podem accionar notificações ou automatização quando as condições especificadas forem cumpridas."
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>Utilizar a Interface de linha de comandos em diferentes plataformas (CLI) para criar alertas para serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLIP](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como configurar alertas Azure utilizando a Interface de linha de comandos (CLI).

>[AZURE.NOTE] Azure Monitor é o novo nome para o que era denominado "Azure informações" até 25 de Setembro de 2016. No entanto, os espaços de nomes e, consequentemente, os comandos abaixo ainda contenham "informações".

Poderá receber um alerta com base no métricas de monitorização para ou eventos no seus serviços Azure.

- **Métricas valores** - os accionadores alertas quando o valor de uma métrica especificado este se cruza um determinado limiar que atribuir em qualquer direção. Isto é, accionar ambos quando a condição é cumprida pela primeira vez e, em seguida, posteriormente quando o condição é já não está a ser cumprida.    
- **Eventos de registo de atividade** - um alerta pode acionar *todos os* eventos, ou, apenas quando ocorre um determinado número de eventos.

Pode configurar um alerta para fazer o seguinte quando accionar:

- enviar notificações de correio eletrónico para o administrador de serviços e coadministradores
- Envie e-mail a mensagens de correio eletrónico adicionais que especificar.
- ligar uma webhook
- começar a execução de um livro de execuções Azure (apenas a partir do portal Azure neste momento)

Pode configurar e obter informações sobre regras de alertas utilizando

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de comandos (CLI)](insights-alerts-command-line-interface.md)
- [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Pode sempre recebe ajuda para comandos ao escrever um comando e colocando - ajuda no final. Por exemplo:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Criar regras de alertas utilizando o clip

1. Execute a pré-requisitos e iniciar sessão no Azure. Consulte o artigo [Azure Monitor Clip amostras](insights-cli-samples.md). Resumindo, instale o clip e executar estes comandos. Estes obter que tem sessão iniciada, mostrar qual a subscrição que está a utilizar e prepará-lo para executar comandos Azure Monitor.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Para listar regras existentes num grupo de recursos, utilize o formulário seguintes **informações azure alertas regra lista** *[opções] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Para criar uma regra, tem de ter várias partes importantes de informações pela primeira vez.
    - O **ID do recurso** para o recurso que pretende definir um alerta para
    - As **definições de métricas** disponível para esse recurso

    Uma forma de obter o ID do recurso é utilizar o portal Azure. Assumindo que o recurso já tiver sido criado, selecione-o no portal. Em seguida, na pá seguinte, selecione *Propriedades* na secção *Definições* . O *ID do recurso* é um campo a pá seguinte. Outra forma é utilizar o [Azure recurso Explorer](https://resources.azure.com/).

    É um id do recurso de exemplo para uma aplicação web

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Para obter uma lista das disponível métrica e unidades para esses métricas para que o exemplo anterior do recurso, utilize o seguinte comando clip:  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ é a granularidade da medição disponível (intervalos de 1 minuto). Utilizar diferentes granularidades devem ser dá-lhe opções métricas diferentes.


4. Para criar uma regra com base em métrica alerta, utilize um comando da seguinte forma:

    **métrica de regra de alertas de informações Azure definida** *[opções] &lt;Nomedaregra&gt; &lt;localização&gt; &lt;resourceGroup&gt; &lt;Tamanhodajanela&gt; &lt;operador&gt; &lt;limiar&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    O exemplo seguinte configura um alerta num recurso de web site. Alertas accionadores sempre que qualquer tráfego de forma consistente receba para 5 minutos e novamente quando recebe sem o tráfego para 5 minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Para criar webhook ou enviar correio eletrónico quando um alerta é acionada, crie primeiro o e-mail e/ou webhooks. Em seguida, crie a regra imediatamente posteriormente. Não pode associar webhook ou e-mails com já tiver criado utilizando o clip de regras.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Para criar um alerta que é acionada numa condição específica no registo de atividade, utilize o formulário:

    **regra de alertas de informações registar conjunto** *[opções] &lt;Nomedaregra&gt; &lt;localização&gt; &lt;resourceGroup&gt; &lt;operationName&gt; *

    Por exemplo

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    O operationName corresponde a um tipo de evento para uma entrada no registo de atividade. Alguns exemplos incluem *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    Pode utilizar o comando do PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) para obter uma lista de possíveis operationNames. Em alternativa, pode utilizar o portal do Azure para o registo de atividade de consulta e localizar específico anteriores operações ao qual pretende criar um alerta para. As operações mostradas na vista de gráfico registo amigável nomes. Procurar JSON para a entrada e separar o valor de OperationName.   

7. Pode verificar que foram criados os alertas do corretamente verificando numa regra individual.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Para eliminar regras, utilize um comando do formulário:

    **Eliminar a regra de alertas de informações** [opções] &lt;resourceGroup&gt; &lt;Nomedaregra&gt;

    Estes comandos eliminam as regras criadas anteriormente neste artigo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Próximos passos

* [Obter uma descrição geral da monitorização Azure](monitoring-overview.md) incluindo os tipos de informação podem recolher e monitorizar.
* Saiba mais sobre [Configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks de automatização do Azure](..\automation\automation-starting-a-runbook.md).
* Obter uma [Descrição geral de recolher registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recolher detalhadas métricas de alta frequência no seu serviço.
* Obter uma [Descrição geral da coleção de métricas](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.

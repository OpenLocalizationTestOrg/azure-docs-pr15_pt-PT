<properties 
   pageTitle="Monitorizar métricas e registos de acesso e o desempenho do Gateway aplicação | Microsoft Azure"
   description="Saiba como ativar e gerir os registos de acesso e o desempenho do Gateway de aplicação"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Registo de diagnóstico e métricas para Gateway de aplicação

Azure fornece a capacidade para monitorizar recursos com o registo e métricas

[**Registo**](#enable-logging-with-powershell) - permite a funcionalidade de registo de desempenho, acesso e outros registos a ser guardadas ou consumidas a partir de um recurso para fins de monitorização.

[**Métricas**](#metrics) - gateway de aplicação atualmente tem uma métrica. Esta métrica medidas débito do gateway aplicação em Bytes por segundo.

Pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas de gateways de aplicação. Algumas destes registos podem ser acedidas através do portal e todos os registos podem ser extraídos de um armazenamento de Blobs do Azure e viu no ferramentas diferentes, tal como [A análise de registo](../log-analytics/log-analytics-azure-networking-analytics.md), o Excel e obter. Pode obter mais informações sobre os diferentes tipos de registos na seguinte lista:

- **Registos de auditoria:** Pode utilizar os [Registos de auditoria Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecido como registos operacionais) para ver todas as operações a ser submetidas a sua subscrição do Azure e o respetivo estado. Registos de auditoria estiverem ativados por predefinição e podem ser visualizados no portal do Azure pré-visualização.
- **Aceder a registos de início:** Pode utilizar este registo para ver o padrão de acesso de gateway de aplicação e analisar informações, incluindo IP do autor da chamada, URL pedido, latência da resposta, devolvem código, bytes e reduzir importantes. Registo de acesso é recolhido cada 300 segundos. Este registo contém um registo por instância do gateway de aplicação. A instância da aplicação gateway pode ser identificada por propriedade 'instanceId'.
- **Registos de desempenho:** Pode utilizar este registo para ver como instâncias da aplicação de gateway estão a executar. Este registo para capturar informações sobre o desempenho per instância trimestralmente incluindo pedido total servido, débito em bytes, total de pedidos servidas, pedido falhado contagem, contar saudável e danificado instância de back-end. Registo de desempenho é recolhido cada 60 segundos.
- **Da firewall registos:** Pode utilizar este registo para ver os pedidos de que tem sessão iniciados através do modo de deteção ou prevenção de um gateway de aplicação que está configurado com uma firewall de aplicação web.

>[AZURE.WARNING] Registos só estão disponíveis para recursos implementados no modelo de implementação de Gestor de recursos. Não pode utilizar os registos para recursos no modelo de implementação clássica. Para compreender melhor de dois modelos, referenciar o artigo [Noções sobre o Gestor de recursos de implementação e implementação clássica](../resource-manager-deployment-model.md) .

## <a name="enable-logging-with-powershell"></a>Ativar o registo com o PowerShell

Registo de auditoria está ativado automaticamente para todos os recursos de Gestor de recursos. Tem de ativar o acesso e o desempenho registo para iniciar a recolha de dados disponíveis através desses registos. Para ativar o registo, consulte os seguintes passos: 

1. Tenha em atenção ID do recurso da sua conta de armazenamento, onde os dados do registo estão armazenados. Este seria do formulário: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>. Pode ser utilizada qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal de pré-visualização para encontrar estas informações.

    ![Pré-visualizar portal - diagnósticos do Gateway de aplicação](./media/application-gateway-diagnostics/diagnostics1.png)

2. Note ID do recurso seu gateway de aplicação para o registo de que está a ser ativado. Este seria do formulário: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nome da aplicação do gateway\>. Pode utilizar o portal de pré-visualização para encontrar estas informações.

    ![Pré-visualizar portal - diagnósticos do Gateway de aplicação](./media/application-gateway-diagnostics/diagnostics2.png)

3. Ativar o registo de diagnóstico utilizando o seguinte cmdlet do powershell:

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] registos de auditoria não necessita de uma conta de armazenamento em separado. A utilização de armazenamento para aceder e registo do desempenho tarifas de serviço.

## <a name="enable-logging-with-azure-portal"></a>Ativar o registo com o Azure portal

### <a name="step-1"></a>Passo 1

Navegue até ao seu recurso no portal do Azure. Clique em **registos de diagnóstico**. Se esta for a primeira vez configurar diagnósticos a pá aspeto a imagem seguinte:

Para o gateway de aplicação, estão disponíveis 3 registos.

- Registo de acesso
- Registo de desempenho
- Registo de firewall

Clique em **Ativar diagnósticos** para iniciar a recolha de dados.

![Pá de definição de diagnóstico][1]

### <a name="step-2"></a>Passo 2

No pá **definições dos diagnósticos do** , as definições de como os registos de diagnóstico estão definidos. Neste exemplo, a análise de registo é utilizada para armazenar os registos. Em **Análise de registo** para configurar a área de trabalho, clique em **Configurar** . Concentradores de evento e uma conta de armazenamento podem ser utilizados para guardar os registos de diagnóstico também.

![Pá de diagnóstico][2]

### <a name="step-3"></a>Passo 3

Selecione uma área de trabalho OMS existente ou crie um novo. Para este exemplo é utilizada existente.

![OMS áreas de trabalho][3]

### <a name="step-4"></a>Passo 4

Quando estiver concluído, confirme as definições e clique em **Guardar** para guardar as definições.

![confirmar a seleção][4]

## <a name="audit-log"></a>Registo de auditoria

Este registo (anteriormente conhecido como "registo operacional") é gerado pelo Azure por predefinição.  Os registos são preservados para cerca de 90 dias na loja de registos dos eventos do Azure. Saiba mais sobre estes registos lendo o artigo [Ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="access-log"></a>Registo de acesso

Este registo só é gerado caso a tenha ativado numa base de Gateway de aplicação, tal como descrito nos passos anteriores por. Os dados são armazenados na conta de armazenamento que especificou quando activou o início de sessão. Cada acesso do Gateway aplicação é registado no formato JSON, conforme visto no exemplo seguinte:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>Registo de desempenho

Este registo só é gerado caso a tenha ativado numa base de Gateway de aplicação, tal como descrito nos passos anteriores por. Os dados são armazenados na conta de armazenamento que especificou quando activou o início de sessão. Os seguintes dados tem sessão iniciados:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>Registo de firewall

Este registo só é gerado caso a tenha ativado num por aplicação gateway base detalhadas nos passos anteriores. Este registo também requer que firewall de aplicação web de ser configurado num gateway de aplicação. Os dados são armazenados na conta de armazenamento que especificou quando activou o início de sessão. Os seguintes dados tem sessão iniciados:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>Ver e analisar o registo de auditoria

Pode ver e analisar dados de registo de auditoria utilizar qualquer um dos seguintes métodos:

- **Ferramentas azure:** Obter informações dos registos de auditoria através do Azure PowerShell, Azure Interface linha de comandos (CLI), o Azure REST API ou o portal do Azure pré-visualização.  Instruções passo a passo para cada método são detalhadas no artigo [operações de auditoria com o Gestor de recursos](../resource-group-audit.md) .
- **Do power BI:** Se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing) , pode tentá-lo gratuitamente. Utilizar o [pacote do Power BI de conteúdo de registos de auditoria Azure](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) pode analisar os seus dados com dashboards pré-configurada que pode utilizar como-está ou personalizar.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Ver e analisar o registo de acesso, desempenho e de firewall

Azure [Registo Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) pode recolher o contador de registo de eventos ficheiros e da sua conta de armazenamento de BLOBs e inclui visualizações e poderosas capacidades de procura para analisar os registos.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para registos de acesso e o desempenho. Depois de transferir os ficheiros JSON, pode convertê-las para CSV e view no Excel, obter ou qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se estiver familiarizado com o Visual Studio e conceitos básicos de alterar os valores para constantes e as variáveis nos c#, pode utilizar as [Ferramentas do conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponível a partir do Github.

## <a name="metrics"></a>Métricas

Métricas são uma funcionalidade para determinados recursos Azure, onde pode ver contadores de desempenho no portal. Para o Gateway de aplicação, uma métrica está disponível no momento da escrever deste artigo. Esta métrica é débito e pode ser visualizada no portal. Navegue para um gateway de aplicação e clique em **métricas**.  Selecione débito na secção **métricas disponíveis** para ver os valores. Na seguinte imagem, pode ver um exemplo com os filtros que podem ser utilizados para apresentar os dados em intervalos de tempo diferentes.

Para ver uma lista de atuais métricas de suporte, visite [métricas suportados com o Monitor do Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![métrica vista][5]

## <a name="alert-rules"></a>Regras de alertas

Regras de alertas podem ser iniciadas baseia de métricas num recurso. Isto significa para o gateway de aplicação, um alerta, pode ligar um webhook ou um administrador de e-mail, se o caudal do gateway aplicação acima ou abaixo ou a um determinado limiar para um período de tempo especificado.

O exemplo seguinte irá guiá-lo através da criação de uma regra de alerta que envia uma mensagem de e-mail a um administrador após um determinado limiar débito foi infringido.

### <a name="step-1"></a>Passo 1

Clique em **Adicionar métrica alerta** para começar. Este pá também é possível alcançar a pá métricas.

![Pá de alerta de regras][6]

### <a name="step-2"></a>Passo 2

No pá **Adicionar regra** , preencha o nome, condição e notifique secções e clique em **OK** quando terminar.

Selector de **condição** permite 4 valores, **maior**, **igual ou maior do que**, **menor que**ou **menor ou igual a**.

Selector de **período** , permite para recolha de um período de 5 minutos para 6 horas.

Ao selecionar **os proprietários de correio eletrónico, contribuintes e os leitores de** correio eletrónico pode ser dinâmico com base nos utilizadores que têm acesso a esse recurso. Caso contrário, uma lista de separados por ponto e de utilizadores pode ser fornecida na caixa de texto **email(s) administrador adicionais** .

![Adicionar pá de regra][7]

Se o limiar for quebrado, uma mensagem de e-mail chega semelhante na seguinte imagem:

![mensagem de correio electrónico limiar quebrado][8]

Assim que um alerta de métrico foi criado e fornece uma descrição geral de todas as regras de alertas, é apresentada uma lista dos alertas.

![Vista de alerta de regra][9]

Para saber mais sobre as notificações de alerta, visite [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Para compreender melhor webhooks e como utilizá-los com os alertas, visite [configurar um webhook num alerta métrico Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Próximos passos

- Visualizar contador e registos dos eventos com [A análise de registo](../log-analytics/log-analytics-azure-networking-analytics.md) 
- Mensagem de blogue [visualizar os registos de auditoria Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Vista e analisar os registos de auditoria Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) mensagem no blogue.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
<properties
   pageTitle="Monitorizar operações, eventos e contadores para Balanceador de carga | Microsoft Azure"
   description="Saiba como ativar eventos de alertas e sonda de registo de estado de funcionamento para Balanceador de carga Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Análise de registo de Balanceador de carga Azure (pré-visualização)

Pode utilizar diferentes tipos de registos no Azure para gerir e Balanceadores de carga de resolução de problemas. Alguns destes registos podem ser acedido através do portal. Todos os registos podem ser extraídos de um armazenamento de Blobs do Azure e visualizados nas ferramentas de diferentes, como o Excel e obter. Pode obter mais informações sobre os diferentes tipos de registos a partir da lista abaixo.

- **Registos de auditoria:** Pode utilizar os [Registos de auditoria Azure](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecido como registos operacionais) para ver todas as operações a ser submetidas para as suas subscrições Azure e o respetivo estado. Registos de auditoria estiverem ativados por predefinição e podem ser visualizados no portal do Azure.
- **Alertar registos dos eventos:** Pode utilizar este registo para ver que alertas para Balanceador de carga são elevados. O estado da Balanceador de carga é recolhido em cinco minutos. Este registo só é escrito se um evento de alerta de Balanceador de carga é elevado.
- **Registos de sonda de estado de funcionamento:** Pode utilizar este registo para verificar a existência de estado de verificação do Estado de funcionamento sonda, quantas instâncias estão online na Balanceador de carga back-end e percentagem de máquinas virtuais receber o tráfego de rede do Balanceador de carga. Este registo é escrito alteração de evento sonda estado.

>[AZURE.IMPORTANT] Inicie sessão analytics atualmente só funciona para opostas balanceadores de carga de Internet. Registos só estão disponíveis para recursos implementados no modelo de implementação de Gestor de recursos. Não pode utilizar os registos para recursos no modelo de implementação clássica. Para mais informações acerca dos modelos de implementação, consulte [Noções sobre o Gestor de recursos de implementação e implementação clássica](../../articles/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Ativar o registo

Registo de auditoria está ativado automaticamente para todos os recursos de Gestor de recursos. Tem de ativar o evento e estado de funcionamento sonda registo para iniciar a recolha de dados disponíveis através desses registos. Utilize os passos seguintes para ativar o registo.

Início de sessão no [portal do Azure](http://portal.azure.com). Se ainda não tiver um balanceador de carga, [criar um balanceador de carga](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. No portal do, clique em **Procurar**.
2. Selecione **balanceadores de carga**.

    ![Portal - Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selecione uma existente Balanceador de carga >> **Todas as definições**.
4. No lado direito da caixa de diálogo sob o nome do Balanceador de carga, desloque-se para **monitorização**, clique em **diagnóstico**.

    ![Portal - definições do Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. No painel de **diagnóstico** , em **Estado**, selecione **no**.
6. Clique na **conta de armazenamento**.
7. Em **registos**, selecione uma conta de armazenamento existente ou crie um novo. Utilize o controlo de deslize para determinar quantos dias evento dat serão mantidos nos registos de eventos. 8. Clique em **Guardar**.

    ![Portal - os registos de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] registos de auditoria não necessita de uma conta de armazenamento em separado. A utilização de armazenamento para evento e estado de funcionamento do registo de sonda irá implicam encargos do serviço.

## <a name="audit-log"></a>Registo de auditoria

O registo de auditoria é gerado por predefinição. Os registos são preservados para cerca de 90 dias na loja de registos dos eventos do Azure. Saiba mais sobre estes registos lendo o artigo [Ver eventos e registos de auditoria](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Alerta de registo de eventos

Este registo só é gerado caso a tenha ativado numa base de Balanceador de carga por. Os eventos são registados no formato JSON e armazenados na conta de armazenamento que especificou quando activou o início de sessão. Segue-se um exemplo de um evento.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

O resultado da JSON mostra a propriedade de *NomeDoAcontecimento* que vai descrever a razão para o Balanceador de carga criado um alerta. Neste caso, o alerta gerado foi devido TCP porta esgotamento causado pela origem IP NAT limites (SNAT).

## <a name="health-probe-log"></a>Registo de sonda de estado de funcionamento

Este registo só é gerado caso a tenha ativado numa carga balanceador base por tal como descrito acima. Os dados são armazenados na conta de armazenamento que especificou quando activou o início de sessão. É criado um contentor 'loadbalancerprobehealthstatus de registos de informações' com o nome e os seguintes dados tem sessão iniciados:

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

Mostra o resultado da JSON no campo propriedades as informações básicas para o estado de funcionamento sonda. A propriedade *dipDownCount* mostra o número total de instâncias back-end que não estão a receber o tráfego de rede devido a respostas sonda falha.

## <a name="view-and-analyze-the-audit-log"></a>Ver e analisar o registo de auditoria

Pode ver e analisar dados de registo de auditoria utilizar qualquer um dos seguintes métodos:

- **Ferramentas azure:** Obter informações dos registos de auditoria através do Azure PowerShell, Azure Interface linha de comandos (CLI), o Azure REST API ou o portal do Azure pré-visualização. Instruções passo a passo para cada método são detalhadas no artigo [operações de auditoria com o Gestor de recursos](../../articles/resource-group-audit.md) .
- **Do power BI:** Se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing) , pode tentá-lo gratuitamente. Utilizar os [registos de auditoria Azure conteúdo pack para o Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), pode analisar dados com dashboards pré-configurada ou pode personalizar vistas para se adequar às suas necessidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Ver e analisar a sonda de estado de funcionamento e registo de eventos

É necessário ligar à sua conta de armazenamento e obter as entradas de registo JSON para registos de sonda de eventos e estado de funcionamento. Depois de transferir os ficheiros JSON, pode convertê-las para CSV e view no Excel, obter ou qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se estiver familiarizado com o Visual Studio e conceitos básicos de alterar os valores para constantes e as variáveis nos c#, pode utilizar as [Ferramentas do conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponível a partir do Github.

## <a name="additional-resources"></a>Recursos adicionais

- Mensagem de blogue [visualizar os registos de auditoria Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Vista e analisar os registos de auditoria Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) mensagem no blogue.

## <a name="next-steps"></a>Próximos passos

[Compreender sondas do Balanceador de carga](load-balancer-custom-probe-overview.md)

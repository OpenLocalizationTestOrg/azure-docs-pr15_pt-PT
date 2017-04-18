<properties
   pageTitle="Monitorizar operações, eventos e contadores para NSGs | Microsoft Azure"
   description="Saiba como ativar contadores, eventos e registo operacional para NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Registo a análise de grupos de segurança de rede (NSGs)

Pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas NSGs. Alguns destes registos podem ser acedido através do portal e todos os registos podem ser extraídos de um armazenamento de Blobs do Azure e visualizados nas ferramentas de diferentes, tais como [A análise de registo](../log-analytics/log-analytics-azure-networking-analytics.md), Excel e obter. Pode obter mais informações sobre os diferentes tipos de registos a partir da lista abaixo.

- **Registos de auditoria:** Pode utilizar os [Registos de auditoria Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecido como registos operacionais) para ver todas as operações a ser submetidas para as suas subscrições Azure e o respetivo estado. Registos de auditoria estiverem ativados por predefinição e podem ser visualizados no portal do Azure pré-visualização.
- **Registos dos eventos:** Pode utilizar este registo para ver quais as regras que NSG são aplicadas ao VMs e funções de instância baseadas no MAC endereço. O estado destas regras é recolhido cada 60 segundos.
- **Contador registos:** Pode utilizar este registo para ver quantas vezes cada regra NSG foi aplicada aos negar ou permitir o tráfego.

>[AZURE.WARNING] Registos só estão disponíveis para recursos implementados no modelo de implementação de Gestor de recursos. Não pode utilizar os registos para recursos no modelo de implementação clássica. Para compreender melhor de dois modelos, referenciar o artigo [Noções sobre o Gestor de recursos de implementação e implementação clássica](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Ativar o registo
Registo de auditoria está ativado automaticamente em todas as horas para cada recurso de Gestor de recursos. Tem de ativar o evento e contador de registo para iniciar a recolha de dados disponíveis através desses registos. Para ativar o registo, siga os passos abaixo.

1.  Início de sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver um grupo de segurança de rede existente, [criar uma NSG](virtual-networks-create-nsg-arm-ps.md) antes de continuar.

2.  No portal do pré-visualização, clique em **Procurar** >> **grupos de segurança de rede**.

    ![Portal de pré-visualização - grupos de segurança de rede](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Selecione um grupo de segurança de rede existente.

    ![Portal de pré-visualização - definições de grupo de segurança de rede](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Na pá **Definições** , clique em **diagnóstico**e, em seguida, no painel de **diagnóstico** , ao lado do **Estado**, clique **no**
5. No pá **Definições** , clique em **Conta de armazenamento**e, em selecione um armazenamento existente de conta ou crie um novo.  

>[AZURE.INFORMATION] registos de auditoria não necessita de uma conta de armazenamento em separado. A utilização de armazenamento para eventos e registo regra irá assumir taxas de serviço.

6. Na lista pendente apenas em **Conta de armazenamento**, selecione se pretende iniciar sessão eventos, contadores ou ambos e, em seguida, clique em **Guardar**.

    ![Portal de pré-visualização - os registos de diagnóstico](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Registo de auditoria
Este registo (anteriormente conhecido como "registo operacional") é gerado pelo Azure por predefinição.  Os registos são preservados para cerca de 90 dias na loja de registos dos eventos do Azure. Saiba mais sobre estes registos lendo o artigo [Ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Registo de contador
Este registo só é gerado caso a tenha ativado-la numa base por NSG tal como descrito acima. Os dados são armazenados na conta de armazenamento que especificou quando activou o início de sessão. Cada regra aplicada a recursos tem sessão iniciada no formato JSON, conforme visto abaixo.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Registo de eventos
Este registo só é gerado caso a tenha ativado-la numa base por NSG tal como descrito acima. Os dados são armazenados na conta de armazenamento que especificou quando activou o início de sessão. Os seguintes dados tem sessão iniciados:

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Ver e analisar o registo de auditoria
Pode ver e analisar dados de registo de auditoria utilizar qualquer um dos seguintes métodos:

- **Ferramentas azure:** Obter informações dos registos de auditoria através do Azure PowerShell, Azure Interface linha de comandos (CLI), o Azure REST API ou o portal do Azure pré-visualização.  Instruções passo a passo para cada método são detalhadas no artigo [operações de auditoria com o Gestor de recursos](../resource-group-audit.md) .
- **Do power BI:** Se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing) , pode tentá-lo gratuitamente. Utilizar o [pacote do Power BI de conteúdo de registos de auditoria Azure](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) pode analisar os seus dados com dashboards pré-configurada que pode utilizar como-está ou personalizar.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Ver e analisar contador e registo de eventos

Azure [Registo Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) pode recolher o contador de registo de eventos ficheiros e da sua conta de armazenamento de BLOBs e inclui visualizações e poderosas capacidades de procura para analisar os registos.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para registos de eventos e contador. Depois de transferir os ficheiros JSON, pode convertê-las para CSV e view no Excel, obter ou qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se estiver familiarizado com o Visual Studio e conceitos básicos de alterar os valores para constantes e as variáveis nos c#, pode utilizar as [Ferramentas do conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponível a partir do Github.

## <a name="next-steps"></a>Próximos passos

- Visualizar contador e registos dos eventos com [A análise de registo](../log-analytics/log-analytics-azure-networking-analytics.md)
- Mensagem de blogue [visualizar os registos de auditoria Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Vista e analisar os registos de auditoria Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) mensagem no blogue.

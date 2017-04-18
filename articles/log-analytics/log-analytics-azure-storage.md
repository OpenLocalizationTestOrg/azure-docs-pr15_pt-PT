<properties
    pageTitle="Recolha de dados de armazenamento Azure na descrição geral do registo Analytics | Microsoft Azure"
    description="Recursos Azure podem escrever métricas e registos a uma conta de armazenamento Azure, muitas vezes utilizando os diagnósticos do Azure. Pode indexar estes dados e torná-lo pesquisável de análise de registo."
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

# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Recolha de dados de armazenamento Azure na descrição geral do registo Analytics

Muitos recursos Azure que conseguem escrever métricas e registos a uma conta de armazenamento Azure. Pode consumir estes dados e tornam mais fácil monitorizar os recursos do Azure de análise de registo.

Para escrever ao armazenamento Azure um recurso pode utilizar os diagnósticos do Azure ou tem as suas próprias maneira de escrever dados. Estes dados podem ser escritos em vários formatos para uma das seguintes localizações:

+ Tabela do Azure
+ BLOBs do Azure
+ EventHub

Análise de registo suporta Azure serviços que escrevem dados utilizando o [Azure registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Além disso, a análise de registo suporta outros serviços de registos e métricas nos formatos diferentes e localizações de saída.  

>[AZURE.NOTE] São cobradas taxas dados Azure normal para armazenamento e transações quando envia diagnóstico para uma conta de armazenamento e para quando o registo de análise lê os dados da sua conta de armazenamento.

![Diagrama de armazenamento Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Recursos Azure suportados

Análise de registo pode recolher dados para os seguintes recursos Azure:

| Tipo de recurso | Registos de início (diagnóstico categorias) | Solução de análise de registo |
| --------------------------------------- | -------------------------------- | --------------- |
| Informações de aplicação | Disponibilidade <br> Eventos personalizados <br> Exceções <br> Pedidos de <br> | Informações de aplicação (pré-visualização) |
| Gestão de API | | *nenhum* (Pré-visualização) |
| Automatização <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (pré-visualização) |
| Cofre chave <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (pré-visualização) |
| Gateway de aplicação <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (pré-visualização) |
| Grupo de segurança de rede <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (pré-visualização) |
| Serviço ferro                          | ETWEvent <br> Evento operacional <br> Ator fiável evento <br> Serviço fiável evento| ServiceFabric (pré-visualização) |
| Máquinas virtuais | Linux Syslog <br> Eventos do Windows <br> Registo do IIS <br> ETWEvent do Windows | *nenhum* |
| Funções da Web <br> Funções de trabalho | Linux Syslog <br> Eventos do Windows <br> Registo do IIS <br> ETWEvent do Windows | *nenhum* |

>[AZURE.NOTE] Para monitorizar Azure máquinas virtuais (Linux e Windows), recomendamos que instale a [extensão de registo de análise VM](log-analytics-azure-vm-extension.md). O agente fornece-lhe mais aprofundadas informações no seu máquinas virtuais que se utilizar os diagnósticos escritos ao armazenamento.

Pode ajudar-na atribuir prioridades a registos adicionais para OMS analisar por voto na nossa [página de comentários](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).


- Ver [registos de diagnóstico analisar Azure utilizando a análise de registo](log-analytics-azure-storage-json.md) para saber mais sobre como registo Analytics pode ler os registos a partir do Azure serviços que suportam o [Azure registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  - Azure cofre chave
  - Automatização Azure
  - Gateway de aplicação
  - Grupos de segurança de rede
- Ver o [armazenamento de BLOBs utilizar para o IIS e armazenamento de tabela para eventos](log-analytics-azure-storage-iis-table.md) para saber mais sobre como a análise de registo pode ler os registos de início para o Azure services esse diagnósticos de escrita para o armazenamento de tabela ou os registos do IIS escritos blob armazenamento, incluindo:
  - Serviço ferro
  - Funções da Web
  - Funções de trabalho
  - Máquinas virtuais


Informações de aplicação está na pré-visualização do privado e exportar contínua de utilizações para blob armazenamento. Para participar pré-visualização do privado, contacte o seu Microsoft Account team ou consultar os detalhes no [site de comentários](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Próximos passos

- [Registos de diagnóstico analisar Azure utilizando a análise de registo](log-analytics-azure-storage-json.md) para ler os registos a partir do Azure dos serviços que diagnósticos de escrita ao armazenamento blob do formato JSON.
- [Armazenamento de BLOBs utilizar para o IIS e armazenamento de tabela para eventos](log-analytics-azure-storage-iis-table.md) para ler os registos de início para Azure dos serviços que diagnósticos de escrita para armazenamento de tabela ou os registos do IIS escritos blob armazenamento.
- [Ativar soluções](log-analytics-add-solutions.md) para fornecer visão os dados.
- [Utilizar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.

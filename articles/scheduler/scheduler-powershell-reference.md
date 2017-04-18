<properties
 pageTitle="Referência de Cmdlets do PowerShell de programador"
 description="Referência de Cmdlets do PowerShell de programador"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>Referência de Cmdlets do PowerShell de programador

A tabela seguinte descreve e ligações para a página de referência de cada um dos cmdlets principais no Azure Programador de tarefas.

Para instalar o PowerShell do Azure e associá-la com a sua subscrição Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). 

Para mais informações sobre [os cmdlets do Gestor de recursos do Azure](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx), consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).

|Cmdlet|Descrição do cmdlet|
|---|---|
[Desativar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Desativa uma coleção de tarefa. 
[Ativar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Permite uma coleção de tarefa.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Obtém do Programador de tarefas.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Obtém tarefa coleções de sites.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Obtém histórico da tarefa.
[Novo AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Cria uma tarefa de HTTP.
[Novo AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Cria uma coleção de tarefa.
[Novo AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Cria uma tarefa do serviço bus fila de espera.
[Novo AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Cria uma tarefa de tópico bus do serviço.
[Novo AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Cria uma tarefa de fila de armazenamento. 
[Remover AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Remove uma tarefa de programador.  
[Remover AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Remove uma coleção de tarefa. 
[Definir AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifica uma tarefa de HTTP programador.
[Definir AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifica uma coleção de tarefa. 
[Definir AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifica uma tarefa do serviço bus fila de espera.  
[Definir AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifica uma tarefa de tópico bus do serviço. 
[Definir AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifica uma tarefa de fila de armazenamento.   

Para obter informações mais detalhadas, pode executar qualquer uma das seguintes cmdlets: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Consulte também


 [O que é o Scheduler?](scheduler-intro.md)

 [Azure Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Limites de programador Azure, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)

 [Azure autenticação de saída de programador](scheduler-outbound-authentication.md)

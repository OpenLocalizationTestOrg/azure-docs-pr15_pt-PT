<properties 
   pageTitle="Gerir dados de automatização do Azure | Microsoft Azure"
   description="Este artigo contém vários tópicos para gerir um ambiente do Azure automatização.  Atualmente inclui retenção de dados e cópias de segurança recuperação de falhas de automatização Azure no Azure automatização."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Gerir dados de automatização do Azure

Este artigo contém vários tópicos para gerir um ambiente do Azure automatização.

## <a name="data-retention"></a>Retenção de dados

Quando elimina um recurso de automatização do Azure, são retido para cerca de 90 dias efeitos de auditoria antes de a ser removida permanentemente.  Não pode ver ou utilizar o recurso durante este período de tempo.  Esta política também se aplica aos recursos que pertencem a uma conta de automatização que é eliminada.

Automatização Azure elimina automaticamente e remove permanentemente tarefas mais antigas do que cerca de 90 dias.

A tabela seguinte resume a política de retenção para recursos diferentes.

|Dados|Política|
|:---|:---|
|Contas|Removido definitivamente cerca de 90 dias após a conta será eliminada por um utilizador.|
|Elementos|Removido definitivamente cerca de 90 dias após o elemento é eliminado por um utilizador ou 90 dias após a conta que detém o que elemento é eliminado por um utilizador.|
|Módulos|Removido definitivamente cerca de 90 dias após o módulo é eliminado por um utilizador ou 90 dias após a conta que detém que o módulo é eliminado por um utilizador.|
|Runbooks|Removido definitivamente cerca de 90 dias após o recurso é eliminado por um utilizador ou 90 dias após a conta que detém que o recurso é eliminado por um utilizador.|
|Tarefas|Eliminados e removidos de forma permanente 90 dias após a duração modificada. Isto pode ser depois da tarefa for concluída, está parada ou está suspensa.|
|Ficheiros de configurações/MOF nó| Configuração de nó antigo é removida definitivamente cerca de 90 dias após uma configuração de nó novo é gerada.|
|DSC nós| Removido definitivamente cerca de 90 dias após o nó não está registado a partir de automatização conta utilizando o Azure portal ou o cmdlet [Unregister AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) no Windows PowerShell. Nós são removidos também permanentemente 90 dias após a conta que detém que o nó é eliminado por um utilizador. |
|Relatórios de nó| Removidas de forma permanente cerca de 90 dias após é gerado um relatório novo para esse nó|

A política de retenção aplica-se a todos os utilizadores e atualmente não pode ser personalizada.

## <a name="backing-up-azure-automation"></a>Criar cópias de segurança de automatização do Azure

Quando elimina uma conta de automatização no Microsoft Azure, todos os objetos na conta são eliminados incluindo runbooks, módulos, configurações, definições, tarefas e recursos. Não não possível recuperar os objetos depois da conta será eliminada.  Pode utilizar as seguintes informações para efectuar cópia o conteúdo da sua conta de automatização antes de o eliminar. 

### <a name="runbooks"></a>Runbooks

Pode exportar o seu runbooks para ficheiros de script através do Portal de gestão do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) no Windows PowerShell.  Estes ficheiros de script podem ser importados para outra conta de automatização, tal como é abordado no [criar ou importar um livro de execuções](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Módulos de integração

Não pode exportar módulos de integração de automatização do Azure.  Tem de garantir que estão disponíveis fora da conta de automatização.

### <a name="assets"></a>Elementos

Não pode exportar [elementos](https://msdn.microsoft.com/library/dn939988.aspx) a partir do Azure automatização.  Utilizar o Portal de gestão do Azure, deve ter em consideração os detalhes de variáveis, credenciais, certificados, ligações e agendas.  Em seguida, deve criar manualmente quaisquer activos que são utilizados pelo runbooks importados para o outra automatização.

Pode utilizar [os cmdlets do Azure](https://msdn.microsoft.com/library/dn690262.aspx) para obter detalhes de activos não encriptados e guardá-los para referência futura ou criar activos equivalentes na outra conta de automatização.

Não consegue obter o valor para variáveis encriptadas ou o campo da palavra-passe de credenciais utilizar cmdlets do.  Se não souber estes valores, pode obtê-los a partir de um livro de execuções utilizando as atividades de [Obter AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Obter AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) .

Não pode exportar certificados a partir do Azure automatização.  Tem de garantir que quaisquer certificados estão disponíveis fora do Azure.

### <a name="dsc-configurations"></a>Configurações de DSC

Pode exportar as configurações para ficheiros de script através do Portal de gestão do Azure ou o cmdlet [AzureRmAutomationDscConfiguration exportar](https://msdn.microsoft.com/library/mt603485.aspx) no Windows PowerShell. Estas configurações podem ser importadas e utilizadas na outra conta de automatização.


##<a name="geo-replication-in-azure-automation"></a>Replicação geo na automatização Azure

Geo-replicação, padrão nas contas do Azure automatização, cópias de dados de conta para uma região geográfica diferente para a redundância de. Pode escolher uma região principal quando configurar a sua conta e, em seguida, em que uma região secundária é atribuída automaticamente à mesma. Os dados secundários, copiados a partir da região principal, são constantemente atualizados em caso de perda de dados.  

A tabela seguinte mostra os pares de região disponíveis principais e secundários.

|Principal            |Secundário
| ---------------   |----------------
|Sul Central (EUA)   |América do Norte Central (EUA)
|Leste dos EUA 2          |Central (EUA)
|Europa Ocidental        |Europa Norte
|Sudeste asiático    |Este asiático
|Japão leste         |Japão oeste

Na eventualidade que dados uma região primária é perdido, Microsoft tenta recuperá-la. Se os dados principais não não possível recuperar, em seguida, geo com falha é executada e os clientes afetados serão notificados sobre isto através da sua subscrição.


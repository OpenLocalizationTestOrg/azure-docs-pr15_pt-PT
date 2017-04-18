<properties
 pageTitle="Planos e faturação no Azure Scheduler"
 description="Planos e faturação no Azure Scheduler"
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

# <a name="plans-and-billing-in-azure-scheduler"></a>Planos e faturação no Azure Scheduler

## <a name="job-collection-plans"></a>Planos de coleção de tarefa

Coleções de tarefa são a entidade cobrar no Azure Programador de tarefas. Tarefa colecções contiverem um número de tarefas e de surgir em três planos – livre, padrão e Premium – que são descritos abaixo.

|**Plano de recolha de tarefa**|**# Max de tarefas por coleção de tarefa**|**Máximo periodicidade**|**Coleções de máximo tarefa por subscrição**|**Limites**|
|:---|:---|:---|:---|:---|
|**Gratuito**|tarefas de 5 por coleção de tarefa|Uma vez por hora. Não é possível executar tarefas mais frequentemente uma vez por hora|Uma subscrição é permitida até 1 coleção de tarefa gratuito|Não é possível utilizar o [objeto de autorização de saída de HTTP](scheduler-outbound-authentication.md)
|**Padrão**|tarefas de 50 por coleção de tarefa|Uma vez por minuto. Não é possível executar tarefas mais frequentemente uma vez num minuto|Uma subscrição é permitida até 100 coleções de tarefa padrão|Acesso ao conjunto de funcionalidades completo do Programador de|
|**P10 Premium**|tarefas de 50 por coleção de tarefa|Uma vez por minuto. Não é possível executar tarefas mais frequentemente uma vez num minuto|Uma subscrição é permitida até 10.000 coleções de tarefa P10 Premium. <a href="mailto:wapteams@microsoft.com">Contacte-nos</a> para obter mais informações.|Acesso ao conjunto de funcionalidades completo do Programador de|
|**P 20 Premium**|tarefas de 1000 por coleção de tarefa|Uma vez por minuto. Não é possível executar tarefas mais frequentemente uma vez num minuto|Uma subscrição é permitida até 10.000 coleções de tarefa de p 20 Premium. <a href="mailto:wapteams@microsoft.com">Contacte-nos</a> para obter mais informações.|Acesso ao conjunto de funcionalidades completo do Programador de|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>As atualizações e Downgrades dos planos de coleção de tarefa

Pode atualizar ou alterar um plano de coleções de sites em qualquer altura do trabalho, entre os planos livre, padrão e Premium. No entanto, quando mudar para uma coleção de tarefa gratuito, poderá falhar a redução de um dos seguintes motivos:

- Tarefa gratuito já existe uma colecção na subscrição
- Uma tarefa na coleção de tarefa tem uma periodicidade mais elevada que permitidos para trabalhos de coleções de tarefa gratuito. A periodicidade máxima permitida numa coleção de tarefa gratuito é uma vez por hora
- Existem mais do que 5 tarefas na coleção de tarefa
- Uma tarefa na coleção de tarefa tem uma ação de HTTP ou HTTPS que utiliza um [objeto de autorização de saída de HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Planos de faturação e Azure

Subscrições não serão cobradas gratuitamente coleções de tarefa. Se tiver mais de 100 coleções de tarefa padrão (unidades de faturaçãohttps padrão 10), é um melhor negócio ter todas as colecções de tarefa no plano de premium.

Se tiver uma colecção de tarefa padrão e de uma coleção de tarefa premium, está faturado um padrão faturação unidade _e_ uma premium faturação unidade. Os títulos de serviço de programador com base no número de colecções de tarefa ativo que estão definidos para padrão ou premium; Isto é explicado mais nas duas secções.

## <a name="standard-billable-units"></a>Unidades cobrar padrão

Uma unidade cobrar padrão pode incluir até 10 coleções de tarefa padrão. Uma vez uma coleção de tarefa padrão pode ter até 50 tarefas por coleção de tarefa, uma unidade de faturação padrão permite uma subscrição do tenham até 500 tarefas – até execuções do projecto quase milhões de 22 por mês.

Se tiver entre 1 e 10 colecções de tarefa padrão, vai ser faturada 1 unidade faturação padrão. Se tiver entre 11 e 20 coleções de tarefa padrão, vai ser faturada 2 unidades faturaçãohttps padrão. Se tiver entre 21 e colecções de 30 tarefa padrão, serão cobrados 3 unidades faturação padrão e assim sucessivamente.

## <a name="p10-premium-billable-units"></a>P10 Premium cobrar unidades

Uma unidade de cobrar premium P10 pode incluir até 10.000 P10 premium tarefa coleções de sites. Uma vez uma coleção de tarefa de premium P10 pode ter até 50 tarefas por coleção de tarefa, uma unidade de faturação premium permite uma subscrição do tenham até 500.000 tarefas – até execuções do projecto quase mil milhões de 22 por mês.

Se tiver entre 1 e 10.000 coleções de tarefa premium, vai ser faturada unidade de faturação 1 P10 premium. Se tiver entre 10,001 e coleções de tarefa 20.000 premium, serão cobrados para 2 P10 premium faturaçãohttps as unidades e assim sucessivamente.

Assim sendo, coleções de tarefa P10 premium têm as mesmas funcionalidades como as coleções de tarefa padrão mas fornecem uma quebra de preço caso a aplicação requer muitas coleções de tarefa.

## <a name="p20-premium-billable-units"></a>P 20 Premium cobrar unidades

Uma unidade de cobrar premium p 20 pode incluir até 5000 p 20 premium tarefa coleções de sites. Uma vez uma coleção de tarefa de premium p 20 pode ter até 1.000 tarefas por coleção de tarefa, uma unidade de faturação premium permite uma subscrição do tenham até 5,000,000 tarefas – até execuções do projecto quase mil milhões de 220 por mês.

Coleções de tarefa p 20 premium fornece as mesmas capacidades como colecções de tarefa P10 premium, mas também suporta um maior projectos número por coleção de tarefa e um número total de tarefas gerais superior premium P10 permitindo-lhe ter mais escalabilidade.

## <a name="billing-and-active-status"></a>Faturação e ativo o Estado

Coleções de tarefa são sempre ativas, a menos que a sua subscrição inteira tem desaparece para alguns fase de Desativação temporário devido a problemas de faturação. A única forma para se certificar de que uma coleção de tarefa não está a ser faturada é para ou defina-lo para o plano de _Free_ ou eliminar a coleção de tarefa.

Apesar de poderá desativar todas as tarefas numa coleção de tarefa numa única operação, tal não irá alterar o estado de faturação da coleção de tarefa – a coleção de tarefa irá _ainda_ ser faturada. Da mesma forma, coleções de projecto vazios são consideradas ativas e vai ser faturadas.

## <a name="pricing"></a>Preços

Para preços detalhes, consulte o artigo [Scheduler preços](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Consulte também


 [O que é o Scheduler?](scheduler-intro.md)

 [Azure Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure cmdlets do PowerShell de Programador de referência](scheduler-powershell-reference.md)

 [Azure Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Limites de programador Azure, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)

 [Azure autenticação de saída de programador](scheduler-outbound-authentication.md)

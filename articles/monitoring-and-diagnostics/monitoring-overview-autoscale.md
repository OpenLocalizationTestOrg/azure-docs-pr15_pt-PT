<properties
    pageTitle="Descrição geral de autoscale em máquinas virtuais do Microsoft Azure, serviços em nuvem e Web Apps | Microsoft Azure"
    description="Descrição geral das autoscale no Microsoft Azure. Aplica-se ao máquinas virtuais, serviços em nuvem e aplicações Web."
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
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Descrição geral de autoscale em máquinas virtuais do Microsoft Azure, serviços em nuvem e Web Apps

Este artigo descreve o que autoscale do Microsoft Azure é, os benefícios e como começar a utilizá-lo.  

Azure Monitor autoscale aplica-se apenas ao [Serviço de aplicação - Web Apps](https://azure.microsoft.com/services/app-service/web/), [Serviços em nuvem](https://azure.microsoft.com/services/cloud-services/)e [Conjuntos de escala de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

>[AZURE.NOTE] Azure tem dois métodos de autoscale. Uma versão anterior do autoscale aplica-se ao máquinas virtuais (disponibilidade conjuntos). Esta funcionalidade tem suporte limitado e recomendamos migrar para conjuntos de escala VM autoscale mais rápida e fiável ao suporte. É incluída uma hiperligação sobre como utilizar a tecnologia mais antiga neste artigo.  


## <a name="what-is-autoscale"></a>O que é autoscale?

Autoscale permite-lhe têm a quantidade de à direita de recursos em execução para processar a carga da sua aplicação. Permite-lhe adicionar recursos para processar aumentos de carga e também poupar dinheiro removendo recursos que sentada são inactivo. Especificar um número mínimo e máximo de instâncias para executar e adicionar ou remover VMs automaticamente baseados num conjunto de regras. Ter um mínimo torna-se de que a aplicação está sempre a ser executado mesmo sem carga. Ter um máximo limita a sua possível por hora custo total. Dimensionar automaticamente entre estes dois extremos utilizando regras de que criar.

 ![Explicação das Autoscale. Adicionar e remover VMs](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Quando as condições da regra forem cumpridas, uma ou mais ações autoscale é acionada. Pode adicionar e remover VMs ou realizar outras ações. O diagrama conceptual seguinte mostra este processo.  

 ![Diagrama de fluxo de conceptual Autoscale](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>Processo de Autoscale explicadas
Explicação seguinte aplicam-se às partes do diagrama anterior.   

### <a name="resource-metrics"></a>Métricas de recursos
Recursos emitem métricas, o que mais tarde são processadas pelo regras. Métricas vir através de métodos diferentes.
Conjuntos de escala VM utiliza dados de telemetria de agentes de diagnóstico Azure, Considerando que vem de telemetria para aplicações Web e serviços em nuvem diretamente a partir do infraestrutura do Azure. Algumas estatísticas utilizadas mais frequentemente incluem a utilização da CPU, utilização de memória, contagens de tópico, comprimento de fila de espera e a utilização do disco. Para obter uma lista de dados de telemetria que pode utilizar, consulte o artigo [Autoscale comuns métricas](insights-autoscale-common-metrics.md).

### <a name="time"></a>Tempo
Regras baseadas no agenda são baseadas em UTC. Tem de definir o fuso horário correctamente quando configurar as suas regras.  

### <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de autoscale, mas pode ter muitos dos mesmos. Pode criar regras sobrepostas complexas, conforme necessário para a sua situação.  Incluem tipos de regra  

 - **Com base em métrica** - por exemplo, execute esta ação quando a utilização da CPU for superior a 50%.
 - **Com base no tempo** - por exemplo, acionar uma webhook cada 8: 00 Sábado de um determinado fuso horário.

Regras baseadas em métrica medem carga da aplicação e adicionar ou remover VMs com base nessa carga. Regras baseadas no agenda permitem-lhe dimensionar quando ver os padrões de tempo na sua carga e quer Dimensionar antes de um aumento de carga possíveis ou diminuir ocorre.  


### <a name="actions-and-automation"></a>Ações e de automatização

Regras podem acionar uma ou mais tipos de ações.

- **Escala** - VMs escala ou reduzir
- **E-mail** - enviar correio eletrónico para administradores de subscrição, co-administradores e/ou endereço de e-mail adicionais que especificou
- **Automatizar através do webhooks** - webhooks de chamada, o que pode acionar a várias acções complexas dentro ou fora da Azure. No interior do Azure, pode iniciar um livro execuções Azure automatização, função Azure ou Azure lógica de aplicação. Exemplo 3º festa URL fora Azure incluir serviços como o margem e Twilio.


## <a name="autoscale-settings"></a>Definições de Autoscale
Autoscale utilize as seguintes terminologia e estrutura.

- Uma **definição de autoscale** é lido pelo motor autoscale determinar se Dimensionar para cima ou para baixo. Contém mais perfis, informações sobre o recurso de destino e definições de notificação.
    - Um **perfil de autoscale** é uma combinação de uma capacidade de definição de um conjunto de regras que regem a accionadores e ações de escala para o perfil e uma periodicidade. Pode ter vários perfis, que lhe permitem encarregam-se de requisitos sobrepostos diferentes.
        - Uma **definição de capacidade** indica o mínimo, máximo e valores predefinidos para o número de instâncias. [local apropriado para utilizar figo 1]
        - Uma **regra** inclui um accionador — um accionador métrico ou um acionador de tempo — e uma ação de escala, que indica se autoscale deve Dimensionar para cima ou para baixo quando nessa regra é cumprida.
        - Uma **Periodicidade** indica quando autoscale devo postos este perfil em efeito. Pode ter perfis autoscale diferentes para diferentes horas do dia ou dias da semana, por exemplo.
- Uma **definição de notificação** define as notificações de que devem ocorrer quando ocorre um evento de autoscale com base no que satisfaça os critérios de um dos perfis da definição de autoscale. Autoscale pode notificar um ou mais endereços de e-mail ou efetuar chamadas para um ou mais webhooks.

![Definição de Azure autoscale, perfil e a estrutura de regra](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa dos campos configuráveis e descrições está disponível no [Autoscale REST API](https://msdn.microsoft.com/library/dn931928.aspx).

Para obter exemplos código, consulte o artigo

* [Configuração avançada do Autoscale utilizar modelos de Gestor de recursos para conjuntos de escala VM](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Autoscale REST API](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Dimensionamento vertical do vs horizontais

Autoscale aumenta recursos no escalas apenas na horizontal, que é um aumento ("saída") ou da diminuição ("") no número de ocorrências VM.  Horizontal dimensionamento, que é mais flexível numa situação na nuvem como permite-lhe executar potencialmente milhares de VMs para processar carga. Dimensionamento vertical é diferente. Mantém o mesmo número de VMs, mas faz com que a VM mais ("para cima") ou menos ("para baixo") eficientes. Power é medido em memória, velocidade da CPU, espaço em disco, etc.  Dimensionamento vertical tem limitações mais. É dependente da disponibilidade de hardware maior, que pode variar por região e acertos rapidamente e o limite superior. Normalmente, também dimensionamento vertical requer um parar VM e iniciar. Para mais informações, consulte o artigo [verticalmente Dimensionar Azure máquina virtual com a automatização Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## <a name="methods-of-access"></a>Métodos de acesso
Pode configurar o autoscale através do

- [Portal do Azure](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Interface de linha de comandos em diferentes plataformas (CLI)](insights-cli-samples.md#autoscale )
- [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Serviços suportados para autoscale


| Serviço                              | Esquema e documentos                                       |
|--------------------------------------|-----------------------------------------------------|
| Web Apps                             | [Dimensionamento Web Apps](insights-how-to-scale.md)              |
| Serviços em nuvem                       | [Autoscale um serviço na nuvem](../cloud-services/cloud-services-how-to-scale.md) |
| Máquinas virtuais: clássico           | [Conjuntos de disponibilidade do dimensionamento Máquina Virtual clássico](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuais: Conjuntos de escala de Windows| [Dimensionamento VM escala define no Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Máquinas virtuais: Conjuntos de escala Linux  | [Dimensionamento VM escala conjuntos no Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Máquinas virtuais: Exemplo de Windows   | [Configuração avançada do Autoscale utilizar modelos de Gestor de recursos para conjuntos de escala VM](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre autoscale, utilize os tutoriais Autoscale listado anteriormente ou referir-se para os seguintes recursos:

- [Métricas comuns do Azure Monitor autoscale](insights-autoscale-common-metrics.md)
- [Práticas recomendadas para autoscale Monitor do Azure](insights-autoscale-best-practices.md)
- [Utilizar as ações de autoscale para enviar e-mails e webhook as notificações de alerta](insights-autoscale-to-webhook-email.md)
- [Autoscale REST API](https://msdn.microsoft.com/library/dn931953.aspx)
- [Resolução de problemas Autoscale de conjuntos de escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

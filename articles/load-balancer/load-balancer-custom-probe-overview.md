<properties
  pageTitle="Carregar sondas personalizadas balanceador e monitorizar o estado de funcionamento | Microsoft Azure"
  description="Saiba como utilizar sondas personalizadas para Balanceador de carga Azure para monitorizar as instâncias atrás Balanceador de carga"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
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

# <a name="understand-load-balancer-probes"></a>Compreender sondas do Balanceador de carga

Azure Balanceador de carga oferece a funcionalidade para monitorizar o estado de funcionamento de instâncias do servidor utilizando sondas. Quando uma sonda deixar de responder, Balanceador de carga deixa de enviar novas ligações para a instância danificada. Ligações existentes não são afetadas e novas ligações são enviadas para saudáveis instâncias.

Funções de serviço de nuvem (funções de trabalho e funções web) utilizam um agente de convidado para monitorizar a sonda. TCP ou HTTP sondas personalizadas tem de ser configuradas quando utiliza máquinas virtuais atrás Balanceador de carga.

## <a name="understand-probe-count-and-timeout"></a>Compreender a contagem de sonda e tempo limite

Comportamento sonda depende:

- O número de sondas bem sucedidas, que permitem uma instância para ser identificados como até.
- O número de falhadas sondas que causam uma instância para ser identificados como para baixo.

O valor de tempo limite e frequência configurado no SuccessFailCount determinar se uma instância é confirmada para ser executar ou não a ser executado. No portal do Azure, o tempo limite está definido para duas vezes o valor da frequência.

A configuração de sonda de todas as instâncias de balanceamento de carga para um ponto final (ou seja, um conjunto balanceamento de carga) tem de ser a mesma. Isto significa que não pode ter uma configuração de sonda diferente para cada instância de função ou máquina virtual mesmo alojado do serviço de para uma combinação de ponto final específico. Por exemplo, cada instância tem de ter idênticas portas locais e tempos limite.

>[AZURE.IMPORTANT] Uma sonda Balanceador de carga utiliza o endereço IP 168.63.129.16. Este endereço IP público facilita a comunicação para recursos de plataforma interna para o trazer-your-proprietário-IP cenário de rede do Azure Virtual. O endereço IP público 168.63.129.16 virtual é utilizado em todas as regiões e não será alterada. Recomendamos que permitir que este endereço IP na quaisquer políticas de local firewall. -Não deve ser considerada um risco de segurança uma vez que apenas a plataforma Azure interna pode da origem de uma mensagem a partir desse endereço. Se não o fizer, haverá um comportamento inesperado numa variedade de cenários, como configurar o mesmo intervalo de endereços IP de 168.63.129.16 e ter duplicada endereços IP.

## <a name="learn-about-the-types-of-probes"></a>Saiba mais sobre os tipos de sondas

### <a name="guest-agent-probe"></a>Sonda de agente de convidado

Este sonda só está disponível para serviços em nuvem Azure. Balanceador de carga utiliza o agente de convidado no interior da máquina virtual e, em seguida, recebe e responde com uma resposta HTTP 200 OK apenas quando a instância está no estado pronta (ou seja, não noutro Estado como ocupado, a Reciclagem ou parar a).

Para mais informações, consulte o artigo [configurar o ficheiro de definição de serviço (csdef) para o estado de funcionamento sondas](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [começar a criar um balanceador de carga de acesso à Internet para serviços em nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>O que torna uma sonda de agente de convidado marcar uma instância como danificado?

Se o agente de convidado falha responder com HTTP 200 OK, Balanceador de carga assinala a instância como responder e deixa de enviar tráfego para essa instância. O Balanceador de carga continua a executar o ping a instância. Se o agente de convidado responder com uma 200 HTTP, Balanceador de carga envia o tráfego para essa instância novamente.

Quando utiliza uma função da web, o código de Web site normalmente é executado no w3wp.exe, que não é controlada pelo Azure agente ferro ou convidado. Isto significa que não irão ser comunicadas falhas no w3wp.exe (por exemplo, as respostas HTTP 500) para o agente de convidado e Balanceador de carga não terão essa instância terminar rotação.

### <a name="http-custom-probe"></a>Sonda personalizada HTTP

A sonda de Balanceador de carga de HTTP personalizada substitui a sonda de agente de convidado predefinido, que significa que, pode criar o seu próprio lógica personalizada para determinar o estado de funcionamento da instância função. O Balanceador de carga sondas o ponto final de cada 15 segundos, por predefinição. A instância é considerada a estar a rotação do Balanceador de carga se responder com uma 200 HTTP dentro do período de tempo limite (segundos 31 por predefinição).

Isto pode ser útil se pretender implementar o seu próprio lógica para remover instâncias de rotação do Balanceador de carga. Por exemplo, poderá decidir remover uma instância se for fornecida acima CPU 90% e devolve um Estado de que não sejam 200. Se tiver funções web que utilizam w3wp.exe, isto também significa que obtém automática monitorização do seu Web site, uma vez que falhas no seu código do Web site irão devolver um Estado de que não sejam 200 à sonda Balanceador de carga.

>[AZURE.NOTE] A sonda personalizada HTTP suporta caminhos relativos e HTTP apenas para o protocolo. HTTPS não é suportada.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>O que torna uma sonda personalizada de HTTP marcar uma instância como danificado?

- A aplicação de HTTP devolve um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Esta é uma confirmação positiva que a instância da aplicação deverá ser reencaminhada fora do serviço de imediato.
- O servidor de HTTP não responder a todos os após o período de tempo limite. Consoante o valor de limite de tempo definido, isto poderá significa que sonda múltiplos pedidos ir novas antes da sonda obtém marcada como não a ser executado (ou seja, antes de SuccessFailCount sondas são enviadas).
- O servidor fecha a ligação através de uma reposição TCP.

### <a name="tcp-custom-probe"></a>Sonda personalizada TCP

TCP sondas iniciar uma ligação ao efetuar um handshake três-se com a porta definido.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>O que torna uma sonda personalizada do TCP marcar uma instância como danificado?

- O TCP o servidor não responde sequer após o período de tempo limite. Quando a sonda é marcada como não a ser executado depende do número de pedidos de falha sonda que tenham sido configurados para ir não respondidas antes de marcar a sonda como não a ser executado.
- A sonda recebe um TCP repor da instância de função.

Para mais informações sobre como configurar uma sonda de estado de funcionamento HTTP ou uma sonda TCP, consulte o artigo [começar a criar um balanceador de carga de acesso à Internet no Gestor de recursos através do PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Adicionar instâncias saudáveis novamente no rotação do Balanceador de carga

As pesquisas TCP e HTTP são consideradas saudáveis e marcar a instância de função como saudável quando:

- O Balanceador de carga obtém uma sonda positiva pela primeira vez a VM botas.
- O número SuccessFailCount (descrito anteriormente) define o valor de sondas bem sucedidas, que são necessários para marcar a instância de função como saudável. Se uma instância de função foi removida, o número de sondas bem sucedidas, sucessivas tem igual ou exceder o valor de SuccessFailCount para marcar a instância de função como executar.

>[AZURE.NOTE] Se o estado de funcionamento de uma instância da função é flutuante, Balanceador de carga já espera antes de colocar a instância de função novamente no estado de saudável. Isto é feito através da política para proteger o utilizador e a infraestrutura.

## <a name="use-log-analytics-for-load-balancer"></a>Utilizar a análise de registo para Balanceador de carga

Pode utilizar [a análise de registo de Balanceador de carga](load-balancer-monitor-log.md) para verificar o estado de funcionamento sonda e sonda contagem. Registo pode ser utilizado com Power BI ou Azure operacionais informações para fornecer estatísticas sobre o estado de funcionamento do Balanceador de carga.

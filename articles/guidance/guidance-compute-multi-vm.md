<properties
   pageTitle="Executar vários VMs | Arquitetura de referência | Microsoft Azure"
   description="Como executar várias instâncias VM no Azure para escalabilidade, RDP, capacidade de gestão e segurança."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Executar vários VMs no Azure para escalabilidade e disponibilidade 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve um conjunto de provas dadas práticas para executar várias instâncias de máquinas virtuais (VM) para melhorar a escalabilidade, disponibilidade, capacidade de gestão e segurança.   

Nesta arquitectura, é distribuída a carga de trabalho em várias instâncias de VM. Existe um único endereço IP público e o tráfego da Internet é distribuído a VMs utilizando um balanceador de carga. Esta arquitetura pode ser utilizada para uma aplicação de camada única, tal como um cluster de aplicação ou de armazenamento da web sem estado. Também é um bloco modular para aplicações de N camadas. 

Este artigo constrói sobre como [executar uma única VM no Azure][single vm]. As recomendações no artigo também se aplicam a esta arquitectura.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

VMs no Azure necessitam de rede e armazenamento de recursos de suporte.

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está ativada a "cluster - separador VM multi." 

![[0]][0]

A arquitetura tem os seguintes componentes: 

- **Conjunto de disponibilidade.** [Definir disponibilidade] [ availability set] contém os VMs e é necessário para suportar a [disponibilidade SLA para Azure VMs][vm-sla].

- **VNet**. Cada VM no Azure é implementada para uma rede virtual (VNet) ainda mais estiver dividida em **sub-redes**.

- **Balanceador de carga Azure.** O [Balanceador de carga] distribui pedidos recebidos para as instâncias VM num conjunto de disponibilidade da Internet. O Balanceador de carga inclui alguns recursos relacionados:

  - **Endereço IP público.** Um endereço IP público é necessário para o Balanceador de carga receber o tráfego da Internet.

  - **Configuração de front-end.** Associa o endereço IP público Balanceador de carga.

  - **Conjunto de endereços de back-end.** Contém as interfaces de rede (NIC) para os VMs que irão receber o tráfego de entrada.

- **Regras do Balanceador de carga.** Utilizado para distribuir o tráfego de rede entre todos os VMs no conjunto de endereços de back-end. 

- **Regras NAT.** Utilizado para encaminhar tráfego para um VM específico. Por exemplo, para ativar o protocolo de ambiente de trabalho remoto (RDP) para os VMs, crie uma regra de tradução (NAT) do endereço de rede separada para cada VM. 

- **Interfaces de rede (NIC)**. Cada VM tem uma imagem para se ligar à rede.

- **Armazenamento.** Contas de armazenamento mantenha as imagens VM e outros recursos relacionados com o ficheiro, tal como os dados de diagnóstico VM capturados por Azure.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue as recomendações descritas abaixo. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico que não suporte uma recomendação. 

### <a name="availability-set-recommendations"></a>Disponibilidade definir recomendações

Tem de criar, pelo menos, duas VMs a disponibilidade definida para suportar a [disponibilidade SLA para Azure VMs][vm-sla]. Repare que o Balanceador de carga Azure requer também que balanceamento de carga VMs pertencem, para o mesmo conjunto de disponibilidade.

### <a name="network-recommendations"></a>Recomendações de rede

VMs atrás Balanceador de carga devem ser todos colocados dentro da mesma sub-rede. Não expõem VMs diretamente à Internet, mas dar em vez disso, cada VM um endereço IP privado. Os clientes se ligam utilizando o endereço IP público do Balanceador de carga.

### <a name="load-balancer-recommendations"></a>Recomendações de Balanceador de carga

Adicione todos os VMs a disponibilidade definida para o conjunto de back-end endereços do Balanceador de carga.

Defina regras de Balanceador de carga para direct tráfego de rede para os VMs. Por exemplo, para permitir o tráfego de HTTP, crie uma regra que mapas porta 80 da configuração de front-end a porta 80 no conjunto de endereços de back-end. Quando o Balanceador de carga recebe um pedido de porta 80 do endereço IP público,-lo será encaminhar o pedido para porta 80 das NIC no conjunto de endereços de back-end.

Defina regras NAT para encaminhar tráfego para um VM específico. Por exemplo, para ativar o RDP para os VMs criar uma regra NAT separada para cada VM. Cada regra deve mapear um número de porta distintos à porta 3389, a porta predefinida para RDP. (Por exemplo, utilize a porta 50001 para "VM1," porta 50002 para "VM2" etc.) Atribua as regras NAT para as NIC dos VMs. 

### <a name="storage-account-recommendations"></a>Recomendações de conta de armazenamento

Criar contas de armazenamento Azure separada para cada VM colocar em espera virtual disco rígido (VHDs), para evitar atingir as operações de entrada/saída por segundo [(IOPS) limites] [ vm-disk-limits] para contas de armazenamento. 

Crie uma conta de armazenamento para registos de diagnóstico. Esta conta de armazenamento pode ser partilhada por todos os as VMs.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Existem duas opções para escalar para fora VMs no Azure: 

- Utilize um balanceador de carga para distribuir o tráfego de rede por um conjunto de VMs. Para dimensionar saída, aprovisionar VMs adicionais e colocá-los por trás do Balanceador de carga. 

- Utilizar [conjuntos de escala de Máquina Virtual][vmss]. Um conjunto de escala contém um número de speficied de VMs idênticos atrás de um balanceador de carga. Escala VM define autoscaling suporte com base na métricas de desempenho. À medida que aumenta a carga de VMs, VMs adicionais são automaticamente adicionados ao balanceador de carga. 

As secções seguintes compare estas duas opções.

### <a name="load-balancer-without-vm-scale-sets"></a>Balanceador de carga sem VM conjuntos de escala

Um balanceador de carga assume pedidos recebidos de rede e distribui-los através de NIC no conjunto de endereços de back-end. Para dimensionar horizontalmente, adicionar mais instâncias VM para o conjunto de disponibilidade (ou retirar VMs para dimensionar para baixo). 

Por exemplo, suponha que estiver a executar o servidor web. É necessário adicionar uma regra de Balanceador de carga de portas 80 e/ou porta 443 (para SSL). Quando um cliente envia um pedido de HTTP, Balanceador de carga escolhe um endereço IP de back-end utilizando um [hashing algoritmo] [ load balancer hashing] que inclua o endereço IP de origem. De que forma, pedidos de cliente são distribuídos por todos os as VMs. 

> [AZURE.TIP] Quando adiciona uma nova VM a disponibilidade de uma conjunto, certifique-se criar uma imagem para a VM e adicionar NIC ao conjunto de back-end endereço no balanceador de carga. Caso contrário, o tráfego de Internet não ser encaminhado para a nova VM.

Cada subscrição Azure tem limites predefinidos no local, incluindo um número máximo de VMs por região. Pode aumentar o limite através de apresentação de um pedido de suporte. Para obter mais informações, consulte o artigo [subscrição Azure e limites de serviço, quotas e as restrições][subscription-limits].  

### <a name="vm-scale-sets"></a>Conjuntos de escala VM 

Conjuntos de escala VM ajudá-lo a implementar e gerir um conjunto de VMs idênticos. Com todos os outros VMs configurada do mesmo, conjuntos de escala VM suportam autoscale verdadeiro, sem VMs, tornando mais fácil criar serviços em grande escala de filtragem de cluster grande, dados de grandes e das cargas de trabalho em contentores de aprovisionamento previamente. 

Para mais informações acerca de conjuntos de escala VM, consulte o artigo [Descrição geral de conjuntos de escala de Máquina Virtual da][vmss].

Considerações para utilizar os conjuntos de escala VM:

- Se precisar de rapidamente Dimensionar saída VMs ou precisa de autoscale, considere conjuntos de escala. 

- Atualmente, conjuntos de escala não suportam discos de dados. As opções para armazenar os dados são armazenamento de ficheiros Azure, a unidade de sistema operativo, a unidade Temp ou um arquivo externo, como o armazenamento do Windows Azure. 

- Todas as instâncias VM dentro de uma escala de definidos automaticamente pertencem o mesmo conjunto de disponibilidade, com 5 falhas domínios e 5 atualização.

- Por predefinição, conjuntos de escala utilizam "overprovisioning", que significa que o conjunto de escala inicialmente disposições VMs mais do que lhes solicita, em seguida, elimina os VMs extra. Este procedimento melhora a taxa de sucesso global quando Aprovisiona os VMs. 

- Recomendamos que não mais, em seguida, que 20 VMs por armazenamento conta com overprovisioning VMs ativadas ou não mais de 40 com overprovisioning desativado.  

- Pode encontrar modelos de Gestor de recursos para implementar escala define nos [Modelos de guia de introdução do Azure][vmss-quickstart].

- Existem duas formas básicas para configurar VMs implementados num conjunto de escala: criar uma imagem personalizada ou utilizar extensões para configurar a VM depois está aprovisionado.

    - Todos os VHDs de disco SO dentro de uma conta de armazenamento tem de criar um conjunto de escala criada com base numa imagem personalizada. 

    - Com imagens personalizadas, tem de manter a imagem atualizados.

    - Com as extensões, poderá demorar mais de uma VM recentemente aprovisionada giratório para cima.

Para considerações adicionais, consulte o artigo [Estruturar VM escala conjuntos de escala][vmss-design].

> [AZURE.TIP]  Ao utilizar qualquer solução Dimensionar automaticamente, testá-la com cargas de trabalho de nível de produção bem com antecedência. 

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

A disponibilidade conjunto faz com que a aplicação mais flexível para planeados e não eventos de manutenção.

- _Manutenção planeada_ ocorre quando o Microsoft atualiza a plataforma subjacente, por vezes, a causar VMs de ser reiniciado. Azure torna-se de que o VMs num conjunto de disponibilidade não são todos reiniciado ao mesmo tempo, pelo menos um é mantido executar enquanto outras pessoas estão a ser reiniciado.

- _Manutenção imprevistas_ acontece se existir uma falha de hardware. Azure assegura que configurações VMs num conjunto de disponibilidade nos bastidores de servidor mais do que uma. Isto ajuda a reduzir o impacto da falhas de hardware, falhas de rede, power interrupções e assim sucessivamente.

Para mais informações, consulte o artigo [Gerir a disponibilidade de máquinas virtuais][availability set]. O vídeo seguinte também tem uma boa visão geral dos conjuntos de disponibilidade: [Efetue como posso configurar um conjunto de disponibilidade para escala VMs][availability set ch9]. 

> [AZURE.WARNING]  Certifique-se configurar a disponibilidade definir quando aprovisionar o a VM. Atualmente, não existe nenhuma forma de adicionar uma VM Gestor de recursos para uma disponibilidade definida após a VM está aprovisionada.

O Balanceador de carga utiliza [do Estado de funcionamento sondas] para monitorizar a disponibilidade de instâncias VM. Se uma sonda não é possível alcançar uma instância dentro de um período de tempo de espera, o Balanceador de carga deixa de enviar tráfego para esse VM. No entanto, o Balanceador de carga irão continuar a sonda de e, se a VM voltar a ficar disponível, o Balanceador de carga currículos enviar tráfego para esse VM.

Aqui estão algumas recomendações sobre sondas de estado de funcionamento do Balanceador de carga:

- Podem testar o sondas HTTP ou TCP. Se executar VMs um servidor HTTP (IIS, nginx, Node.js aplicação e assim sucessivamente), crie uma sonda HTTP. Caso contrário, crie uma sonda TCP.

- Sonda de HTTP, especifique o caminho para o ponto final de HTTP. A sonda verifica se ocorre uma resposta de HTTP 200 deste caminho. Isto pode ser o caminho da raiz ("/") ou um ponto final de monitorização que implementa algumas lógica personalizada para verificar o estado de funcionamento da aplicação. O ponto final têm de permitir pedidos HTTP anónimos.

- Enviado a sonda de um [conhecidos] [ health-probe-ip] em endereço IP, 168.63.129.16. Certifique-se de que não a bloquear o tráfego para ou a partir deste IP em qualquer políticas de firewall ou as regras de grupo (NSG) de segurança de rede.

- Utilizar [os registos do Estado de funcionamento sonda] [ health probe log] para ver o estado dos sondas estado de funcionamento. Ative o início de sessão no portal do Azure para cada Balanceador de carga. Registos são escritos ao armazenamento de Blobs do Azure. Os registos de mostram VMs quantos no back-end não está a receber o tráfego de rede devido a respostas sonda falha.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Com vários VMs, que se torne importante automatizar processos, para que fiquem fiável e possa ser repetida. Pode utilizar a [Automatização Azure] [ azure-automation] para automatizar a implementação, a aplicação de patches SO e outras tarefas. Automatização Azure é um serviço de automatização que é executada no Azure e é baseado no Windows PowerShell. Exemplo de automatização de scripts está disponíveis a partir da [Galeria de livro execuções] no TechNet.

## <a name="security-considerations"></a>Considerações de segurança

Redes virtuais são um limite de isolamento de tráfego no Azure. VMs numa que vnet não é possível comunicar diretamente para VMs num VNet diferente. VMs dentro da mesma VNet pode comunicar, a não ser que crie [grupos de segurança de rede] [ nsg] (NSGs) para restringir o tráfego. Para obter mais informações, consulte o artigo [serviços em nuvem da Microsoft e a segurança de rede][network-security].

Para receber tráfego da Internet, as regras do Balanceador de carga definem qual o tráfego pode contactar o back-end. No entanto, as regras do Balanceador de carga não suportam IP whitelisting, pelo que, se pretende lista branca endereços determinada IP público, adicionar um NSG à sub-rede.

## <a name="solution-deployment"></a>Implementação da solução

Uma implementação para uma arquitetura de referência que implementa estas recomendações está disponível no GitHub. Esta arquitetura de referência inclui uma rede virtual (VNet), grupo de segurança de rede (NSG), Balanceador de carga e duas máquinas virtuais (VMs).

A arquitetura de referência pode ser implementada com Windows ou Linux VMs ao seguir as instruções abaixo: 

1. Botão direito do rato no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Assim que tiver aberto a ligação no portal do Azure, tem de introduzir valores para algumas das definições: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Utilizar existente** e introduza `ra-multi-vm-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Selecione o **Tipo de SO** no pendente de caixa, **windows** ou **linux**. 
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

4. Os ficheiros de parâmetro incluem codificada administrador nome de utilizador e palavra-passe e é vivamente recomendado que imediatamente altere ambos. Clique no VM denominada `ra-multi-vm1` no portal do Azure. Em seguida, clique em **Repor palavra-passe** no pá de **suporte + resolução de problemas** . Selecione **Repor palavra-passe** na caixa de lista pendente **modo** , em seguida, selecione um novo **nome de utilizador** e **palavra-passe**. Clique no botão de **atualização** para guardar o novo nome de utilizador e palavra-passe. Repita para a VM denominada `ra-multi-vm2`.

Para obter informações sobre formas adicionais de implementar esta arquitetura de referência, consulte o ficheiro Leia-me na [orientação-único-vm] [ github-folder] GitHub pasta. 

## <a name="next-steps"></a>Próximos passos

Colocar várias VMs atrás de um balanceador de carga é um bloco modular para criar arquitecturas de várias camadas. Para obter mais informações, consulte o artigo [a executar o Windows VMs para uma arquitetura de camadas no Azure] [ n-tier-windows] e [Executar VMs Linux para uma arquitetura de camadas no Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[Estado de funcionamento sondas]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[Balanceador de carga]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Galeria de livro execuções]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Arquitetura de uma solução multi VM no Azure que inclua uma conjunto com duas VMs e um balanceador de carga de disponibilidade"

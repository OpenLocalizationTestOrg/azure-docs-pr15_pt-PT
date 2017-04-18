<properties
   pageTitle="Tipos de nó ferro de serviço e conjuntos de escala de VM | Microsoft Azure"
   description="Descreve como tipos de nó serviço ferro relacionam a VM os conjuntos de escala e como remoto para ligar a uma instância do conjunto de escala VM ou um nó de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>A relação entre tipos de nó ferro de serviço e conjuntos de escala de Máquina Virtual

Os conjuntos de escala de máquina virtual são um recurso Azure calcular, que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó é definido num cluster de serviço ferro está configurado como um conjunto de escala de VM em separado. Cada tipo de nó, em seguida, pode ser dimensionado para cima ou para baixo de forma independente, têm diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente.

A seguinte captura de ecrã mostra um cluster de que tem dois tipos de nó: front-end e back-end.  Cada tipo de nó tem cinco nós.

![Captura de ecrã de um cluster de que tem dois tipos de nó][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mapeamento de instâncias VM escala definida para nós

Como pode ver acima, as instâncias VM escala definida iniciar a partir instância 0 e, em seguida, vai para cima. A numeração é reflectida na nomes. Por exemplo, BackEnd_0 é instância 0 do conjunto de back-end VM escala. Este conjunto de escala de VM determinado tem cinco instâncias, com o nome BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando dimensionar o uma escala de VM configurar é criada uma nova instância. O novo nome da instância VM escala definida normalmente vai ser o nome do conjunto de escala VM + o número da instância seguinte. No nosso exemplo, é BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mapeamento de escala VM definir balanceadores de carga para cada nó tipo/VM conjunto de escala

Se já implementou o seu cluster a partir do portal ou tiver utilizado o modelo de Gestor de recursos de exemplo que fornecemos, em seguida, quando receber uma lista de todos os recursos num grupo de recursos, em seguida, verá os balanceadores de carga para cada tipo VM escala definida ou nó.

O nome seria algo parecido com: **LB -&lt;nome do tipo de nó&gt;**. Por exemplo, LB-sfcluster4doc-0, conforme apresentado nesta captura de ecrã:


![Recursos][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Remote ligar a uma instância do conjunto de escala VM ou um nó de cluster
Cada tipo de nó é definido num cluster está configurado como um conjunto de escala de VM em separado.  Isto significa que os tipos de nó podem ser dimensionados para cima ou para baixo para a forma independente e podem ser efetuadas de diferentes VM SKUs. Ao contrário única ocorrência VMs, as instâncias VM escala definida não obter um endereço IP virtual da sua própria. Por isso, pode ser um pouco um desafio quando está a procurar um endereço IP e porta que pode utilizar remoto para ligar a uma instância específica.

Eis os passos que pode seguir para descobri-los.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Passo 1: Descobrir o endereço IP virtual para o tipo de nó e, em seguida, regras de entrada NAT para RDP

Para obter que, o que precisa para obter os valores de regras NAT entrados que foram definidos como parte da definição do recurso para **Microsoft.Network/loadBalancers**.

No portal do, navegue para a pá do Balanceador de carga e, em seguida, **Definições**.

![LBBlade][LBBlade]


Em **Definições**, clique na **entrada NAT regras**. Este agora oferece-lhe o endereço IP e porta que pode utilizar para remoto ligar-se para a primeira instância VM escala definida. Na captura de ecrã abaixo, é **104.42.106.156** e **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Passo 2: Localizar a porta que pode utilizar para remoto ligar ao específico VM escala definida instância/nó de saída

Anteriormente neste documento, posso falou como as instâncias VM escala definida mapeiam para os nós. Irá utilizamos que descobrir a porta exata.

As portas são atribuídas por ordem ascendente da instância VM escala definida. por isso, meu exemplo para o tipo de nó front-end, as portas para cada um dos cinco ocorrências são as seguintes. Agora tem de fazer o mesmo mapeamento para sua instância VM escala definida.

|**Escala VM defina instância**|**Porta**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Passo 3: Remote ligar para a instância específica do VM escala definida

Na captura de ecrã abaixo posso utilizar a ligação de ambiente de trabalho remoto para ligar para o FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Como alterar os valores de intervalo de porta RDP

### <a name="before-cluster-deployment"></a>Antes da implementação de cluster

Quando estiver a configurar o cluster utilizando um modelo de Gestor de recursos, pode especificar o intervalo na **inboundNatPools**.

Ir para a definição de recursos para **Microsoft.Network/loadBalancers**. Em que encontrará a descrição para **inboundNatPools**.  Substitua os valores *frontendPortRangeStart* e *frontendPortRangeEnd* .

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Após a implementação de cluster
Este é um pouco mais complexo e pode resultar em VMs introdução reciclados. Agora terá de configurar novos valores através do Azure PowerShell. Certifique-se de que o Azure PowerShell 1.0 ou posterior está instalado no seu computador. Se já não tiver concluído isto antes, posso vivamente que segue os passos descritos no [como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Inicie sessão sua conta Azure. Se este comando do PowerShell falhar por algum motivo, deve verificar se tem Azure PowerShell instalado corretamente.

```
Login-AzureRmAccount
```

Execute o seguinte para obter detalhes sobre Balanceador de carga e verá os valores para a descrição **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Agora definido *frontendPortRangeEnd* e *frontendPortRangeStart* para os valores que pretende.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Próximos passos

- [Descrição geral da funcionalidade de "Implementar em qualquer lugar" e uma comparação com clusters gerido Azure](service-fabric-deploy-anywhere.md)
- [Cluster de segurança](service-fabric-cluster-security.md)
- [Serviço ferro SDK e começar a trabalhar](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

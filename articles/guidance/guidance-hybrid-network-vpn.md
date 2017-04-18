<properties
   pageTitle="Implementar uma arquitetura de rede híbrido com VPN Azure e no local | Microsoft Azure"
   description="Como implementar uma arquitetura de rede de site para o site seguro que abrange uma rede virtual Azure e uma rede no local ligado através de uma VPN."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Implementar uma arquitetura de rede híbrida com o Azure e VPN no local

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve um conjunto de práticas para expandir uma rede no local para Azure utilizando uma rede privada virtual (VPN) do site para o site. O tráfego flua entre à rede no local e uma rede Virtual através de um túnel IPSec VPN Azure (VNet). Esta arquitetura é adequada para aplicações híbridas com as seguintes características:

- Peças da aplicação executar no local, enquanto outras pessoas executar no Azure.

- O tráfego entre no local hardware e na nuvem é provável que seja claro ou é vantajoso comércio latência ligeiramente adicional para a flexibilidade e a capacidade de processamento na nuvem.

- A rede adicional constitui um sistema fechado. Não existe nenhuma direto caminho da Internet para o VNet Azure.

- Os utilizadores ligar à rede no local para utilizar os serviços alojados no Azure. Bridge entre à rede no local e os serviços em execução no Azure é transparente para os utilizadores.

Exemplos de cenários que se ajustam a este perfil incluem:

- Aplicações de linha de negócio utilizadas numa organização, onde parte da funcionalidade tenha sido migrada para a nuvem.

- Desenvolvimento / / laboratório de teste das cargas de trabalho.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos do Azure] [ resource-manager-overview] e clássico. Este plano utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes nesta arquitectura:

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está na "híbrido rede - VPN" página.

![[0]][0]

- **Rede no local.** Esta é uma rede de computadores e dispositivos, ligados através de uma rede privada de rede local em execução no interior de uma organização.

- **Aparelho VPN.** Este é um dispositivo ou serviço que fornece conectividade externa à rede no local. Aparelho VPN pode ser um dispositivo de hardware ou pode ser uma solução de software como o encaminhamento e o serviço de acesso remoto (RRAS) no Windows Server 2012.

> [AZURE.NOTE] Para obter uma lista de eletrodomésticos de VPN suportados e informações sobre como configurar selecionados eletrodomésticos de VPN para ligar a um Gateway de VPN Azure, consulte as instruções para o dispositivo adequado na [lista de dispositivos VPN suportados pelo Azure][vpn-appliance].

- **Aplicação da nuvem de camadas.** Esta é a aplicação alojada no Azure. Pode incluir várias camadas, com várias sub-redes ligados através de balanceadores de carga Azure. O tráfego em cada sub-rede poderá estar sujeito a regras definidas ao utilizar [Grupos de segurança de rede do Azure (NSGs)][azure-network-security-group]. Para obter mais informações, consulte o artigo [Introdução à segurança do Microsoft Azure][getting-started-with-azure-security].

> [AZURE.NOTE] Este artigo descreve a aplicação de nuvem, como uma única entidade. Consulte o artigo [executar uma arquitetura de camadas no Azure] [ implementing-a-multi-tier-architecture-on-Azure] para obter informações detalhadas.

- **[Rede virtual (VNet)][azure-virtual-network].** A aplicação de nuvem e os componentes do Azure VPN Gateway residem na VNet mesmo.

- **[Azure VPN Gateway][azure-vpn-gateway].** O serviço de gateway VPN permite-lhe ligar a VNet à rede no local através de um aparelho VPN. Para mais informações, consulte o artigo [ligar uma rede no local para uma rede virtual do Microsoft Azure][connect-to-an-Azure-vnet]. O Gateway VPN inclui os seguintes elementos:

  - **Gateway de rede virtual.** Este é um recurso que fornece um virtual aparelho de VPN para a VNet. É responsável por encaminhamento de tráfego da rede no local para o VNet.

  - **Gateway de rede local.** Esta é uma abstracção do aparelho VPN no local. O tráfego de rede da aplicação na nuvem à rede no local é encaminhado através deste gateway.

  - **Ligação.** A ligação tem propriedades que especificar o tipo de ligação (IPSec) e a chave partilhado com aparelho VPN no local para encriptar o tráfego.

  - **Sub-rede de gateway.** O gateway de rede virtual é mantido na sua própria sub-rede, que está sujeito a requisitos diferentes, tal como descrito na secção de recomendações abaixo.

- **Balanceador de carga interno.** O tráfego de rede do VPN Gateway é encaminhado para a aplicação na nuvem através de um balanceador de carga interno. O Balanceador de carga está localizado no sub-rede front-end da aplicação.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="vnet-and-gateway-subnet"></a>Sub-rede VNet e Gateway

Crie um VNet Azure para a suspensão os componentes na nuvem. O espaço de endereços da VNet Azure tem de ser grande o suficiente para acomodar os endereços utilizados pelo VMs e sub-redes a VNet. Certifique-se de que o espaço de endereços VNet tem espaço suficiente para o crescimento se VMs adicionais provavelmente ser necessárias no futuro. O espaço de endereços da VNet não deve sobrepor-se com a rede no local. Por exemplo, o diagrama acima utiliza o 10.20.0.0/16 espaço endereço para a VNet.

Crie uma sub-rede denominada _GatewaySubnet_, com um intervalo de /27 endereços. Esta sub-rede uma ferramenta necessária ao gateway rede virtual e, a atribuição de 32 endereços para esta sub-rede irá ajudar a evitar que está a executar compara limitações de tamanho do gateway possíveis no futuro. Evite colocar nesta sub-rede no meio o espaço de endereços. É aconselhável definir o espaço de endereços para a sub-rede gateway na extremidade superior do espaço de endereços VNet. O exemplo apresentado no diagrama utiliza 10.20.255.224/27.  Um procedimento rápido para calcular o CIDR é da seguinte forma:

1. Defina os bits variáveis no espaço de endereço do VNet como 1, até os bits a ser utilizado por sub-rede gateway, em seguida, defina os restantes bits como 0.

2. Converter os bits resultantes decimal e expressá-lo como um espaço de endereços com o conjunto de comprimento prefixo para o tamanho da sub-rede gateway.

Por exemplo, para um VNet com um intervalo de endereços IP do 10.20.0.0/16, aplicar passo #1 acima torna-se 10.20.0b11111111.0b11100000.  Converter que decimal e expressá-lo como um espaço de endereços rendimentos 10.20.255.224/27

> [AZURE.WARNING] Não implemente outras máquinas virtuais ou instâncias de função à sub-rede de gateway. Além disso, não atribua um NSG para esta sub-rede, à medida que vai fazer com que o gateway para parar de funcionar.

### <a name="virtual-network-gateway"></a>Gateway de rede virtual

Atribua um endereço IP público para o gateway de rede virtual.

Criar um gateway de rede virtual na sub-rede de Gateway e atribuí-la o endereço IP público recentemente atribuído. Utilizar o tipo de gateway que corresponda aos requisitos da melhor e o que é activado pelo seu aparelho VPN:

Criar um [gateway baseada em políticas] [ policy-based-routing] se precisar de estreitamente controlar como os pedidos são encaminhados. com base em critérios de política como prefixos de endereço. Baseada em políticas gateways utilizam encaminhamento estático e só funcionam com ligações de site para o site.

Criar um [gateway com base em encaminhar] [ route-based-routing] se ligar à rede no local utilizando RRAS, a suportar ligações de sites com várias ou região cruzada ou implementar ligações VNet para VNet (incluindo rotas percorrer vários VNets). Com base em encaminhar gateways utilizam o encaminhamento dinâmico para direct tráfego entre redes. Estes podem tolerar falhas no caminho da rede melhor do rotas estáticas, uma vez que o podem experimentar rotas alternativas. Com base em encaminhar gateways também podem reduzir o overhead gestão, porque rotas poderão não ter de ser atualizadas manualmente quando alterar endereços de rede.

Para uma lista de eletrodomésticos de VPN suportados, consulte o artigo [sobre VPN dispositivos para ligações de Site para o Site VPN Gateway][vpn-appliances].

> [AZURE.NOTE] Depois do gateway foi criado, não é possível alterar entre os tipos de gateway sem eliminar e voltar a criar o gateway.

Selecione o Azure VPN Gateway SKU que corresponda aos requisitos da débito melhor. Azure VPN Gateway está disponível em três SKUs apresentados na seguinte tabela. Cada SKU fornece débito diferente, [sejam cobrados] [ azure-gateway-charges] com base na quantidade de tempo que o gateway está aprovisionado e disponíveis.

| SKU | VPN débito | Estabelece um túnel IPSec máximo|
|-----|----------------|------------------|
| Básicas | 100 Mbps | 10 |
| Padrão | 100 Mbps | 10 |
| Alto desempenho | 200 Mbps | 30 |

> [AZURE.NOTE] Não é compatível com o Azure ExpressRoute o SKU básica. Pode [alterar o SKU] [ changing-SKUs] após a criação de gateway.

Crie regras de encaminhamento para a sub-rede gateway tráfego de aplicação de entrada direta de gateway Balanceador de carga interno em vez de permitir pedidos transmitir diretamente para os VMs que implementam a aplicação.

### <a name="on-premises-network-connection"></a>Ligação de rede no local

Crie um gateway de rede local. Especifique o endereço IP público do aparelho VPN no local e o espaço de endereços da rede no local. Tenha em atenção que aparelho VPN no local tem de ter um endereço IP público que pode ser acedido por gateway rede local no Azure VPN Gateway. Não é possível localizar o dispositivo VPN atrás de um dispositivo NAT.

Crie uma ligação de site para o site para o gateway de rede virtual e o gateway de rede local. Selecione o tipo de ligação de Site para site (IPSec) e, especifique a chave partilhada. Encriptação de site para o site com o Gateway de VPN Azure baseia o protocolo IPSec, utilizar teclas de pré-partilhadas para autenticação. Especificar a chave ao criar o Gateway de VPN Azure. Tem de configurar o aparelho VPN em execução no local com a mesma chave. Outros mecanismos de autenticação atualmente não são suportados.

Certifique-se de que no local infraestrutura encaminhamento está configurada para reencaminhar pedidos destinados endereços na VNet Azure para o dispositivo VPN.

Abra qualquer portas necessárias pela aplicação na nuvem na rede no local.

Teste a ligação para verificar que:

- Aparelho VPN no local corretamente encaminha o tráfego para a aplicação na nuvem através do Gateway de VPN Azure.

- O VNet corretamente encaminha o tráfego voltar à rede no local.

- Tráfego proibido em ambos os sentidos é bloqueado corretamente.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

É possível alcançar limitada escalabilidade vertical movendo a partir da Basic ou padrão SKUs de Gateway de VPN para a alta VPN SKU do desempenho.

Para VNets esperar um grande volume de tráfego VPN, considere distribuir as cargas de trabalho diferentes para separada VNets mais pequeno e configuração de um Gateway de VPN para cada um deles.

Pode criar partições a VNet horizontal ou verticalmente. Para criar partições horizontalmente, mova algumas instâncias VM de cada camada em sub-redes de VNet o novo. O resultado é que cada VNet tem a mesma estrutura e a funcionalidade. Para criar partições verticalmente, de reestruturar as cada camada para dividir a funcionalidade de em diferentes áreas lógicas (como o processamento de encomendas, a faturação, gestão de conta de cliente e assim sucessivamente). Cada área funcional, em seguida, pode ser colocada na sua própria VNet.

Replicação um controlador de domínio do Active Directory no local na VNet e implementar DNS no VNet, podem ajudar a reduzir alguns do tráfego relacionado com a segurança e administrativo fluir de no local para a nuvem.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Se precisar para se certificar de que a rede no local permanece disponível para o Gateway de VPN Azure, implemente um cluster de activação pós-falha para o Gateway de VPN no local.

Se a sua organização tiver vários sites no local, criar [ligações de sites com várias] [ vpn-gateway-multi-site] para um ou mais VNets Azure. Esta abordagem requer (baseado em encaminhar) encaminhamento dinâmico, por isso, certifique-se de que o Gateway de VPN no local suporta esta funcionalidade.

Consulte o artigo [SLA para o VPN Gateway] [ sla-for-vpn-gateway] para os detalhes sobre a SLA de Gateway VPN.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Monitorizar as informações de diagnóstico do eletrodomésticos de VPN no local. Este processo depende de funcionalidades fornecidas pelo aparelho VPN. Por exemplo, se estiver a utilizar o encaminhamento e o serviço de acesso remoto no Windows Server 2012, deverá ativar [registo RRAS][rras-logging].

Utilizar o [Azure VPN Gateway diagnósticos] [ gateway-diagnostic-logs] para capturar informações sobre problemas de conectividade. Estes registos podem ser utilizados para controlar informações tais como a origem e destinos de ligação pedidos, protocolo que foi utilizado e como a ligação foi estabelecida (ou porque é que a tentativa de falhou).

Monitorize os registos operacionais do Azure VPN Gateway utilizando os registos de auditoria disponíveis no portal do Azure. Estão disponíveis para o gateway de rede local, o gateway Azure rede e a ligação de registos separados. Estas informações podem ser utilizadas para controlar quaisquer alterações efetuadas para o gateway e podem ser útil se um gateway anteriormente funcionamento deixa de funcionar por algum motivo.

![[2]][2]

Monitorizar conectividade e controlar os eventos de falha da conectividade. Pode utilizar um pacote de monitorização como [Nagios] [ nagios] para capturar e comunicar estas informações.

## <a name="security-considerations"></a>Considerações de segurança

Gere uma chave partilhada diferente para cada gateway VPN. Utilize uma chave partilhada forte para ajudar a resistir ataques de força.

> [AZURE.NOTE] Atualmente, não pode utilizar o Azure Cofre de chave do gateway Azure VPN chaves previamente partilhadas.

Certifique-se de que o aparelho de VPN no local utiliza um método de encriptação que seja [compatível com o Gateway de VPN Azure][vpn-appliance-ipsec]. Para o encaminhamento de baseada em políticas, o Gateway de VPN Azure suporta algoritmos de encriptação AES256, AES128 e 3DES. Com base em encaminhar gateways suportam AES256 e 3DES.

Se for o seu aparelho VPN no local numa rede de perímetro que tem uma firewall entre rede de perímetro e a Internet, poderá ter que configurar [regras de firewall adicionais] [ additional-firewall-rules] para permitir a ligação VPN do site para o site.

Se a aplicação na VNet envia dados para a Internet, considere a [implementação de túnel forçada] [ forced-tunneling] para encaminhar todo o tráfego de Internet vinculadas através da rede no local. Esta abordagem permite-lhe para auditoria enviados pedidos feitos pela aplicação a partir de infraestrutura no local.

> [AZURE.NOTE] Túnel forçada pode ter impacto conectividade ao Azure serviços (o serviço de armazenamento, por exemplo) e o Gestor de licença do Windows.

## <a name="troubleshooting-considerations"></a>Considerações sobre a resolução de problemas

> [AZURE.NOTE] Visite o blogue de acesso remoto de encaminhamento e para obter informações gerais sobre [resolução de erros comuns relacionados com VPN][troubleshooting-vpn-errors].

Se não conseguir percorrer a ligação VPN tráfego, verifique o seguinte:

### <a name="on-premises-vpn-appliance"></a>Aparelho de VPN no local:

**Aparelho VPN no local está a funcionar corretamente?**

Verificação de qualquer gerados pelo aparelho VPN existência de erros e falhas de ficheiros de registo. A localização destas informações variam de acordo com o seu aparelho. Por exemplo, se estiver a utilizar RRAS no Windows Server 2012, pode utilizar o seguinte comando do PowerShell para apresentar informações de evento de erro do serviço RRAS:

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

A propriedade da _mensagem_ de cada entrada fornece uma descrição do erro. Alguns exemplos comuns são:

- Impossibilidade de ligar, possivelmente devido a um endereço IP incorreto especificado para o Gateway de VPN Azure na configuração da interface rede RRAS VPN:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- A chave partilhada mal a ser especificada na configuração da interface rede RRAS VPN:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

Também pode obter informações de registo de eventos sobre tentativas de se ligar através do serviço RRAS utilizando o seguinte comando PowerShell:

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

Em caso de falha para ligar, este registo irá conter erros que têm um aspeto semelhantes ao seguinte:

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**É o VPN aparelho corretamente encaminhamento de tráfego através do Gateway de VPN Azure?**

Utilizar uma ferramenta como [PsPing] [ psping] para verificar a conectividade e de encaminhamento através de gateway de VPN. Por exemplo, para testar a conectividade a partir de uma máquina no local para um servidor web localizado na VNet, execute o seguinte comando (substituir `<<web-server-address>>` com o endereço do servidor web):

```
PsPing -t <<web-server-address>>:80
```

Se o computador no local pode encaminhar o tráfego para o servidor web, deverá ver saída semelhante ao seguinte:

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Se o computador no local não é possível comunicar com o destino especificado, irá ver as mensagens da seguinte forma:

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**A firewall no local permite o tráfego VPN passar? Foi abertas as portas corretas?**

Certifique-se de que o aparelho de VPN no local utiliza um método de encriptação que seja [compatível com o Gateway de VPN Azure][vpn-appliance].

Para o encaminhamento de baseada em políticas, o Gateway de VPN Azure suporta algoritmos de encriptação AES256, AES128 e 3DES. Com base em encaminhar gateways suportam AES256 e 3DES.

### <a name="azure-vnet-gateway"></a>Azure VNet Gateway:

Examinar [registos de diagnóstico do Azure VPN Gateway] [ gateway-diagnostic-logs] para potenciais problemas.

**São as Azure VPN Gateway e aparelho de VPN no local configurados com a mesma chave de autenticação partilhada?**

Pode ver a chave partilhada armazenada ao Azure VPN Gateway utilizando o comando Azure clip seguinte:

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Utilize o comando adequado ao seu aparelho de VPN no local para mostrar a chave partilhada configurada para esse aparelho.

Certifique-se de que a sub-rede _GatewaySubnet_ mantendo que Azure VPN Gateway não está associado uma NSG.

Pode ver os detalhes de sub-rede utilizando o comando Azure clip seguinte:

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Garantir que não haja é nenhum campo de dados com o nome _id do grupo de segurança de rede_. O exemplo seguinte mostra os resultados por exemplo dos _GatewaySubnet_ que tem um NSG atribuída (_Grupo de Gateway VPN_). Isto pode causar impedir que o gateway da funcionar corretamente se existirem todas as regras definidas para esta NSG:

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**São as máquinas virtuais no VNet Azure configuradas para permitir o tráfego provenientes de fora da VNet? Verifique todas as regras NSG associadas sub-redes que contém estas máquinas virtuais.**

Pode ver todas as regras de NSG utilizando o comando Azure clip seguinte:

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**O Gateway de VPN Azure foi desligado?**

Pode utilizar o seguinte comando Azure PowerShell para verificar o estado atual da ligação Azure VPN. O `<<connection-name>>` parâmetro é o nome da ligação Azure VPN que liga o gateway de rede virtual e o gateway local.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

Os seguintes fragmentos realçar a saída gerada se o gateway está ligado (o primeiro exemplo) e perder a ligação (o segundo exemplo):

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Configuração do anfitrião de VM, utilização da largura de banda de rede e desempenho da aplicação:

Certifique-se de que a firewall no sistema operativo convidado em execução no VMs Azure na sub-rede estão configurados corretamente para permitir o tráfego permitido dos intervalos IP no local.

**Está disponível para o Gateway de VPN Azure o volume de tráfego fechar para o limite da largura de banda?**

Ferramentas depende das instalações disponíveis para o seu aparelho VPN em execução no local. Por exemplo, se estiver a utilizar RRAS no Windows Server 2012, pode utilizar o Monitor de desempenho para controlar o volume de dados a ser recebidos e transmitidos através da ligação VPN; utilizando o objeto _RAS Total_ , selecione os contadores _Bytes recebidos/seg_ e _Bytes transmitidos/seg_ :

![[3]][3]

Deve comparar os resultados com a largura de banda disponível para o gateway VPN (100 Mbps para Basic e SKUs padrão e 200 Mbps para o SKU desempenho alta):

![[4]][4]

**São qualquer uma das máquinas virtuais no VNet Azure lento? São que estes sobrecarregado, são suficiente dos mesmos para processar a carga, existem todos os balanceadores de carga corretamente configurados?**

Isto requer que [capturar e analisar informações de diagnóstico][azure-vm-diagnostics]. Pode examinar os resultados através do portal Azure, mas muitas ferramentas de terceiros estão também disponíveis que pode fornecer informações detalhadas dos dados de desempenho.

**É utilizar a aplicação tornar eficiente de recursos na nuvem?**

Instrument aplicação a execução de código em cada VM para determinar se aplicações estejam a efectuar a melhor utilização de recursos. Pode utilizar ferramentas como [Informações de aplicação] [ application-insights] para o ajudar a efetuar as seguintes tarefas.

## <a name="solution-deployment"></a>Implementação da solução

Se tiver uma infraestrutura no local existente já tenha configurada com um aparelho VPN, pode implementar a arquitetura de referência ao seguir estes passos:

1. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Aguarde que a ligação abra no portal do Azure e, em seguida, siga estes passos: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-hybrid-vpn-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

## <a name="next-steps"></a>Próximos passos

- [Instalar controladores de domínio adicionais para um domínio do Active Directory no local utilizar VMs numa VNet Azure][installing-ad].

- [Diretrizes para implementar o Active Directory do Windows Server no Azure VMs][deploying-ad].

- [Criar um servidor de DNS num VNet][creating-dns].

- [Configurar e gerir registos DNS num VNet][configuring-dns].

- [Usar eletrodomésticos de segurança de rede de Stormshield no local em está numa VPN][stormshield].

- [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Estrutura de um híbrido de rede que abrangem no local e na nuvem infra-estruturas"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Criar a partições um VNet para melhorar a escalabilidade"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Os registos de auditoria no portal do Azure"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Contadores de desempenho para monitorizar o tráfego de rede VPN"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Gráfico de desempenho de rede VPN de exemplo"
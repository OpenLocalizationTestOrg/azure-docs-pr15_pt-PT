<properties
   pageTitle="Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute | Arquitetura de referência | Microsoft Azure"
   description="Como implementar uma arquitetura de rede de site para o site seguro que abrange uma rede virtual Azure e uma rede no local ligado utilizando o Azure ExpressRoute."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
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
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as melhores práticas para ligar uma rede no local à redes virtuais no Azure utilizando ExpressRoute. ExpressRoute ligações são efectuadas utilizando uma ligação privada dedicada através de um fornecedor de conectividade de terceiros. A ligação privada expande a sua rede no local para o access fornece Azure à sua própria infraestrutura IaaS no Azure, públicos pontos finais utilizados no PaaS serviços e os serviços de SaaS do Office 365. Este documento foca-se em utilizar ExpressRoute para ligar a uma única Azure virtual rede (VNet) utilizando o que é denominado efectuado privado.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Este plano utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Casos de utilização típica para esta arquitectura incluem:

- Aplicações híbridas onde das cargas de trabalho são distribuídas entre uma rede no local e o Azure.

- Aplicações em execução em grande escala, críticos das cargas de trabalho que requerem um elevado grau de escalabilidade.

- Em grande escala meios de cópia de segurança e restauro de dados que tem de ser guardados noutro local.

- Processamento Big Data das cargas de trabalho.

- Utilizar o Azure como um site de recuperação de falhas.

> [AZURE.NOTE] [Descrição geral técnica de ExpressRoute] [ expressroute-technical-overview] fornece uma introdução à ExpressRoute.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura:

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama é na página "Híbrido rede - ER".

![[0]][0]

- **Redes virtuais Azure (VNets).** Cada VNet reside numa única região Azure e pode alojar várias camadas de aplicação. Camadas de aplicação podem ser segmentadas utilizar sub-redes em cada VNet e/ou grupos de segurança (NSGs) de rede. 

- **Serviços de públicos Azure.** Estes são os serviços Azure que podem ser utilizados dentro de uma aplicação híbrida. Estes serviços também estão disponíveis através da Internet pública, mas a aceder a através de um circuito ExpressRoute fornece latência baixa e desempenho mais previsível, uma vez que o tráfego não aceda através da Internet. Ligações são executadas utilizando **efectuado público**, com os endereços que são pertencente a sua organização ou fornecidos pelo seu fornecedor de conectividade. 

- **Serviços do Office 365.** Estes são os publicamente disponíveis aplicações do Office 365 e serviços fornecidos pela Microsoft. Ligações são executadas ao utilizar o **Microsoft efectuado**, com os endereços que são pertencente a sua organização ou fornecidos pelo seu fornecedor de conectividade.

>[AZURE.NOTE] Também pode ligar diretamente para o Microsoft CRM Online através de um efectuado da Microsoft.

- **Rede de empresa no local.** Esta é uma rede de computadores e dispositivos, ligados através de uma rede privada de rede local em execução no interior de uma organização.

- **Routers limite local.** Estes são routers ligam à rede no local ao circuito gerido pelo fornecedor. Dependendo de como a sua ligação está aprovisionada, tem de fornecer os endereços IP públicos utilizados pelos routers. 

- **Routers de limite da Microsoft.** Estes são os dois routers numa configuração activo activo altamente disponível. Estes routers ativar um fornecedor de conectividade ligar os seus circuitos diretamente para o Centro de dados. Dependendo de como a sua ligação está aprovisionada, tem de fornecer os endereços IP públicos utilizados pelos routers.

- **ExpressRoute circuito.** Esta é uma camada 2 ou circuito camada 3 fornecido pelo fornecedor de conectividade essa rede de associações no local com o Azure através de routers o limite. O circuito utiliza a infraestrutura de hardware gerida pelo fornecedor de conectividade.

- **Fornecedores de conectividade.** Estes são empresas que fornecem uma ligação utilizando a camada 2 ou a conectividade de camada 3 entre o seu centro de dados e um centro de dados do Azure.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="connectivity-providers"></a>Fornecedores de conectividade

Selecione um fornecedor de conectividade ExpressRoute adequado para a sua localização. Para obter uma lista de fornecedores de conectividade disponíveis na sua localização, utilize o seguinte comando Azure PowerShell:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Fornecedores de conectividade de ExpressRoute ligam o seu centro de dados para a Microsoft das seguintes formas:

- **Cocriação localizado num exchange na nuvem.** Se estiver a cocriação localizado numa instalação com o exchange uma nuvem, pode encomendar ligações de publicação em virtual na nuvem da Microsoft através de intercâmbio de Ethernet o fornecedor de cocriação localização. Fornecedores de cocriação localização podem oferecer ligações de publicação em camada 2 ou geridas Layer 3 publicação em várias ligações entre sua infraestrutura da funcionalidade de cocriação localização e na nuvem da Microsoft...

- **Ligações de Ethernet ponto a ponto.** Pode ligar os seus escritórios/centros de dados de no local para a nuvem da Microsoft através de ligações de Ethernet ponto a ponto. Fornecedores de Ethernet ponto a ponto podem oferecer ligações camada 2, ou geridos Layer 3 ligações entre o site e na nuvem da Microsoft.

- **Qualquer para qualquer redes de (IPVPN).** Pode integrar o seu WAN com a nuvem da Microsoft. Fornecedores de IPVPN (normalmente MPLS VPN) oferecem qualquer para qualquer conectividade entre o seu filiais e centros de dados. Pode ser interligada na nuvem da Microsoft para o seu WAN de que tal como qualquer outro filial. Fornecedores de WAN oferecem normalmente gerida Layer 3 conectividade.

Para obter mais informações acerca dos fornecedores de conectividade, consulte o artigo [ExpressRoute circuitos e domínios de encaminhamento][connectivity-providers].

### <a name="expressroute-circuit"></a>ExpressRoute circuito

Certifique-se de que a sua organização passou os [requisitos de pré-requisito ExpressRoute] [ expressroute-prereqs] para ligar à Azure.

Se ainda não o fez, adicione uma sub-rede denominada `GatewaySubnet` para sua VNet Azure e criar um gateway de rede virtual ExpressRoute através do serviço do Azure VPN Gateway. Para obter mais informações sobre este processo, consulte o artigo [ExpressRoute fluxos de trabalho para os Estados dos circuitos e de aprovisionamento de circuito][ExpressRoute-provisioning].

Crie um circuito ExpressRoute da seguinte forma:

1. Execute o seguinte comando do PowerShell:

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Enviar a `ServiceKey` para o novo circuito ao fornecedor de serviços.

3. Aguarde para o fornecedor de aprovisionamento de circuito. Pode verificar o estado de aprovisionamento de um circuito utilizando o comando PowerShell seguinte:

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

O `Provisioning state` campo a `Service Provider` secção do resultado será alterado do `NotProvisioned` para `Provisioned` quando o circuito está pronto.

>[AZURE.NOTE]Se estiver a utilizar uma ligação de camada 3, o fornecedor deve configurar e gerir o encaminhamento para si; fornecer as informações necessárias para permitir que o fornecedor implementar as rotas apropriadas.

Se estiver a utilizar uma ligação de camada 2, siga os passos abaixo:

1. Reservar dois/30 sub-redes composto por endereços IP públicos válidos para cada tipo de efectuado que pretende implementar. Estes/30 sub-redes serão utilizadas para fornecer endereços IP para routers utilizados para o circuito. Se estiver a implementar privado, público e Microsoft efectuado, terá de 6 /30 sub-redes com endereços IP públicos válidos.     

2. Configure o encaminhamento para o circuito ExpressRoute. Que precisa para executar o comando abaixo para cada tipo de efectuado que pretende configurar (privado, público e Microsoft).

    >[AZURE.NOTE]Consulte o artigo [criar e modificar o encaminhamento para um circuito ExpressRoute] [ configure-expressroute-routing] para obter detalhes. Utilize os seguintes comandos do PowerShell para adicionar uma efectuado para encaminhar o tráfego de rede:

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Reserve outro conjunto de dados de endereços IP públicos válidos para utilização para NAT para o público e Microsoft efectuado. Recomenda-se de ter um conjunto diferente para cada efectuado. Especifique o agrupamento ao seu fornecedor de conectividade, de modo que pode configurar anúncios de BGP para desses intervalos.

[Ligar o seu VNet(s) privado na nuvem ao circuito ExpressRoute][link-vnet-to-expressroute]. Utilize os seguintes comandos do PowerShell:

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Tenha em atenção os seguintes pontos:

- ExpressRoute utiliza o limite Gateway Protocol (BGP) para o intercâmbio de informações de encaminhamento entre o Azure e de rede.

- Pode ligar a várias VNets localizado na regiões diferentes para o mesmo circuito ExpressRoute desde que VNets todas as e o circuito ExpressRoute estão localizados dentro da mesma região geopolítica.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

ExpressRoute circuitos fornecem um caminho de largura de banda alta entre redes. Em geral, quanto maior a largura de banda maior será o custo. Apesar de alguns fornecedores de lhe permitir alterar a largura de banda, certifique-se de que escolher uma largura de banda inicial que substitui as suas necessidades e fornece sala para crescimento. No caso de precisar de aumentar a largura de banda no futuro, ficam com duas opções.

Aumente a largura de banda. Lembre-se de que não em todos os fornecedores permitem-lhe para o fazer dinamicamente. E evitar deve ter de fazer neste quanto possível. Mas, se for necessário, depois de verificar com o seu fornecedor, execute os comandos abaixo.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Pode aumentar a largura de banda sem perda de conectividade. Mudar a largura de banda irá resultar em interrupção de conectividade. Tem de eliminar o circuito e recrie-lo com a nova configuração.

Se estiver a utilizar o SKU padrão para ExpressRoute e precisa de atualizar para o Premium ou alterar está a sua preços planeie (com tráfego limitado ou ilimitado), execute os comandos abaixo. O `Sku.Tier` propriedade pode ser `Standard` ou `Premium`; o `Sku.Name` propriedade pode ser `MeteredData` ou `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Certifique-se a `Sku.Name` propriedade correspondências a `Sku.Tier` e `Sku.Family`. Se alterar a família e camada, mas não o nome, a ligação estarão desativada.

Pode atualizar o SKU sem interrupção, mas não é possível mudar a partir do plano de preços ilimitado para com tráfego limitado. Quando mudar o SKU, o consumo de largura de banda tem de permanecer dentro do limite predefinido do SKU padrão.

> [AZURE.NOTE] ExpressRoute oferece dois comparar planos aos clientes, com base em dados de medição ou ilimitados. Consulte o artigo [ExpressRoute preços] [ expressroute-pricing] para obter detalhes. Encargos variam de acordo com largura de banda de circuito. Largura de banda disponível poderão variar do fornecedor para fornecedor. Utilizar o `Get-AzureRmExpressRouteServiceProvider` cmdlet para ver os fornecedores de disponíveis na sua região e larguras de banda que disponibilizam.

Um circuito ExpressRoute único pode suportar um número de peerings e VNet ligações. Consulte o artigo [limites de ExpressRoute] [ expressroute-limits] para obter mais informações.

Para um encargo extra, fornece ExpressRoute Premium suplemento:

- Até 10.000 encaminha por circuito. Sem ExpressRoute Premium suplemento, o limite é 4.000 rotas por circuito.

- Conectividade global, ativar um circuito ExpressRoute parte do mundo para aceder aos recursos no qualquer região em vez de regiões apenas o mesmo continente localizado em qualquer lugar.

- Um aumento no número de ligações de VNet por circuito de 10 para um limite maior, consoante a largura de banda do circuito.

ExpressRoute circuitos foram concebidos para permitir a rajada temporários à rede até duas vezes a largura de banda limite que adquiridos sem custo adicional. Pode fazê-lo com ligações de redundantes. No entanto, nem todos os fornecedores de conectividade suportam esta funcionalidade; Certifique-se de que o seu fornecedor de conectividade permite esta funcionalidade antes de dependendo-lo.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Pode configurar elevada disponibilidade para a sua ligação do Azure formas diferentes, consoante o tipo de fornecedor que utiliza e o número de ExpressRoute circuitos e ligações de gateway de rede virtual que está disposto a configurar. Os pontos abaixo resumem as suas opções de disponibilidade:

- ExpressRoute não suporta protocolos de redundância router como HSRP e VRRP para implementar elevada disponibilidade. Em vez disso, utiliza um par de sessões BGP por efectuado redundante. Para facilitar a ligações altamente disponíveis à sua rede, Microsoft Azure Aprovisiona lhe com duas portas redundantes nos dois routers (parte do Microsoft edge) numa configuração activo activo.

- Se estiver a utilizar uma ligação de camada 2, implemente routers redundantes na sua rede no local numa configuração activo activo. Ligar o circuito principal para um router e o circuito secundário para a outra. Isto irá dar-lhe uma ligação altamente disponível em ambas as extremidades da ligação. Isto é necessário se necessitar do SLA ExpressRoute. Consulte o artigo [SLA para Azure ExpressRoute] [ sla-for-expressroute] para obter detalhes.

A seguinte diagrama mostra uma configuração com routers redundantes no local ligado aos circuitos principais e secundários. Cada circuito processa o tráfego para um público efectuado e um efectuado privada (cada efectuado está designado um par de /30 endereço espaços, conforme descrito na secção anterior).

![[1]][1]

Se estiver a utilizar uma ligação de camada 3, certifique-se de que fornece BGP redundante sessões que processam disponibilidade por si.

Redes virtuais podem ser ligadas a várias ExpressRoute circuitos e cada circuitos podem ser fornecido pelo fornecedores de serviços diferente. Esta estratégia fornece alta disponibilidade adicional e falhas capacidades de recuperação.

Configure uma VPN do Site para o Site como um caminho de activação pós-falha para ExpressRoute. Isto só é aplicável ao efectuado privado. Para os serviços do Azure e no Office 365, a Internet é o caminho apenas activação pós-falha.

Por predefinição, sessões BGP utilizam um valor de tempo limite inactivo de 60 segundos. Assim que uma sessão estiver excedido 3 vezes, a rota está marcada como indisponível e todo o tráfego é redirecionado para o router restante. Este limite de tempo do segundo 180 poderá ser demasiado longo para as aplicações críticas. Neste caso, pode alterar as definições de tempo limite BGP no router no local para um valor mais pequeno.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Pode utilizar o [Toolkit de conectividade do Azure (AzureCT)] [ azurect] para monitorizar a conectividade entre o seu centro de dados no local e o Azure.

## <a name="security-considerations"></a>Considerações de segurança

Pode configurar opções de segurança para a sua ligação do Azure formas diferentes, dependendo do seu preocupações de segurança e conformidade necessidades. Os pontos abaixo resumem as opções de segurança.

ExpressRoute funciona na camada 3. Podem ser evitadas ameaças na camada de aplicação ao utilizar um dispositivo de segurança de rede que restringe o tráfego para recursos legítimos. Para além disso, as ligações de ExpressRoute com efectuado público apenas podem ser iniciadas a partir no local. Isto impede que um serviço de rogue acedam e comprometer a dados no local da Internet público.

Para maximizar a segurança, adicione aplicações de segurança de rede entre à rede no local e routers de limite do fornecedor. Isto irá ajudar a impedir que o fluxo de tráfego não autorizado a VNet:

![[2]][2]

Para fins de auditoria ou conformidade, poderá ser necessário proibi-acesso direto de componentes em execução na VNet à Internet e implementar [forçada túnel][forced-tuneling]. Esta situação, deverá redirecionado voltar a através de um proxy em execução no local onde pode ser auditada tráfego da Internet. O proxy pode ser configurado para bloquear o tráfego não autorizado a fluir saída e filtrar potencialmente malicioso tráfego de entrada.

![[3]][3]

Por predefinição, o Azure VMs expor os pontos finais utilizados para fornecer acesso de início de sessão para fins de gestão - RDP e Powershell remoto para VMs Windows e SSH para baseado em Linux VMs quando implementada através do portal do Azure. Para maximizar a segurança, não ativar um endereço IP público para o seu VMs e utilize NSGs para garantir que estes VMs não são acessíveis. VMs só devem estar disponíveis utilizando o endereço IP interno. Estes endereços podem ser efectuados acessíveis através da rede ExpressRoute, permitindo docentes de DevOps no local efetuar qualquer configuração necessárias ou manutenção.

Se tem de expor os pontos finais de gestão para VMs a uma rede externa, utilize NSGs e/ou listas de controlo para restringir a visibilidade destas portas a uma lista branca de endereços IP ou redes de acesso.

## <a name="troubleshooting-considerations"></a>Considerações sobre a resolução de problemas

Se um circuito ExpressRoute anteriormente funcionamento agora não conseguir ligar-se na ausência de qualquer configuração alterações no local ou no seu VNet privado, poderá ter de contactar o fornecedor de conectividade e trabalhar com os mesmos para corrigir o problema. Também pode utilizar os seguintes comandos Azure PowerShell para executar alguns limitado a verificar e ajudar a determinar onde problemas poderão situar-se:

- Certifique-se de que o circuito foram aprovisionado:

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

O resultado deste comando mostra várias propriedades do seu circuito, incluindo `ProvisioningState`, `CircuitProvisioningState`, e `ServiceProviderProvisioningState` conforme apresentado abaixo.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Se o `ProvisioningState` não estiver definida para `Succeeded` depois de ter tentado criar um novo circuito, remova o circuito utilizando o comando abaixo e tente criá-lo novamente.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Se o seu fornecedor de já tinha aprovisionado circuito bem como a `ProvisioningState` está definido para `Failed`, ou o `CircuitProvisioningState` não é `Enabled`, contacte o seu fornecedor para obter assistência.

## <a name="solution-deployment"></a>Implementação da solução

Se tiver uma infraestrutura no local existente já configurada com um dispositivo de rede adequadas, pode implementar a arquitetura de referência ao seguir estes passos:

Se tiver uma infraestrutura no local existente já tenha configurada com um aparelho VPN, pode implementar a arquitetura de referência ao seguir estes passos:

1. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Aguarde que a ligação abra no portal do Azure e, em seguida, siga estes passos: 
  - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-hybrid-er-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

4. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Aguarde que a ligação abra no portal do Azure e, em seguida, siga estes passos: 
  - Selecione **utilizar existentes** na secção do **grupo de recursos** e introduza `ra-hybrid-er-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

6. Aguarde para a implementação concluir.

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [Implementar uma arquitetura de rede híbrido altamente disponível] [ highly-available-network-architecture] para obter informações sobre como aumentar a disponibilidade de uma rede híbrido com base em ExpressRoute a uma falha ao longo para uma ligação VPN.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Arquitetura de rede híbrida com o Azure ExpressRoute"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "Utilizar routers redundantes com ExpressRoute principais e secundários circuitos"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Adicionar dispositivos de segurança à rede no local"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "Utilizar forçada túnel para auditoria tráfego de Internet vinculadas"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Localizar a ServiceKey num circuito ExpressRoute"

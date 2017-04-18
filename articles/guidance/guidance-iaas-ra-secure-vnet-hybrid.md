<properties
   pageTitle="Implementar uma arquitetura de rede seguro híbrido no Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede seguro híbrido no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>Implementar uma DMZ entre o Azure e o seu centro de dados no local

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve os procedimentos recomendados para implementar uma rede híbrido seguro que se expande uma rede no local para Azure. Esta arquitetura de referência implementa uma DMZ entre uma rede no local e uma rede virtual Azure utilizando rotas definidas pelo utilizador (UDRs). A DMZ inclui rede altamente disponível virtual eletrodomésticos (NVAs) que implementam a funcionalidade de segurança como firewalls e inspeção de pacotes. Todo o tráfego de saída a partir do VNet é túnel vigor na Internet através da rede no local para que pode ser auditada. 

Esta arquitetura exige uma ligação para o Centro de dados no local implementado utilizando um [VPN gateway][ra-vpn], ou uma [ExpressRoute] [ ra-expressroute] ligação.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Casos de utilização típica para esta arquitectura incluem:

- Aplicações híbridas onde das cargas de trabalho executar parcialmente no local e parcialmente no Azure.

- Infraestrutura Azure que encaminha o tráfego de entrada no local e na internet.

- Aplicações necessárias para auditoria tráfego de saída. Isto é, muitas vezes, um requisito regulamentação muitos sistemas comerciais e pode ajudar a impedir a divulgação pública das informações privadas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura:

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama é na página "DMZ – privado".

[! [0]][0]

- **Rede no local.** Esta é uma rede de computadores e dispositivos ligados através de um privado rede local implementado numa organização.

- **Rede virtual Azure (VNet).** O VNet aloja a aplicação e outros recursos a executar na nuvem.

- **Gateway.** O gateway fornece conectividade entre os routers na rede no local e o VNet.

- **Aparelho virtual (NVA) da rede.** NVA é um termo genérico que descreve uma VM desempenhar tarefas como permitir ou negar acesso como uma firewall, optimizar operações WAN (incluindo a compressão de rede), encaminhamento personalizada ou outras funcionalidades de rede. 

- **Camada, camada de negócio e dados camada sub-redes na Web.** Estes são sub-redes que aloja a VMs e serviços que implementam uma aplicação de 3 camadas de exemplo em execução na nuvem. Consulte o artigo [a executar o Windows VMs para uma arquitetura de camadas no Azure] [ ra-n-tier] para obter mais informações.

- **Encaminha definidas pelo utilizador (UDR).** [Rotas definidas pelo utilizador] [ udr-overview] definir o fluxo de tráfego IP do Azure VNets. 

> [AZURE.NOTE] Dependendo dos requisitos da sua ligação VPN, pode configurar rotas limite Gateway Protocol (BGP) como alternativa à utilização UDRs para implementar as regras de reencaminhamento de chamadas direcionar tráfego de volta através da rede no local.

- **Sub-rede gestão.** Esta sub-rede contém VMs implementam gestão e as capacidades de monitorização para os componentes em execução na VNet. 

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="rbac-recommendations"></a>Recomendações RBAC

Crie várias RBAC funções para gerir os recursos na sua aplicação. Considere criar uma DevOps [função personalizada] [ rbac-custom-roles] com permissões para administrar a infraestrutura de para a aplicação. Considere criar uma centralizada de administrador do IT [função personalizada] [ rbac-custom-roles] para gerir os recursos de rede e um administrador do IT [função personalizada] de segurança separado[ rbac-custom-roles] para gerir recursos de rede segura, tais como as NVAs. 

A função DevOps deve incluir permissões para implementar o componentes da aplicação, bem como monitorizar e reinicie o VMs. A função de administrador IT centralizada deve incluir as permissões para monitorizar recursos de rede. Nenhuma destas funções devem ter acesso aos recursos NVA como devem ser restrita para a função de administrador do IT de segurança.

### <a name="resource-group-recommendations"></a>Recomendações de grupo de recursos

Recursos Azure como VMs, VNets e Balanceadores de carga podem ser facilmente geridos agrupando-os em conjunto em grupos de recursos. Em seguida, pode atribuir funções acima para cada grupo de recursos para restringir o acesso.

Recomendamos que a criação dos seguintes procedimentos:

- Um grupo de recursos que contém os sub-redes (excluindo os VMs), NSGs e os recursos de gateway para ligar à rede no local. Atribua a função de administrador IT centralizada para este grupo de recursos.

- Um grupo de recursos que contém os VMs para NVAs (incluindo o Balanceador de carga), a caixa de salto e outros VMs gestão e UDR para a sub-rede de gateway força todo o tráfego através de NVAs. Atribua a função de administrador do IT de segurança para este grupo de recursos.

- Separe os grupos de recursos para cada camada de aplicação, que contêm os Balanceador de carga e VMs. Note que este grupo de recursos não deva incluem as sub-redes para cada camada. Atribua a função DevOps para este grupo de recursos.

### <a name="virtual-network-gateway-recommendations"></a>Recomendações de gateway de rede virtual

Transmite tráfego no local para o VNet através de um gateway de rede virtual. Recomendamos que um [gateway Azure VPN] [ guidance-vpn-gateway] ou um [gateway Azure ExpressRoute][guidance-expressroute]. 

### <a name="nva-recommendations"></a>Recomendações NVA

NVAs fornecem diferentes serviços para gerir e monitorizar o tráfego de rede. Azure Marketplace oferece várias NVAs de outros programadores, incluindo:

- [Aplicação Firewall da Web barracuda] [ barracuda-waf] e [Barracuda NextGen Firewall][barracuda-nf]

- [Redes coesa VNS3 Firewall/Router/VPN][vns3]

- [Fortinet FortiGate-VM][fortinet]

- [Firewall de aplicação SecureSphere Web][securesphere]

- [Firewall de aplicação DenyAll Web][denyall]

- [Ponto de verificação vSEC][checkpoint]

Se nenhuma destes NVAs de terceiros às necessidades, pode criar uma NVA personalizado utilizando VMs. Para obter um exemplo de criação de NVAs personalizados, consulte o artigo DMZ nesta arquitectura de referência que implementa as seguintes funcionalidades:

- O tráfego é encaminhado utilizando [reencaminhamento IP] [ ip-forwarding] na NIC NVA.

- Tráfego é permitido ao passar o NVA apenas se for adequado fazê-lo. Cada VM NVA na arquitetura de referência é um simple Linux router com entregue na rede interface *eth0*e regras de correspondência de tráfego de saída definidas pela scripts personalizados distribuídos através da rede interface *eth1*o tráfego de entrada. 

- Tráfego encaminhado para a sub-rede gestão não atravessa os NVAs e os NVAs só podem ser configuradas da sub-rede gestão. Se o tráfego para a sub-rede gestão é necessária para ser encaminhada através de NVAs, não existe nenhuma rota à sub-rede de gestão para corrigir os NVAs se estes deverão falhar.  

- VMs para o NVA estão incluídos na disponibilidade de definir atrás de um balanceador de carga. UDR na sub-rede de gateway direciona os pedidos NVA ao balanceador de carga.

Recomendação outra a ter em conta está a ligar vários NVAs série com cada NVA efetuar uma tarefa de segurança especializado. Esta opção permite-cada função de segurança para ser gerido numa base por NVA. Por exemplo, uma NVA uma firewall de execução poderia colocado em série com uma NVA serviços de identidade. A variação de Facilidade de gestão é a adição de saltos de rede extra que podem aumentar latência, por isso, certifique-se de que esta não afeta o desempenho da sua aplicação.

### <a name="nsg-recommendations"></a>Recomendações NSG

O gateway VPN expõe um endereço IP público para a ligação à rede no local. Recomendamos criar um grupo de segurança de rede (NSG) para as NVA sub-rede execução regras de entrada bloquear todo o tráfego não provenientes à rede no local.

Recomendamos também que implementar NSGs para cada sub-rede para fornecer um segundo nível de proteção contra o tráfego de entrada ignorar uma NVA incorretamente configurado ou desativada. Por exemplo, a sub-rede camada web a arquitetura de referência implementa uma NSG com uma regra para ignorar todos os pedidos de além dos recebido a partir da rede no local (192.168.0.0/16) ou o VNet e outra regra que ignora todos os pedidos não feitos na porta 80. 

### <a name="internet-access-recommendations"></a>Recomendações de acesso à Internet

[Forçar túnel] [ azure-forced-tunneling] todo o tráfego da internet saída através da sua rede no local com o túnel VPN do site para o site e encaminhar à internet utilizando rede endereço tranlation (NAT). Isto irá impedir a perda acidental de quaisquer informações confidenciais armazenadas na sua camada dados tanto também permitir inspeção e auditoria de todo o tráfego de saída.

> [AZURE.NOTE] Completamente não bloquear o tráfego da Internet das camadas web, empresas e de aplicações. Se estes camadas utilizam os serviços de Azure PaaS dependem de endereços IP públicos para o registo de diagnóstico VM, transfira de extensões VM e outras funcionalidades. Diagnósticos do Azure também requer que componentes podem ler e escrever para uma conta de armazenamento Azure dependente da internet.

Ainda mais Recomendamos que verifique o tráfego de saída internet é túnel forçar corretamente. Se estiver a utilizar uma ligação VPN com o [serviço de acesso remoto e encaminhamento] [ routing-and-remote-access-service] num servidor no local, utilize uma ferramenta como [WireShark] [ wireshark] ou [Microsoft mensagem Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Recomendações de gestão do sub-rede

A sub-rede gestão contém uma caixa de hiperligação que executa gestão e a funcionalidade de monitorização. Implementa recomendações para a caixa de hiperligação que se seguem:
- Não crie um endereço IP público para a caixa de hiperligação. 
- Crie uma rota para aceder à caixa de salto através do gateway recebido e implementar um NSG na sub-rede de gestão de apenas responder aos pedidos a partir da rota permitida.
- Restringir a execução de todas as tarefas de gestão de seguro para a caixa de hiperligação.

### <a name="nva-recommendations"></a>Recomendações NVA

Incluir uma camada 7 NVA para terminar as ligações de aplicação ao nível do NVA e manter afinidade com as camadas de back-end. Isto garante conectividade simétrica na qual o tráfego de resposta das camadas de back-end devolve através de NVA.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

A arquitetura de referência implementa um balanceador de carga que encaminha o tráfego de rede no local para um conjunto de dispositivos NVA. Como debatido anteriormente, os dispositivos NVA são VMs executar regras de encaminhamento de tráfego de rede e são implementados para um [conjunto de disponibilidade][availability-set]. Este modelo permite-lhe monitorizar o débito das NVAs ao longo do tempo e adicionar dispositivos NVA em resposta ao aumentos de carregamento.

O gateway SKU VPN padrão suporta débito constante de até 100 Mbps. O SKU desempenho alta fornece Mbps de até 200. Para mais elevadas larguras de banda, considere efetuar a atualização para um gateway ExpressRoute. ExpressRoute fornece até 10 Gbps da largura de banda com uma latência inferior de uma ligação VPN.

> [AZURE.NOTE] Os artigos [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN] [ guidance-vpn-gateway] e [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute] [ guidance-expressroute] descrevem problemas adjacente escalabilidade de Azure gateways.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

A arquitetura de referência implementa um balanceador de carga distribuir a pedidos de no local para um conjunto de dispositivos NVA no Azure. Os dispositivos NVA são VMs executar regras de encaminhamento de tráfego de rede e são implementados para um [conjunto de disponibilidade][availability-set]. O Balanceador de carga regularmente consulta uma sonda de estado de funcionamento implementada em cada NVA e irá remover qualquer NVAs responder do conjunto. 

Se estiver a utilizar o Azure ExpressRoute para fornecer conectividade entre a rede VNet e no local, [configurar um gateway VPN para fornecer activação pós-falha] [ guidance-vpn-failover] se a ligação ExpressRoute ficar indisponível.

Para obter informações específicas sobre manter a disponibilidade para ligações VPN e ExpressRoute, consulte os artigos [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN] [ guidance-vpn-gateway] e [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute][guidance-expressroute].

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Todas as aplicações e monitorização do recurso devem ser realizados pela caixa de hiperligação na sub-rede de gestão. Consoante os requisitos da aplicação, poderá ter de Adicionar monitorização de recursos adicionais na sub-rede de gestão, mas novamente qualquer um dos seguintes recursos adicionais deve ser acedido através da caixa de hiperligação.

Se for conectividade de gateway da sua rede no local ao Azure para baixo, ainda pode alcançar a caixa de hiperligação ao implementar um PIP, adicioná-lo para a caixa de salto e remoto da internet.

Sub-rede cada camada a arquitetura de referência é protegido por NSG regras e pode ser necessário criar uma regra para abrir a porta 3389 para acesso RDP no Windows VMs ou 22 de acesso SSH no Linux VMs. Outras ferramentas de monitorização e gestão precisem de regras para abrir portas adicionais.

Se estiver a utilizar ExpressRoute para fornecer a conectividade entre o seu centro de dados no local e o Azure, utilize o [Toolkit de conectividade do Azure (AzureCT)] [ azurect] a monitorização e resolução de problemas de ligação.

> [AZURE.NOTE] Pode encontrar informações adicionais especificamente destinadas a monitorização e gestão de ligações de VPN e ExpressRoute nos artigos [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN] [ guidance-vpn-gateway] e [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute][guidance-expressroute].

## <a name="security-considerations"></a>Considerações de segurança

Esta arquitetura de referência implementa vários níveis de segurança: 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Encaminhamento de todos os pedidos de utilizador no local através de NVA

UDR na sub-rede de gateway bloqueia todos os pedidos de utilizador além dos recebeu no local. As fases UDR permitido pedidos para os NVAs na sub-rede DMZ privada e estes pedidos são transmitidos para a aplicação se estão autorizados pelas regras de NVA. Outras rotas podem ser adicionadas a UDR, mas certifique-se de que não inadvertidamente ignorar o NVAs ou bloquear o tráfego administrativo destinados a sub-rede gestão.

O Balanceador de carga à frente de NVAs também funciona como um dispositivo de segurança ignorando tráfego nas portas que não estão abertos nas regras de balanceamento de carga. Balanceadores de carga na arquitetura de referência escutam apenas para pedidos de HTTP na porta 80 e HTTPS na porta 443. Quaisquer regras adicionais à balanceadores de carga devem ser documentadas e o tráfego deve ser monitorizado para garantir que não existem problemas segurança.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Utilizar NSGs para bloquear/fase de tráfego entre camadas de aplicação

Cada uma das camadas web, empresas e dados restringir o tráfego entre elas utilizando NSGs. Isto é, a camada de empresas utiliza uma NSG para bloquear todo o tráfego que não têm origem na camada web e a camada de dados utiliza uma NSG para bloquear todo o tráfego que não têm origem na camada empresas. Se tiver um requisito para expandir as regras NSG para permitir o acesso mais amplo para estes camadas, avaliar estes requisitos contra os riscos de segurança. Cada novo caminho entrado representa uma oportunidade de danos acidentais ou purposeful dados de perda ou aplicação.

### <a name="devops-access"></a>Acesso DevOps

Restringir as operações que pode executar DevOps cada camada utilizando [RBAC] [ rbac] para gerir as permissões. Quando conceder permissões, siga o [princípio do menor privilégio][security-principle-of-least-privilege]. Inicie sessão todas as operações administrativas e executar auditorias normais para garantir que quaisquer alterações de configuração foram planeadas.

> [AZURE.NOTE] Para informações mais abrangente, exemplos e cenários sobre a gestão de segurança da rede com o Azure, consulte o artigo [serviços em nuvem da Microsoft e a segurança de rede][cloud-services-network-security]. Para obter informações detalhadas sobre como proteger recursos na nuvem, consulte o artigo [Introdução à segurança do Microsoft Azure][getting-started-with-azure-security]. Para detalhes adicionais sobre endereçar preocupações de segurança através de uma ligação do Azure gateway, consulte o artigo [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN] [ guidance-vpn-gateway] e [Implementar uma arquitetura de rede híbrida com o Azure ExpressRoute][guidance-expressroute].

## <a name="solution-deployment"></a>Implementação da solução

Uma implementação para uma arquitetura de referência que implementa estas recomendações está disponível no Github. Esta arquitetura de referência inclui uma rede virtual (VNet), grupo de segurança de rede (NSG), Balanceador de carga e duas máquinas virtuais (VMs).

A arquitetura de referência pode ser implementada com Windows ou Linux VMs ao seguir as instruções abaixo: 

1. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela":  
[![Implementar Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Assim que tiver aberto a ligação no portal do Azure, tem de introduzir valores para algumas das definições: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-private-dmz-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

4. Os ficheiros de parâmetro incluem o nome de utilizador codificada administrador e a palavra-passe para todos os VMs e é vivamente recomendado que imediatamente altere ambos. Para cada VM na implementação, selecione-o no portal do Azure e, em seguida, clique em **Repor palavra-passe** no pá de **suporte + resolução de problemas** . Selecione **Repor palavra-passe** na caixa de lista pendente **modo** , em seguida, selecione um novo **nome de utilizador** e **palavra-passe**. Clique no botão de **atualização** para persistir.

## <a name="next-steps"></a>Próximos passos

- Saiba como implementar uma [DMZ entre o Azure e na Internet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Saiba como implementar uma [arquitetura de rede híbrido altamente disponível](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Seguro arquitetura de rede híbrido"
